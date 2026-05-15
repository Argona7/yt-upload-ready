# yt-upload-ready

Tiny local YouTube downloader that saves videos in an upload-friendly MP4 format.

It wraps `yt-dlp`, keeps it fresh in a private virtual environment, and then
normalizes every downloaded video with `ffmpeg` so platforms like X/Twitter do
not reject files because of AV1, Opus, 4K, odd pixel formats, or a missing
`faststart` atom.

## What It Produces

Every successful download is converted to:

```text
MP4 container
H.264 video
AAC audio
max 1280x720
30 fps
yuv420p
faststart enabled
```

The script does not trim video length. If an upload platform rejects a very long
video, that is usually an account/platform limit rather than a codec problem.

## Requirements

- macOS or Linux
- Bash
- Python 3
- `ffmpeg`
- Optional: Node, Bun, or Deno for YouTube JS challenge solving

## Usage

```sh
yt "https://www.youtube.com/watch?v=VIDEO_ID"
yt "https://www.youtube.com/shorts/VIDEO_ID"
```

Files are saved to `~/Downloads` by default.

To save somewhere else for one command:

```sh
YT_DOWNLOAD_DIR="$HOME/Desktop/videos" yt "https://www.youtube.com/watch?v=VIDEO_ID"
```

## Browser Cookies

If YouTube asks to confirm you are not a bot, the wrapper automatically tries to
use cookies from Chrome, Brave, Firefox, or Safari. Cookies are read locally at
runtime and are never written into this repository.

```sh
YT_COOKIES_BROWSER=brave yt "https://www.youtube.com/watch?v=VIDEO_ID"
YT_COOKIES_BROWSER=none yt "https://www.youtube.com/watch?v=VIDEO_ID"
```

## Output Tuning

The defaults are intentionally conservative for upload compatibility. You can
override them with environment variables:

```sh
YT_TARGET_MAX_WIDTH=1920 YT_TARGET_MAX_HEIGHT=1080 yt "https://www.youtube.com/watch?v=VIDEO_ID"
YT_TARGET_VIDEO_BITRATE=2500k YT_TARGET_AUDIO_BITRATE=128k yt "https://www.youtube.com/watch?v=VIDEO_ID"
```

Available knobs:

```text
YT_TARGET_MAX_WIDTH
YT_TARGET_MAX_HEIGHT
YT_TARGET_VIDEO_BITRATE
YT_TARGET_VIDEO_MAXRATE
YT_TARGET_VIDEO_BUFSIZE
YT_TARGET_AUDIO_BITRATE
```

## Install

Clone the repo and put the `yt` script somewhere on your `PATH`:

```sh
chmod +x yt
ln -s "$PWD/yt" /usr/local/bin/yt
```

On first run, the script creates `.venv/` and installs or updates `yt-dlp`.
That directory is local machine state and is ignored by git.

## Safety Notes

- Downloaded media files are ignored by git.
- Browser cookies are only accessed by `yt-dlp` at runtime.
- `.venv/`, `.env*`, cookie files, logs, and partial downloads are ignored.
- The repo contains only the wrapper script, README, ignore rules, and license.
