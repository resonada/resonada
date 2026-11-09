<div align="center">

<img src="ResonadaScreenshots/logo.jpg" width="140" alt="Resonāda logo">

# Resonāda

### Bit-perfect, reference-grade music player for Android — engineered for uncompromised acoustic transparency.

[![Latest release](https://img.shields.io/github/v/release/resonada/resonada?style=for-the-badge&label=Download&color=E0B567)](https://github.com/resonada/resonada/releases/latest)
`[![Downloads](https://img.shields.io/github/downloads/resonada/resonada/total?style=for-the-badge&color=9C27B0)](https://github.com/resonada/resonada/releases)
[![Platform](https://img.shields.io/badge/Platform-Android%208.1%2B-3DDC84?style=for-the-badge&logo=android&logoColor=white)](#-installation)
[![Community](https://img.shields.io/badge/Community-Telegram-26A5E4?style=for-the-badge&logo=telegram&logoColor=white)](https://t.me/+urRX_WELImQ0MjU1)
[![License](https://img.shields.io/badge/License-Freeware-blue?style=for-the-badge)](#-license)

**[⬇️ Download Official APK (v2.2.0.2 · Yathārtha)](https://github.com/resonada/resonada/releases/latest)**

</div>

---

## 📽️ Video Demonstration

Experience Resonāda's real-time analysis tools, native USB engine, and interface ergonomics in action:

<div align="center">

<video controls width="100%" poster="ResonadaScreenshots/03_now_playing_bitxact_exclusive.png">
  <source src="ResonadaScreenshots/resonada_walkthrough_demonstration.mp4" type="video/mp4">
  Your browser does not support the video tag.
</video>

<p align="center">
  <sub>If the embedded video player does not load in your client: <a href="ResonadaScreenshots/resonada_walkthrough_demonstration.mp4"><b>Download or stream the 1080p MP4 directly</b></a></sub>
</p>

</div>

---

## ⚡ Overview & Architecture

**Resonāda** is an audiophile music playback and forensic acoustic analysis engine for Android. Standard Android players route audio through the operating system's `AudioFlinger` mixer and vendor Hardware Abstraction Layer (HAL), which silently resamples audio, truncates bit depths, and introduces inter-sample distortions.

Resonāda eliminates this compromise by implementing its own **user-space USB Audio Class (UAC) driver**. By streaming isochronous PCM and native DSD packets directly to hardware endpoints via `usbdevfs`, Resonāda re-clocks connected DACs to the native sample rate of every track without intermediate conversion.

Complementing this bit-perfect pipeline is a **laboratory-grade analysis suite** featuring live signal flow inspection, whole-track acoustic integrity verification, dynamic mastering analysis, and a calibrated Short-Time Fourier Transform (STFT) spectrogram.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            RESONĀDA SIGNAL PATH                             │
└─────────────────────────────────────────────────────────────────────────────┘
 [ Lossless Audio File ] ──► [ Native C++ Decoder ] (libFLAC, WavPack, APE, DSD)
                                     │
                             (Bit-Exact PCM / DSD)
                                     │
                                     ▼
                      [ BitXact Verification Engine ] ──► [ Real-Time Telemetry ]
                                     │
                          (0.0 dB DSP Neutrality)
                                     │
                                     ▼
                      [ Exclusive User-Space UAC Driver ]
                                     │
                              (Direct usbdevfs)
                                     │
                                     ▼
                   [ USB DAC / Hardware Converter Endpoint ]
```

---

## 🌟 Core Technical Capabilities

### 🔊 Bit-Perfect Playback Engine (**bitXact**)
- **Direct User-Space USB Audio Driver**: Bypasses the Android audio mixer and HAL completely. Audio streams straight to the USB DAC endpoint with bit-for-bit fidelity across all standard PCM rates (44.1 kHz up to 384 kHz) and direct native DSD / DoP (DSD over PCM).
- **Native DSD & SACD Disc Images**: Direct decoding of `.dsf`, `.dff`, and SACD `.iso` disc images straight from the physical table of contents, with native gapless playback across same-rate DSD albums (over DoP, Native DSD, Direct DAP DSD, and DSD-to-PCM) and support for Direct Stream Transfer (DST) compressed DSDIFF files.
- **Hardware-Controlled Volume Integration**: Communicates with DAC USB Audio Feature Unit (#7) volume registers. Analog attenuation is executed in hardware at the converter, preserving the untouched digital bitstream. For software volume, True TPDF (Triangular Probability Density Function) dither prevents quantization truncation.
- **Dedicated Hardware Output Paths**: On audiophile-grade devices featuring integrated dedicated hi-res DACs, Resonāda routes through an exclusive native source-rate path, badged with dedicated **Direct BitXact** status indicators.
- **Multichannel Pass-Through & Folddown**: Discrete multichannel PCM streams route bit-perfect to multichannel USB DACs, or fold down to stereo through high-precision binaural HRTFs.

### 🔬 Forensic Acoustic Inspection & Signal Diagnostics
- **Acoustic Integrity Inspector & Spectrum Contour**: Generates peak-hold spectrum contours (dBFS vs Hz) plotted against theoretical 16-bit (-96 dBFS) and 24-bit (-144 dBFS) dynamic range floors. Measures physical acoustic cutoffs, verifies high-frequency harmonic extension, and detects synthetic upscales or transcoded lossy sources with high confidence — across PCM, DSD64–DSD1024 (`.dsf`, mono/multichannel `.dff`, SACD `.iso`), DoP in FLAC/WAV/AIFF/ALAC/APE/WavPack, `.cue` virtual track windows, and TAK/TTA/Wave64/CAF/WMA Lossless.
- **Signal Path Inspector**: Live stage-by-stage verification monitor mapping the active chain: `Source -> Decoder Engine -> Sample Rate Converter -> DSP Chain -> Output Endpoint`. Displays active buffer frame sizes, latency, and outputs an objective **Signal Integrity Score (100/100)**.
- **"Fix My Sound" Neutralization**: A one-tap emergency reset that clears all active equalization, zeroes gain adjustments, disables dynamics processing, and restores an uncolored, transparent signal path.
- **Audio X-Ray Telemetry**: Live spectral and dynamics monitoring of the output PCM stream, evaluating dynamic compression, peak/RMS power, stereo bandwidth, and phase correlation to pinpoint mastering flaws entirely on-device.
- **Calibrated STFT Spectrogram**: Per-channel Fast Fourier Transform with configurable window sizes (2048 to 8192-point STFT, Blackman windowing). Displays true Nyquist frequency ceilings, calibrated decibel scales, and includes Mid/Side channel isolation to detect artificial stereo expansion and joint-stereo artifacts.
- **Mastering & Loudness Metering**: Computes whole-track Mastering Scores (0–100), official Dynamic Range (DR) ratings, True Peak levels (dBTP) with inter-sample over detection against EBU R128 / ITU-R BS.1770 standards, integrated LUFS, Loudness Range (LRA), and DC bias offsets.

### 🔌 USB DAC Hardware Descriptors & Stream Monitoring
- **Deep USB Descriptor Decoding**: Inspects connected hardware identities (Vendor ID, Product ID, Manufacturer, Serial, USB Audio Class UAC1/UAC2, max isochronous packet sizes).
- **Hardware Clock Drift Monitoring**: Measures and displays live clock sync drift in Parts Per Million (PPM), buffer queue depth, and Android bit-perfect mixer lock states.
- **Per-Device Hardware Profiles**: Remembers DAC-specific configurations upon reconnect, including exclusive mode priority, allowed sample rate ceilings, hardware volume modes, call pause handling (**Pause on calls (USB DAC)**), per-device volume compatibility toggles, hardware reconstruction filter and gain controls on supported players, and configurable **DAC Start Pad** delays (0–3000 ms silence burst to wake sleep-state converter dongles without clipping track intros).

### 🎚️ Modular DSP Suite
- **Zero-Allocation Node Graph**: Drag-and-drop DSP processing chain reordering guided by an automated Topology Advisor. Configuration profiles export and import via validated JSON schemas.
- **32-Band Parametric Equalization**: Precision RBJ biquad filters with click-free coefficient interpolation and inline interactive frequency response curves.
- **Convolution Engine**: FIR impulse response processing for headphone frequency-response correction and acoustic room equalization.
- **Acoustic Optimization, AutoEQ & Target Matching**: Native headphone compensation database integration with headphone-to-headphone target matching and on-device parametric EQ optimization from exported frequency response measurements (Harman IE, Harman OE, or custom target curves).
- **Spatial Audio, Sound-Shaping & Dynamics**: Binaural spatialization via AES69 `.sofa` HRTF sets and ±30° virtual loudspeakers, IEM driver timing alignment, subsonic and ultrasonic speaker protection filters, subwoofer crossover control, speaker crosstalk cancellation, 432 Hz reference tuning, real-time pitch shifting, multi-curve crossfading, and peak limiting with automatic headroom protection and 64-bit double-precision processing.

### 🔄 System-Wide Routing: AuxBridge
- **Universal Application Capture**: Route audio from external applications (streaming services, browsers, and games) directly into the Resonāda exclusive user-space USB DAC driver.
- **Configurable Interception Rates**: Selectable bridge sample rates (44.1 kHz, 48 kHz, 96 kHz, 192 kHz) with independent software volume scaling.
- **OEM Audio Route Resolution**: Built-in guidance and optional command authorization to disable conflicting OEM system USB routing for glitch-free streaming.

### 📚 Library Auditing, Health & Discovery
- **Direct Filesystem Scanning**: Direct parallel disk reads bypassing Android MediaStore limitations, capturing comprehensive metadata, multi-disc architectures, and CUE sheet splits.
- **Universal App & Settings Search**: Search tracks, albums, artists, playlists, settings, audio tools, streaming sources, and sidebar screens directly from the Search tab with auto-scroll and card highlighting across all 5 supported languages.
- **Persistent Forensic Caching**: Stores mastering analysis, DR values, and acoustic verification data into local `.fidelity.json` sidecar files adjacent to audio tracks, surviving library rescans and app reinstalls.
- **Library Health & Integrity Audit**: Computes overall Lossless Purity percentages, flags duplicate audio files, detects lossy transcode fakes, and catalogs lowest-bitrate tracks as re-rip candidates.
- **Skip Intelligence & Curated Flow**: Analyzes completion percentages and skip frequencies to optimize playback queues, with an upcoming Flow pick preview card offering instant Skip and Keep actions.
- **Resonair Local Broadcast**: An on-device local radio experience with 6 curated sonic channels, an interactive artist blend tuner, a harmonic focus lens, DAC rate-matching filters, tempo and dynamic-range arc visualization, track lineage badges, live Boost/Dismiss steering, and 1-tap launch from Artist and Album screens.
- **Harmonic Matching ("Sounds Like")**: Local on-device queue synthesis matching harmonic musical keys, tempo BPM, and genre metadata without network dependence.
- **Smartlist 50+ Field Rule Engine**: Dynamic playlist generation across 50+ tag, file, signal, and listening-habit fields featuring `AND`/`OR`/`NOT` boolean condition grouping, musical key matching, sorting priorities, duration and artist diversity caps, playlist snapshots, and rule import/export.

### 🎨 Liquid Glass UI, Network & Ergonomics
- **Acoustic Refraction & Translucent Acrylics**: System-wide glassmorphism with true AMOLED-black options, momentum spring physics, swipe-to-dismiss gesture sheets, and local `.mp4`/`.mov`/`.m4v`/`.webm` animated video covers.
- **Customizable Tool Pill & Volume Studio**: Modular quick-access toolbar on the Now Playing screen supporting up to 5 user-selected action tools (Fidelity, Flow, Sounds Like, Spectrogram, Meta, Lyrics, Sleep, Tempo, Volume Studio), plus optional 180° reverse portrait rotation for DAC/amp stacking.
- **Syllable-Level Synced Lyrics**: High-precision word sweep highlighting loaded from embedded tags or local sidecar formats (`.lrc`, `.lyricsfile`), with support for multi-provider lookup and offline translation.
- **Native Music Servers & Network Share Media-Server UI**: Dedicated client for Subsonic, OpenSubsonic, Jellyfin, Emby, and Plex, plus a full media-server interface for SMB, WebDAV, FTP, FTPS, and SFTP network shares with remote `.cue` track splitting and a Cloud Cache · bitXact toggle.
- **Lossless Internet Radio**: Integrated lossless FLAC and AAC streaming radio with interactive channel selectors, multi-song DJ block track scrubbing, and a built-in World Audiophile & Genre station catalog.

---

## 🎵 Supported Audio Formats

| Category | Formats & Containers | Technical Capabilities |
| :--- | :--- | :--- |
| **Lossless Hi-Res PCM** | FLAC, ALAC, WAV, AIFF, Wave64 (`.w64`), Core Audio (`.caf`) | Up to 32-bit / 384 kHz+, bit-perfect pass-through, CUE sheet splitting |
| **Direct Stream Digital** | DSD64–DSD1024 (`.dsf`, `.dff`), SACD Disc Images (`.iso`) | Native DSD, DoP (including FLAC/WAV/AIFF/ALAC/APE/WavPack containers), native gapless DSD, DST decompression, PCM decimation fallback |
| **Advanced Lossless** | WavPack (`.wv`), APE (Monkey's Audio), TAK, TTA, WMA Lossless | Full APEv2 tag read/write, gapless playback, WavPack-DSD high-rate PCM, full forensic & spectrogram analysis |
| **Lossy Streams** | MP3, AAC / M4A, Ogg Vorbis, Opus, WMA | Native decoder pipeline, ID3v2/Vorbis comment metadata parsing, full acoustic & spectrogram inspection |
| **Multichannel Surround** | Discrete 5.1 / 7.1 Multichannel Audio | Bit-perfect multichannel USB DAC pass-through or high-fidelity stereo downmix |

---

## 📸 Interface & Feature Tour

The screenshots below illustrate Resonāda's interface hierarchy, analysis instrumentation, and configuration screens.

### Primary Playback & Real-Time Signal Telemetry

| Now Playing (bitXact Exclusive) | Signal Path Inspector | Audio X-Ray Telemetry |
| :---: | :---: | :---: |
| <img src="ResonadaScreenshots/03_now_playing_bitxact_exclusive.png" width="230" alt="Now Playing Screen"> | <img src="ResonadaScreenshots/04_signal_path_stage_inspector.png" width="230" alt="Signal Path Inspector"> | <img src="ResonadaScreenshots/07_audio_xray_pcm_analysis.png" width="230" alt="Audio X-Ray"> |
| *Bit-exact USB streaming with gold status seal* | *Stage-by-stage pipeline verification & buffer metrics* | *Real-time PCM spectral energy and dynamics monitor* |

| Signal Diagnostics & "Fix My Sound" | Liquid Glass Interface | Modular Tool Pill Customizer |
| :---: | :---: | :---: |
| <img src="ResonadaScreenshots/05_signal_path_fix_my_sound_diagnostics.png" width="230" alt="Signal Diagnostics"> | <img src="ResonadaScreenshots/06_now_playing_liquid_glass_ui.png" width="230" alt="Liquid Glass UI"> | <img src="ResonadaScreenshots/19_customize_tool_pill_reorder.png" width="230" alt="Customize Tool Pill"> |
| *Instant transparent reset & 100/100 integrity score* | *Edge-to-edge acrylic surface with gesture controls* | *Modular 5-slot quick action bar configuration* |

---

### Laboratory Acoustic Inspection & Forensics

| Acoustic Integrity & Spectrum Contour | Fidelity Verification & Upscale Detection | Dynamic Mastering & Loudness Scoring |
| :---: | :---: | :---: |
| <img src="ResonadaScreenshots/08_acoustic_integrity_spectrum_contour.png" width="230" alt="Acoustic Integrity"> | <img src="ResonadaScreenshots/09_fidelity_authenticity_verification.png" width="230" alt="Fidelity Verification"> | <img src="ResonadaScreenshots/10_mastering_dynamics_scoring.png" width="230" alt="Mastering Scoring"> |
| *Peak-hold contour vs theoretical bit-depth floors* | *Authentic CD/Hi-Res validation & transcode detection* | *Whole-track DR, True Peak dBTP, and EBU R128 LUFS* |

| Calibrated STFT Spectrogram | Mid/Side Channel Analysis | Harmonic "Sounds Like" Discovery |
| :---: | :---: | :---: |
| <img src="ResonadaScreenshots/11_spectrogram_stft_calibrated.png" width="230" alt="Calibrated Spectrogram"> | <img src="ResonadaScreenshots/12_spectrogram_mid_side_analysis.png" width="230" alt="Mid/Side Spectrogram"> | <img src="ResonadaScreenshots/13_harmonic_discovery_sounds_like.png" width="230" alt="Harmonic Discovery"> |
| *2048-pt STFT with Blackman window & dBFS grid* | *Side-channel inspection for artificial upmixes* | *Musical key and tempo matching queue generation* |

---

### USB Hardware Integration & External Routing

| USB DAC Hardware Descriptors | Audio Interfaces & Volume Unit | Live Stream Telemetry & Clock Sync |
| :---: | :---: | :---: |
| <img src="ResonadaScreenshots/14_usb_dac_hardware_descriptors.png" width="230" alt="USB Hardware Descriptors"> | <img src="ResonadaScreenshots/15_usb_dac_audio_interfaces.png" width="230" alt="USB Audio Interfaces"> | <img src="ResonadaScreenshots/16_usb_dac_stream_status_clock_sync.png" width="230" alt="Stream Telemetry"> |
| *UAC1/UAC2 descriptor and endpoint decode* | *Feature Unit #7 hardware volume negotiation* | *Live PPM clock drift and mixer lock diagnostics* |

| Output Devices Routing Hub | USB Tweaks & Exclusive Mode | AuxBridge System Audio Routing |
| :---: | :---: | :---: |
| <img src="ResonadaScreenshots/32_output_devices_routing_hub.png" width="230" alt="Output Devices Hub"> | <img src="ResonadaScreenshots/33_usb_output_tweaks_bitxact.png" width="230" alt="USB Tweaks"> | <img src="ResonadaScreenshots/39_auxbridge_system_audio_routing.png" width="230" alt="AuxBridge"> |
| *Centralized routing for Internal, USB, and Bluetooth* | *BitXact enforcement and auto-launch configuration* | *Stream third-party apps through the native driver* |

---

### Library Management, Curation & Discovery

| Personalized Home & Daily Mixes | Library Songs with Resolution Chips | Direct Filesystem Folder Browser |
| :---: | :---: | :---: |
| <img src="ResonadaScreenshots/01_home_daily_mixes.png" width="230" alt="Personalized Home"> | <img src="ResonadaScreenshots/23_library_songs_quality_chips.png" width="230" alt="Songs Library"> | <img src="ResonadaScreenshots/25_library_folders_filesystem_browser.png" width="230" alt="Folder Browser"> |
| *On-device Daily Mixes and smart continuation* | *Lossless quality indicators and multi-key sorting* | *Direct directory hierarchy without MediaStore delays* |

| Smartlist Rule Builder | Library Health & Re-Rip Audit | Skip Intelligence & Curation |
| :---: | :---: | :---: |
| <img src="ResonadaScreenshots/31_smartlist_rule_builder.png" width="230" alt="Smartlist Builder"> | <img src="ResonadaScreenshots/37_library_health_quality_audit.png" width="230" alt="Library Health"> | <img src="ResonadaScreenshots/38_skip_intelligence_library_curation.png" width="230" alt="Skip Intelligence"> |
| *Boolean condition grouping and diversity limits* | *Lossless purity audit and low-bitrate detection* | *Identification of frequently skipped tracks* |

---

### Sources, Lyrics & Automation

| Lossless Internet Radio Blocks | Syllable-Level Synced Lyrics | Automation & Intent Rules |
| :---: | :---: | :---: |
| <img src="ResonadaScreenshots/22_internet_radio_lossless_blocks.png" width="230" alt="Internet Radio"> | <img src="ResonadaScreenshots/17_synced_lyrics_word_level.png" width="230" alt="Synced Lyrics"> | <img src="ResonadaScreenshots/40_automation_event_rules_engine.png" width="230" alt="Automation Engine"> |
| *FLAC live radio with interactive DJ tracklist blocks* | *Real-time syllable highlighting and typography controls* | *When->Then event triggers and system automation* |

---

## 🗂️ Complete Screenshot Index

The screenshots directory (`ResonadaScreenshots/`) has been organized with semantic, descriptive filenames matching their architectural function:

```
ResonadaScreenshots/
├── 01_home_daily_mixes.png                      # Home feed with time greetings and on-device Daily Mixes
├── 02_home_rediscover_recommendations.png        # Rediscover and algorithmic local recommendations
├── 03_now_playing_bitxact_exclusive.png          # Now Playing with gold bitXact seal and timeline
├── 04_signal_path_stage_inspector.png            # Live stage-by-stage signal flow and buffer metrics
├── 05_signal_path_fix_my_sound_diagnostics.png   # Fix My Sound transparent reset & 100/100 score
├── 06_now_playing_liquid_glass_ui.png            # Acrylic glass interface with gesture navigation
├── 07_audio_xray_pcm_analysis.png                # Audio X-Ray live spectral and dynamic monitor
├── 08_acoustic_integrity_spectrum_contour.png    # Peak-hold spectrum contour vs theoretical floors
├── 09_fidelity_authenticity_verification.png     # Lossy upscale detection & physical cutoff analysis
├── 10_mastering_dynamics_scoring.png             # Whole-track DR, True Peak dBTP, and LUFS scoring
├── 11_spectrogram_stft_calibrated.png            # Calibrated STFT spectrogram with dBFS amplitude grid
├── 12_spectrogram_mid_side_analysis.png          # Mid/Side channel spectrogram decomposition
├── 13_harmonic_discovery_sounds_like.png         # Harmonic key and tempo matching discovery queue
├── 14_usb_dac_hardware_descriptors.png           # USB descriptor inspection (VID/PID, UAC Class, packets)
├── 15_usb_dac_audio_interfaces.png               # Audio interfaces, DoP support, and Feature Unit #7
├── 16_usb_dac_stream_status_clock_sync.png       # Live stream state, clock PPM drift, and mixer lock
├── 17_synced_lyrics_word_level.png               # Syllable-level sweep highlighting and typography
├── 18_lyrics_source_selector.png                 # Multi-provider lyric source selector and sidecars
├── 19_customize_tool_pill_reorder.png            # Tool Pill customization and action ordering
├── 20_customize_tool_pill_active_slots.png       # 5 active tool slots with overflow drawer
├── 21_sources_hub_network_radio.png              # Sources hub: storage, DLNA/UPnP, Cast, and feeds
├── 22_internet_radio_lossless_blocks.png         # Lossless FLAC internet radio with DJ block metadata
├── 23_library_songs_quality_chips.png            # Track catalog with bit-depth and sample rate chips
├── 24_library_playlists_m3u.png                  # Playlist manager with manual lists and M3U support
├── 25_library_folders_filesystem_browser.png     # Direct filesystem folder tree browser
├── 26_library_artists_grid.png                   # Artists catalog with high-resolution portraits
├── 27_library_album_artists_catalog.png          # Album artist catalog with multi-artist separation
├── 28_artist_detail_metadata_tools.png           # Artist biography, batch BPM, and R128 loudness tools
├── 29_library_decades_timeline_filter.png        # Chronological release year and decade distribution
├── 30_smartlists_presets_overview.png            # Smartlist overview with dynamic library presets
├── 31_smartlist_rule_builder.png                 # Boolean condition rule builder with diversity caps
├── 32_output_devices_routing_hub.png             # Output device router (Internal, USB, Bluetooth)
├── 33_usb_output_tweaks_bitxact.png              # BitXact enforcement and auto-launch USB configuration
├── 34_usb_diagnostics_device_profiles.png        # Saved hardware profiles and diagnostics monitor
├── 35_listening_stats_dna_overview.png           # Total play stats, listening streak, and Listening DNA
├── 36_listening_dna_purity_breakdown.png         # Lossless purity percentage and monthly recaps
├── 37_library_health_quality_audit.png           # Lossless ratio, duplicate finder, and re-rip audit
├── 38_skip_intelligence_library_curation.png     # Skip intelligence tracking high-abandonment tracks
├── 39_auxbridge_system_audio_routing.png         # AuxBridge third-party app USB audio interception
├── 40_automation_event_rules_engine.png          # In-app event-driven triggers and external intents
├── 41_settings_system_hub.png                    # Centralized settings and system management
├── 42_media_library_storage_analysis.png         # Filesystem scan rules and DSP analysis throttle
├── 43_media_library_sidecar_persistence.png      # Sidecar .fidelity.json analysis persistence settings
├── 44_media_library_policies_sidecars.png        # Article stripping, cover precedence, and lyrics rules
├── resonada_walkthrough_demonstration.mp4        # 1080p video demonstration walkthrough
├── logo.jpg                                      # Official Resonāda insignia (JPEG)
└── logo.png                                      # Official Resonāda insignia (PNG)
```

---

## 📲 Installation

> **Platform Requirement**: Android 8.1 (API Level 27) or higher. Recommended for devices running Android 10+ with USB OTG support.

1. Navigate to the **[Official Releases Page](https://github.com/resonada/resonada/releases)**.
2. Download the latest release APK (`arm64-v8a` is recommended for standard 64-bit devices).
3. On your Android device, open the downloaded APK via your browser or file manager.
4. When prompted by Android security, enable **"Install unknown apps"** for that installer.
5. Tap **Install**, then launch **Resonāda**.
6. Grant storage and audio permissions to enable direct library indexing.

*All official builds are cryptographically signed. Successive releases install seamlessly over previous builds without data loss.*

---

## ❓ Frequently Asked Questions

**Does Resonāda require an external USB DAC to function?**
No. Resonāda operates as a standalone reference player using your device's internal output. When an external USB DAC is connected, Resonāda automatically engages its custom user-space USB driver for bit-perfect output. Devices equipped with integrated dedicated high-resolution DACs automatically engage the native direct path.

**How does the bitXact status seal verify accuracy?**
The gold **bitXact** indicator is calculated from the complete physical signal chain. It illuminates only when the audio data delivered to the DAC endpoint matches the file source bit-for-bit. If equalization, digital volume reduction, sample rate conversion, or Android system mixing modifies the stream, the gold seal remains off.

**Are my library contents or analysis data uploaded to the cloud?**
No. All scanning, decoding, spectrogram computation, acoustic verification, and mastering metrics execute strictly on-device. Network functionality is restricted to user-configured streams, optional lyric lookups, and scrobbling endpoints.

**What is the purpose of `.fidelity.json` sidecar files?**
Deep acoustic and mastering analysis requires significant computational processing. Resonāda optionally saves these results as lightweight JSON sidecars beside local audio files. If you reinstall the application or move your music library, the analysis data loads instantly without requiring re-computation.

---

## 📜 License & Intellectual Property

**Copyright © 2026 R Kaurav. All rights reserved.**

Resonāda is distributed as **proprietary freeware** for personal, non-commercial listening:
- You may install and execute the signed binaries at no cost.
- You may distribute direct links to the official GitLab releases page.
- You may not reverse-engineer, decompile, modify, or extract components from the binary.
- You may not redistribute, rehost, or commercialize the installation package.

Open-source libraries utilized within Resonāda (such as native decoding components) are licensed under their respective open-source terms. Full legal notices and licenses are inspectable within the application under **Settings → About → Open-source licenses**.

For technical inquiries or distribution permissions, contact: **ralkau@proton.me** .

---

<div align="center">

**Resonāda** — _the vibrating point of origin._

Made with precision by **R Kaurav** and **VamsiHyperion**.

<sub>Keywords: bit-perfect audio player · USB Audio Class · UAC2 · native DSD · DoP · SACD ISO · FLAC · ALAC · WavPack · APE · spectrogram · STFT · acoustic integrity · mastering score · EBU R128 · True Peak · parametric EQ · convolution FIR · AuxBridge · Smartlists</sub>

</div>
