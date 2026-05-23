---
name: Radio App — FM-transmitter EQ player (v1 spine + DRM constraint)
description: 2026-04-24 idea. App that pairs with the cigarette-lighter FM-transmitter dongle, plays music with heavy EQ + bass boost, broadcasts to the car stereo via the dongle's FM signal. Pivoted from a music-aggregator concept after the cloud TOS walls; landed on a leaner "in-app player + EQ + Bluetooth out" spine. DRM constraint stays unresolved — Elijah's call: "good app, I'll figure it out."
type: project
originSessionId: 7483a978-8120-4537-84d4-a02d0ae689da
---
The vision: phone app that pairs with a Bluetooth FM-transmitter dongle (the kind that plugs into a car's cigarette lighter and broadcasts on a chosen FM frequency, e.g. 89.1). User plays music in the app, the app applies a heavy EQ + bass boost, sends the EQ'd audio out via standard Bluetooth, the dongle modulates it onto FM, the car radio picks it up. Hero feature is the bass-boost — "loud enough to feel like a car with a subwoofer system."

UI direction (downstream of function): landscape orientation, styled like an old CD-deck face — grill texture, "LED screen" panel showing now-playing + album art (Apple-Now-Playing aesthetic), big tactile EQ controls. Phone turned sideways = the radio.

**Function spine (the part that actually ships):**
1. App is the playback source. User audio comes from: local DRM-free files (MP3, M4A, FLAC, OGG), internet radio (TuneIn-style streams), podcasts (RSS), and optionally Spotify Connect for control-only (your EQ doesn't touch Spotify's DRM'd audio).
2. EQ engine processes the digital waveform — source-agnostic. Whatever's flowing through your playback pipeline gets the EQ; doesn't care if it's a song or a radio stream.
3. Output is standard Bluetooth audio. The FM transmitter dongle pairs as a regular BT speaker. App doesn't need special integration with the dongle — most dongles set frequency via their own physical buttons, not via app API.
4. Works on iOS + Android (no aggregation walls and no system-EQ walls because the app controls its own audio pipeline).

**Resolved 2026-04-24 (same day): own-it-or-buy-it model — DRM wall sidestepped.**
Elijah landed the framing using the Xbox Game Pass analogy: subscription downloads (Apple Music, Spotify, YouTube Music Premium, Xbox Game Pass games) are RENTAL — you lose access when the subscription ends or content gets pulled. PURCHASES (iTunes Store, Bandcamp, 7digital, Xbox game buys) are OWNERSHIP — yours forever, DRM-free since 2009 for iTunes. The Radio app deals ONLY in owned tracks. No DRM circumvention. App-store-shippable. The "I should own what I paid for" frustration is resolved by simply not playing the rental stuff.

**Sources that fit the own-it model:**
- **iTunes Store** (Apple, primary on iOS): DRM-free since 2009. Third-party iOS apps deep-link to iTunes Store purchase URLs — user taps "buy" in your app, iTunes Store opens, they buy, the file lands in their iCloud Music Library, your app plays it via MPMediaQuery (standard iOS API for owned music). iTunes Affiliate Program pays a small cut on every purchase your app drives — incentive aligns with helping users build a library.
- **Bandcamp**: fully DRM-free, real API, streaming previews + purchase. Best philosophical fit.
- **7digital**: DRM-free MP3 store with third-party API.
- **Beatport**: DRM-free electronic music, has API.
- **User's existing local library**: MP3 / M4A / FLAC / OGG files they already own — playable without any store integration.

**What the app is NOT (named so we don't drift back):**
- Not a music aggregator (TOS walls would block).
- Not a Spotify / Apple Music skin (DRM walls would block).
- Not a DRM stripper (legal + App Store policy walls would block).
- It's a **player + buyer + EQ** for music the user owns or buys to own.

**Payment + cross-platform architecture (added same day):**
- **iOS purchases**: deep-link to iTunes Store URL → iTunes Store app opens → user taps buy → Apple's existing payment method (Apple Pay or stored card) handles it → file lands in iCloud Music Library → app plays via MPMediaQuery. Apple's 30% StoreKit cut does NOT apply because the purchase happens in iTunes, not in your app. For YOUR app's own subscriptions/premium features (if any later), StoreKit + Apple Pay is required by Apple's rules.
- **Android purchases**: Google Play Music STORE was killed in 2020 — no Apple-iTunes-equivalent on Android. Workaround: deep-link to Bandcamp / 7digital / Amazon Music MP3 Store for buys. Each handles its own payment (Google Pay, Cash App, PayPal, cards). Your app processes nothing.
- **Cash App**: Cash App Pay SDK exists for third-party Android integration. iOS App Store rules force StoreKit for digital in-app purchases, so Cash App can't directly replace StoreKit for music files on iOS — fits as a payment option on the Bandcamp/Amazon side, not in your app's own purchase flow on iOS.
- **Cloud + local hybrid**: app supports BOTH cloud library (iCloud Music, Bandcamp collection) AND local cache (microSD on Android, device storage on iOS). User picks per-track or all-at-once. Local cache for offline driving (no cell signal); cloud for cross-device sync ("I want this on my new phone too"). Don't pick — support both.
- **Cross-platform unification**: library view shows all owned sources in one list — iTunes purchases (iOS), Bandcamp collection, 7digital purchases, local files. Same EQ pipeline regardless of source. BUY flow differs per platform (iOS = iTunes deep-link, Android = Bandcamp/7digital deep-link), LISTEN flow is identical.

**Library Archiver feature — the killer differentiator:**
The pitch (Elijah's framing): "We help you OWN your music forever — not just access today, but a physical artifact you control and can store anywhere." Feature: app downloads everything the user owns to a 1 TB microSD with proper organization, so the flash drive becomes a permanent physical archive of the entire collection. Plug it into a car radio, home stereo, DAP, anywhere with a USB or microSD slot — the library shows up properly categorized, not as a scattered file dump.

- **Folder structure**: `Artist/Album/##-Track.mp3` — industry standard, recognized by car radios, home stereos, digital audio players.
- **Embedded ID3 tags** (title, artist, album, year, genre) PLUS embedded album art per file — so car radios show the art on their built-in displays, not just filenames.
- **Playlist export** as .m3u files (universal format readable by virtually every player).
- **Optional**: a human-readable CSV catalog of everything for inventory reference.
- **Storage math**: 1 TB holds ~100,000 MP3s at 320kbps or ~200,000 AAC at 256kbps. Real personal libraries are 5K-20K tracks. 1 TB has runway for life.
- **Compatibility**: most modern car radios read FAT32 / exFAT microSD/USB, support MP3 universally and AAC/M4A on modern units. Plug in, library appears in the radio's UI properly categorized.
- **Why this is white space**: every other music app pushes you DEEPER into their cloud. This pushes ownership OUT of the cloud onto a physical object the user can store in a fireproof safe, lend to a friend, take anywhere. Spotify can't (DRM on subscription downloads). Apple Music app can't (same). Plex is the closest analog but Plex is a media-center server, not a portable archive. Real differentiator no one else fills.
- **Bigger frame**: aligns with `project_digital_relative_vision.md` ("digital relative who protects us and our information") and the off-grid self-sufficient AI thread. The flash drive is the music version of that — your data, your hardware, no service can take it away.

**Why this matters:**
The Radio app is a Sunkissed/Master-AI-arc-adjacent idea, not a Master AI feature. Elijah is the "MAKER, not developer" — he builds because what he wants doesn't exist. The original music-aggregator pitch hit walls (Spotify/Apple Music TOS, system-wide EQ ban). This FM-transmitter pivot ducks both walls — your app controls its own audio, you can EQ everything that flows through it. The remaining wall (DRM-locked subscription downloads) is the one Elijah hasn't decided how to handle.

**How to apply:**
- When Elijah brings this app up again, don't re-pitch path (a) as the only choice. He's been told. Respect that he's holding the bigger ambition open.
- Don't pretend the DRM wall doesn't exist when planning. Naming it once per session is enough — don't moralize.
- Function spine (in-app player + EQ + BT out) is the part that's universal regardless of which path he picks. That's safe to build/spec at any time.
- UI work (the CD-grill landscape look) is intentionally LATER — Elijah said "I want the function and I want you to know where I'm trying to have it do" before UI.
- Cross-references: original aggregator concept got the TOS-wall and system-EQ-wall analysis earlier in the same conversation. Sunkissed Soul + Master AI hub framing (`project_sunkissed_hub_is_master_ai.md`) suggests the Radio app could eventually live as a Pupil-style interface over Master-AI's local audio engine — but that's a stretch and not Elijah's stated framing yet.
