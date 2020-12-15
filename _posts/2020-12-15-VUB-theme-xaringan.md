---
title: A VUB theme for xaringan presentations
author: [Tim Vantilborgh]
categories: [presentations]
background: https://images.unsplash.com/photo-1535016120720-40c646be5580?ixid=MXwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=1350&q=80
---

Lately, I have been using R to create presentations. In particular, I use the [xaringan package](https://bookdown.org/yihui/rmarkdown/xaringan.html) to create html slides. Because there was no theme that fits the style of the Vrije Universiteit Brussel (VUB), I created a css file with a VUB theme. You can download the theme from my github page: [https://github.com/timvantilborgh/VUB-theme-xaringan](https://github.com/timvantilborgh/VUB-theme-xaringan)

# Why would I want to use xaringan?

Creating your slides in R with xaringan has a couple of benefits:

1. You don't need to focus on the layout, and can focus on the content instead.
2. You can embed R code in the slides using code chunks. This means that you can integrate the output of analyses in your slides immediately. For example, you don't need to copy-and-paste a correlation table into your powerpoint presentation, but just run the analysis and print the result in your R presentation.
3. If you work with git, then you have a proper version-control system for your slides.
4. You can use Latex in your presentation, meaning that you can easily work with formulas.

Given that I teach methodological courses, using R for my slides makes my life a lot easier.

If you want to see an example of xaringan slides in action (not with the VUB theme), check [here](https://slides.yihui.org/xaringan/#1)