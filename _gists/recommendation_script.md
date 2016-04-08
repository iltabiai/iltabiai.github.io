---
layout: post
title:  "Recommendation script"
date:   2016-04-14 10:30:00
comments: false
categories: 
tags: 
---

```
# -*- coding: utf-8 -*-
from nalyzer_core import Entity
import logging, pydash as _
from user import User
import numpy   
import operator
from sklearn.decomposition import NMF

logger = logging.getLogger(__name__)

class Recommend(Entity):
    _collection = 'users'

    def __init__(self, K_latent = 2):
        logger.info(" Starting Recommendation analysis")
        logger.info(" Latent features set up to %s" %(K_latent))
        self.K_latent = K_latent
        self.get_list_all_items()
        self.generate_recommend_matrix()
        self.generate_init_guesses()
        self.scipy_nmf()
        #self.matrix_factorization()
        self.update_user_recommendations()
                
    #This function returns a list containing all the known items in the 
    #database and a dict 
    def get_list_all_items(self):
        all_items = []
        item_index = {}
        all_users_couch = User.get_all_in_view('search/by_username') 
        self.all_users_couch = all_users_couch
        for user in self.all_users_couch:
            for item in user["value"]["podcasts"]:
                #This list contains all the known items by the system (unique)
                if item not in all_items:
                    all_items.append( str(item) ) 
                    #This index will be used later to identify each item in the 
                    #list
                    item_index[ int(all_items.index(item)) ] =  item
        logger.info(" Total list of %s unique items." % len(all_items))
        self.item_index = item_index
        self.all_items = all_items

      
    #This functions generates a vector of length all known items for each 
    # user, containing 0 if item not in user list, 1 if in 
    # We'll call these vectors presence vectors
    def generate_user_vector_items(self, user):
        user_vector = []
        self.all_users_list.append( user["value"]["_id"] )
        for item in self.all_items:
            if item in user["value"]["podcasts"]:
                user_vector.append(int(1))
            else:
                user_vector.append(int(0))
        return user_vector

    #This functions generates a list a matrix R structured such as:
    #               item_index[0] pod_index[1] ... pod_index[n]
    # user_index[0]     0/1               0/1              0/1
    # user_index[1]     0/1               0/1              0/1
    # ...
    # user_index[n]     0/1               0/1              0/1
    def generate_recommend_matrix(self):
        R = []
        self.all_users_list = []
        user_index = {}
        for user in self.all_users_couch:
            #We generate R by appending user vector presence
            R.append( self.generate_user_vector_items(user) )
            user_index[ int(self.all_users_list.index(user["value"]["_id"])) ] = str(user["value"]["_id"])
        logger.info(" Created presence vectors for %s users" % len(self.all_users_list))
        self.user_index = user_index
        self.R = numpy.array(R)
        
    def scipy_nmf(self):
        """
            W or P   : model.transform returns W, shape:(N x K)
            H or Q.T : attribute fit.components_, shape:(K x M)
            R        : Input matrix (N x M)
            K        : the number of latent features
        """
        logger.info(" Starting Non-Negative Matrix Factorization using scipy-sklearn package")
        #Setup NMF parameters
        #http://scikit-learn.org/stable/modules/generated/sklearn.decomposition.NMF.html
        model = NMF(n_components=2, init='nndsvd', solver='cd', tol=0.00000001, max_iter=10000, random_state=None, alpha=0.0, l1_ratio=0.0, verbose=0, shuffle=False, nls_max_iter=2000, sparseness=None, beta=1, eta=0.1)
        #print self.R
        #fit.model to get H or Q.T
        fit = model.fit(self.R)
        #transform to get P or W
        transform = model.transform(self.R)
        #print "transform: W"
        #print transform
        #print "components_: H"
        #print fit.components_
        #Build approximation of R (Matrix factorization)
        # R = W .H
        self.nR_scipy = numpy.dot(transform, fit.components_)
        #print "self.nR_scipy:"
        #print self.nR_scipy
        logger.info(" Convergence reached after %s steps" % fit.n_iter_)
        logger.info(" Reconstruction error = %s" % model.reconstruction_err_)
        
        
    def generate_init_guesses(self):
        # Initial guesses are random, that means that the minimization could
        # sometimes fail because of specific intial values (super rare)
        # If it does, just relaunch and new random values will solve the problem
        N = len( self.R )
        M = len( self.R[0] )
        self.P_init = numpy.random.rand(N, self.K_latent)
        self.Q_init = numpy.random.rand(M, self.K_latent)
        logger.info(" Initial guess matrixes ready")
    
    #This is the heart of the problem, we want to find 2 matrixes P and Q
    #such that R ~= P x Q.T
    # Default values: beta = 0.02 and alpha = 0.0002
    def matrix_factorization(self, steps=100000, alpha=0.0002, beta=0.02, relative_convergence=0.00001, absolute_convergence=0.001):
        """
        @INPUT:
            R     : a matrix to be factorized, dimension N x M
            P     : an initial matrix of dimension N x K
                    represents the strength of association between a user and
                    the features
            Q     : an initial matrix of dimension M x K
                    represents the strength of association between an item
                    and the features
            K     : the number of latent features
            N     : number of users
            M     : number of items
            steps : the maximum number of steps to perform the optimisation
            alpha : the learning rate
            beta  : the regularization parameter
        @OUTPUT:
            the final matrices P and Q
        """
        R = self.R
        Q = self.Q_init
        P = self.P_init
        K = self.K_latent
        
        logger.info(" Matrix refactorization:")
        logger.info(" Max steps= %s; alpha= %s; beta= %s" %(steps, alpha, beta))
        logger.info(" Relative convergence= %s; Absolute convergence= %s" %(relative_convergence, absolute_convergence))
        
        #numpy: X.T transposes X
        Q = Q.T
        e_previous = 0
        for step in xrange(steps):
            for i in xrange(len(R)):
                for j in xrange(len(R[i])):
                    if R[i][j] > 0:
                        #The error (residual) is computed here
                        eij = R[i][j] - numpy.dot(P[i,:],Q[:,j])
                        #We compute the regularized gradient (gradient descent)
                        for k in xrange(K):
                            P[i][k] = P[i][k] + alpha * (2 * eij * Q[k][j] - beta * P[i][k])
                            Q[k][j] = Q[k][j] + alpha * (2 * eij * P[i][k] - beta * Q[k][j])
            eR = numpy.dot(P,Q)
            e = 0
            for i in xrange(len(R)):
                for j in xrange(len(R[i])):
                    if R[i][j] > 0:
                        #We then compute the error from the regularized gradient
                        e = e + pow(R[i][j] - numpy.dot(P[i,:],Q[:,j]), 2)
                        for k in xrange(K):
                            e = e + (beta/2) * ( pow(P[i][k],2) + pow(Q[k][j],2) )
            #If error smaller than threshold, break the loop
            e_now = e
            #Since it is a minimization algorithm, absolute convergence is hard to reach
            if e < absolute_convergence:
                logger.info(" ACHIEVED ABSOLUTE CONVERGENCE")
                break
            relative_e = abs(e_now-e_previous)
            #Relative convergence is more likely to occur
            if relative_e < relative_convergence:
                logger.info(" ACHIEVED RELATIVE CONVERGENCE")
                break
                
            if int(step) % 1000 == 0:
                logger.info(" Step:%s, Absolute Error:%s, Relative Error: %s" % (step, e, relative_e))
            e_previous = e_now
        logger.info(" Convergence reached after %s steps" % step)
        logger.info(" Final ABSOLUTE error = %s; Final RELATIVE error = %s" % (e, relative_e))
        logger.info(" If the error is too high, you might want to make the learning rate alpha higher, or add more steps")
        logger.info(" This is a minimization algo, it rarely reaches an ansolute error of 0.")
        self.nP = P
        self.nQ = Q.T 
        #Each line of nR contains a row of values, the higher the value, the 
        #more likely the user will love the item
        self.nR = numpy.dot(self.nP, self.nQ.T)


```
