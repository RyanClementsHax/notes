# Browsers

## [Speculative parsing](https://developer.mozilla.org/en-US/docs/Glossary/speculative_parsing)

> Traditionally in browsers the HTML parser ran on the main thread and was blocked after a </script> tag until the script has been retrieved from the network and executed. Some HTML parser, such as Firefox since Firefox 4, support speculative parsing off of the main thread. It parses ahead while scripts are being downloaded and executed. The HTML parser starts speculative loads for scripts, style sheets and images it finds ahead in the stream and runs the HTML tree construction algorithm speculatively. The upside is that when a speculation succeeds, there's no need to reparse the part of the incoming file that was already scanned for scripts, style sheets and images. The downside is that there's more work lost when the speculation fails.

## Read/writing dom

Writing to the dom is expensive so it is in your best interest to batch your reads and your writes so you reduce the number of paints that have to be done
