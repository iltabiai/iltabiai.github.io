---
layout: post
title:  "Explore scientific publications"
date:   2017-04-17 18:29:00
comments: true
categories: [research]
tags: research
---

Lately, I have been exploring methods to extract large amount of data from scientific publications as part of my work with [Kambiz Chizari](https://twitter.com/kambizchizari) for the [Colper Science project](https://colperscience.com/), the [Mozilla Open Science Labs 2017](https://science.mozilla.org/) and some future works we intend to do with the [Nalyze team](https://nalyze.net/).

In this post, I explore 3 packages offered by the [Content Mine group](http://contentmine.org/): getpapers, ami and norma. These 3 packages should allow us to download large sets of papers about a certain subject, normalize the obtained data to better explore it and then start analyzing using basic tools such as word counts and regular expressions.

The first consists in getting the scientific papers, to do so, we need to get started with `getpapers`.
You can get `getpapers` in one of the [ContentMine organization repositories](https://github.com/ContentMine/getpapers),
clone the repo, `cd` into the folder and use:

`sudo npm install --global getpapers`

to install the package. The package is used for: _"getpapers can fetch article metadata, fulltexts (PDF or XML), and supplementary materials. It's designed for use in content mining, but you may find it useful for quickly acquiring large numbers of papers for reading, or for bibliometrics."_  (from the repo)

We are going to try to investigate [Polycaprolactone (PCL)](https://en.wikipedia.org/wiki/Polycaprolactone) and [FDM 3D printing](https://en.wikipedia.org/wiki/Fused_deposition_modeling). PCL is an interesting polymer because it is biodegradable and has a very low melting temperature (60\\(^o\\) C), which means that it can easily be remodeled by hand, simply by pouring hot water on it. The subject I am interested in here is FDM 3D printing of PCL, mostly for biomedical applications.
We are fist going to try using [EuropePMC](https://europepmc.org/), a repository of **open access** scientific data (books, articles, patents...). `EuropePMC` is attached to `PubMed`, and I quickly realized that it seems to mostly host papers about medical and biomedical applications (that is why our research will be focused on biomedical applications).

The first thing we need to do is query `EuropePMC` to obtain a list of papers which contain the words in our query. Since we would like to have the papers about `3D printing` and `PCL`, these are going to be the words in our query. The default API used by `getpapers` is `EuropePMC`, otherwise, it is also possible to use `IEEE` or `ArXiv`.

The query we will try will be:

`ilyass@ilyass-PC:~/Repositories/getpapers$ getpapers -q 'PCL 3D print' -n -o '3DPCL'`

* `-n` runs the query in _no-execute_ mode, which means that nothing will actually be downloaded, but the number of results found will be returned.
* `-q` is the query itself
* `-o` is the output folder for the query

```
ilyass@ilyass-PC:~/Repositories/getpapers$ getpapers -q 'PCL 3D print' -n -o '3DPCL'
info: Searching using eupmc API
info: Running in no-execute mode, so nothing will be downloaded
info: Found 57 open access results
```

Now, we simply need to use the same command again, without the `-n` flag to download the results. It is *necessary* to add the`-x` flag, which will download the full text article in the `.XML` structured format (we need to analyze the data later). It is possible to add a `-p` to the command to automatically download the PDF files for the request. When I tried it for this request, 8 papers among the 57 found had no PDF files available.
Run it:

`ilyass@ilyass-PC:~/Repositories/getpapers$ getpapers -q 'PCL 3D print' -o '3DPCL' -p -x`

(It might take a while depending of how many papers your request yields)

Take a look at the data:

```terminal
ilyass@ilyass-ThinkPad-X1:~/Repositories/getpapers/3DPCL$ tree
3DPCL/
├── eupmc_fulltext_html_urls.txt
├── eupmc_results.json
├── PMC2657346
│   ├── eupmc_result.json
│   └── fulltext.pdf
├── PMC2935622
│   ├── eupmc_result.json
│   └── fulltext.pdf
├── PMC3002806
│   └── eupmc_result.json
...
```

* `eupmc_fulltext_html_urls.txt` contains the list of URLs for all articles
* `eupmc_results.json` contains the result from the API, it is the best place to start exploring the data. Each paper is a `JSON` object, with an author, abstract, etc...
* Then, there is a folder for each paper, and each folder contains a `eupmc_result.json` which is basically the `JSON` object from the master `eupmc_result.json` file. It will also contain `full_text.pdf` and `full_text.xml` if you used the `-p` and `-x` flags.

<br>
Now that we have the data, we need to normalize it to [ScholarlyHTML](https://github.com/ScholarlyHTML/spec/). Most APIs (wether it is PubMed, `ArXiv`, `EuropePMC` or others) will return data in different structured formats. `ScholarlyHTML` is a common format designed to explore data from any of these APIs.
To convert the data we have, we need [norma](https://github.com/ContentMine/norma/releases). In order to install it, [head to the releases on the Github repository](https://github.com/ContentMine/norma/releases) and install the `.deb` package on your Linux machine using: `sudo dpkg -i <norma.deb file>`

Then use it with:

`ilyass@ilyass-PC:~/Repositories/getpapers$ norma --project 3DPCL -i fulltext.xml -o scholarly.html --transform nlm2html`

* `-i` provides the input file names in each folder in the project folder
* `-o` is the desired name for the Scholarly output

Now if you take a look at your data again:

```terminal
3DPCL
├── eupmc_fulltext_html_urls.txt
├── eupmc_results.json
├── PMC2657346
│   ├── eupmc_result.json
│   ├── fulltext.pdf
│   ├── fulltext.xml
│   └── scholarly.html
├── PMC2935622
│   ├── eupmc_result.json
│   ├── fulltext.pdf
│   ├── fulltext.xml
│   └── scholarly.html
├── PMC3002806
│   └── eupmc_result.json
...
```

There is a `scholarly.html` file for each folder where there is also a `fulltext.xml`.
The `scholarly.html` file is a document you can open and explore with your web browser.


<div align="center">
<img src="{{ site.url }}/assets/scholarly_example.jpg"/>
</div>

<br>
The next step is now to analyze the data we have in such a way that it makes your future readings more efficient, faster and to find the papers you should really read throughly among all the ones we found.
To do so, we are going to try the [ami package](https://github.com/ContentMine/workshop-resources/tree/master/software-tutorials/ami). It is a collection of plugins designed to extract specific pieces of information called _facts_. Currently, `ami` plugins appear to be optimized to extract facts about genes, proteins, agronomy, chemical species, phylogenetics, some diseases. There are no plugins for engineering or material sciences (yet), so we will use two basic plugins for now to try to get some insights about our data: word frequencies and regular expressions (regex).

But first, install it by downloading the latest `.deb` package release [here](https://github.com/ContentMine/ami/releases) and install it using `sudo dpkg -i <.deb file>`.

Now let's run a basic word frequency plugin from `ami` on our data:

`ilyass@ilyass-PC:~/Repositories/getpapers$ ami2-word --project 3DPCL -i scholarly.html --w.words wordFrequencies`

If you look at your data again:

```terminal
3DPCL/
├── eupmc_fulltext_html_urls.txt
├── eupmc_results.json
├── PMC2657346
│   ├── eupmc_result.json
│   ├── fulltext.pdf
│   ├── fulltext.xml
│   ├── results
│   │   └── word
│   │       └── frequencies
│   │           ├── results.html
│   │           └── results.xml
│   └── scholarly.html
...
```

* `results.html` file which shows the frequency of each word in each article using a cloud of words (the size of word depends of its frequency)
* `results.xml` which shows the occurence of each word:

```
<?xml version="1.0" encoding="UTF-8"?>
<results title="frequencies">
 <result title="frequency" word="and" count="370"/>
 <result title="frequency" word="the" count="278"/>
 <result title="frequency" word="for" count="106"/>
 <result title="frequency" word="printing" count="97"/>
 <result title="frequency" word="tissue" count="83"/>
 ...
 ```

 The problem, as you might have noticed, is that common words (such as `and` or `the`) are faking our results. We can get rid of them thanks to a stopwords list. I wrote a very simple python script which writes the stopwords from [nltk/corpora](http://www.nltk.org/book/ch02.html) to a text file which can then be used by `ami`. The script [can be found here](https://gist.github.com/ilyasst/6e7998246845f0925d8fc7ec6cdbdb54), and its result, the text file containing all the stopwrods can directly be [downloaded here](https://gist.github.com/ilyasst/d90c9e0c6cb0e808c7fb994ee1a7616c).

 We can launch again the word counter plugin with the stopwords:

 `ilyass@ilyass-PC:~/Repositories/getpapers$ ami2-word --project 3DPCL -i scholarly.html --w.words wordFrequencies --w.stopwords stopwords.txt`

 The results obtained this time are more interesting since they only contain _relevant_ words:

 ```xml
 <?xml version="1.0" encoding="UTF-8"?>
<results title="frequencies">
 <result title="frequency" word="tissue" count="53"/>
 <result title="frequency" word="cell" count="50"/>
 <result title="frequency" word="cells" count="46"/>
 <result title="frequency" word="ECM" count="43"/>
 <result title="frequency" word="Biomaterials" count="36"/>
 <result title="frequency" word="heart" count="33"/>
 <result title="frequency" word="mechanical" count="31"/>
 ...
 ```

 We can now explore all the word frequency results by going through them with a data mining script written with respect to what we are looking for.

 Let's try now to explore the regex functionality provided by `ami`. To do so, we need to create a `.XML` file which contains the [regular expressions](https://en.wikipedia.org/wiki/Regular_expression) we will use.
 For this case, I will use a simple file which finds all occurences of:
 * "PCL" or "Polycaprolactone" or "polycaprolactone"
 * "FDM" or "Fused Deposition Modeling" or "Fused deposition modeling" or "fused deposition modeling"

 The file I will create has to be respect the following format:

 ```xml
<compoundRegex title="3DPCL">
<regex weight="1.0" fields="PCL">([Pp]olycaprolactone)</regex>
<regex weight="1.0" fields="PCL">(PCL)</regex>
<regex weight="1.0" fields="FDM">([Ff]used\s[Dd]eposition\s[Mm]odeling)</regex>
<regex weight="1.0" fields="FDM">(FDM)</regex>
</compoundRegex>
```

The weight parameter influences the relative importance given to each match (I kept it at 1 for now), while the regex-query itself is provided between `()` in each line.

This file should be saved in the folder of your project (in my case it is `3DPCL`) and should be a `.xml` file. In my case, I named it `pcl_fdm.xml`. We can then use the regex plugin:

`ilyass@ilyass-PC:~/Repositories/getpapers$ ami2-regex --project 3DPCL/ -i scholarly.html --r.regex 3DPCL/pcl_fdm.xml --context 40 60`
* `--context` flag is convenient as it will provide each result us with 40 characters before the regular expression is found and 60 characters after it, allowing us to quickly evaluate how relevant each answer is

If we take a look at the results again after running the command:

```terminal
3DPCL/
├── eupmc_fulltext_html_urls.txt
├── eupmc_results.json
├── pcl_fdm.xml
├── PMC4709372
│   ├── eupmc_result.json
│   ├── fulltext.pdf
│   ├── fulltext.xml
│   ├── results
│   │   ├── regex
│   │   │   └── 3DPCL
│   │   │       └── results.xml
│   │   └── word
│   │       └── frequencies
│   │           ├── results.html
│   │           └── results.xml
...
```

We now have results for our regex query too. If we open a result:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<results title="3DPCL">
 <result pre="" name0="PCL" value0="PCL" post="is similar to PLA and PGA but it has a much slower" xpath="/html[1]/body[1]/div[2]/div[6]/p[2]"/>
 <result pre="ote osteoblast growth and maintain its phenotype, " name0="PCL" value0="PCL" post="scaffold has been used as a long-term implant in t" xpath="/html[1]/body[1]/div[2]/div[6]/p[2]"/>
 <result pre="981; Rich et al. 2002). However, the synthesis of " name0="PCL" value0="PCL" post="with other fast-degradable polymers can tune degra" xpath="/html[1]/body[1]/div[2]/div[6]/p[2]"/>
 <result pre="inting technologies [i.e. 3D printing (3DP)], and " name0="FDM" value0="FDM" post="are most widely used for the construction of tissu" xpath="/html[1]/body[1]/div[2]/div[6]/p[2]"/>
 ```

 For each result, we get the context in the `pre` and `post` fields, the `value0` and `name0` which are related to the regex query we created and the `xpath`, which is the exact position of the sentence in the HTML tree of the `ScholarlyHTML` file attached to this article.

 This result might seem a bit rough on the eyes, but `.XML` is a structured format which can be explored quite easily with `python` for example.
