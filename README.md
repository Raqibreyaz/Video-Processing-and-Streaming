# Video Processing — Study Notes

A structured set of notes covering the complete path from **what a video actually is** to **how video is delivered to a user over the internet**.

This is a study repository, not a software project.

---

## About

This repository contains personal learning notes built around a deliberate question:

> *How does video work — from raw pixels all the way to adaptive streaming at scale?*

The notes progress through video fundamentals, compression theory, codecs, practical FFmpeg usage, HTTP streaming, and full delivery architecture. Each chapter builds directly on the previous one, so concepts introduced early (bitrate, frames, color models) reappear and deepen as the material advances.

The goal is not to produce a codec engineer, but to develop enough understanding to reason clearly about encoding decisions, transcoding pipelines, HLS, adaptive bitrate streaming, and CDN delivery — the kind of depth a backend or infrastructure engineer needs when working with video at the system level.

---

## What This Repository Covers

### Video Fundamentals
Understanding what video actually is at a technical level — pixels, color, frames, resolution, and how they relate to one another.

### Compression
Why raw video is too large to store or transmit in its raw form, how color space conversion and chroma subsampling reduce data, and how codecs exploit temporal and spatial redundancy through I-frames, P-frames, prediction, and residuals.

### FFmpeg
The practical toolset for working with video — how to think about the FFmpeg pipeline, how to inspect media, apply filters, transcode, and control encoding quality and bitrate.

### Video Delivery
How a video file transitions into a streaming format — HTTP progressive download, multiple representations, segmented streaming, the HLS format and m3u8 manifest structure, and adaptive bitrate streaming.

### Full Architecture
How the individual pieces (encoding, containers, CDN, HLS, ABR) connect into a complete video delivery system.

---

## Repository Structure

Each chapter is a self-contained directory. Chapters with a single note contain one `note.md`. Chapters with multiple sub-topics are broken into numbered files.

```text
Video-Processing/
├── 01-What-is-a-Digital-Image/
│   └── note.md
├── 02-Resolution-and-Aspect-Ratio/
│   └── note.md
├── 03-Video-is-Images-Changing-Over-Time/
│   └── note.md
├── 04-Bitrate/
│   └── note.md
├── 05-Resolution-vs-Bitrate/
│   └── note.md
├── 06-Bit-depth-and-Color/
│   └── note.md
├── 07-Compression-and-Codecs/
│   ├── 01-Why-Raw-Video-is-Huge.md
│   ├── 02-from-RGB-to-YUV.md
│   ├── 03-Chroma-Subsampling.md
│   ├── 04-Compression.md
│   └── 05-I-Frames-and-P-Frames.md
├── 08-Container-vs-Codec/
├── 09-FFmpeg-Fundamentals/
│   ├── 01-ffmpeg-mental-model.md
│   ├── 02-inspecting-media-with-ffprobe.md
│   ├── 03-ffmpeg-input-output-basics.md
│   ├── 04-video-filters.md
│   └── topic-list.txt
├── 10-Bitrate-Encoding-in-FFmpeg/
├── 11-HTTP-Video-Streaming/
├── 12-Multiple-MP4-Representations/
├── 13-Why-Segmented-Streaming-Exists/
├── 14-HLS-and-m3u8/
├── 15-Adaptive-Bitrate-Streaming/
├── 16-HLS-vs-Multi-MP4/
└── 17-Full-Video-Delivery-Architecture/
```

---

## Note Structure

Each note follows a consistent format:

- **What it is** — a precise definition or mental model for the concept
- **Core explanation** — the concept built up step by step, often with ASCII diagrams
- **Common mistakes / gotchas** — explicit corrections of typical misunderstandings
- **What remains to learn** — open areas at the end of incomplete topics
- **Key takeaways** — a summary of the most important points
- **Minimal self-test** — questions to check understanding before moving on

Notes avoid vague explanations in favour of concrete mental models. The aim is to understand *why* something works, not just *that* it works.

---

## Topic Map

### Phase 1 — Video Fundamentals (Chapters 1–3)

| Chapter | Topic |
|---|---|
| [01](./01-What-is-a-Digital-Image/note.md) | Digital image & pixels |
| [02](./02-Resolution-and-Aspect-Ratio/note.md) | Resolution & aspect ratio |
| [03](./03-Video-is-Images-Changing-Over-Time/note.md) | Frames & FPS |

### Phase 2 — Compression (Chapters 4–7)

