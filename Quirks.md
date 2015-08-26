There are quirks or bugs in almost every browser and operating system that make dealing with Flash and Javascript difficult.  This page documents some of the issues that arise.  If you have your own experiences or work-arounds to these problems, please drop a comment below.

# Transparency #
Hiding Flash while retaining the ability to communicate to it via Javascript is a maze of bugs and work-arounds.  No single method suffices.

## 1. Moving The Flash ##
Moving the Flash off-screen and most DOM modifications cause the Flash to reload in Firefox.  This in turn means that the app loses Microphone security settings, unless the client clicks "remember".  For more details on this browser-quirk, see:

http://stackoverflow.com/questions/3963283/can-i-move-a-flash-object-within-the-dom-without-it-reloading

## 2. visibility = "hidden" ##
Setting the visibility of the Flash element to "hidden" seems to work in most browsers.  Unfortunately some people still consider Internet Explorer a browser.  In IE the visibility approach has some issues. For some reason, as soon as you change the visibility to "hidden", the Javascript can no longer communicate with the invisible Flash.  You can't change the size of the Flash without losing the security settings, but for most other Flash apps that work around would be fine:

http://forums.adobe.com/thread/618123?tstart=-1

## 3. wmode = "transparent" ##
An alternative is to make the Flash do the work of disappearing by setting wmode to "transparent" and ensuring backgroundAlpha is "0". This doesn't work on Ubuntu, however, where the security panel doesn't show up.  I'm not sure this bug is officially reported anywhere, but the wmode issues in LInux have been known for some time:

https://bugs.launchpad.net/ubuntu/+source/flashplugin-nonfree/+bug/49613

## Recommended Solution ##

A combination of approached 2 and 3 suffice to ensure that the Flash is hidden in all browser/OS combinations.  Well... except maybe IE on Ubuntu... but who in there right mind would do that?


# Security Panel #

There are times with the microphone privacy security panel becomes un-clickable.  This appears to happen most regularly when using Opera on a Mac.  If you experience this issue, please comment and let us know the browser and OS you are testing.  In general, if you find yourself stuck trying to approve microphone access there is always this hacky work-around:

http://my.opera.com/missevilat/blog/2010/12/18/flash-settings

# Streaming #

Flash seems to only support true streaming through its RTMP protocol.  Despite the fact that HTTP 1.1 supports the chunked encoding of the body of a POST, in Flash, it appears that there is no way to do this:

http://www.sephiroth.it/weblog/archives/2009/06/a_long_journey_through_chunked_transf.php

The streaming solution we have opted for here is to use multiple POSTs.  It is then up to you to re-concatenate the audio on the other side.  Keep in mind that the chunks could arrive out of order.

# Sample Rate #

Ubuntu does not seem to support recording in Flash at the 11025 sample rate.  Although there is no explicit error message, the sample handler never gets called.  8kHz and 16kHz appear to be supported the browser/OS combinations I have tried, but they will not play back properly since they are not divisors of 44.1kHz.  Thus, the three aforementioned sample rates can only be used with an extra `WamiParam` that indicates their use is allowed.

There may be more hiccups regarding sample rate and system configuration that I have not run into yet.  I will update this section if I find or am notified of any more.

# Listening #

Given a click-to-talk interface, people sometimes start talking too early, so it can be helpful to record a little extra audio to prepend to the beginning.  This means, however, that the Flash app needs to constantly listen to the user, since it is impossible to know when they will click.  In Firefox, if it just so happens that two Flash apps (perhaps on two different tabs) are constantly listening for audio at the same time, they can steal eachother's samples.  To get rid of this problem, you have to be careful to use `recorder.startListening()` and `recorder.stopListening()` when a window gains and loses focus respectively.