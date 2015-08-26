# Flex SDK #

<ol>
<li>Download Adobe's free <a href='http://www.adobe.com/products/flex.html'>Flex SDK</a>.</li>
<li>Check out the project into a directory (e.g. <code>~/wami-recorder</code>) and compile it from the command line within that directory:</li>
</ol>

```
        mxmlc -compiler.source-path=src \
              -static-link-runtime-shared-libraries=true \
              -output example/client/Wami.swf \
              src/edu/mit/csail/wami/client/Wami.mxml
```

# Flash Builder #

If you prefer to develop in Eclipse, you have two choices.  You can either download Flash Builder and use the standalone IDE (which is based off of Eclipse), or you can install it as a plug-in using [these directions](http://kb2.adobe.com/cps/905/cpsid_90599.html#main_Install%20your%20software).