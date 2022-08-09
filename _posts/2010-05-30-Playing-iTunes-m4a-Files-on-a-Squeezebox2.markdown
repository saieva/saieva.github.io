---
layout: post
title:  "Playing iTunes .m4a Files on a Squeezebox2"
date:   2010-05-30 06:00:00 -0400
categories: Audiophile
---
I encountered a problem playing iTunes .m4a files on [Squeezebox2][Squeezebox2WikiURL] devices. It was interesting that
from the same Squeezebox Server the files played fine on a [Squeezebox3][Squeezebox3WikiURL], but when attempting to play
on a Squeezebox2 an error appeared on the Squeezebox Server’s Web interface, “Problem: Can’t open file for: <file>.m4a”.
Other files, such as .mp3 filetypes, played fine.

I validated that the .m4a file existed in the music directory and there were no permission issues.
To troubleshoot the problem I activated debug logging for os.files, os.paths, player.source, player.streaming, and scan.scanner
and then restarted the Squeezebox Server; the logging messages were recorded in the server.log file. The problem was straightforward:

```
Slim::Player::TranscodingHelper::checkBin (242)   Found command: [faad] -q -w -f 1 $FILE$ | [lame] –silent -q $QUALITY$ $BITRATE$ – –
Slim::Utils::Misc::findbin (100) Looking for executable: [lame]
Slim::Utils::Misc::findbin (111) Checking for lame in /usr/share/squeezeboxserver/Bin/i386-linux/lame
Slim::Utils::Misc::findbin (111) Checking for lame in /usr/share/squeezeboxserver/Bin/lame
Slim::Utils::Misc::findbin (111) Checking for lame in /usr/kerberos/bin/lame
Slim::Utils::Misc::findbin (111) Checking for lame in /usr/local/bin/lame
Slim::Utils::Misc::findbin (111) Checking for lame in /bin/lame
Slim::Utils::Misc::findbin (111) Checking for lame in /usr/bin/lame
Slim::Utils::Misc::findbin (111) Checking for lame in /usr/bin/lame
Slim::Utils::Misc::findbin (111) Checking for lame in /usr/local/bin/lame
Slim::Utils::Misc::findbin (111) Checking for lame in /usr/libexec/lame
Slim::Utils::Misc::findbin (111) Checking for lame in /usr/sbin/lame
Slim::Utils::Misc::findbin (130) Didn’t find binary for lame
Slim::Player::TranscodingHelper::checkBin (265)    couldn’t find binary for: lame
Slim::Player::TranscodingHelper::getConvertCommand2 (421) Error: Didn’t find any command matches for type: mp4
```

So it seems that lame is needed to stream music data from an m4a file to a Squeezebox2 device.
(But lame is not used when streaming music data to a Squeezebox3. That’s interesting too.)

I needed lame for Redhat Linux, it’s an open source project maintained on SourceForge here.
After downloading the source, run a ./configure, make, and make install. And that fixed the problem.

[Squeezebox2WikiURL]: https://wiki.slimdevices.com/index.php/Squeezebox2.html
[Squeezebox3WikiURL]: https://wiki.slimdevices.com/index.php/Squeezebox_3rd_Generation.html
