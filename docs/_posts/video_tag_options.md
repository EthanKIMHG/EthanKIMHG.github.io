# HTML `<video>` Tag Options (Attributes)

The HTML `<video>` tag is used to embed video content into a web page. It supports a variety of attributes that control how the video is played, displayed, and loaded. Understanding these options is crucial for providing a good user experience and optimizing performance.

Here are the most common and important attributes for the `<video>` tag:

1.  **`src`**
    *   **Purpose:** Specifies the URL of the video file.
    *   **Usage:** While you can use `src` directly on the `<video>` tag, it's generally recommended to use nested `<source>` elements for better browser compatibility and format fallbacks.
    *   **Example:** `<video src="my-video.mp4"></video>`

2.  **`controls`**
    *   **Purpose:** Displays the browser's default video controls (play/pause button, volume, progress bar, fullscreen toggle, etc.).
    *   **Usage:** A boolean attribute. If present, controls are shown.
    *   **Example:** `<video controls></video>`
    *   **Note:** For custom controls, you would omit this attribute and build your own using JavaScript.

3.  **`autoplay`**
    *   **Purpose:** Specifies that the video should start playing automatically as soon as it is loaded.
    *   **Usage:** A boolean attribute. If present, video autoplays.
    *   **Important Considerations:**
        *   **Browser Restrictions:** Most modern browsers (especially mobile) block autoplay with sound unless the user has interacted with the page or the video is muted. This is to prevent annoying user experiences.
        *   **User Experience:** Autoplay can be intrusive. Use it sparingly, primarily for muted background videos or when explicitly requested by the user.
    *   **Example:** `<video autoplay muted></video>`

4.  **`loop`**
    *   **Purpose:** Specifies that the video should start over again every time it finishes.
    *   **Usage:** A boolean attribute. If present, video loops.
    *   **Example:** `<video loop></video>`

5.  **`muted`**
    *   **Purpose:** Specifies that the audio output of the video should be muted.
    *   **Usage:** A boolean attribute. If present, video is muted.
    *   **Note:** Often used in conjunction with `autoplay` to bypass browser autoplay restrictions.
    *   **Example:** `<video autoplay muted></video>`

6.  **`poster`**
    *   **Purpose:** Specifies an image to be shown while the video is downloading, or until the user hits the play button. It acts as a placeholder.
    *   **Usage:** Takes a URL to an image file.
    *   **Example:** `<video poster="thumbnail.jpg"></video>`

7.  **`preload`**
    *   **Purpose:** Specifies how the browser should load the video. It's a hint to the browser, not a strict command.
    *   **Values:**
        *   `none`: The browser should not preload any video data.
        *   `metadata`: The browser should only preload the video's metadata (dimensions, duration, etc.). This is a good balance for performance.
        *   `auto` (default): The browser should preload the entire video file. This can consume significant bandwidth and is generally not recommended unless the video is critical and expected to be played immediately.
    *   **Example:** `<video preload="metadata"></video>`
    *   **Note:** If `autoplay` is present, `preload` is ignored as the video will start loading immediately anyway.

8.  **`width` and `height`**
    *   **Purpose:** Specifies the width and height of the video player in pixels.
    *   **Usage:** Takes a numeric value (e.g., `width="640"`).
    *   **Important:** Always specify `width` and `height` to prevent Cumulative Layout Shift (CLS) and improve page rendering performance. The browser can reserve space for the video before it loads.
    *   **Example:** `<video width="640" height="360"></video>`

9.  **`playsinline`**
    *   **Purpose:** (Primarily for iOS Safari) Specifies that the video should play inline within the element's playback area, rather than automatically entering fullscreen mode when playback begins.
    *   **Usage:** A boolean attribute. If present, video plays inline.
    *   **Example:** `<video playsinline></video>`

## Using `<source>` for Multiple Formats

As mentioned, it's best practice to use the `<source>` element within the `<video>` tag to provide multiple video formats for cross-browser compatibility. The browser will choose the first `source` it supports.

```html
<video controls width="640" height="360" poster="thumbnail.jpg" preload="metadata">
  <source src="my-video.webm" type="video/webm">
  <source src="my-video.mp4" type="video/mp4">
  <p>Your browser does not support HTML5 video. Here is a <a href="my-video.mp4">link to the video</a> instead.</p>
</video>
```

**`type` attribute on `<source>`:** Specifies the MIME type of the video, allowing the browser to quickly determine if it can play the format without downloading it.

By thoughtfully using these attributes, developers can create robust and user-friendly video experiences on the web.
