# Compression, Prediction & Residual

## 1. What is Compression?

**Compression means representing information using fewer bits.**

The basic goal is:

```text
Original information
        ↓
Find a more efficient representation
        ↓
Fewer bits
        ↓
Smaller data
```

This matters for video because video contains a huge amount of information:

```text
Pixels per frame
×
Frames per second
×
Duration
```

For example:

```text
1920 × 1080
= 2,073,600 pixels per frame

At 30 FPS:
2,073,600 × 30
≈ 62 million pixel positions every second
```

Storing every pixel independently would produce enormous amounts of data.

Compression reduces this.

---

# 2. Lossless vs Lossy Compression

## Lossless Compression

After decompression, we get **exactly the original information**.

```text
Original
   ↓
Lossless compression
   ↓
Compressed data
   ↓
Decompression
   ↓
Exactly original
```

A simple conceptual example:

```text
[4, 4, 4, 4, 4, 4, 4, 4]
```

Could be represented as:

```text
(4, 8)
```

Meaning:

> "The value 4 occurs 8 times."

This is similar to **run-length encoding**.

---

## Lossy Compression

Some information is intentionally lost.

```text
Original
   ↓
Lossy compression
   ↓
Compressed data
   ↓
Decompression
   ↓
Slightly different result
```

Video codecs such as H.264 commonly use lossy compression.

The goal is not:

> "Preserve every single original value."

The goal is closer to:

> **Remove or simplify information in ways that save many bits while keeping the visual result acceptable.**

---

# 3. Why Does Video Have So Much Redundancy?

A video contains a lot of information that is **repeated or very similar**.

This is called **redundancy**.

There are two major types we care about.

---

# 4. Spatial Redundancy

**Spatial** means related to positions within the same frame.

Nearby parts of an image are often similar.

For example, a blue sky:

```text
🟦 🟦 🟦 🟦 🟦
🟦 🟦 🟦 🟦 🟦
🟦 🟦 🟦 🟦 🟦
```

There is a lot of repeated/similar information.

Instead of treating every pixel as completely unrelated, compression can exploit those similarities.

So:

> **Spatial redundancy = similar or repeated information within the same frame.**

---

# 5. Temporal Redundancy

**Temporal** means related to time.

Video is a sequence of frames:

```text
Frame 1 → Frame 2 → Frame 3 → Frame 4
```

Usually, nearby frames are similar.

For example, a person standing still:

```text
Frame 1: person standing
Frame 2: person standing
Frame 3: person standing
Frame 4: person standing
```

There is no reason to store every frame as if it were completely unrelated.

Even when something moves, much of the scene may remain unchanged.

For example:

```text
Frame 1:

🟦 🟦 🟦 🟦
🟦 🟥 🟥 🟦
🟦 🟥 🟥 🟦
🟦 🟦 🟦 🟦
```

Frame 2:

```text
🟦 🟦 🟦 🟦
🟦 🟦 🟥 🟥
🟦 🟦 🟥 🟥
🟦 🟦 🟦 🟦
```

Much of the information is still similar.

So:

> **Temporal redundancy = similar or repeated information between frames over time.**

---

# 6. Frames Are Divided Into Blocks

Video codecs work with smaller rectangular regions instead of always treating an entire frame as one giant object.

Conceptually:

```text
+----+----+----+----+
| B1 | B2 | B3 | B4 |
+----+----+----+----+
| B5 | B6 | B7 | B8 |
+----+----+----+----+
| B9 | B10| B11| B12|
+----+----+----+----+
```

This is useful because different areas can behave differently.

For example:

```text
Background → almost unchanged
Person     → moving
Ball       → moving
```

The codec can handle those regions differently.

---

# 7. Prediction

The word **prediction** can be misleading.

It does NOT mean:

> "Predict the future."

Here, prediction means:

> **Use information that is already available to make a good guess of what the current block should look like.**

The general idea is:

```text
Available information
        ↓
Build a guess
        ↓
Prediction
```

There are two important kinds of prediction.

---

# 8. Temporal Prediction

Temporal prediction uses information from **another frame**.

Imagine a red ball moving to the right.

### Frame 1

```text
┌──────────────────┐
│                  │
│    🔴            │
│                  │
└──────────────────┘
```

### Frame 2

```text
┌──────────────────┐
│                  │
│       🔴         │
│                  │
└──────────────────┘
```

The encoder already has Frame 1.

