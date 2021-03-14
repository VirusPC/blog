# JavaScript in HTML

## Table of Contents

- [JavaScript in HTML](#javascript-in-html)
  - [Table of Contents](#table-of-contents)
  - [The ```<script>``` Element](#the-script-element)
    - [Inline Script](#inline-script)
    - [External Script](#external-script)
  - [Inline Code versus External Files](#inline-code-versus-external-files)
  - [Document Modes](#document-modes)
  - [The ```<noscript>``` Element](#the-noscript-element)

## The ```<script>``` Element

### Inline Script

1. The rest of the page content is not loaded and/or displayed until after all of the code inside the ```<script>``` element has been evaluated.

2. using ```<script>```

### External Script

1. The same as inline script, processing of the page is halted while the external file is interpreted.

2. Do not use ```<script src="example.js"/>```, it is not self-closed element.

3. If a ```<script>``` element using the ```src``` attribute and include additional JavaScript code between the ```<script>``` and ```</script>``` tags at the same time, the script file is downloaded and executed while the inline code is ignored.

4. The *GET* request of external script do not subject to the browser's cross-origin restrictions, but any JavaScript returned and executed will be.

5. For the reason behind, when including JavaScript files from a different domain, make sure you are the domain owner or the domain is owned by a trusted source. The ```<script>``` tag’s ```integrity``` attribute gives you a tool to defend against this; however, it has lim-
ited browser support.

### Tag Placement

1. Putting all ```<script>``` elements were placed **within the ```<head>``` element** on a page is a traditionally practice.
    1. Main purpose: keep external file references, both CSS files and JavaScript files, in the same area.
    2. Disadvantages: For pages that require a lot of JavaScript code, this can **cause a noticeable delay** in page rendering

2. Modern web applications typically include all JavaScript references in the <body> element, **after the page content**.
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

4. Execution order: Differ from ```deffer```, Scripts marked as ```async``` are **not guaranteed to execute in the order** in which they are specified. It is imortant that the there are no dependencies between the two.

5. Execution time: Asynchronous scripts are guaranteed to execute before the page’s ```load``` event and may execute before or after ```DOMContentLoaded```

6. Best practice: It’s recommended that asynchronous scripts not modify the DOM as they are loading.

### Dynamic Script Loading

1. You can use the DOM API in a script to add another script elements and load the resources.

    ```js
    let script = document.createElement("script");
    script.src = 'gibberish.js';
    document.head.appendChild(script);
    ```

2. The request above will be generated until the ```HTMLElement``` is attached to the DOM, and until the script itself runs.

3. By default, script that are created in this fasion are **async**. Since not all browsers support ```async``` script requests, to unify the dynamic script loading behavior, you can explicitly mark the tag as synchronous.

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

1. *XHTML* is an application of XML. Unlike HTML, in XHTML, the ```<script>``` element requires that you specify the type attribute as text/javascript.

2. In HTML, the ```<script>``` element has special rules governing how its contents should be parsed; in XHTML, these special rules don’t apply. (This means that the less-than symbol (<) in the statement a < b is interpreted as the beginning of a tag, which causes a syntax error because a less-than symbol must not be followed by a space.) 

    There are two options for fixing the XHTML syntax error.

    1.  replace all occurrences of the less-than symbol (<) with its HTML entity (```&lt;```)

    2. wrap the JavaScript code in a CDATA section.

---

## Inline Code versus External Files

---

## Document Modes

---

## The ```<noscript>``` Element

---
