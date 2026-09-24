# Inspecting Media with `ffprobe`

## What it is

**`ffprobe`** is a tool from the FFmpeg toolkit that lets us inspect the contents and properties of a media file.

For example:

```bash
ffprobe input.mp4
```

Instead of processing the video, `ffprobe` helps us answer questions such as:

* What video codec is being used?
* What is the resolution?
* What is the frame rate?
* What pixel format is being used?
* What audio codec is present?
* What is the audio sample rate?
* Is the audio mono or stereo?
* What bitrate is being used?

A useful mental model is:

```text
Media file
    ↓
  ffprobe
    ↓
Inspect its streams and properties
```

---

# 1. Example `ffprobe` output

For the actual file discussed in the source, we saw:

```text
Video: h264 (High)
1024x768
60 fps
yuv420p
```

and:

```text
Audio: aac (LC)
48000 Hz
stereo
192 kb/s
```

We can break this information down field by field.

---

# 2. Resolution

The video resolution is:

```text
1024x768
```

This means:

```text
width  = 1024 pixels
height = 768 pixels
```

So the video frame contains:

```text
1024 pixels horizontally
×
768 pixels vertically
```

Conceptually:

```text
             1024 pixels
      ←────────────────────→
      ┌──────────────────────┐
      │                      │
      │                      │
      │        Frame         │ 768 pixels
      │                      │
      │                      │
      └──────────────────────┘
```

### Important

Resolution describes the **spatial dimensions** of each video frame.

It does not tell us:

* which codec is used
* how much the file is compressed
* how many frames are displayed per second

Those are separate properties.

---

# 3. Frame rate

The video has:

```text
60 fps
```

FPS means **frames per second**.

Therefore:

```text
60 fps
=
60 frames / second
```

A video is essentially a sequence of individual frames shown rapidly one after another:

```text
Frame 1
   ↓
Frame 2
   ↓
Frame 3
   ↓
...
Frame 60
```

At 60 fps, approximately 60 frames are displayed every second.

For example:

```text
1 second
│
├── Frame 1
├── Frame 2
├── Frame 3
├── ...
└── Frame 60
```

### Important distinction

Frame rate is not the same as resolution.

```text
1024×768
    ↓
size of each frame

60 fps
    ↓
number of frames per second
```

---

# 4. Video codec

The video codec is:

```text
h264
```

This means the video stream uses the **H.264 compression format**.

Conceptually:

```text
Raw video
   ↓
H.264 encoder
   ↓
Compressed H.264 video
```

When `ffprobe` reports:

```text
Video: h264
```

it is telling us which codec is used for that video stream.

This connects directly to the FFmpeg mental model:

```text
Raw frames
    ↓
H.264 encoder
    ↓
H.264 compressed video
```

And during playback:

```text
H.264 compressed video
    ↓
H.264 decoder
    ↓
Raw frames
```

---

# 5. Pixel format

The video uses:

```text
yuv420p
```

This is the **pixel format**.

It tells us how the pixel/color information is represented in the video frames.

At this stage, we do not need to study all of its internal details yet.

We already know from the YUV discussion that:

```text
YUV
 │
 ├── Y
 └── U + V
```

represents image information using luma and chroma.

The `420` part relates to **chroma subsampling**, while the `p` indicates a particular storage arrangement that will be studied later.

For now, the important point is simply:

> **`yuv420p` describes the pixel format, not the video codec.**

So:

```text
h264
 ↓
video codec

yuv420p
 ↓
pixel format
```

Do not confuse the two.

---

# 6. Audio codec

The audio stream uses:

```text
aac (LC)
```

Here:

```text
AAC
```

is the audio codec.

The `(LC)` indicates the **Low Complexity** profile of AAC.

So the file contains:

```text
Video → H.264
Audio → AAC
```

This is another example of why a media file can contain multiple different streams.

---

# 7. Audio sample rate

The audio sample rate is:

```text
48000 Hz
```

This means the audio waveform is sampled:

```text
48,000 times per second
```

Conceptually:

```text
Original sound waveform
        ↓
     sampling
        ↓
48,000 samples/second
```

A higher-level mental model is:

```text
Audio
  ↓
measure waveform repeatedly
  ↓
store numerical samples
```

So:

```text
48000 Hz
=
48000 audio samples / second
```

### Important

Audio sample rate is different from video frame rate.

```text
60 fps
    ↓
video frames per second

48000 Hz
    ↓
audio samples per second
```

They describe completely different things.

---

# 8. Audio channels

The audio is:

```text
stereo
```

Stereo means the audio has **two channels**:

```text
Left
Right
```

Conceptually:

```text
Audio
 │
 ├── Left channel
 └── Right channel
```

This is different from the number of samples per second.

For example:

```text
48000 Hz
    ↓
sampling rate

Stereo
    ↓
2 audio channels
```

So:

```text
sample rate ≠ number of channels
```

---

# 9. Audio bitrate

The source also shows:

```text
192 kb/s
```

This is the audio bitrate.

It describes the approximate amount of encoded audio data being produced per second:

```text
192 kb/s
=
192 kilobits / second
```

It is an **encoded data rate**, not the audio sample rate.

Compare:

```text
48000 Hz
    ↓
audio sampling frequency

192 kb/s
    ↓
encoded audio data rate
```

These are different concepts.

---

# 10. Container vs streams

This is one of the most important ideas when working with multimedia.

It is incorrect to think:

```text
input.mp4
=
H.264
```

The better mental model is:

