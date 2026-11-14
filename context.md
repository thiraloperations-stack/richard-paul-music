# Richard Paul, singer portfolio: project context

Read this first when picking the project up on a new machine or in a new session.

## What this is

A single static page whose only job is to be the link Richard sends when someone
asks for samples of his singing. A visitor (usually on a phone, arriving from a
WhatsApp or Instagram chat) should hear his voice within one tap and be able to
contact him just as easily. The goal is getting hired as a singer.

Richard wants to do music full time and also works as a platform engineer; the
About section says both, in that order.

## Layout of the repo

```
context.md            this file
links                 Richard's source list of YouTube/Instagram links, with priorities
*.mp3                 original audio clips (the .wav duplicates are git-ignored)
raasathi.mp4, videos/ raw videos and the portrait; the .mp4 originals are git-ignored
                      and exist only on Richard's Mac. Web copies are in site/video/
site/                 the website; this folder is what gets hosted
  index.html          the whole page: HTML, CSS and JS in one file, no build step
  audio/              mp3 clips played on the page
  video/              web copies of the videos (720px wide, about 1 Mbps, faststart,
                      original audio stream copied) plus a .jpg poster for each
  img/                richard-paul.jpg (portrait, cropped, metadata stripped)
                      aagaya-vennila.jpg (YouTube thumbnail for the film song)
                      share.jpg (1200x630 link-preview image)
```

## Preview locally

```
cd site && python3 -m http.server 8765
```

Then open http://localhost:8765. Use a server rather than opening the file
directly: the YouTube player refuses to load from a `file://` page.

## Content and its order

Richard marks priority with a `_#N` suffix in the `links` file and in audio file
names (#1 is highest). The page follows that order strictly.

| # | Piece | Type | Label on page |
|---|-------|------|---------------|
| 1 | Aagaya Vennila (film Arivaan) | YouTube `tasmzYgkVbE`, starts at 0:13 | featured video |
| 2 | Raasathi | local video, 1:07, plus Instagram link `C4ACXrdSyqv` | My natural voice |
| 3 | Nee Singam Dhan | local video, 1:20, plus Instagram link `DN5drdkEvvT` | Range |
| 4 | Ennodu Nee Irundhal | audio, 0:50 | Studio clip |
| 4 | Ennodu Nee Irundhal, harmony | audio, 0:14 | Layered harmonies |
| 4 | Koodapirannor | audio, 0:41 | Studio clip |
| 5 | Nee Pogaathe | Instagram `DM4MSpeSYNf` | High pitch and modulations |
| - | Monica | local video, 1:09, no Instagram link | Contemporary |
| 6 | Vaa Machane | Instagram `C5dcl3vyl7l` | An all-round performance |

Credit for #1 comes from the YouTube description: "Singer: Richard Paul",
composed, arranged and produced by Karthik ERA, released by Trend Music.

Contact details on the page: WhatsApp +91 86673 90029 (the +91 was assumed),
email, Instagram @richardpaulv_.

## How the page works

- **Film song:** a thumbnail with a play button. On tap it is replaced by a
  YouTube iframe (`youtube-nocookie.com`, `start=13&autoplay=1`). Nothing from
  YouTube loads until the tap, so the page opens fast. There is deliberately no
  end time: the best 30 seconds start at 0:13 but the listener can keep going.
- **List ("More of my voice"):** each `<li class="item">` is either
  `data-audio="audio/x.mp3"` (plays with a progress bar in the row),
  `data-video="video/x"` (no extension: the script adds `.mp4` and `.jpg` for
  the poster, and plays it in place; if the item also has `data-ig`, a "Watch it
  on Instagram" link appears under the video), or `data-ig="<post id>"` alone
  (expands an Instagram embed iframe in place, with an "Open it on Instagram"
  fallback link). Richard prefers local video over Instagram embeds when he
  has the file. Instagram cannot start at a timestamp
  and offers no thumbnails without an API, which is why those are rows and the
  YouTube song is the featured piece.
- **One thing plays at a time:** starting anything pauses the audio clips,
  closes other Instagram panels, and pauses the YouTube video.
- **Floating WhatsApp button:** shown only while neither the hero buttons nor
  the contact section is on screen.

### Adding a song

Copy an existing `<li class="item">` block in `site/index.html`, put it in
priority position, and change the `data-audio` or `data-ig` value, the title,
the label and (for audio) the duration text. For audio, add an mp3 to
`site/audio/` (not wav: too heavy for a web page). For video, make a web copy:

```
ffmpeg -i in.mp4 -c:v libx264 -preset slow -crf 27 -maxrate 1000k -bufsize 2000k \
  -pix_fmt yuv420p -c:a copy -map_metadata -1 -movflags +faststart site/video/name.mp4
ffmpeg -ss 2 -i in.mp4 -frames:v 1 -vf scale=540:-2 site/video/name.jpg
```

## Design

Theme taken from the featured song: "Aagaya Vennila" means moon of the sky. A
night-sky page with the portrait as the moon.

- Colours (CSS variables in `:root`): night `#0e1630`, dusk `#17234b`,
  moon `#f3efe3` (text), mist `#a9b3d1` (secondary text), glow `#f4d58d` (accent).
- Type: Gloock for the name and headings, Figtree for everything else (Google Fonts).
- Phone first, single column, left aligned, max width 44rem.
- The only automatic motion is the portrait rising on load; it is disabled for
  `prefers-reduced-motion`.
- Avoid: all-caps labels, decorative numbering, extra cards and shadows. Keep
  the copy plain and short.

## Hosting

Live at https://richard.thiral.io/

GitHub Pages, deployed by `.github/workflows/pages.yml`: every push to `main`
that touches `site/` publishes the `site/` folder automatically (about a
minute). The link-preview tags (`og:url`, `og:image`) in `index.html` contain
this address, so update them if the site moves to a custom domain.

## Not done yet

1. **Real-device check.** Layout was verified in screenshots at 390px and
   1280px, and one local video was played in a test browser. Tapping every
   player on an actual phone has not been verified.
2. **Custom domain** (optional): the current address carries the GitHub account
   name rather than Richard's.
3. Open questions for Richard: should the film song hard-stop after 30 seconds,
   and is +91 the right country code.
