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

## Inline Code versus External Files

## Document Modes

## The ```<noscript>``` Element