```text
MP4 container
│
├── Video stream → H.264
│
└── Audio stream → AAC
```

The **MP4** is the **container**.

Inside that container are different streams.

For this example:

```text
MP4
 │
 ├── Video
 │     ↓
 │   H.264
 │
 └── Audio
       ↓
      AAC
```

### Container

The container packages the streams together.

Examples of container formats include:

```text
MP4
MKV
WebM
MOV
```

### Stream

A stream is one type of media data inside the container.

For example:

```text
Video stream
Audio stream
```

### Codec

A codec defines how that stream's data is encoded/compressed.

For example:

```text
Video stream → H.264
Audio stream → AAC
```

So these are different layers:

```text
Container
    ↓
holds streams

Stream
    ↓
video/audio data

Codec
    ↓
how that stream is encoded
```

---

# 11. Putting the actual file together

From the source, we can build this picture:

```text
                  input.mp4
                     │
                     ↓
                MP4 container
                     │
            ┌────────┴────────┐
            ↓                 ↓
       Video stream       Audio stream
            │                 │
            ↓                 ↓
      H.264 (High)        AAC (LC)
            │                 │
            ↓                 ↓
       1024 × 768         48000 Hz
       60 fps             stereo
       yuv420p            192 kb/s
```

This gives us a much clearer understanding of what is actually inside the file.

---

# 12. Connecting `ffprobe` to the FFmpeg pipeline

Earlier, we learned the processing pipeline:

```text
Input
 ↓
Demux
 ↓
Decode
 ↓
Raw media
 ↓
Filter
 ↓
Encode
 ↓
Mux
 ↓
Output
```

`ffprobe` is useful because it lets us inspect the properties of the input/output media without necessarily performing the processing itself.

For example, we can discover:

```text
Container
   ↓
MP4

Video stream
   ↓
H.264
   ↓
1024×768
   ↓
60 fps
   ↓
yuv420p

Audio stream
   ↓
AAC
   ↓
48000 Hz
   ↓
stereo
   ↓
192 kb/s
```

So `ffprobe` is essentially a **media inspection tool**.

---

# Common mistakes / gotchas

## 1. Thinking MP4 is a codec

Wrong:

```text
MP4 = codec
```

Correct:

```text
MP4 = container
```

For the example:

```text
MP4 container
├── H.264 video
└── AAC audio
```

---

## 2. Thinking H.264 is the file format

H.264 is the **video codec**.

The file/container can be MP4:

```text
input.mp4
   ↓
MP4 container
   ↓
H.264 video stream
```

---

## 3. Confusing codec and pixel format

These are different:

```text
h264
 ↓
video codec

yuv420p
 ↓
pixel format
```

The codec tells us how the video is encoded.

The pixel format tells us how the decoded image samples are represented.

---

## 4. Confusing FPS and audio sample rate

These both involve "per second," but they measure different things:

```text
60 fps
↓
60 video frames / second

48000 Hz
↓
48000 audio samples / second
```

---

## 5. Confusing audio bitrate with sample rate

```text
48000 Hz
↓
sampling frequency

192 kb/s
↓
encoded data rate
```

They are not interchangeable.

---

# Quick comparison

| Field             | Example   | What it tells us                        |
| ----------------- | --------- | --------------------------------------- |
| **Container**     | MP4       | How media streams are packaged          |
| **Video codec**   | H.264     | How video is encoded/compressed         |
| **Resolution**    | 1024×768  | Width and height of each video frame    |
| **Frame rate**    | 60 fps    | Video frames per second                 |
| **Pixel format**  | `yuv420p` | How image/color samples are represented |
| **Audio codec**   | AAC (LC)  | How audio is encoded/compressed         |
| **Sample rate**   | 48000 Hz  | Audio samples per second                |
| **Channels**      | Stereo    | Two audio channels: left and right      |
| **Audio bitrate** | 192 kb/s  | Encoded audio data rate                 |

---

# Key takeaways

* **`ffprobe`** lets us inspect what's inside a media file.
* `ffprobe input.mp4` can reveal important video, audio, and container information.
* `1024x768` means:

  * width = 1024 pixels
  * height = 768 pixels
* `60 fps` means 60 video frames per second.
* `h264` identifies the video codec.
* `yuv420p` identifies the pixel format.
* `48000 Hz` means the audio is sampled 48,000 times per second.
* `stereo` means two audio channels:

  * Left
  * Right
* `192 kb/s` is the encoded audio bitrate.
* **MP4 is a container**, not a codec.
* A container can hold multiple streams.
* In this example:

```text
MP4
├── Video → H.264
└── Audio → AAC
```

* Keep these concepts separate:

```text
Container
   ↓
MP4

Video codec
   ↓
H.264

Pixel format
   ↓
yuv420p

Audio codec
   ↓
AAC

Video frame rate
   ↓
60 fps

Audio sample rate
   ↓
48000 Hz

Audio bitrate
   ↓
192 kb/s
```

---

# Minimal self-test

1. What is `ffprobe` used for?
2. What does `1024x768` tell us?
3. What does `60 fps` mean?
4. What does `h264` tell us?
5. What does `yuv420p` describe?
6. Is `yuv420p` a codec?
7. What does `48000 Hz` mean for audio?
8. What does stereo mean?
9. What does `192 kb/s` represent?
10. What is the difference between an MP4 container and an H.264 video stream?
11. Can one MP4 container contain both video and audio?
12. In the example, which codec is used for video and which for audio?
