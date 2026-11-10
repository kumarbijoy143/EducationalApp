# EducationalApp — Android TV playlist library

This version reads playlist links from `playlists.txt` in your GitHub Pages site. The Android app loads the page from GitHub Pages, while the YouTube Data API key is supplied by the Android app through a WebView JavaScript bridge. **Do not put the API key in this public repository.**

## Repository files

- `index.html` — TV-friendly library and YouTube playlist player
- `playlists.txt` — the only file you need to edit when adding/removing playlists

## Add playlists

Open `playlists.txt` and paste one YouTube playlist link per line:

```text
https://www.youtube.com/playlist?list=PLxxxxxxxxxxxxxxxx
https://www.youtube.com/watch?v=VIDEO_ID&list=PLyyyyyyyyyyyyyyyy
```

Blank lines and lines starting with `#` are ignored. Duplicate playlist IDs are ignored. You may optionally set a display title yourself:

```text
Class 10 Mathematics | https://www.youtube.com/playlist?list=PLxxxxxxxxxxxxxxxx
```

If you paste only a link, the app uses the YouTube Data API key supplied by the Android app to fetch the playlist's real title and thumbnail. If the API is unavailable, the playlist can still be played, but the card may show a generic title and fallback image.

After saving/committing `playlists.txt`, open the app and press **Refresh**. GitHub Pages may take a short time to publish a commit.

## Enable GitHub Pages

In the repository, open **Settings → Pages**. Select **Deploy from a branch**, choose `main` and `/ (root)`, then save. The app expects these URLs:

- Page: `https://kumarbijoy143.github.io/EducationalApp/`
- Playlist list: `https://kumarbijoy143.github.io/EducationalApp/playlists.txt`

## Android / Sketchware Pro WebView bridge

The page calls `AndroidBridge.getApiKey()` to get the key at runtime. Add a JavaScript interface to the WebView **before** loading the GitHub Pages URL. Example Java code (adapt the WebView variable name to your Sketchware project):

```java
import android.webkit.JavascriptInterface;

public class AndroidBridge {
    @JavascriptInterface
    public String getApiKey() {
        return "PASTE_YOUR_NEW_YOUTUBE_DATA_API_V3_KEY_HERE";
    }
}

// Before webView.loadUrl(...):
webView.getSettings().setJavaScriptEnabled(true);
webView.addJavascriptInterface(new AndroidBridge(), "AndroidBridge");
webView.loadUrl("https://kumarbijoy143.github.io/EducationalApp/");
```

Use a **new** API key, restrict it to YouTube Data API v3, and set suitable application/API restrictions. A key embedded in an APK can still be extracted by a determined person; this approach simply keeps it out of the public GitHub repository and avoids re-entering it when playlist content changes.

**Important WebView security:** only allow your trusted GitHub Pages origin to remain inside this WebView. Do not let arbitrary websites load in the same WebView while the JavaScript interface is attached, because a JavaScript bridge can be called by loaded page content. Open external links outside the WebView or block them.

## TV controls

- D-pad / arrow keys: move between playlist cards
- OK / Enter: play selected playlist
- Back / Escape: close the player
- Refresh button: reload `playlists.txt`

YouTube playback requires internet access and may be affected by YouTube embedding, account, device, or regional restrictions. The YouTube Data API key is used for playlist titles/thumbnails; the embedded player itself can play a playlist by ID without that key.
