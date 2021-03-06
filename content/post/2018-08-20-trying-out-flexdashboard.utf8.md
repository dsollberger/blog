---
title: Trying Out FlexDashboard
author: Derek Sollberger
date: '2018-08-20'
slug: trying-out-flexdashboard
categories:
  - reporting
tags:
  - flexdashboard
header:
  caption: ''
  image: ''
---

Thanks to Dr. Elaine McVey's course at DataCamp, I feel that I can convert my Data Science course's lab assignments and slides to `flexdashboard`.  Today, I converted the first lab assignment.  The hope is that the students can clearly see the instructions that I am trying to convey rather than facing a wall of text.

To convert my lab, which was previously in an R Markdown document for HTML output, I had to

* explicitly used `echo = TRUE` in each code block that should be presented to the student
* used the `\huge` LaTeX font size modifier on math-mode elements
* changed the YAML heading to call for `output:
  flexdashboard::flex_dashboard:
    orientation: columns`
    
From there, I also started to arrange the "paragraphs" into separate columns for a nice arrangement.

<img src = "for Twitter.jpg">
