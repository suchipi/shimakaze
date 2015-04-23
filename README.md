shimakaze
=========

shimakaze is a commandline utility that seeks to
assist the manipulation of media inputs and
outputs while maintaining a human-readable, simple
syntax.

Less generally, shimakaze is a tool that assists
in the following tasks:

* Extracting fonts from a video
* Burning subtitles into a video
* Streaming a video to a remote RTMP server

shimakaze is almost entirely just some nice
aliases for ffmpeg and mkvtoolnix commands. If
you want more power and flexibility, I highly
suggest you look further into those projects.
For those of you who just want to 'get things
done', though, shimakaze should suit your needs
rather well.

Usage
-----

`shimakaze input [options] output`

input should be a video file, usually an mkv.
output can be either a path to a filename or an rtmp address.

Valid options:
--------------

```
-a, --audio-track: Specify the audio track to use. Default: 0
-s, --subtitle-track: Specify the subtitle track to use. Default: 0
-v, --video-track: Specify the video track to use. Default: 0
-h, --hardsub: Hardsub the video.
-xf, --extract-fonts: Extract the fonts from the video into ~/.fonts. This will automatically occur when hardsubbing.
-fm, --ffmpeg-options: Additional ffmpeg options to pass in. They will be added to the end so you can override whatever you like.
-t, --start-time: Start video at a certain time, in seconds.
```

Examples
--------

```sh
shimakaze video.mp4 rtmp://example.com/live/abcd # Stream video.mp4 to an rtmp server
shimakaze video.mkv --hardsub video-out.mp4 # Hardsub video.mkv to video-out.mp4
shimakaze video.mkv --hardsub rtmp://example.com/live/abcd # Hardsub and stream
shimakaze video.mkv --audio-track 1 --subtitle-track 1 --hardsub rtmp://example.com/live/abcd # Hardsub and stream, using specific audio and subtitle tracks
shimakaze video.mp4 --extract-fonts # Just extract the fonts from the video to ~/.fonts. It will skip ones that already exist.
```

Todo
----

A lot of the ffmpeg options are vestiges of my usage of this application. Namely:

* Bitrates are hardcoded. They should have sane defaults but be able to be changed with
intuitive flags (maybe `--quality good`, `--quality okay`, etc)
* Resolution transformations would be nice
* `-tune animation` should be optional via a flag rather than the default

Ideally, I'd like to support:

* More streaming options than RTMP (though I only use RTMP so this would probably have to come from a PR)
* Easy syntax for popular streaming services like Twitch.TV
(though, really, you shouldn't be putting non-live video there...)
* Easy-to-use output format 'presets', eg `--format webm`. If this is implemented, it
should be watched carefully so that it doesn't evolve into something like `--container-format mp4 --video-codec h264 --audio-codec aac`
because that's against the KISS principle. The `--format` flag would be used for standardized (be them de facto or de jure)
formats that have a recognized 'normal' video and audio codec.
* Stream pausing and resuming, if possible with ffmpeg
* ~~Starting a stream at a timestamp. Right now this doesn't work
properly because of the behaviour when using ffmpeg's `-ss` and `-re` tags together.~~ Done
* Selecting audio and subtitle tracks optionally by language or name instead of only ID

Contributing
------------

Feature contributions and bugfixes are welcome! However, nitpicky
style/method changes (including bash optimizations) are not,
unless the change also fixes a bug or decreases runtime by an order of magnitude.

When contributing, please keep in mind the following goals of the project:
* The number one guideline to follow is KISS: Keep It Simple, Stupid.
* The script should use sane defaults for unspecified flags.
* Flags should have a verbose, human-readable long version, and a short, one or two letter version.
The long version should be prefixed with `--` and the short with `-`.
* Don't use abbreviations in long tags like 'acodec' or 'vbitrate'; that's what the short tags are for.
The long tags are for keeping things human-readable.
* This project does not seek to replace ffmpeg or mkvtoolnix- just act as a human-readable wrapper
for them. Features that will not be commonly used are better left to the original tools themselves
(eg I don't want flags that allow the user to reverse all the frames in the video)

To contribute, fork the project, make a topical branch, add some commits, then make a PR.

Notes
-----

This script was created on FreeBSD 10.1, and depends on the following ports:
```
shells/bash
multimedia/ffmpeg (with libass and libx264 enabled in config)
multimedia/mkvtoolnix
```
I highly suggest you run `make build-depends-list` in `/usr/ports/multimedia/ffmpeg`,
because it depends on some long-to-build development tools like `gmake` and `pkgconf`. 
You can save a lot of time by just using `pkg` to grab those before you build ffmpeg.

`mkvtoolnix` has its fair share of dependencies, too.

### Linux

If you want to run this script on Linux, you'll need to:
* Build or install ffmpeg, ensuring that it has support for libx264 and libass enabled
* Build or install mkvtoolnix. Your package manager's version should be fine.
* Change the first line of the script from `#!/usr/local/bin/bash` to `#!/bin/bash`

### Lazy Fingers

You can save a few keystrokes by adding the following functions to your `~/.bashrc`:
```bash
stream() {
  shimakaze "$1" rtmp://example.com/your/rtmp/server/goes/here
}
hardsub() {
  shimakaze "$1" --hardsub $2
}
hardsub+stream(){
  shimakaze "$1" --hardsub rtmp://example.com/your/rtmp/server/goes/here
}
```
Then you can just do stuff like:
```
stream video.mkv
hardsub video.mkv
hardsub video.mkv 'video-hardsubbed.mp4'
hardsub+stream video.mkv
```

Resources
---------

If you want to run your own RTMP server, I highly suggest you use the [NGINX RTMP module](https://github.com/arut/nginx-rtmp-module).

[Open Broadcast Software](https://obsproject.com/forum/resources/how-to-set-up-your-own-private-rtmp-server-using-nginx.50/) has a nice article on how to do this.

License
-------

MIT License
