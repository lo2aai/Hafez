# Product Requirements Document (PRD)

## Quran Memorization Companion

**Version:** 1.0  
**Date:** February 14, 2026  
**Status:** Draft - MVP Scope

---

## 1. Product Name

**Quran Memorization Companion** (Working Title)

---

## 2. Product Vision

A mobile application that helps children memorize and revise Quran through structured, automated listening sessions composed of full surahs or specific ayah ranges with intelligent repetition and session control — eliminating manual playlist handling by parents.

---

## 3. Goals & Objectives

### Primary Goals

- Enable parents to create structured memorization sessions
- Allow playback of specific ayah ranges
- Provide automated repetition by count or duration
- Remove manual time tracking and playlist editing
- Provide distraction-free audio playback

### Success Criteria (MVP)

- Parent can create session in < 60 seconds
- Child can start session with one tap
- Zero manual switching between surahs during session
- Accurate ayah playback ranges

---

## 4. Target Users

### Primary User
**Children memorizing Quran**
- Age range: 5-15 years
- Requires simple, intuitive interface
- Needs guided, structured learning

### Secondary User
**Parents supervising memorization sessions**
- Responsible for session setup
- Monitors progress
- Requires quick configuration tools

---

## 5. Problem Statement

**Current Workflow (Pain Points):**

Today, parents manually create YouTube playlists for each memorization session:
1. Find individual surah videos on YouTube
2. Manually add them to a playlist
3. Calculate repetition timing themselves
4. Sit and manually skip/replay during the session
5. Recreate playlist when memorization plan changes

Current issues faced by parents:

- **Cannot play partial surahs easily**: Existing Quran apps don't allow playing specific ayah ranges (e.g., ayah 1-8 only)
- **Need to manually switch between surahs**: Parents must constantly change playlists and tracks
- **Video intros cause distraction**: YouTube videos have channel intros that break focus and distract children
- **Session timing handled manually**: Parents manually track time and stop playback, often interrupting mid-surah
- **Repetition management is inefficient**: No automated way to repeat segments by count or duration
- **Mid-surah interruptions**: Sessions often end in the middle of a surah when time runs out, disrupting memorization flow
- **Playlist recreation overhead**: Every time the child's memorization plan changes, parents must recreate the entire playlist

**Impact:** Parents spend significant time managing playback instead of focusing on their child's learning, leading to disrupted memorization sessions and decreased effectiveness.

---

## 6. Core Features (MVP)

### 6.1 Session Builder

Parent creates a session consisting of segments.

**Segment Configuration:**
- Surah selection
- Start ayah
- End ayah
- Repeat by count
- Repeat by duration

**Example Session:**
```
1. Al-Adiyat (1-8) — repeat 5 times
2. At-Takathur (full) — repeat 3 times
3. Al-Humazah (full) — repeat until 10 minutes
```

**Features:**
- Drag-and-drop segment ordering
- Save session templates
- Duplicate sessions
- Edit existing sessions

---

### 6.2 Smart Playback Engine

**Capabilities:**
- Sequential playback across segments
- Mixed repetition logic (count + duration)
- Resume from last position
- Automatic transitions between segments
- Intelligent repeat handling

**Playback Logic:**
- Honors ayah-level boundaries
- Smooth transitions between ayahs
- No audio gaps or overlaps
- Accurate repeat counting

---

### 6.3 Clean Audio Player

**Requirements:**
- Pure audio (no video distractions)
- Ayah-level playback precision
- Speed control (0.75x - 1.5x)
- Configurable pause between ayahs
- Repeat single ayah option
- Background audio support

**Controls:**
- Play/Pause
- Skip to next ayah
- Skip to previous ayah
- Speed adjustment
- Volume control

---

### 6.4 Session Timer

**Features:**
- Total session duration display
- Auto stop at session end
- Auto fade out (smooth ending)
- Progress indicator
- Elapsed vs. remaining time
- **Smart Stop**: Complete current ayah/segment before stopping (never cut mid-ayah)

**Configuration:**
- Set maximum session duration
- Enable/disable auto-stop
- Fade out duration control
- **Stop Behavior Options:**
  - Stop immediately at time limit
  - Complete current ayah then stop (default)
  - Complete current segment then stop

