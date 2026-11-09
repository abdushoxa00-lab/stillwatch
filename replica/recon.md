# Recon map: Stillwatch (a focused YouTube client)

## Scope
- **Original:** YouTube (iOS app / m.youtube.com)
- **Platform:** iPhone web app (home-screen icon), hosted on GitHub Pages
- **Slice:** watch videos from chosen channels, search, and study while watching. Not uploads, comments, Shorts, live chat or creator tools.
- **For:** Abu's personal use, focused learning without the algorithm.

## Sources
| source | url |
| --- | --- |
| YouTube Data API v3 docs (data model, quotas) | https://developers.google.com/youtube/v3 |
| IFrame Player API | https://developers.google.com/youtube/iframe_api_reference |
| API developer policies (what's allowed) | https://developers.google.com/youtube/terms/developer-policies |
| Required minimum functionality (player rules) | https://developers.google.com/youtube/terms/required-minimum-functionality |

## Screens
| ID | screen | how you get there | purpose | key components | states |
| --- | --- | --- | --- | --- | --- |
| S01 | Onboarding | first launch | name, pick daily limit, add channels | stepper, channel search | empty |
| S02 | Feed | tab "Feed" | newest uploads from followed channels only | video card, topic chips | empty, loading, filled, error (no key / quota) |
| S03 | Search | tab "Search" | find videos / channels on purpose | search field, results list | idle, loading, results, none |
| S04 | Watch | tap a video | player + study panel | player, speed, notes, bookmark, queue | playing, paused, ended, limit reached |
| S05 | Library | tab "Library" | watch later, bookmarks, notes, history | segmented list | empty, filled |
| S06 | Channels | Library → Channels | follow/unfollow, topics | channel row | empty, filled |
| S07 | Stats & settings | tab "You" | watch time, limit, API key, export | stat tiles, inputs | key missing, set |
| S08 | Limit reached | auto, when daily time hits limit | stop the binge | overlay with +5 min / done | — |

## Flows
- **F01 Watch a new upload:** S02 → S04. 1 tap.
- **F02 Take a timestamped note:** S04 → type note → save (records current time). Tapping a note seeks there.
- **F03 Follow a channel:** S03 (channel result) → Follow, or paste @handle on S06. 2 taps.
- **F04 Save for later:** S02/S04 → ⋯ → Watch later. Shows in S05.
- **F05 Daily limit:** watching in S04 adds to today's minutes, and S08 appears at the limit.
- Edges: no API key, quota exceeded, video not embeddable, offline.

## Components
Video card (thumb, title, channel, duration, age, saved state) · channel row · chip · bottom tab bar · sheet · toast · stat tile · note row (time + text) · segmented control · overlay.

## Data model (client-side, localStorage)
- `Channel` id, title, handle, thumb, uploadsPlaylistId, topic. *Source: channels.list. High confidence.*
- `Video` id, title, channelId, channelTitle, publishedAt, duration, thumb. *Source: playlistItems.list + videos.list. High confidence.*
- `Note` videoId, t (seconds), text, createdAt.
- `Bookmark` videoId, t, label. `Queue` videoId[]. `History` videoId, lastT, watchedSec, at.
- `Stats` per day: watchedSec. `Settings` dailyLimitMin, apiKey, speed, hideShorts.

## Out of scope / skip
- **Background audio and picture-in-picture:** prohibited for API clients by YouTube's developer policies. This is a Premium feature.
- Downloading or offline video: prohibited. Comments, Shorts, uploads, live chat, the recommendation algorithm: excluded by design.
- YouTube's logo, red play-button mark, fonts and copy: not used. Original branding.

## Size
8 screens, 5 flows, 7 entities. Hard parts: API quota (search costs 100 units), the player's 200×200 minimum (no tiny mini-player, so the player docks at the top instead), iOS autoplay rules. Size: **S**.
