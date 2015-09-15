---
layout: post
title:  "Latex + R + tikzDevice"
date:   2015-09-15 16:30:00
categories: [latex, plot, r]
tags: latex, tikzDevice, R
---

Implementing nice looking plots in a **Latex** document was harder than expected. I use **R** (with *Rstudio*) combined with **ggplot2** to plot my data and wanted a convenient way to insert my plots into Latex documents. I use something similar with [Inkscape](https://www.ctan.org/tex-archive/info/svg-inkscape?lang=en). It automatically sets the right font, properly writes Latex symbols and respects the proper font sizes. 

The best solution I found is using the [tikzDevice package](https://github.com/yihui/tikzDevice) for R and the [tikz (available in the **pgf** package)](https://www.ctan.org/pkg/pgf?lang=en) package from Latex. **TikZ** plots will consiste of vectors that will directly be coded into the LaTeX document so that there is no loss in image quality.
First you need to install **tikzDevice** in R through `install.packages("tikzDevice")`. For the following example to work, you will also need to install `ggplot2`.
Once you have both R packages, you can use this Rscript as an example:

	library(tikzDevice)
	library(ggplot2)

	#For some reason, Rstudio needs to know the time zone...
	options(tz="CA")

	#Dummy data for the plot
	y <- exp(seq(1,10,.1))
	x <- 1:length(y)

	data <- data.frame(x = x, y = y)

	#Create a .tex file that will contain your plot as vectors
	#You need to set the size of your plot here, if you do it in LaTeX, font consistency with the rest of the document will be lost
	tikz(file = "test.tex", width = 5, height = 5)
	#Simple plot of the dummy data using LaTeX elements
	plot <- ggplot(data, aes(x = x, y = y)) + 
		geom_line() +
		#Space does not appear after Latex
		ggtitle( paste("Fancy \\LaTeX ", "\\hspace{0.01cm} title")) +
		labs( x = "$x$ = Time", y = "$\\Phi$ = Innovation output") +
		theme_bw()

	#This line is only necessary if you want to preview the plot right after compiling
	print(plot)

	#Necessary to close or the tikxDevice .tex file will not be written
	dev.off()

The output provided by this script in R looks like this:

<div align="center">
<img src="{{ site.url }}/assets/2015-09-15-R-output-plot.png"style="width:604px;height:456px;">
</div>

As you can see, the LaTeX codes are clearly visible. The font is R's default font for now. If you check the folder where you **sourced** your file, you will find a [test.tex file]("{{ site.url }}/assets/test.tex") which contain the plot information as vectors.
You should now create a **.tex** in the same folder your **test.tex** file is in and use this simple code to implement the image in it:

	\documentclass{article}
	%The package tikz is available in pgf
	\usepackage{tikz}

	\begin{document}

	\begin{figure}
		%Do not try to scale figure in .tex or you loose font size consistency
	    	\centering
		%The code to input the plot is extremely simple
		\input{test.tex}
		%Captions and Labels can be used since this is a figure environment
		\caption{Sample output from tikzDevice}
		\label{plot:test}
	\end{figure}

	\end{document}

The result should look like this. As you can see, LaTeX symbols are now properly written, the font is also similar to the rest of the document and the plot can be inserted in your LaTeX document !

<div align="center">
<img src="{{ site.url }}/assets/2015-09-15-Latex-output-plot.png"style="width:604px;height:456px;">
</div>

The only problem I still have concerns the font size which is slightly larger than the rest of the document...
When checking the **test.tex** file, it seems that a scale is applied to the text. The **scale** parameter is specific for each element of the graph (title, axis legend, ...).

	\node[text=drawColor,anchor=base,inner sep=0pt, outer sep=0pt, scale=  1.44] at (200.36,336.38) {Fancy \LaTeX  \hspace{0.01cm} title};

It should be possible to modify this parameter in R, but I still don't know how to properly manipulate it in connection with LaTex. To be continued !
