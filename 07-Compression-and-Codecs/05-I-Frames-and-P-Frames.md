# I-Frames and P-Frames

## 1. Why Do We Need I-Frames and P-Frames?

After understanding prediction and residual, we can understand how video frames can be represented differently.

Instead of storing every frame as a completely independent picture, a video can contain different types of encoded frames.

The two important ones for now are:

```text
I-frame
P-frame
```

They solve different problems.

---

# 2. I-Frame

An **I-frame** is a compressed frame that can be reconstructed without depending on another video frame for its basic reconstruction.

Think:

```text
Encoded I-frame
      ↓
   Decoder
      ↓
Complete reconstructed picture
```

The I-frame is still compressed.

It is NOT:

```text
I-frame = raw/uncompressed frame ❌
```

Instead:

```text
I-frame = compressed, independently decodable picture ✅
```

So:

> **I-frame contains enough encoded information to reconstruct the whole picture independently.**

---

# 3. P-Frame

A **P-frame** is compressed information that can use an earlier reference picture to reconstruct the current picture.

Conceptually:

```text
Reference picture
       +
P-frame information
       ↓
Decoder
       ↓
Current reconstructed picture
```

A P-frame generally does **not** contain the entire current picture independently.

It uses information that is already available from a reference.

---

# 4. Why Does a P-Frame Save Space?

Suppose:

```text
Frame A = almost identical to Frame B
```

We have two choices.

### Store both completely

```text
Frame A
+
Complete Frame B
```

A lot of information is duplicated.

### Reuse Frame A

```text
Frame A
+
information describing how B differs from A
```

This avoids storing the same information again.

That is the main reason P-frames can be much smaller than independently storing the whole current picture.

---

# 5. Concrete Example — Moving Red Ball

Imagine:

### Frame 1

```text
┌──────────────────┐
│                  │
│    🔴            │
│                  │
└──────────────────┘
```

The encoder chooses Frame 1 as an I-frame:

```text
Frame 1 → I-frame
```

Now:

### Frame 2

```text
┌──────────────────┐
│                  │
│       🔴         │
│                  │
└──────────────────┘
```

The encoder already has Frame 1.

It can use Frame 1 as a reference.

Conceptually:

```text
Frame 1
  ↓
Find matching ball region
  ↓
Use/move it to the new position
  ↓
Prediction of Frame 2
```

Then:

```text
Actual Frame 2
      −
Prediction of Frame 2
      ↓
Residual
```

The P-frame stores the information needed to recreate the prediction plus the remaining difference.

Simplified:

```text
P-frame data ≈
"how to build the prediction"
+
"remaining difference"
```

---

# 6. What Exactly Happens During Encoding?

This is extremely important.

The encoder has access to the **original video**.

Suppose:

```text
Original Frame 1
Original Frame 2
Original Frame 3
```

The encoder might produce:

```text
Frame 1 → I-frame
Frame 2 → P-frame
Frame 3 → P-frame
```

For Frame 2:

```text
Original Frame 2
       ↑
       │ compare
       │
Prediction of Frame 2
       ↑
       │
Reference Frame 1
```

Conceptually:

```text
Reference Frame 1
        ↓
Build prediction
        ↓
Prediction of Frame 2
        ↓
Compare with original Frame 2
        ↓
Residual
        ↓
Compress/store information
        ↓
P-frame
```

The same basic idea can be applied to later predicted frames using suitable reference information.

---

# 7. What Exactly Happens During Playback?

This happens **later**, after the encoded video has already been saved.

The saved video contains encoded information:

```text
I → P → P → P → I → P → P
```

The decoder does NOT have the original video frames.

It reconstructs them.

---

## Decoding an I-frame

```text
Encoded I-frame
      ↓
   Decoder
      ↓
Reconstructed picture
```

---

## Decoding a P-frame

The decoder already has the reconstructed reference picture.

```text
Reference reconstructed picture
             +
      P-frame information
             ↓
      Rebuild prediction
             ↓
          Prediction
             +
       Residual information
             ↓
      Reconstructed picture
```

The decoder is **not creating a new P-frame**.

The P-frame was already created during encoding.

The decoder is decoding the already-created P-frame information.

---

# 8. Why Can't the Decoder Decode the P-Frame Alone?

Because the P-frame generally does not contain the whole current picture.

Imagine:

```text
Reference picture:
        🔴
```

and the P-frame essentially says:

```text
"Use that region here,
and apply these remaining changes."
```

Without the reference picture, the decoder does not have the information that the P-frame intentionally chose not to store again.

So:

```text
Reference picture
       +
P-frame information
       ↓
Current picture
```

is required.

If the P-frame contained the entire picture independently, much of the space-saving benefit would disappear.

---

# 9. Encoding vs Decoding

Keep these two stages completely separate.

## Encoding

```text
Original video
      ↓
Encoder
      ↓
Find redundancy
      ↓
Prediction
      ↓
Residual
      ↓
Compression
      ↓
I/P frame data
      ↓
Saved video
```

## Decoding

```text
Saved video
      ↓
Decoder
      ↓
I-frame → reconstruct picture

P-frame + reference
      ↓
rebuild prediction
      ↓
apply residual
      ↓
reconstruct picture
```

