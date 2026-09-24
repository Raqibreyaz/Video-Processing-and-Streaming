# FFmpeg Input / Output Basics

## What it is

A basic FFmpeg command follows this structure:

```bash
ffmpeg -i input.mp4 output.mp4
```

At a high level, it means:

```text
input.mp4
    ↓
   FFmpeg
    ↓
output.mp4
```

Or more conceptually:

```text
Read this file
      ↓
  process it
      ↓
Produce this file
```

This is the simplest form of an FFmpeg command.

---

## One-sentence summary

**`-i` tells FFmpeg which file to read, while the final filename normally tells FFmpeg where to write the processed result.**

---

# 1. The `-i` option

The `-i` option specifies an **input**.

For example:

```bash
-i input.mp4
```

means:

> Take `input.mp4` as the source.

A complete command is:

```bash
ffmpeg -i input.mp4 output.mp4
```

Here:

```text
-i input.mp4
    ↓
Input file
```

So `-i` is essentially telling FFmpeg:

> "This is the media I want you to read."

---

# 2. The output filename

In the basic command:

```bash
ffmpeg -i input.mp4 output.mp4
```

the last filename is normally treated as the **output**.

```text
input.mp4
    ↓
   FFmpeg
    ↓
output.mp4
```

So:

```text
input.mp4
```

is the source, while:

```text
output.mp4
```

is the result.

---

# 3. Reading the complete command

Break this command into pieces:

```bash
ffmpeg -i input.mp4 output.mp4
```

### `ffmpeg`

Runs FFmpeg.

### `-i`

Specifies an input.

### `input.mp4`

The input media file.

### `output.mp4`

The output media file.

So the command can be understood as:

```text
ffmpeg
  ↓
read input.mp4
  ↓
process the media
  ↓
write output.mp4
```

---

# 4. Simple mental model

For now, think of FFmpeg like this:

```text
             FFmpeg
               │
               ↓
       ┌───────────────┐
       │ input.mp4     │
       └───────────────┘
               │
               ↓
            process
               │
               ↓
       ┌───────────────┐
       │ output.mp4    │
       └───────────────┘
```

This is only the **basic** mental model.

Internally, FFmpeg may perform many more steps:

```text
Input
  ↓
Demux
  ↓
Decode
  ↓
Filter
  ↓
Encode
  ↓
Mux
  ↓
Output
```

But the command-line structure is still:

```bash
ffmpeg -i input.mp4 output.mp4
```

---

# 5. What this basic command does NOT explain yet

The command looks simple, but FFmpeg has many rules and features that we have not covered.

For example:

### Multiple inputs

FFmpeg can work with more than one input.

Conceptually:

```text
Input 1 ──┐
          ├──→ FFmpeg → Output
Input 2 ──┘
```

---

### Multiple outputs

FFmpeg can also produce multiple outputs:

```text
             ┌──→ output1.mp4
Input ──→ FFmpeg
             └──→ output2.mp4
```

---

### Stream selection

A media file can contain multiple streams.

For example:

```text
input.mp4
│
├── Video
├── Audio
└── Subtitle
```

FFmpeg needs rules for deciding which streams should be used.

---

### Stream mapping

FFmpeg provides mechanisms to explicitly control which streams go where.

This is called **stream mapping**.

For example, conceptually:

```text
Input
│
├── Video ─────→ Output video
├── Audio ─────→ Output audio
└── Subtitle ──→ Output subtitle
```

We have not covered the details yet.

---

### Output format selection

The output filename can influence which container format FFmpeg uses.

For example:

```bash
ffmpeg -i input.mp4 output.mp4
```

and:

```bash
ffmpeg -i input.mp4 output.mkv
```

have different output container formats.

However, the exact rules for format selection are a deeper topic.

---

### Automatic codec selection

FFmpeg may automatically select codecs for the output when you do not explicitly specify them.

For example, a simple command can leave codec selection to FFmpeg:

```bash
ffmpeg -i input.mp4 output.mp4
```

Understanding exactly **which codec gets selected and why** is a separate topic.

---

# 6. Why this topic is only the beginning

The command:

```bash
ffmpeg -i input.mp4 output.mp4
```

looks extremely simple.

But to understand FFmpeg properly, we eventually need to understand:

```text
                 FFmpeg
                   │
        ┌──────────┼──────────┐
        ↓          ↓          ↓
     Inputs      Streams    Outputs
        │          │          │
        ↓          ↓          ↓
   multiple?   mapping?   format?
                            codec?
```

The basic input/output syntax is therefore just the starting point.

---

# Common mistakes / gotchas

## 1. Thinking `-i` means output

It does not.

```bash
-i input.mp4
```

means:

```text
input.mp4 → input
```

The final filename normally represents the output:

```bash
output.mp4
```

---

## 2. Thinking every FFmpeg command has only one input

Not necessarily.

FFmpeg can work with:

```text
multiple inputs
```

This becomes important for operations such as combining video and audio from different files.

---

## 3. Thinking every input stream automatically goes to the output

FFmpeg has stream-selection and stream-mapping rules.

For simple files, automatic behavior may be enough.

For more complicated media files, explicit mapping can become necessary.

---

## 4. Thinking the output filename only changes the filename

The output filename can also influence the output container format.

For example:

```bash
output.mp4
```

and:

```bash
output.mkv
```

indicate different container formats.

---

# Key takeaways

* The basic FFmpeg command is:

```bash
ffmpeg -i input.mp4 output.mp4
```

* `ffmpeg` starts FFmpeg.
* `-i` specifies an **input**.
* `input.mp4` is the source media.
* `output.mp4` is normally the output file.
* The basic mental model is:

```text
Input
  ↓
FFmpeg
  ↓
Output
```

* Internally, processing can involve:

```text
Demux
  ↓
Decode
  ↓
Filter
  ↓
Encode
  ↓
Mux
```

* This basic syntax does **not** yet explain:

  * multiple inputs
  * multiple outputs
  * stream selection
  * stream mapping
  * output format selection
  * automatic codec selection

* These topics are necessary for understanding more advanced FFmpeg commands.

---

# Minimal self-test

1. What does `-i` mean in an FFmpeg command?
2. Which file is the input in `ffmpeg -i input.mp4 output.mp4`?
3. Which file is normally the output?
4. What is the basic meaning of `ffmpeg -i input.mp4 output.mp4`?
5. Can FFmpeg work with multiple inputs?
6. Can FFmpeg produce multiple outputs?
7. What is stream mapping?
8. Can an output filename influence the output container format?
9. What does automatic codec selection mean?
10. What important FFmpeg topics still need to be learned after basic input/output syntax?

---

# What to learn next

The next logical topic is **FFmpeg stream selection and stream mapping**.

That will explain how FFmpeg deals with files containing multiple streams, such as:

```text
input.mp4
│
├── Video stream
├── Audio stream
└── Subtitle stream
```

and how you can explicitly control which streams are copied, encoded, or sent to each output.
