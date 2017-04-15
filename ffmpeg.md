# Encoding Video

### Installing

Install FFmpeg with homebrew. All codecs.

```shell
brew install ffmpeg --with-fdk-aac --with-ffplay --with-freetype --with-libass --with-libquvi --with-libvorbis --with-libvpx --with-opus --with-x265
```

If you already have ffmpeg installed, but not with the other libraries, use the `reinstall` command.

```shell
brew reinstall ffmpeg --with-libvpx --with-opus
```

[FFmpeg options](https://ffmpeg.org/ffmpeg.html#Options). The `-c:v` option is an alias for `-vcodec` and `-c:a` is an alias for `-acodec`. `-crf` is Constant Rate Factor.

### Constant Rate Factor

> This method allows the encoder to attempt to achieve a certain output quality for the whole file when output file size is of less importance. This provides maximum compression efficiency with a single pass. Each frame gets the bitrate it needs to keep the requested quality level. The downside is that you can't tell it to get a specific filesize or not go over a specific size or bitrate.

## Convert to MP4

When converting to an MP4, you want to use the h264 video codec and the aac audio codec because IE11 and earlier only support this combination. The [FFmpeg and H.264 Encoding Guide](https://trac.ffmpeg.org/wiki/Encode/H.264) can walk you through some of the H.264 specific options.

```shell
ffmpeg -i input.mov -c:v h264 -c:a aac -strict -2 output.mp4
```

For maximum compatibility, use the `profile` option. This may, however, increase the bit rate quite a bit. You can disable the audio stream with the `-an` option. `-pix_fmt yuv420p` is for Apple Quicktime support.

In this example, `input.mov` is converted to `output.mp4` with maximum compatibility, with Quicktime support, and without an audio stream.

```shell
ffmpeg -an -i input.mov -c:v libx264 -pix_fmt yuv420p -profile:v baseline -level 3 output.mp4
```

## Convert to WebM

#### VP9

VP9 can encode videos at half the file size :clap: Check out Google's [VP9 encoding guide](http://wiki.webmproject.org/ffmpeg/vp9-encoding-guide) for the recommend settings or the [FFmpeg VP9 guide](https://trac.ffmpeg.org/wiki/Encode/VP9).

Here's an example from the FFmpeg guide:

```shell
ffmpeg -i input.mov -c:v libvpx-vp9 -b:v 1M -c:a libvorbis output.webm
```

And here's Google's "Best Quality (Slowest) Recommended Settings". You need to run the first line(s). It will create a log file (and warn you the out.webm is empty). On the second pass, the video will be output.

```shell
ffmpeg -i <source> -c:v libvpx-vp9 -pass 1 -b:v 1000K -threads 1 -speed 4 \
  -tile-columns 0 -frame-parallel 0 -auto-alt-ref 1 -lag-in-frames 25 \
  -g 9999 -aq-mode 0 -an -f webm /dev/null


ffmpeg -i <source> -c:v libvpx-vp9 -pass 2 -b:v 1000K -threads 1 -speed 0 \
  -tile-columns 0 -frame-parallel 0 -auto-alt-ref 1 -lag-in-frames 25 \
  -g 9999 -aq-mode 0 -c:a libopus -b:a 64k -f webm out.webm
```

Using max threads for a little faster encoding. (Also almost lossless video)
* -threads 0 only uses 1 core, -threads 8 uses 2 cores, and -threads 16 uses 4 cores.

```shell
ffmpeg -i input.mp4 -c:v libvpx-vp9 -crf 10 -threads 16 -b:v 5000k -c:a libvorbis output.webm
```


#### Referenced from [Vestride](https://gist.github.com/Vestride/278e13915894821e1d6f)