| Chapter | Topic |
|---|---|
| [04](./04-Bitrate/note.md) | Bitrate |
| [05](./05-Resolution-vs-Bitrate/note.md) | Resolution vs bitrate |
| [06](./06-Bit-depth-and-Color/note.md) | Bit depth & color |
| [07 — Why raw video is huge](./07-Compression-and-Codecs/01-Why-Raw-Video-is-Huge.md) | Raw video size |
| [07 — RGB to YUV](./07-Compression-and-Codecs/02-from-RGB-to-YUV.md) | Color space conversion |
| [07 — Chroma subsampling](./07-Compression-and-Codecs/03-Chroma-Subsampling.md) | Chroma subsampling |
| [07 — Compression](./07-Compression-and-Codecs/04-Compression.md) | Compression model |
| [07 — I-frames & P-frames](./07-Compression-and-Codecs/05-I-Frames-and-P-Frames.md) | Temporal compression |

### Phase 3 — Codec & Container (Chapter 8)

| Chapter | Topic |
|---|---|
| [08](./08-Container-vs-Codec/) | Container vs codec *(in progress)* |

### Phase 4 — FFmpeg (Chapters 9–10)

| Chapter | Topic |
|---|---|
| [09 — Mental model](./09-FFmpeg-Fundamentals/01-ffmpeg-mental-model.md) | How FFmpeg works |
| [09 — ffprobe](./09-FFmpeg-Fundamentals/02-inspecting-media-with-ffprobe.md) | Inspecting media files |
| [09 — Input/output basics](./09-FFmpeg-Fundamentals/03-ffmpeg-input-output-basics.md) | Basic FFmpeg commands |
| [09 — Video filters](./09-FFmpeg-Fundamentals/04-video-filters.md) | `-vf`, `scale`, `crop`, `fps` |
| [10](./10-Bitrate-Encoding-in-FFmpeg/) | Bitrate encoding in FFmpeg *(in progress)* |

### Phase 5 — Video Delivery (Chapters 11–16)

| Chapter | Topic |
|---|---|
| [11](./11-HTTP-Video-Streaming/) | HTTP video streaming *(in progress)* |
| [12](./12-Multiple-MP4-Representations/) | Multiple MP4 representations *(in progress)* |
| [13](./13-Why-Segmented-Streaming-Exists/) | Why segmented streaming exists *(in progress)* |
| [14](./14-HLS-and-m3u8/) | HLS & m3u8 manifest format *(in progress)* |
| [15](./15-Adaptive-Bitrate-Streaming/) | Adaptive bitrate streaming *(in progress)* |
| [16](./16-HLS-vs-Multi-MP4/) | HLS vs multi-MP4 comparison *(in progress)* |

### Phase 6 — Full Architecture (Chapter 17)

| Chapter | Topic |
|---|---|
| [17](./17-Full-Video-Delivery-Architecture/) | Full video delivery architecture *(in progress)* |

---

## How to Use This Repository

**Follow the chapter order.**
The chapters are sequenced deliberately. Concepts introduced in early chapters (bitrate, frames, color models) are prerequisites for later ones (HLS segments, ABR ladder, transcoding pipelines). Reading out of order will leave gaps.

**Use the self-test questions.**
Each completed note ends with a set of questions. These are the most efficient way to check whether the concept has actually landed before moving on.

**Check "What remains to learn."**
Notes that are partially complete end with an explicit section listing what has not yet been covered in that topic. This makes it clear what is missing before moving to the next chapter.

**Use the notes as a reference.**
Once a concept has been studied, the notes serve as a quick reference when the topic reappears later — for example, when I-frame spacing comes up again in the context of HLS segment alignment.

---

## Current Status

This repository is actively being built. The learning journey is in progress.

| Status | Chapters |
|---|---|
| ✅ Complete | 1, 2, 3, 4, 5, 6, 7 |
| 🔄 In progress | 9 (4 of 12 topics done) |
| ⬜ Not yet started | 8, 10, 11, 12, 13, 14, 15, 16, 17 |

Notes and topics are added chapter by chapter. Partially complete chapters include a `topic-list.txt` or an explicit **"What remains to learn"** section to track remaining work.

---

## Learning Goals

By the end of this repository, the goal is to be able to reason clearly about:

- Why a video is encoded at a particular bitrate and resolution
- What a codec is actually doing (prediction, residuals, I/P/B frames)
- The difference between a container and a codec
- How to use FFmpeg to inspect, transcode, filter, and encode video
- Why segmented streaming exists and what problem it solves
- How HLS and m3u8 manifests work
- How adaptive bitrate streaming selects representations at runtime
- How a complete video upload → transcode → CDN → player pipeline is structured
