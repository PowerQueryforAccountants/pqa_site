---
title: "Power Query Syntax Highlighter in Sublime Text"
linktitle: "Power Query Syntax Highlighter in Sublime Text"
date: 2019-09-30
draft: true
tags: [
    "power query"
]
archives: ["2019"]
prev: /post/creating-a-simple-accounting-system-with-excel-and-power-query-p2/
next: /post/creating-a-simple-accounting-system-with-excel-and-power-query-p3/
---


## Power Query Syntax Highlighter in Sublime Text
<br>

Power Query advanced editor is a great tool for writing Power Query M code. However, sometimes I still use my favorite text editor when dealing with M code especially the features like the multiple cursors, syntax higlighting, etc.

Sublime text is a great text editor. It's fast, flexible, extensible with packages, and with unlimited trial period (I encourage you to buy a license though). However, it does not support syntax highlighting for M code by default. Searching the Package Control repository does not show up also any existing packages to support M code syntax-highlighting.

So I decided to search in Github. I found this [link](https://github.com/microsoft/DataConnectors). It contains `.tmLanguage` files for PowerQuery inside the `editors/Sublime Text folder`. 

Sublime Text 3 supports `.tmLanguage` files but the office documentation shows only how to use the `.sublime-syntax` file. I hack around the files and came up with my `.sublime-syntax`. You can download it [here](https://github.com/PowerQueryforAccountants/PowerQuerySublimeSyntax).

To use it:

1. Open Sublime Text 3 and go to **Tools > Developer > New Syntax**. 
2. Copy the `.sublime-syntax` file to the new file.
3. Save it to your packages location.

### Results
The repo contains also a sample Power Query M code. If you open the file in Sublime Text, it should look like this

![Syntax Highlight](/img/power_query_syntax_highlighter/syntax.png)

Notice how the keywords `let` and `in` are highlighted correctly. Also, `null` and `true` primitive types are differentiated quite nicely.

### Conclusion
The `.sublime-syntax` file I made is far from perfect. I've just hacked around the files and test it. If you notice some bugs, feel free to leave a comment below so I could check it.

Till then, **Stay Querious & Happy coding!**