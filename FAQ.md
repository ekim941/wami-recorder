# Introduction #

I'll maintain a list of frequently asked questions here.  Add your own comments, questions, and answers if you like.

## 1.  Development ##

Flash can sometimes act funny when running from a local machine.  In Windows, for example, if you run the WAMI recorder from a file:/// address, you may not see the security panel or buttons.  Unfortunately, you won't see any error messages for this either.  A post on stackoverflow explains [the issue](http://stackoverflow.com/questions/6829004/flash-and-localhost-environment-no-connection-to-the-real-web).

The solution is to go to the Flash security settings, and add your local file as a "trusted site".  It's the 3rd tab over from the left on the [global security settings](http://www.macromedia.com/support/documentation/en/flashplayer/help/settings_manager04.html).  For example, add `file:///C:/ ... /wamiDir` and it should work.

## 2.  Containers vs. Encodings ##

Audio processing vocabulary (e.g. "format", "encoding", "container") tends to cause a lot of confusion.  An "encoding" refers to the way the bits represent the audio itself, whereas the "container" is a wrapper for the audio bits that specifies meta-information.  Confusing everything is the word "format", which I've seen refer to the container, the encoding, or both together.  To be completely clear what you mean when talking about audio processing, it's best to specify both a container and an encoding.  For example, Speex is an encoding, which can be found wrapped in any number of containers.  Most often it is found in the FLV or OGG container.  WAV is a container too.  Most often it houses uncompressed data, but can contain encodings such as μ-law.

## 3. Bandwidth ##

Uncompressed PCM audio generates files that are larger than one might wish, making bandwidth a limitation of this recorder.  So, why not encode the audio before sending it over the network?  At this moment, there's no universal way of doing this.  If you require heavily compressed audio, you're stuck with a flash media server such as Red5, Wowza, or FMS.  Here are some more thoughts on the matter:

### Sample Rate ###

Manipulating the sample rate (by setting it to say 8000) will reduce the number of 2-byte samples taken every second at the cost of fidelity. There are a number of options for the sample rate in [AudioFormat.as](https://code.google.com/p/wami-recorder/source/browse/src/edu/mit/csail/wami/audio/AudioFormat.as)  Some of them have limitations (e.g. a sample rate of 8000 would require resampling before playing it back in Flash), and so a special flag must be set to enable these sample rates just to make sure you know what you're doing.

### μ-law ###

[μ-law](http://en.wikipedia.org/wiki/%CE%9C-law_algorithm) is a lossy form of compression that would be possible to implement in action-script without too much trouble.  It's the encoding used by telephones.  This would be a wonderful addition to the project if someone has the time and motivation to implement it.  Both the WAV and AU audio containers used in this project support μ-law compression.  The implementation can be found at the [μ-law](http://en.wikipedia.org/wiki/%CE%9C-law_algorithm) wikipedia page.

### Other Encodings ###

[Speex](http://en.wikipedia.org/wiki/Speex) would be the obvious encoding choice, since Flash already has Speex capabilities (it's the format you can stream to a flash media server).  Flash, however, does not expose the Speex bits through actionscript, so we cannot make use of this.  For lossless compression [FLAC](http://en.wikipedia.org/wiki/FLAC) might be a good choice.  It would only reduce file sizes by around 50%, but you'd recover your audio exactly as it was recorded when you decompressed it on the server-side.

Due to efficiency concerns, it's very unlikely that you could roll your own implementation of FLAC or Speex in action-script directly.  Flash does not officially support native code, although there are plans to do so (for a price.)  [Alchemy](http://labs.adobe.com/technologies/alchemy/) was a beta version of native-code support in Flash, but was never intended to be relied on.  According to [Adobe's blog](http://blogs.adobe.com/flashplayer/2011/09/updates-from-the-lab.html) It was recently discontinued in preparation for a supposed 2012 release of an official version.  Flash player 11.2 already breaks Alchemy dependent projects.  In the future, this might be the way to go, but at present, I can't recommend it.  Furthermore, while it will remain free for non-commercial entities, it sounds like Adobe plans to charge everybody else.

## 4. Posting and Streaming ##

By default the WAMI recorder gathers up all the audio on the client side before shipping it over to the server via an HTTP Post.  A Post is the same technique that your browser uses to ship data via an HTML form or an XMLHttpRequest.  In this case, however, we set the content type to valid audio mime type.  Note that there are additional security restrictions in Flash's [URLLoader.load()](http://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/flash/net/URLLoader.html#load()) when using the multipart/form-data content type.  We avoid these, but if necessary it is possible to send the audio back out to Javascript and use this content-type.

Streaming can be simulated, but requires a more complex set-up.  For some reason Flash does not allow one to make use of of HTTP 1.1's chunked transfer protocol, which would otherwise perform the streaming.  Others have [tried](http://www.sephiroth.it/weblog/archives/2009/06/a_long_journey_through_chunked_transf.php).  We opt to send multiple HTTP Posts and chunk the data up ourselves.  This requires special server-side code, however, which we have not provided here.  Basically it amounts to reading the chunks and putting them back in order on-the-fly.