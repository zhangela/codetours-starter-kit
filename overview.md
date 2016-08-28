---
title: Overview
code: https://github.com/quora/parsecss/blob/74130ab715bf422779e8a8c3b43d556325fb44b9/src/parse.js
---

`parsecss` is an npm module written by Michael Yong at Quora. This module works as follows: given the contents or path of a CSS stylesheet, return a JSON object which gives you information about how those styles are used. `parsecss` was written to support critical CSS inlining on dynamic pages. The JSON structure it produces is optimized for figuring out which CSS rules should be applied to a given page. We use postcss to do most of the heavy lifting, but our JSON output makes it easier to use than an AST for certain use cases. 

<a href="https://github.com/quora/parsecss/blob/74130ab715bf422779e8a8c3b43d556325fb44b9/src/parse.js#L47-L61" id="targetRepository"><h4>parse font-face and keyframes</h4></a>

We want to specialize case 2 types of @rules: `font-face` and `keyframes`, because they are almost always critical. We use `postcss` to do most of the traversing, and push CSS styles that match `font-face` or `keyframes` onto the arrays storing a list of those rules. 

<a href="https://github.com/quora/parsecss/blob/74130ab715bf422779e8a8c3b43d556325fb44b9/src/parse.js#L75-L77" id="targetRepository"><h4>split rules into individual selectors</h4></a>

This essentially splits `a, b {}` into `a {}` and `b {}`, unless `a` and `b` actually refer to the same classes, or don't refer to any classes. 

<a href="https://github.com/quora/parsecss/blob/74130ab715bf422779e8a8c3b43d556325fb44b9/src/parse.js#L78-L93" id="targetRepository"><h4>add class-specific css to return value</h4></a>

For each css rule who selects classes, we append `[classNames, cssString]` to `classListCssPairs`. If that css rule doesn't apply to any classes, we append it to `globalCss` so that we can conservatively include them when we try to return critical CSS. 

Ultimately, when we are using this tool to return critical CSS for a dynamically rendered page, we simply parse the HTML of that page to get all the CSS classes that were used, then concat all the matching CSS definitions in`classListCssPairs`, along with all the `fontface`, `keyframes`, `glocalCss` rules. 