It can find the region containing the ball and use it to construct a prediction of Frame 2.

Conceptually:

```text
Reference Frame 1
        ↓
Find similar region
        ↓
Use/move that region
        ↓
Prediction of Frame 2
```

This is **temporal prediction** because the information came from another frame.

---

# 9. Spatial Prediction

Spatial prediction uses information from **nearby areas in the same frame**.

Imagine a smooth wall:

```text
🟦 🟦 🟦 🟦 🟦
🟦 🟦 🟦 🟦 🟦
🟦 🟦 🟦 🟦 🟦
```

Suppose the codec is encoding one block.

Nearby pixels or blocks already give it a good idea of what the current block should look like.

Conceptually:

```text
Nearby information
        ↓
Build prediction for current block
        ↓
Prediction
```

This is **spatial prediction**.

---

# 10. Temporal vs Spatial Prediction

The easiest distinction:

```text
Temporal prediction
→ information comes from another frame

Spatial prediction
→ information comes from nearby areas in the same frame
```

Visualized:

```text
                 PREDICTION
                     │
          ┌──────────┴──────────┐
          │                     │
      TEMPORAL               SPATIAL
          │                     │
   another frame          same frame
          │                     │
   reference frame       nearby pixels/
                          blocks
```

---

# 11. Residual

Prediction is usually **not perfect**.

The encoder has:

```text
Actual current block
Prediction of current block
```

It compares them:

```text
Actual
   −
Prediction
   ↓
Residual
```

The residual is:

> **The remaining difference that the prediction did not explain.**

For example:

```text
Actual:
[52, 50, 48, 51]

Prediction:
[50, 50, 50, 50]
```

Then:

```text
Residual = Actual − Prediction

[ 2, 0, -2, 1]
```

---

# 12. Prediction + Residual

The important relationship is:

```text
Prediction + Residual = Actual
```

Using the example:

```text
Prediction:
[50, 50, 50, 50]

+

Residual:
[ 2,  0, -2,  1]

=

[52, 50, 48, 51]
```

So the prediction provides most of the information, while the residual describes what was missing or wrong.

---

# 13. Why Does the Residual Help Compression?

Suppose:

```text
Prediction:
[50, 50, 50, 50]

Actual:
[50, 50, 51, 49]
```

Then:

```text
Residual:
[0, 0, 1, -1]
```

The residual is much simpler than storing the entire block again.

Conceptually, instead of storing:

```text
Complete current block
```

we can store:

```text
How to build the prediction
+
Small remaining difference
```

This can save a lot of data.

Important:

> The residual is **not always small**. It is small when the prediction is good.

---

# 14. Prediction + Residual During Encoding

This is where prediction and residual actually enter the compression process.

The encoder has:

```text
1. Original current frame
2. Available reference/neighbor information
```

It does:

```text
Available information
        ↓
Build prediction
        ↓
Compare prediction with
original current frame
        ↓
Calculate residual
        ↓
Compress/store the required information
```

So:

> **The encoder can calculate the residual because it has access to the original current frame.**

---

# 15. Prediction + Residual During Decoding

The decoder does NOT have the original current frame.

It receives the compressed information.

For a predicted block, conceptually:

```text
Reference / neighboring information
             ↓
       Rebuild prediction
             ↓
         Prediction
             +
      Stored residual
             ↓
     Reconstructed block
```

So encoding and decoding are two halves of the same idea.

---

# 16. Can We Reconstruct the Exact Original?

If the exact residual is preserved:

```text
Prediction
    +
Exact residual
    ↓
Exact original values
```

For example:

```text
Prediction:
[50, 50, 50, 50]

Residual:
[2, 0, -2, 1]

Result:
[52, 50, 48, 51]
```

However, real video compression is usually **lossy**.

The residual and other information can themselves undergo lossy processing.

Therefore:

```text
Original
   ↓
Prediction + residual
   ↓
Lossy compression
   ↓
Stored information
   ↓
Decoder
   ↓
Reconstructed frame
```

The reconstructed frame may therefore be slightly different from the original.

---

# 17. Why This Matters for Video Compression

The entire idea can now be summarized as:

```text
Video contains redundancy
        ↓
Find information that can be reused
        ↓
Use it to build a prediction
        ↓
Store only what is needed to describe the difference
        ↓
Compress that information
        ↓
Much less data
```

This is the conceptual bridge to I-frames and P-frames.