---

### 6.5 Kid Mode Interface

**Design Principles:**
- One big start button (primary action)
- Minimal UI clutter
- Visual progress indicator
- Encouraging visuals (stars, progress bar)
- Large, readable text
- High contrast colors
- Simple navigation

**Features:**
- Session name display
- Current surah/ayah indicator
- Simple pause/resume
- Progress celebration animations

---

## 7. Non-Functional Requirements

### Performance
- Offline-first audio support
- Low latency playback (< 200ms)
- Fast app startup (< 2 seconds)
- Smooth transitions between ayahs

### Scalability
- Support for multiple reciters
- Modular architecture for future features
- Efficient audio caching

### Reliability
- Background playback support
- Handles interruptions (calls, notifications)
- Persistent session state
- Auto-save progress

### Usability
- Intuitive parent interface
- Child-friendly playback mode
- Minimal learning curve
- Accessible design

### Technical
- Cross-platform support (iOS/Android)
- Efficient battery usage
- Minimal storage footprint
- Secure data handling

---

## 8. Data Model (Conceptual)

### Surah
```javascript
{
  id: Number,
  name: String,
  nameArabic: String,
  totalAyahs: Number,
  revelationType: String // "Meccan" | "Medinan"
}
```

### AyahAudio
```javascript
{
  id: String,
  surahId: Number,
  ayahNumber: Number,
  audioUrl: String,
  duration: Number, // in seconds
  timestampStart: Number, // milliseconds
  timestampEnd: Number, // milliseconds
  reciterId: String
}
```

### Segment
```javascript
{
  id: String,
  surahId: Number,
  startAyah: Number,
  endAyah: Number,
  repeatCount: Number | null, // null if using duration
  repeatDuration: Number | null, // in minutes, null if using count
  order: Number
}
```

### Session
```javascript
{
  id: String,
  name: String,
  segments: Array<Segment>,
  totalDuration: Number, // estimated in minutes
  createdAt: Date,
  updatedAt: Date,
  lastPlayedAt: Date | null,
  isTemplate: Boolean
}
```

### Reciter
```javascript
{
  id: String,
  name: String,
  nameArabic: String,
  baseAudioUrl: String,
  isDefault: Boolean
}
```

### PlaybackState
```javascript
{
  sessionId: String,
  currentSegmentIndex: Number,
  currentRepeatCount: Number,
  currentAyahIndex: Number,
  elapsedTime: Number,
  isPaused: Boolean,
  lastUpdated: Date
}
```

---

## 9. User Flows

### Parent Flow: Create Session
1. Open app → Navigate to "Create Session"
2. Enter session name
3. Add segment → Select surah
4. Set ayah range (start/end)
5. Choose repeat method (count or duration)
6. Add more segments (optional)
7. Save session
8. Share with child's device (optional)

### Child Flow: Play Session
1. Open app → See saved sessions
2. Tap on session card
3. Tap large "Start" button
4. Listen to playback
5. View progress indicator
6. Session auto-stops when complete

---

## 10. Use Cases (Based on Real Parent Needs)

### Use Case 1: Partial Surah Memorization
**Scenario:** Child is memorizing Al-Adiyat but only learned ayahs 1-8 so far.

**Current Problem:** 
- Parent must play full surah or manually stop at ayah 8
- No easy way to loop just ayahs 1-8

**Solution:**
```
Create segment:
- Surah: Al-Adiyat
- Range: Ayah 1 to 8
- Repeat: 5 times

Result: Child listens to ayahs 1-8 five times, nothing more
```

---

### Use Case 2: Mixed Review Session
**Scenario:** Child needs to review:
- Al-Adiyat (1-8) - needs heavy repetition
- At-Takathur (full) - just light review
- Al-Humazah (full) - medium review
- Al-Qari'ah (full) - light review

**Current Problem:**
- Parent must create YouTube playlist manually
- Cannot specify different repetition counts
- Must manually track time for each

**Solution:**
```
Create session "Morning Review":

Segment 1: Al-Adiyat (1-8) × 5 times
Segment 2: At-Takathur (full) × 2 times
Segment 3: Al-Humazah (full) × 3 times
Segment 4: Al-Qari'ah (full) × 2 times

Press Play → Everything runs automatically
```

