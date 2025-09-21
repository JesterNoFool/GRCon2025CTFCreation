# Creating the Challenge Signals

The files here were used to create several signals used in the Gnu Radio 2025 conference "capture the flag" (CTF). These flowgraphs were used to create the following signals:
* Always The Same Color (ATSC)
* Never The Same Color (NTSC)
* Please Ask Later (PAL)
* Signal Identification Part 2 (Signal ID Flags 6 - 13)

Along with Gnu Radio Companion, other programs used were:
* [ffmpeg](https://ffmpeg.org/)
* [hacktv](https://github.com/fsphil/hacktv)
* [Audacity](https://www.audacityteam.org/)
* [Inkscape](https://inkscape.org/)
* [Gimp](https://www.gimp.org/)

## Always The Same Color (ATSC)

Creating this signal combined Gnu Radio Companion, ffmpeg, Audacity, and Inkscape.

To start, used Inkscape to create two images used for the two (2) video tracks. One flag each was written on the two images. Used Gimp to make minor alterations to the images from Inkscape.

Next, used ffmpeg to convert the images into two .MP4 video files: 

`ffmpeg -loop 1 -i track-1-image.jpg -t 16 -r 30 channel-1-video.mp4`
`ffmpeg -loop 1 -i track-2-image.jpg -t 16 -r 30 channel-2-video.mp4`

The next step was to create the two audio tracks. All audio was created using Audacity and stored as 48 kHz sample rate .WAV files. 

Gnu Radio Companion was used to create the audio tracks. [The first flowgraph](https://github.com/JesterNoFool/GRCon2025CTFCreation/blob/main/atscMasterChannel1Audio.grc) combined two audio signals, one at baseband and one on a subcarrier. [The second flowgraph](https://github.com/JesterNoFool/GRCon2025CTFCreation/blob/main/atscMasterChannel2Audio.grc) combined a baseband audio signal with a 2FSK subcarrier created using an ASCII text file.

The respective video and audio tracks were combined using ffmpeg. For example, the audio and video tracks for track were were created with the command: 

`ffmpeg -i channel-1-video.mp4 -i Channel-1-full-audio-track-48K0FS.wav -map 0:v -map 1:a -b:a 768k -ar 48k channel-1-audio-and-video.mp4`

The two .MP4 files containing the two audio / video tracks were combined into a single MPEG2 file (for use in the ATSC signal) using the command: 

`ffmpeg -i channel-1-audio-and-video.mp4 -i channel-2-audio-and-video.mp4 -map 0 -map 1 -c:v mpeg2video -b:v 5000k -acodec ac3 -b:a 768k -ar 48k -ac 1 -muxrate 19392658 -f mpegts Challenge-Signal-ATSC-01.ts`

Lastly, created the ATSC signal [using the ATSC flowgraph](https://github.com/JesterNoFool/GRCon2025CTFCreation/blob/main/challengeAtscSignal.grc). 

## Never The Same Color (NTSC)

Started by creating several sentences of encrypted text containing a flag [using GCHQ's online "Cyberchef" tool.](https://gchq.github.io/CyberChef/) Copied this encrypted text into Inkscape, along with an image from the Gnu Radio 2025 conference website image. Exported the image as a JPEG image.

Converted this still JPEG image into MP4 video using the following ffmpeg command:

`ffmpeg -loop 1 -i NTSC-video-screen.jpg -t 30 -r 30 NTSC-video-carrier.mp4`

Used [hacktv](https://github.com/fsphil/hacktv) to turn the MP4 video into an analog NTSC baseband video signal with the following command:

`hacktv -o NTSC-baseband-video.rs16 -m ntsc -s 13500000 NTSC-video-carrier.mp4`

Recorded two audio tracks using Audacity, one for the main audio carrier and one for a Secondary Audio Programming (SAP) carrier. The main audio contained a flag, while the SAP contained a clue on how to solve the encrypted text on the video image.

[Used a Gnu Radio Companion flowgraph](https://github.com/JesterNoFool/GRCon2025CTFCreation/blob/main/challengeNtscSignal.grc) to combine the audio and video, provide proper filtering, adjust the sample rate, and save to a SigMF-format file.

## Please Ask Later (PAL)

This was almost identical to the creation of the [NTSC](#never-the-same-color-ntsc) signal. Started by creating several sentences of encrypted text using [Cyberchef](https://gchq.github.io/CyberChef/). Then used Cyberchef to convert this text to visual Morse code.

Using Inkscape, combined the encrypted Morse code text with the Gnu Radio 2025 conference image. 

Converted the image into MP4 video with the following ffmpeg command:

`ffmpeg -loop 1 -i PAL-video-screen.jpg -t 30 -r 30 PAL-video-carrier.mp4`

Converted the MP4 to a baseband PAL video signal using the following hacktv command:

`hacktv -o PAL-baseband-video-16M0FS.rs16 -m pal -s 16000000 PAL-video-carrier.mp4`

Recorded a single audio file using Audacity. This audio file is to be used for the main audio carrier on the final PAL signal.

[Used a Gnu Radio Companion flowgraph](https://github.com/JesterNoFool/GRCon2025CTFCreation/blob/main/challengePalSignal.grc) to create the final modulated PAL signal, filter it, adjust the sample rate, and save it in the SigMF format.

## Signal Identification Part 2 (Signal ID Flags 6 - 13)

This challenge consists of six basic analog signals all contained with a 1 MHz sample rate IQ file. 

All of the signals used audio created using Audacity, with the exception of the wideband FM signal. It combined two audio signals along with text on the Radio Broadcast Data System (RBDS) subcarrier. Each signal was created in a separate flowgraph, as follows:

* [AM-LSB-SC](https://github.com/JesterNoFool/GRCon2025CTFCreation/blob/main/challengeAmLsb.grc)
* [AM-USB-SC](https://github.com/JesterNoFool/GRCon2025CTFCreation/blob/main/challengeAmUsb.grc)
* [AM-DSB-FC](https://github.com/JesterNoFool/GRCon2025CTFCreation/blob/main/challengeAmDsbFc.grc)
* [AM-DSB-SC](https://github.com/JesterNoFool/GRCon2025CTFCreation/blob/main/challengeAmDsbSc.grc)
* [Narrowband FM (NBFM)](https://github.com/JesterNoFool/GRCon2025CTFCreation/blob/main/challengeNbfm.grc)
* [Wideband FM (WBFM), mimicking a standard US FM broadcast station](https://github.com/JesterNoFool/GRCon2025CTFCreation/blob/main/challengeWbfm.grc)

These six signals were combined into a single IQ file [using another flowgraph](https://github.com/JesterNoFool/GRCon2025CTFCreation/blob/main/challengeSignalCombo.grc). 
