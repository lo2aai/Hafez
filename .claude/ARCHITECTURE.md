# System Architecture Document

## Quran Memorization Companion

**Version:** 1.0  
**Date:** February 14, 2026  
**Status:** Draft - MVP Architecture

---

## Table of Contents

1. [High-Level Architecture](#1-high-level-architecture)
2. [Architecture Diagram](#2-architecture-diagram)
3. [Layer Breakdown](#3-layer-breakdown)
4. [Core Components](#4-core-components)
5. [Data Flow](#5-data-flow)
6. [Audio Strategy](#6-audio-strategy)
7. [Technology Stack](#7-technology-stack)
8. [Scalability Considerations](#8-scalability-considerations)
9. [Performance Optimization](#9-performance-optimization)
10. [Security & Privacy](#10-security--privacy)

---

## 1. High-Level Architecture

```
┌─────────────────────────────────────────────┐
│         Mobile App (React Native)           │
│                                             │
│  ┌───────────────────────────────────────┐ │
│  │      Presentation Layer (UI)          │ │
│  └───────────────┬───────────────────────┘ │
│                  │                          │
│  ┌───────────────▼───────────────────────┐ │
│  │      Application Layer (Logic)        │ │
│  └───────────────┬───────────────────────┘ │
│                  │                          │
│  ┌───────────────▼───────────────────────┐ │
│  │    Playback Engine Layer (Audio)      │ │
│  └───────────────┬───────────────────────┘ │
│                  │                          │
│  ┌───────────────▼───────────────────────┐ │
│  │       Data Layer (Persistence)        │ │
│  └───────────────┬───────────────────────┘ │
└──────────────────┼───────────────────────────┘
                   │
    ┌──────────────▼──────────────┐
    │  Local Storage  │  Remote API │
    └─────────────────┴─────────────┘
```

---

## 2. Architecture Diagram

```
┌──────────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                        │
├──────────────────────────────────────────────────────────────┤
│  SessionBuilder  │  KidPlayer  │  Dashboard  │  Settings    │
│     Screen       │   Screen    │   Screen    │   Screen     │
└────────┬─────────┴─────┬───────┴──────┬──────┴──────┬───────┘
         │               │              │             │
         └───────────────┼──────────────┼─────────────┘
                         │              │
┌────────────────────────▼──────────────▼──────────────────────┐
│                   APPLICATION LAYER                          │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌────────────────┐  ┌──────────────┐  ┌────────────────┐  │
│  │    Session     │  │   Playback   │  │    Reciter     │  │
│  │    Manager     │  │  Controller  │  │    Manager     │  │
│  └────────┬───────┘  └──────┬───────┘  └────────┬───────┘  │
│           │                 │                    │          │
└───────────┼─────────────────┼────────────────────┼──────────┘
            │                 │                    │
            │                 │                    │
┌───────────▼─────────────────▼────────────────────▼──────────┐
│                  PLAYBACK ENGINE LAYER                       │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │    Queue     │  │     Audio    │  │   Repeat     │      │
│  │   Builder    │  │    Player    │  │   Handler    │      │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘      │
│         │                 │                  │              │
│  ┌──────▼─────────────────▼──────────────────▼───────┐      │
│  │          Playback State Manager                   │      │
│  └────────────────────────┬───────────────────────────┘      │
└───────────────────────────┼──────────────────────────────────┘
                            │
┌───────────────────────────▼──────────────────────────────────┐
│                      DATA LAYER                              │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌─────────────┐  ┌──────────────┐  ┌──────────────┐       │
│  │   Session   │  │    Audio     │  │   Settings   │       │
│  │ Repository  │  │    Cache     │  │  Repository  │       │
│  └──────┬──────┘  └──────┬───────┘  └──────┬───────┘       │
│         │                │                  │               │
└─────────┼────────────────┼──────────────────┼───────────────┘
          │                │                  │
┌─────────▼────────────────▼──────────────────▼───────────────┐
│              STORAGE & EXTERNAL SERVICES                     │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   SQLite/    │  │    Audio     │  │   Quran API  │      │
│  │     MMKV     │  │   CDN/Local  │  │  (Metadata)  │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└──────────────────────────────────────────────────────────────┘
```

---

## 3. Layer Breakdown

### 3.1 Presentation Layer

**React Native Screens:**

```javascript
// Core Screens
- SessionBuilderScreen      // Parent creates/edits sessions
- SegmentEditorModal        // Configure individual segments
- KidPlayerScreen           // Child-friendly playback interface
- ParentDashboardScreen     // Overview and management
- AudioSettingsScreen       // Reciter selection, speed, etc.
```

**Responsibilities:**
- Render UI components
- Handle user interactions
- Display session data
- Navigate between screens
- Show loading/error states

**Key Components:**
- `SessionCard`: Display session summary
- `SegmentList`: Draggable segment list
- `PlaybackControls`: Play/pause/skip buttons
- `ProgressIndicator`: Visual progress tracking
- `SurahPicker`: Surah selection component

---

### 3.2 Application Layer

**Core Managers:**

#### Session Manager
```javascript
class SessionManager {
  // Build complete playback queue
  buildPlaybackQueue(session): AyahUnit[]
  
  // Calculate total session duration
  calculateDuration(session): number
  
  // Handle mixed repeat logic (count + duration)
  resolveRepeatStrategy(segment): RepeatConfig
  
  // Validate session configuration
  validateSession(session): ValidationResult
  
  // Save/load sessions
  saveSession(session): Promise<void>
  loadSessions(): Promise<Session[]>
}
```

**Responsibilities:**
- Session CRUD operations
- Queue generation logic
- Duration calculations
- Validation rules
- Template management

---

#### Playback Controller
```javascript
class PlaybackController {
  // Orchestrate playback queue
  startPlayback(session): Promise<void>
  pausePlayback(): void
  resumePlayback(): void
  stopPlayback(): void
  
  // Queue navigation
  nextAyah(): void
  previousAyah(): void
  skipToSegment(index): void
  
  // Ayah transitions
  handleAyahComplete(): void
  handleSegmentComplete(): void
  
  // State management
  getPlaybackState(): PlaybackState
  savePlaybackState(): Promise<void>
}
```

**Responsibilities:**
- Queue orchestration
- Pause/resume functionality
- Ayah transition handling
- Progress tracking
- State persistence

---

#### Reciter Manager
```javascript
class ReciterManager {
  // Load available reciters
  loadReciters(): Promise<Reciter[]>
  
  // Get current reciter
  getCurrentReciter(): Reciter
  
  // Switch audio source
  setReciter(reciterId): Promise<void>
  
  // Build audio URL
  getAudioUrl(reciterId, surahId, ayahNumber): string
  
  // Validate reciter availability
  checkReciterAvailability(reciterId): Promise<boolean>
}
```

**Responsibilities:**
- Reciter configuration loading
- Audio source switching
- URL generation
- Reciter validation

---

### 3.3 Playback Engine Layer

**Core Components:**

#### Queue Builder
```javascript
class QueueBuilder {
  build(segments: Segment[]): AyahUnit[]
  
  // Expand segment into ayah units
  expandSegment(segment): AyahUnit[]
  
  // Apply repetition logic
  applyRepetition(ayahs, repeatConfig): AyahUnit[]
}
```

**AyahUnit Structure:**
```javascript
interface AyahUnit {
  id: string;
  surahId: number;
  ayahNumber: number;
  audioUrl: string;
  duration: number; // in seconds
  segmentId: string;
  repeatIndex: number; // which repetition (1, 2, 3...)
}
```

**Example Queue:**
```javascript
// Session: Al-Adiyat (1-3) repeat 2x
[
  { surahId: 100, ayahNumber: 1, repeatIndex: 1 },
  { surahId: 100, ayahNumber: 2, repeatIndex: 1 },
  { surahId: 100, ayahNumber: 3, repeatIndex: 1 },
  { surahId: 100, ayahNumber: 1, repeatIndex: 2 }, // repeat starts
  { surahId: 100, ayahNumber: 2, repeatIndex: 2 },
  { surahId: 100, ayahNumber: 3, repeatIndex: 2 }
]
```

---

#### Audio Player
```javascript
class AudioPlayer {
  // Load and play ayah
  loadAyah(ayahUnit: AyahUnit): Promise<void>
  play(): Promise<void>
  pause(): void
  
  // Playback control
  setSpeed(speed: number): void // 0.75x - 1.5x
  setVolume(volume: number): void
  
  // Event handlers
  onComplete(callback): void
  onError(callback): void
  onProgress(callback): void
  
  // Offline support
  cacheAyah(ayahUnit): Promise<void>
  isCached(ayahUnit): boolean
}
```

**Technology:**
- `react-native-track-player` for robust audio playback
- Native audio modules for performance
- Background playback support

---

#### Repeat Handler
```javascript
class RepeatHandler {
  // Determine if more repetitions needed
  shouldRepeat(segment, currentCount, elapsedTime): boolean
  
  // Track repetition progress
  trackRepetition(segmentId, type): void
  
  // Reset repetition counter
  resetRepetition(segmentId): void
  
  // Get repetition status
  getRepetitionStatus(segmentId): RepetitionStatus
}
```

**Repetition Types:**
- **By Count**: Repeat segment N times
- **By Duration**: Repeat until X minutes elapsed
- **Infinite**: Repeat until manually stopped (future)

---

#### Smart Stop Handler
```javascript
class SmartStopHandler {
  // Determine if session should stop
  shouldStop(elapsedTime: number, timeLimit: number, stopBehavior: StopBehavior): boolean
  
  // Handle different stop behaviors
  handleTimeLimit(state: PlaybackState): Promise<void>
  
  // Stop behaviors:
  // - IMMEDIATE: Stop right away (may cut mid-ayah)
  // - COMPLETE_AYAH: Finish current ayah then stop
  // - COMPLETE_SEGMENT: Finish current segment then stop
}
```

**Why This Matters:**
The original problem mentioned sessions ending mid-surah, which disrupts memorization. Smart Stop ensures natural completion points.

**Example:**
```
Time Limit: 10 minutes
Elapsed: 9:45
Current: Al-Adiyat Ayah 7/11
Stop Behavior: COMPLETE_AYAH

Action: Play ayah 7 to completion (even if it exceeds 10 min by 15 seconds)
Result: Clean stop at ayah boundary
```

---

#### Playback State Manager
```javascript
class PlaybackStateManager {
  state: {
    sessionId: string;
    currentAyahIndex: number;
    currentSegmentIndex: number;
    elapsedTime: number;
    isPaused: boolean;
    completedAyahs: number;
    totalAyahs: number;
  };
  
  // State operations
  updateState(updates): void
  saveState(): Promise<void>
  loadState(sessionId): Promise<PlaybackState>
  clearState(): void
  
  // Progress calculations
  getProgress(): number // 0-100
  getTimeRemaining(): number
}
```

---

### 3.4 Data Layer

**Repositories:**

#### Session Repository
```javascript
class SessionRepository {
  // CRUD operations
  create(session: Session): Promise<string>
  read(id: string): Promise<Session>
  update(id: string, updates): Promise<void>
  delete(id: string): Promise<void>
  
  // Queries
  getAll(): Promise<Session[]>
  getTemplates(): Promise<Session[]>
  getRecent(limit: number): Promise<Session[]>
  
  // Search
  searchByName(query: string): Promise<Session[]>
}
```

---

#### Audio Cache Repository
```javascript
class AudioCacheRepository {
  // Cache management
  cacheAudio(ayahUnit, audioBlob): Promise<void>
  getCachedAudio(ayahUnit): Promise<Blob | null>
  isCached(ayahUnit): boolean
  
  // Cache optimization
  clearOldCache(daysOld: number): Promise<void>
  getCacheSize(): Promise<number>
  
  // Preloading
  preloadSession(session): Promise<void>
}
```

**Storage Strategy:**
- Cache frequently used ayahs locally
- Lazy load uncommon recitations
- Maximum cache size: 500MB (configurable)
- LRU eviction policy

---

#### Settings Repository
```javascript
class SettingsRepository {
  // User preferences
  getReciterId(): string
  setReciterId(id: string): Promise<void>
  
  getPlaybackSpeed(): number
  setPlaybackSpeed(speed: number): Promise<void>
  
  getPauseBetweenAyahs(): number
  setPauseBetweenAyahs(seconds: number): Promise<void>
  
  // App settings
  getTheme(): 'light' | 'dark'
  setTheme(theme): Promise<void>
}
```

---

## 4. Core Components

### 4.1 Playback Queue Concept

**Key Principle:**
```
PlaybackQueue = Array<AyahUnit>
```

**Why Ayah-Level?**
- ✅ Precise partial playback (e.g., Surah 1-5)
- ✅ Smart looping per segment
- ✅ Accurate progress tracking
- ✅ Easy resume functionality
- ❌ NOT surah-level (too coarse-grained)

**Queue Building Example:**

```javascript
// Input Session
{
  segments: [
    { surahId: 100, start: 1, end: 3, repeatCount: 2 },
    { surahId: 101, start: 1, end: 8, repeatCount: 1 }
  ]
}

// Output Queue (9 ayah units)
[
  // Segment 1 - First iteration
  AyahUnit(100, 1, repeat: 1),
  AyahUnit(100, 2, repeat: 1),
  AyahUnit(100, 3, repeat: 1),
  
  // Segment 1 - Second iteration
  AyahUnit(100, 1, repeat: 2),
  AyahUnit(100, 2, repeat: 2),
  AyahUnit(100, 3, repeat: 2),
  
  // Segment 2 - Single iteration
  AyahUnit(101, 1, repeat: 1),
  AyahUnit(101, 2, repeat: 1),
  AyahUnit(101, 3, repeat: 1),
  // ... up to ayah 8
]
```

---

### 4.2 Mixed Repeat Logic

**Challenge:** Handle both count-based and duration-based repetition.

**Solution:**

```javascript
function shouldContinueRepetition(segment, state) {
  if (segment.repeatCount !== null) {
    return state.currentRepeat < segment.repeatCount;
  }
  
  if (segment.repeatDuration !== null) {
    return state.segmentElapsedTime < segment.repeatDuration * 60;
  }
  
  return false; // No repetition
}
```

**Example:**
- Segment A: Repeat 3 times → Count-based (deterministic queue)
- Segment B: Repeat for 10 minutes → Duration-based (dynamic queue)

---

## 5. Data Flow

### 5.1 Session Creation Flow

```
User Action (Parent) → SessionBuilderScreen
                              ↓
                      SessionManager.createSession()
                              ↓
                      Validate segments
                              ↓
                      Calculate total duration
                              ↓
                      SessionRepository.save()
                              ↓
                      Update UI with new session
```

---

### 5.2 Playback Start Flow

```
User Action (Child) → KidPlayerScreen.onStartPress()
                              ↓
                      PlaybackController.startPlayback(sessionId)
                              ↓
                      SessionManager.buildPlaybackQueue(session)
                              ↓
                      QueueBuilder.build(segments)
                              ↓
                      AudioPlayer.loadAyah(firstAyahUnit)
                              ↓
                      AudioPlayer.play()
                              ↓
                      Update PlaybackState
                              ↓
                      Render progress in UI
```

---

### 5.3 Ayah Transition Flow

```
AudioPlayer.onComplete() → PlaybackController.handleAyahComplete()
                                    ↓
                           Check if segment complete
                                    ↓
                        No → Load next ayah in queue
                                    ↓
                           AudioPlayer.loadAyah(nextAyah)
                                    ↓
                           Apply pause (if configured)
                                    ↓
                           AudioPlayer.play()
                                    ↓
                           Update progress
```

```
                        Yes → Check if repetition needed
                                    ↓
                        Yes → RepeatHandler.shouldRepeat()
                                    ↓
                              Rebuild segment queue
                                    ↓
                              Continue playback
                                    ↓
                        No → Move to next segment
                                    ↓
                             Check if session complete
                                    ↓
                        Yes → Show completion screen
                        No → Continue to next segment
```

---

## 6. Audio Strategy

### 6.1 Critical Decision: Ayah-Level Audio

**❌ Surah-Level Audio (Not Used):**
```
/audio/001.mp3  (entire Al-Fatiha)
/audio/002.mp3  (entire Al-Baqarah - 2+ hours!)
```

**Problems:**
- Cannot play partial surahs (e.g., ayah 1-5)
- Inefficient for repetition
- Large file downloads
- Difficult progress tracking

---

**✅ Ayah-Level Audio (Chosen Approach):**
```
/audio/{reciterId}/001/001.mp3  (Al-Fatiha, ayah 1)
/audio/{reciterId}/001/002.mp3  (Al-Fatiha, ayah 2)
/audio/{reciterId}/002/001.mp3  (Al-Baqarah, ayah 1)
```

**Benefits:**
- ✅ Precise partial playback
- ✅ Efficient repetition
- ✅ Smart caching
- ✅ Accurate progress
- ✅ Resume at exact ayah

---

### 6.2 Audio URL Pattern (Multi-Reciter Ready)

**Pattern:**
```
https://cdn.example.com/audio/{reciterId}/{surahId}/{ayahNumber}.mp3
```

**Examples:**
```
// Abdul Basit
/audio/abdul_basit/001/001.mp3

// Mishary Rashid
/audio/mishary/001/001.mp3

// Maher Al-Muaiqly
/audio/maher/001/001.mp3
```

**Rules:**
- NEVER hardcode reciter in code
- Always parameterize reciter ID
- Use ReciterManager for URL generation

---

### 6.3 Audio Metadata

Each ayah audio should have metadata:

```javascript
{
  surahId: 1,
  ayahNumber: 1,
  reciterId: "abdul_basit",
  duration: 4.2, // seconds
  bitrate: 128, // kbps
  format: "mp3",
  url: "https://cdn.example.com/...",
  timestampStart: 0, // for combined files (future)
  timestampEnd: 4200 // milliseconds
}
```

---

### 6.4 Caching Strategy

**Priority Levels:**
1. **High**: Currently playing session
2. **Medium**: Recently played sessions
3. **Low**: Saved templates

**Cache Rules:**
- Cache ayahs from active session on app start
- Preload next 5 ayahs during playback
- Keep recently used ayahs for 30 days
- Clear cache when exceeding 500MB

---

## 7. Technology Stack

### 7.1 Frontend

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| Framework | React Native | Cross-platform, large community |
| Navigation | React Navigation | Industry standard |
| State Management | Zustand / Redux Toolkit | Global state (sessions, playback) |
| UI Components | React Native Paper | Material Design, accessibility |
| Audio Playback | react-native-track-player | Robust, background support |

---

### 7.2 Data & Storage

| Component | Technology | Rationale |
|-----------|-----------|-----------|
| Local Database | SQLite (via react-native-sqlite-storage) | Complex queries, relationships |
| Key-Value Store | MMKV | Fast settings/cache |
| Audio Cache | FileSystem API | Local audio storage |

---

### 7.3 External Services

| Service | Purpose | Provider Options |
|---------|---------|------------------|
| Quran Metadata API | Surah/ayah info | Quran.com API, Tanzil.net |
| Audio CDN | Ayah audio files | EveryAyah.com, Custom CDN |
| Analytics (optional) | Usage tracking | Firebase Analytics |

---

### 7.4 Development Tools

- **TypeScript**: Type safety
- **ESLint + Prettier**: Code quality
- **Jest + React Native Testing Library**: Unit tests
- **Detox**: E2E testing
- **Fastlane**: CI/CD automation

---

## 8. Scalability Considerations

### 8.1 Multi-Reciter Support (Day 1)

**Architecture Requirements:**
- Reciter as a first-class entity
- Dynamic audio URL generation
- Reciter-specific settings

**Implementation:**
```javascript
// Good: Parameterized
const url = buildAudioUrl(reciterId, surahId, ayahNumber);

// Bad: Hardcoded
const url = `https://cdn.com/abdul_basit/${surahId}/${ayahNumber}.mp3`;
```

**Reciter Configuration:**
```javascript
const reciters = [
  {
    id: "abdul_basit",
    name: "Abdul Basit",
    baseUrl: "https://cdn.com/audio/abdul_basit",
    isDefault: true
  },
  {
    id: "mishary",
    name: "Mishary Rashid Alafasy",
    baseUrl: "https://cdn.com/audio/mishary",
    isDefault: false
  }
];
```

---

### 8.2 Performance Optimization

**Lazy Loading:**
- Don't load entire session queue at once
- Load next segment when current segment is 80% complete

**Memoization:**
- Cache queue building results
- Memoize duration calculations

**Debouncing:**
- Debounce segment editor changes
- Throttle progress updates (max 1/second)

---

### 8.3 Offline-First Architecture

**Principles:**
- App works 100% offline after initial setup
- Sync settings when online
- Queue failed requests for retry

**Critical Offline Features:**
- Cached audio playback
- Local session storage
- Offline progress tracking

---

## 9. Performance Optimization

### 9.1 Audio Performance

**Pre-buffering:**
```javascript
// Preload next 3 ayahs while current playing
async function preloadUpcoming(currentIndex, queue) {
  const upcoming = queue.slice(currentIndex + 1, currentIndex + 4);
  await Promise.all(upcoming.map(ayah => audioPlayer.preload(ayah)));
}
```

**Gapless Playback:**
- Use native audio queue when possible
- Minimize transition delays (< 100ms)

---

### 9.2 Memory Management

**Audio Cache Limits:**
- Max 500MB total cache
- Max 50 ayahs in memory
- Evict least recently used

**React Component Optimization:**
- Use `React.memo` for list items
- Virtualize long segment lists
- Avoid re-renders on playback progress

---

### 9.3 App Startup Time

**Target: < 2 seconds**

**Optimizations:**
- Lazy load non-critical screens
- Defer analytics initialization
- Cache initial data load
- Use splash screen efficiently

---

## 10. Security & Privacy

### 10.1 Data Privacy

**Local-First Approach:**
- All session data stored locally
- No cloud sync in MVP
- No user accounts required

**Future (Cloud Sync):**
- End-to-end encryption for sessions
- Anonymous user IDs
- Opt-in analytics

---

### 10.2 Content Integrity

**Audio Verification:**
- Verify audio checksums (future)
- Use trusted sources only
- Validate ayah count against metadata

**API Security:**
- Use HTTPS for all requests
- Validate API responses
- Handle malformed data gracefully

---

## 11. Error Handling

### 11.1 Graceful Degradation

**Scenarios:**

| Error | Handling |
|-------|----------|
| Audio file not found | Skip to next ayah, log error |
| Network timeout | Use cached version, retry later |
| Invalid session | Show error, prevent playback |
| Low storage | Prompt to clear cache |
| Corrupt audio | Skip ayah, mark for re-download |

---

### 11.2 Logging Strategy

**Log Levels:**
- **Error**: Playback failures, data corruption
- **Warning**: Cache misses, slow performance
- **Info**: Session starts, completions
- **Debug**: Detailed playback events (dev only)

---

## 12. Testing Strategy

### 12.1 Unit Tests

**Critical Components:**
- SessionManager (queue building logic)
- RepeatHandler (count/duration logic)
- QueueBuilder (segment expansion)
- PlaybackController (state management)

**Coverage Target: 80%**

---

### 12.2 Integration Tests

**Test Scenarios:**
- Complete session playback
- Pause/resume functionality
- Segment transitions
- Repeat logic (both types)
- Cache hit/miss behavior

---

### 12.3 E2E Tests

**User Flows:**
1. Parent creates session → Child plays → Session completes
2. Create session with mixed repeat → Verify correct playback
3. Offline playback → No network errors
4. Switch reciter → Audio URLs update

---

## 13. Deployment Architecture

### 13.1 Release Strategy

**Platforms:**
- iOS (TestFlight → App Store)
- Android (Google Play Beta → Production)

**Versioning:**
- Semantic versioning (1.0.0)
- Over-the-air updates for JS changes (CodePush)

---

### 13.2 CI/CD Pipeline

```
Code Push → GitHub
    ↓
Run Tests (Jest + Detox)
    ↓
Build iOS/Android
    ↓
Deploy to TestFlight/Play Beta
    ↓
Manual QA
    ↓
Production Release
```

---

## 14. Future Architecture Enhancements

### 14.1 Cloud Sync (Post-MVP)

**Architecture:**
```
Mobile App ↔ GraphQL API ↔ Database (PostgreSQL)
                ↓
          Cloud Storage (Sessions)
```

---

### 14.2 Multi-Device Support

**Requirements:**
- Session sync across devices
- Progress sync
- Conflict resolution

---

### 14.3 Offline-First Sync

**Strategy:**
- Use CRDTs for conflict-free replication
- Background sync when online
- Optimistic UI updates

---

## 15. Appendix

### 15.1 Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| Ayah-level audio | Enables precise playback and repetition |
| React Native | Cross-platform with native performance |
| Offline-first | Works without internet dependency |
| No cloud in MVP | Reduces complexity, faster launch |
| Zustand state mgmt | Simpler than Redux, sufficient for MVP |

---

### 15.2 Open Technical Questions

- [ ] Which Quran API to use for metadata?
- [ ] Self-host audio or use CDN?
- [ ] SQLite vs Realm for local database?
- [ ] CodePush for JS updates?
- [ ] Analytics provider (Firebase vs Mixpanel)?

---

### 15.3 Performance Benchmarks

| Metric | Target | Measurement |
|--------|--------|-------------|
| App startup | < 2s | Time to interactive |
| Audio load time | < 500ms | First ayah play |
| Transition delay | < 100ms | Between ayahs |
| Queue build time | < 200ms | For 100-ayah session |
| Cache lookup | < 50ms | Audio availability check |

---

### 15.4 Glossary

- **AyahUnit**: Atomic playback unit (single ayah with metadata)
- **Segment**: User-configured portion of Quran (surah range + repeat config)
- **Queue**: Ordered list of AyahUnits ready for playback
- **Reciter**: Voice artist for Quran recitation
- **CDN**: Content Delivery Network for audio files

---

**Document Owner:** Engineering Team  
**Last Updated:** February 14, 2026  
**Next Review:** Post-Implementation Review