---

### Use Case 3: Time-Based Practice
**Scenario:** Parent has exactly 15 minutes before school, wants to maximize repetition of new surah.

**Current Problem:**
- Parent must manually calculate how many repetitions fit in 15 minutes
- Must manually stop playback
- Often interrupts mid-surah

**Solution:**
```
Create segment:
- Surah: Al-Humazah (full)
- Repeat: For 15 minutes
- Stop behavior: Complete current ayah

Result: Surah repeats as many times as possible within 15 min,
        stops cleanly after completing an ayah (not mid-ayah)
```

---

### Use Case 4: No Video Distractions
**Scenario:** Child gets distracted by YouTube channel intros, thumbnails, and related videos.

**Current Problem:**
- YouTube videos have intros (5-10 seconds)
- Autoplay suggests non-educational content
- Visual distractions reduce focus

**Solution:**
- Pure audio playback only
- No videos, no intros
- Minimal, focused interface
- Child stays on task

---

### Use Case 5: Session Templates
**Scenario:** Parent has a standard weekly review routine that rarely changes.

**Current Problem:**
- Must recreate playlist every time
- Time-consuming and error-prone

**Solution:**
```
Save session as template "Weekly Review"
Next week: Load template → Press play
No recreation needed
```

---

## 11. Future Enhancements (Post-MVP)

- Multiple reciter support
- Progress tracking and analytics
- Gamification (badges, streaks)
- Parent dashboard
- Revision scheduler
- Tajweed highlighting
- Translation overlay
- Offline download manager
- Social sharing of progress
- Multi-child profiles

---

## 11. Technical Considerations

### Audio Source
- Verified Quran recitation APIs
- Ayah-level granularity required
- High-quality audio files
- Proper licensing

### Platforms
- React Native for cross-platform
- Native audio libraries for performance
- Offline storage (AsyncStorage / SQLite)

### Third-Party Services
- Quran API (e.g., Quran.com API, EveryAyah.com)
- Audio CDN for streaming
- Analytics (optional)

---

## 12. Success Metrics

### Engagement
- Daily active sessions
- Average session completion rate
- Session creation frequency

### Performance
- App load time < 2s
- Audio playback start time < 500ms
- Zero audio gaps between ayahs

### User Satisfaction
- Parent ease-of-use score (target: 4.5/5)
- Child engagement rate
- Session replay rate

---

## 13. Risks & Mitigation

| Risk | Impact | Mitigation |
|------|--------|------------|
| Audio API downtime | High | Offline-first architecture with cached audio |
| Ayah boundary accuracy | High | Validate against multiple sources, manual QA |
| Large audio files | Medium | Progressive loading, compression |
| Platform-specific audio bugs | Medium | Extensive device testing, fallback mechanisms |
| Complex parent UI | Medium | User testing, iterative design |

---

## 14. Timeline (Estimated)

| Phase | Duration | Deliverables |
|-------|----------|--------------|
| Design & Prototyping | 2 weeks | UI/UX mockups, user flows |
| Core Development | 6 weeks | Session builder, playback engine |
| Audio Integration | 2 weeks | API integration, audio caching |
| Testing & QA | 2 weeks | Bug fixes, performance optimization |
| Beta Release | 1 week | Limited user testing |
| MVP Launch | 1 week | App store submission |

**Total: ~14 weeks**

---

## 15. Open Questions

- Which reciter(s) to support in MVP?
- Should sessions sync across devices?
- Parental controls for app usage time?
- Multi-language support for UI?
- Audio quality vs. file size trade-offs?

---

## 16. Appendix

### Glossary
- **Surah**: Chapter of the Quran
- **Ayah**: Verse within a surah
- **Segment**: A configured portion of Quran within a session
- **Session**: A complete memorization routine with multiple segments
- **Reciter**: Person whose voice is used for recitation

### References
- Quran.com API Documentation
- EveryAyah.com Audio Database
- Islamic Educational App Best Practices

---

**Document Owner:** Product Team  
**Last Updated:** February 14, 2026  
**Next Review:** Post-MVP Launch
