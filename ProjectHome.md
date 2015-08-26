# The Problem #

As of this writing, most browsers still do not support [WebRTC's](http://www.webrtc.org/) [getUserMedia()](http://dev.w3.org/2011/webrtc/editor/getusermedia.html#widl-NavigatorUserMedia-getUserMedia-void-MediaStreamOptions-options-NavigatorUserMediaSuccessCallback-successCallback-NavigatorUserMediaErrorCallback-errorCallback), which promises to give web developers microphone access via Javascript.  This project achieves the next best thing for browsers that support Flash.  Using the WAMI recorder, you can collect audio on your server without installing any proprietary media server software.

# The Solution #
The WAMI recorder uses a light-weight Flash app to ship audio from client to server via a standard HTTP POST.  Apart from the security settings to allow microphone access, the entire interface can be constructed in HTML and Javascript. Try it out below:
&lt;wiki:gadget url="https://wami-recorder.googlecode.com/hg/example/wami-recorder.xml" width="600" height="160" border="0" /&gt;

# The Code #

This project contains both client and server-side code.  If you just want to use the client-side code as-is, perhaps the easiest approach is to download the files directly via the links above.  Otherwise, you can check out this project using
[Mercurial](http://mercurial.selenic.com/) and [get started](https://code.google.com/p/wami-recorder/wiki/GettingStarted) with Flex:

`hg clone https://code.google.com/p/wami-recorder/`


## The Client ##
The Flash app exposes most of its important parameters and functionality to the `Javascript`.
```
Wami.startRecording(myRecordURL);
Wami.stopRecording();

Wami.startPlaying(anyWavURL);
Wami.stopPlaying();
```
You can use the well-respected [SWFObject library](http://code.google.com/p/swfobject/) to embed the Flash app, and then access it in the same way as our [example code](https://code.google.com/p/wami-recorder/source/browse/example/client/recorder.js).  Take a look at our [quirks page](https://code.google.com/p/wami-recorder/wiki/Quirks) to get acquainted with the idiosyncrasies of Flash and Javascript on different browsers and operating systems.

If you want to modify the Flash content you can download the free [Flex SDK](http://www.adobe.com/products/flex.html), and compile it from the command line.  For a full-fledged IDE,  your free options are more limited.  For academic use, such as [collecting audio for a study](http://groups.csail.mit.edu/sls//publications/2010/McGraw_LREC2010.pdf) via [Amazon Mechanical Turk](https://www.mturk.com/mturk/welcome), you can register for a free [educational Adobe Flash Builder license](http://www.adobe.com/devnet/edu.html).

## The Server ##
If you want to collect audio from the browser, there is no getting around the need to host your own server.  However,
a key feature of this project is that there is no need to configure an entire Flash Media Server just to collect audio from the web.  You can choose whatever server-side technology you prefer.  You could, for instance, host this simple [PHP](http://www.php.net/) script on [Apache2](http://httpd.apache.org/):

```
<?php
parse_str($_SERVER['QUERY_STRING'], $params);
$name = isset($params['name']) ? $params['name'] : 'output.wav';
$content = file_get_contents('php://input');
$fh = fopen($name, 'w') or die("can't open file");
fwrite($fh, $content);
fclose($fh);
?>
```
Notice that this code optionally takes a URL query parameter to specify a file name.  With the appropriate permissions, the PHP code will write a file  with this nam to disk.  You can pass a different file name every time you record to distinguish between individual users, sessions, and utterances.  You might wish to use random numbers generated in Javascript and cookies stored in the browser to track users across browser reloads and to name their corresponding files.  It should be noted that the example above suffers from security issues, and should probably be modified for actual deployment.
```
Wami.startRecording('http://localhost/test.php?name=USER.SESSION.UTTERANCE.wav');
```

A slight complication occurs if the URL that you use for playing or recording does not point to the same host that serves the SWF file.  In that case, you will need to serve a [crossdomain.xml](http://wami-recorder.appspot.com/crossdomain.xml) at the root of the host from which the audio is served or recorded.

# The Catch #
Ok, there is really no catch.  Just use this code at your own risk and realize that there may be considerations for your application that are not taken into account here.  If you find this project useful, we would really appreciate it if you referenced it on your website or in your academic work.  Publications utilizing WAMI can be found at http://wami.csail.mit.edu/publications.php

<br />
<br />
<br />
<font size='1' face='arial'>
<code>*</code>WAMI stands for Web-Accessible Multimodal Interface.  This project was born out of a need to transport audio from the browser to a server-side speech recognizer.  Details on the original <a href='http://code.google.com/p/wami/'>WAMI toolkit project</a> can be found at <a href='http://wami.csail.mit.edu'>http://wami.csail.mit.edu</a>.<br>
</font>
