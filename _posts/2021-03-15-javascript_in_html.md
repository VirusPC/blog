---
title: "Chapter 2 - Javascript in HTML" 
categories: ['Professional Javascript']
tags: ['frontend', 'javascript']
resource_path: /blog/assets/2021/03/15
---

# JavaScript in HTML

**Table of Contents**:
- [JavaScript in HTML](#javascript-in-html)
  - [The `<script>` Element](#the-script-element)
    - [Inline Script](#inline-script)
    - [External Script](#external-script)
    - [Tag Placement](#tag-placement)
    - [Deferred Scripts](#deferred-scripts)
    - [Asynchronous Scripts](#asynchronous-scripts)
    - [Dynamic Script Loading](#dynamic-script-loading)
    - [Changes in XHTML](#changes-in-xhtml)
    - [Deprecated Syntax](#deprecated-syntax)
  - [Inline Code versus External Files](#inline-code-versus-external-files)
    - [Why ExternaL Files are Better?](#why-external-files-are-better)
  - [Document Modes](#document-modes)
  - [The `<noscript>` Element](#the-noscript-element)

## The `<script>` Element

### Inline Script

1. The rest of the page content is not loaded and/or displayed until after all of the code inside the `<script>` element has been evaluated.

2. using `<script>`

### External Script

1. The same as inline script, processing of the page is halted while the external file is interpreted.

2. Do not use `<script src="example.js"/>`, it is not self-closed element.

3. If a `<script>` element using the `src` attribute and include additional JavaScript code between the `<script>` and `</script>` tags at the same time, the script file is downloaded and executed while the inline code is ignored.

4. The *GET* request of external script do not subject to the browser's cross-origin restrictions, but any JavaScript returned and executed will be.

5. For the reason behind, when including JavaScript files from a different domain, make sure you are the domain owner or the domain is owned by a trusted source. The `<script>` tag’s `integrity` attribute gives you a tool to defend against this; however, it has lim-
ited browser support.

### Tag Placement

1. Putting all `<script>` elements were placed **within the `<head>` element** on a page is a traditionally practice.
    1. Main purpose: keep external file references, both CSS files and JavaScript files, in the same area.
    2. Disadvantages: For pages that require a lot of JavaScript code, this can **cause a noticeable delay** in page rendering

2. Modern web applications typically include all JavaScript references in the `<body>` element, **after the page content**.
    1. Advantages: The page is completely rendered in the browser before the JavaScript code is processed. The resulting user experience is **perceived as faster** because the amount of time spent on a black browser window is reduced.

### Deferred Scripts

1. Purpose: To indicate that a script won’t be changing the structure of the page as it executes. As such, the script can be run safely after the entire page has been parsed.

2. Supported only for external scripts.

3. **Immediately download, deferred execution.**

4. Execution order: In HTML5 specification: **Executing in order.**. In reality: not always.

5. Execution time: In HTML5 specification: Executing before the ```DOMContentLoaded``` event. In reality, not always. so **it’s best to include just one** when possible.

6. Best practice: Since some browsers may ignore this attribute, it’s still best to **put deferred scripts at the bottom of the page**.

### Asynchronous Scripts

1. Purpose: It indicate that the page need not wait for the script to be downloaded and executed before continuing to load, and it also
need not wait for another script to load and execute before it can do the same.

2. Supported only for external scripts.

3. **immediately dowload**.

4. Execution order: Differ from `deffer`, Scripts marked as `async` are **not guaranteed to execute in the order** in which they are specified. It is imortant that the there are no dependencies between the two.

5. Execution time: Asynchronous scripts are guaranteed to execute before the page’s `load` event and may execute before or after `DOMContentLoaded`

6. Best practice: It’s recommended that asynchronous scripts not modify the DOM as they are loading.

### Dynamic Script Loading

1. You can use the DOM API in a script to add another script elements and load the resources.

    ```js
    let script = document.createElement("script");
    script.src = 'gibberish.js';
    document.head.appendChild(script);
    ```

2. The request above will be generated until the `HTMLElement` is attached to the DOM, and until the script itself runs.

3. By default, script that are created in this fasion are **async**. Since not all browsers support `async` script requests, to unify the dynamic script loading behavior, you can explicitly mark the tag as synchronous.

    ```js
    let script = document.createElement("script");
    script.src = 'gibberish.js';
    script.async = false;
    document.head.appendChild(script);
    ```

4. Resources fetched in this fashion will be hidden from *browser preloaders*. This will severely injure their priority in the resource fetching queue. Depending on how your application works and how it is used, this can severely damage performance. To inform preloaders of the existence of these dynamically requested files, you can explicitly declare them in the document head:

    ```html
    <link rel="subresource" href="gibberish.js">
    ```

### Changes in XHTML

1. *XHTML* is an application of XML. 

2. Unlike HTML, in XHTML, the `<script>` element requires that you specify the type attribute as text/javascript.

3. In HTML, the `<script>` element has special rules governing how its contents should be parsed; in XHTML, these special rules don’t apply. (This means that the less-than symbol (<) in the statement a < b is interpreted as the beginning of a tag, which causes a syntax error because a less-than symbol must not be followed by a space.)

    There are two options for fixing the XHTML syntax error.

    1. replace all occurrences of the less-than symbol (<) with its HTML entity (```&lt;```)

        ```xhtml
        <script type="text/javascript">
            function comppare(a, b) {
                if(a &lt; b) return 1;  
                return -1;
            }
        </script>
        ```

    2. wrap the JavaScript code in a CDATA section.

        ```html
        <script type="text/javascript">
        <![CDATA[
            function comppare(a, b) {
                if(a < b) return 1;  
                return -1;
            }
        ]]>
        </script>
        ```

### Deprecated Syntax

1. The type attribute uses a MIME type string to identify the contents of `<script>`, but MIME types are not standardized across browsers. On the other hand, in some cases an invalid or unrecognized MIME type value for the type attribute will cause some browsers to skip execution of the associated code. Therefore, unless you are using XHTML or the `<script>` tag requests or wraps non-JavaScript, the best practice is to **not specify a type attribute** at all.

---

## Inline Code versus External Files

### Why ExternaL Files are Better?

1. **Maintainability** -- It is much easier to have a directory for all JavaScript files so that developers can edit JavaScript code independent of the markup in which it’s used.

2. **Caching** -- Browsers cache all externally linked JavaScript files according to specific settings, meaning that if two pages are using the same file, the file is downloaded only once. This ultimately means faster page-load times.

3. **Future-proof** -- By including JavaScript using external files, there’s no need to use the XHTML or comment hacks mentioned previously. The syntax to include external files is the same for both HTML and XHTML.

---

## Document Modes

1. *quirks mode*

    Omitting the doctype at the beginning of the document.

2. *standards mode*

    ```html
    <!-- HTML 4.01 Strict -->
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//en" "http://www.w3.org/TR/html4/strict.dtd">

    <!-- XHTML 1.0 Strict -->
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd"> 
    
    <!-- HTML5 -->
    <!DOCTYPE html>>
    ```

3. *almost standards mode*

    ```html
    <!-- HTML 4.01 Transitional -->
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">

    <!-- HTML 4.01 Frameset -->
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Frameset//EN" "http://www.w3.org/TR/html4/frameset.dtd">

    <!-- XHTML 1.0 Transitional -->
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <!-- XHTML 1.0 Frameset --> <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd">
    ```

Because *almost standards mode* is so close to *standards mode*, the distinction is rarely made. People talking about “standards mode” may be talking about either.

---

## The `<noscript>` Element

1. What it used for?

    The `<noscript>` element was created to provide alternate content for browsers without JavaScript.

2. What it contains?

    The `<noscript>` element can contain any HTML elements, aside from `<script>`, that can be included in the document `<body>`.

3. When the content will be showed?

   Any content contained in a `<noscript>` element will be displayed under only the following two circumstances.

   1. The browser doesn’t support scripting.

   2. The browser’s scripting support is turned off.

---

Reference:

- Professional JavaScript for Web Developers 4th Edition