The most important distinction:

> **The encoder creates I/P frame data. The decoder reconstructs pictures from that already-created data.**

---

# 10. I-Frames and P-Frames Are Both Compressed

Do not make this mistake:

```text
I-frame = uncompressed ❌
P-frame = compressed ❌
```

The correct model is:

```text
I-frame = compressed + independently decodable

P-frame = compressed + depends on reference information
```

Both are compressed.

---

# 11. I-Frame as a Starting Point

An I-frame is useful because it gives the decoder a fresh picture without requiring a previous video frame.

For example:

```text
I → P → P → P → P → I → P → P → P
```

The second I-frame creates another independently decodable starting point.

This becomes especially useful for **seeking**.

---

# 12. Seeking

Suppose the user wants to jump to 37 seconds.

The video might have:

```text
30s       31s 32s 33s 34s 35s 36s 37s
 I         P   P   P   P   P   P   P
```

The player can:

```text
Seek to 37s
     ↓
Find suitable I-frame at/before 37s
     ↓
Start at 30s I-frame
     ↓
Decode P-frames
     ↓
Reach 37s
```

The player does not normally start at a later I-frame and decode backward.

---

# 13. If the I-Frame Is After the Target

Suppose the user seeks to 20s:

```text
18s I             20s             21s I
 ↓                 ↓                ↓
 I → P → P → P → P → P → P → P → P → I
                   ↑
                 target
```

The player can use the 18s I-frame:

```text
18s I
 ↓
19s P
 ↓
20s P
```

The 21s I-frame is too late to be the normal starting point for decoding forward to 20s.

So:

> **Seek → find a suitable I-frame at or before the target → decode forward.**

---

# 14. FPS and I-Frame Spacing Are Different

Suppose a video is:

```text
30 FPS
```

That means:

```text
30 frames every second
```

It does NOT mean:

```text
1 I-frame every second
```

Those are separate concepts.

For example, a simplified 30 FPS video could be:

```text
Frame 1    → I
Frame 2    → P
Frame 3    → P
...
Frame 10   → P

Frame 11   → I
Frame 12   → P
...
```

If I-frames were placed every 10 frames, then approximately:

```text
30 frames
3 I-frames
27 P-frames
```

But this is only an example.

The encoder controls I-frame placement separately from FPS.

---

# 15. Are I-Frames Random?

Not simply random.

The encoder can target a particular I-frame/keyframe spacing, while content and encoding decisions can also affect where I-frames are inserted.

So think:

```text
Encoder
   ↓
chooses useful I-frame positions
   ↓
I → P → P → P → I → P → P → P → I
```

The spacing can be regular or can vary.

---

# 16. Why I-Frames Matter for Seeking

I-frame spacing creates a trade-off.

If I-frames are close together:

```text
I → P → P → I → P → P → I
```

A seek may have to decode only a small number of frames before reaching the target.

If I-frames are far apart:

```text
I → P → P → P → P → P → P → P → P → I
```

A seek may need to decode many more frames.

So, conceptually:

```text
Shorter I-frame spacing
→ easier/faster random access
→ more independently encoded data

Longer I-frame spacing
→ potentially more efficient compression
→ potentially more work when seeking
```

The exact trade-off depends on the codec and encoding settings.

---

# 17. I-Frame vs P-Frame — Final Comparison

|                                                                | I-frame                      | P-frame                    |
| -------------------------------------------------------------- | ---------------------------- | -------------------------- |
| Compressed?                                                    | Yes                          | Yes                        |
| Can reconstruct its picture independently?                     | Yes                          | Generally no               |
| Needs another video frame?                                     | No                           | Yes, for predicted content |
| Uses temporal reference?                                       | Not for basic reconstruction | Yes                        |
| Contains complete independently decodable picture information? | Yes                          | Generally no               |
| Useful for seeking?                                            | Yes                          | Not by itself              |
| Can save space through previous-frame reuse?                   | Less than predicted frames   | Yes                        |

---

# 18. The Complete Picture

```text
ORIGINAL VIDEO
     │
     ▼
   ENCODER
     │
     ├── Find spatial/temporal redundancy
     │
     ├── Build predictions
     │
     ├── Calculate residuals
     │
     └── Compress
          │
          ▼
     I → P → P → P → I → P → P
          │
          ▼
      SAVED VIDEO
          │
          ▼
       PLAYBACK
          │
          ▼
       DECODER
          │
          ├── I → reconstruct picture
          │
          └── P + reference
                  ↓
             prediction
                  +
              residual
                  ↓
           reconstruct picture
```

## The core mental model

> **I-frame:** "Here is enough compressed information to reconstruct this picture by itself."

> **P-frame:** "Use an available reference picture plus the information I provide to reconstruct this picture."

> **Prediction:** "Here's my best guess of the current block/picture using information I already have."

> **Residual:** "Here's what was different between that guess and the actual current information."

> **Encoding:** creates the compressed representation.

> **Decoding:** reconstructs pictures from that representation.

> **Seeking:** starts from a suitable I-frame and decodes forward.
