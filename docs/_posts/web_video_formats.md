# Web Video Formats

Understanding web video formats is crucial for delivering high-quality, performant, and widely compatible video content on the web. Different formats offer varying levels of compression, quality, and browser support.

## Key Video Formats for the Web

1.  **MP4 (MPEG-4 Part 14)**
    *   **Codec:** Typically uses H.264 for video and AAC for audio.
    *   **Pros:** Widely supported across almost all browsers and devices. Good balance of compression and quality. Very common for streaming.
    *   **Cons:** Can be less efficient in terms of compression compared to newer codecs like VP9 or AV1.
    *   **Use Case:** The most common and safest choice for broad compatibility.

2.  **WebM**
    *   **Codec:** Uses VP8 or VP9 for video and Vorbis or Opus for audio.
    *   **Pros:** Open-source and royalty-free. Excellent compression, especially with VP9, leading to smaller file sizes than H.264 for similar quality. Good for transparency (alpha channel) in video.
    *   **Cons:** Less universal support than MP4, particularly on older Apple devices (though modern Safari supports VP9).
    *   **Use Case:** Ideal for modern browsers, especially when file size and open standards are a priority. Often used as an alternative to MP4 for better performance.

3.  **Ogg (Theora/Vorbis)**
    *   **Codec:** Uses Theora for video and Vorbis for audio.
    *   **Pros:** Open-source and royalty-free.
    *   **Cons:** Older format, generally superseded by WebM. Poorer compression efficiency compared to H.264 or VP9. Limited browser support today.
    *   **Use Case:** Rarely used for new web video content; mostly for legacy support.

4.  **AV1 (AOMedia Video 1)**
    *   **Codec:** AV1 for video.
    *   **Pros:** Next-generation, open-source, royalty-free video codec. Offers significantly better compression efficiency (up to 30-50% better than VP9/H.265) at the same quality. Designed for high-quality streaming.
    *   **Cons:** High encoding complexity (takes longer to encode). Decoding can be more resource-intensive, though hardware decoding is becoming more common. Browser support is growing but not yet universal (e.g., older devices might struggle).
    *   **Use Case:** Emerging standard for high-quality, low-bandwidth streaming. Future-proof.

5.  **H.265 (HEVC - High Efficiency Video Coding)**
    *   **Codec:** H.265 for video.
    *   **Pros:** Successor to H.264, offering significantly better compression efficiency (up to 25-50% better) for the same quality. Supports 4K and 8K resolutions.
    *   **Cons:** Patented technology with licensing fees, which has hindered its widespread adoption on the web compared to royalty-free alternatives like AV1. Browser support is limited (Safari supports it, but Chrome/Firefox generally do not due to licensing).
    *   **Use Case:** More common in native applications, broadcast, and Apple ecosystem.

## Audio Codecs within Video Containers

While the video codec is often highlighted, the audio codec is also important:
*   **AAC (Advanced Audio Coding):** Common with MP4 (H.264). Good quality and widely supported.
*   **Opus:** Excellent for WebM, offering superior quality at lower bitrates, especially for speech.
*   **Vorbis:** Used with WebM and Ogg.

## Why Multiple Formats are Necessary (`<source>` tag)

Due to varying browser and device support for different video codecs and formats, it's best practice to provide multiple video sources within the `<video>` tag. The browser will then select the first `source` it can play.

```html
<video controls width="640" height="360" preload="metadata">
  <source src="video.webm" type="video/webm">
  <source src="video.mp4" type="video/mp4">
  <p>Your browser doesn't support HTML5 video. Here is a <a href="video.mp4">link to the video</a> instead.</p>
</video>
```

**Order Matters:** Place the most efficient format (e.g., WebM with VP9/AV1) first, followed by the most compatible (MP4 with H.264). This ensures modern browsers get the best experience, while older browsers still have a fallback.

## Streaming Protocols

Beyond file formats, how video is delivered is also critical:
*   **HTTP Progressive Download:** Simple download of the entire file. Not ideal for long videos or adaptive streaming.
*   **HLS (HTTP Live Streaming):** Apple's adaptive bitrate streaming protocol. Divides video into small HTTP-based media segments. Widely supported.
*   **MPEG-DASH (Dynamic Adaptive Streaming over HTTP):** An international standard for adaptive bitrate streaming. Similar to HLS but more flexible.

These protocols are crucial for delivering video efficiently across varying network conditions, allowing the player to switch between different quality versions of the video (adaptive bitrate) to ensure smooth playback.

Choosing the right video formats and delivery strategies is key to optimizing user experience and performance for web video.
