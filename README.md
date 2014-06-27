UBC_adv-r
=========

We are a group of useRs at the University of British Columbia, who meet weekly to discuss Hadley Wickham's [Advanced R Programming](http://adv-r.had.co.nz/).  


We meet Thursdays at **1pm PST in Biodiversity 224.**

## how to contribute to this repository

* sign up for a github account (if you don't have one) [here](https://github.com/join)
* Fork this repository (click `Fork` in the top-right corner of this page)
* You now have your own personal fork of this project on your github account
* Go to your github account.  You'll find the link to your fork of the repository on the right-hand side. If you are using Rstudio, you can use this link to make a local copy of the repository on your computer [directions here](https://support.rstudio.com/hc/en-us/articles/200526207-Using-Projects).
* When you are working through examples or creating demonstrations, write in .Rmd
* push your changes to your github
* submit a [pull request](https://help.github.com/articles/using-pull-requests)
* you're done!  you can get a copy of git for your computer [here](http://www.git-scm.com/) and there is a great little walkthrough [here](https://try.github.io/levels/1/challenges/1) although most of the commands can be done through Rstudio rather than using the command line.

## using knitr

* install the package within Rstudio by:
```{r}
install.packages("knitr")
```
* the R markdown files are suffixed with .Rmd
* when you run knitr, you can choose to create an html, word doc, pdf, or .md file.  The .md files show up nicely on Github, so we reccommend creating those.  To do this, specify "md_document" in your .Rmd header like this:
```{r}
---
title: "Your title"
author: "You"
date: 'if you want a date'
output: md_document
---
```
* now your .Rmd and .md files will be uploaded to the Github repositories

## keeping your fork up to date

Once you have forked this repository, your version will quickly get out of date with the original.  When other people's documents are pulled to the master repository, you will not automatically get copies of these.  This is not a problem, but if you want to keep your version identical to the original, take a look at [this help page on syncing](https://help.github.com/articles/syncing-a-fork) and [this one on configuring a remote fork](https://help.github.com/articles/configuring-a-remote-for-a-fork)
