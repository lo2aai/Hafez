# Development Roadmap

## Quran Memorization Companion

**Version:** 1.0  
**Date:** February 14, 2026  
**Total Timeline:** ~8 weeks to MVP  
**Status:** Planning Phase

---

## Table of Contents

1. [Overview](#overview)
2. [Phased Development Plan](#phased-development-plan)
3. [Architecture Decisions](#architecture-decisions)
4. [Milestones & Deliverables](#milestones--deliverables)
5. [Resource Allocation](#resource-allocation)
6. [Risk Management](#risk-management)
7. [Success Metrics](#success-metrics)

---

## Overview

### Development Philosophy

**Iterative & Incremental Approach:**
- Each phase builds on the previous
- Working deliverables at each milestone
- Early validation of core concepts
- Gradual feature complexity increase

### Timeline Summary

| Phase | Duration | Focus Area | Status |
|-------|----------|------------|--------|
| Phase 0 | 1 week | Technical Foundation | Not Started |
| Phase 1 | 2 weeks | Core Engine MVP | Not Started |
| Phase 2 | 1.5 weeks | Session Builder UI | Not Started |
| Phase 3 | 1 week | Kid Mode Player | Not Started |
| Phase 4 | 1.5 weeks | Offline + Stability | Not Started |
| Phase 5 | Future | Pre-Public Preparation | Planned |

**Total MVP Timeline: ~7 weeks**

---

## Phased Development Plan

---

## 🔵 Phase 0 — Technical Foundation
**Duration:** 1 week  
**Priority:** Critical  
**Team Size:** 1-2 developers

### Objectives

Establish the technical groundwork for the entire application. Validate core audio playback capabilities and set up project infrastructure.

### Tasks

#### 1. Project Setup (Day 1-2)
- [ ] Initialize React Native project (latest stable version)
- [ ] Configure TypeScript with strict mode
- [ ] Set up ESLint + Prettier
- [ ] Configure folder structure (see Architecture doc)
- [ ] Set up Git repository and branching strategy
- [ ] Install core dependencies:
  - `react-native-track-player`
  - `react-navigation`
  - `zustand` or `redux-toolkit`
  - `react-native-sqlite-storage` or `MMKV`
- [ ] Configure iOS and Android build settings
- [ ] Set up environment variables (.env files)

**Folder Structure:**
```
src/
├── components/         # Reusable UI components
├── screens/           # Screen components
├── services/          # Business logic layer
│   ├── audio/        # Audio playback engine
│   ├── session/      # Session management
│   └── storage/      # Data persistence
├── models/           # TypeScript interfaces
├── utils/            # Helper functions
├── constants/        # App constants
├── navigation/       # Navigation configuration
└── store/            # State management
```

---

#### 2. Audio Engine Integration (Day 3-4)
- [ ] Install and configure `react-native-track-player`
- [ ] Create `AudioPlayer` service wrapper
- [ ] Implement basic play/pause functionality
- [ ] Configure background audio capabilities
- [ ] Set up audio event listeners (play, pause, complete, error)
- [ ] Test on both iOS and Android devices

**Deliverable Code:**
```typescript
// services/audio/AudioPlayer.ts
class AudioPlayer {
  async initialize(): Promise<void>;
  async loadAudio(url: string): Promise<void>;
  async play(): Promise<void>;
  async pause(): Promise<void>;
  onComplete(callback: () => void): void;
}
```

---

#### 3. Ayah Playback Prototype (Day 4-5)
- [ ] Create sample ayah audio file set (5-10 ayahs)
- [ ] Implement ayah loading logic
- [ ] Build simple test screen with play button
- [ ] Verify audio plays correctly
- [ ] Test playback completion detection
- [ ] Measure audio load time and optimize

**Test Scenario:**
```
User opens app → Taps "Test Play" → 
Al-Fatiha Ayah 1 plays → 
Automatically plays Ayah 2 → 
Logs completion event
```

---

#### 4. Local Storage Setup (Day 5-6)
- [ ] Choose storage solution (SQLite vs MMKV)
- [ ] Set up database schema (sessions, segments, settings)
- [ ] Implement CRUD operations for sessions
- [ ] Create storage service abstraction layer
- [ ] Test data persistence across app restarts
- [ ] Set up migration strategy for future schema changes

**Database Schema:**
```sql
-- Sessions Table
CREATE TABLE sessions (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  created_at INTEGER,
  updated_at INTEGER,
  is_template INTEGER DEFAULT 0
);

-- Segments Table
CREATE TABLE segments (
  id TEXT PRIMARY KEY,
  session_id TEXT,
  surah_id INTEGER,
  start_ayah INTEGER,
  end_ayah INTEGER,
  repeat_count INTEGER,
  repeat_duration INTEGER,
  order_index INTEGER,
  FOREIGN KEY (session_id) REFERENCES sessions(id)
);

-- Settings Table
CREATE TABLE settings (
  key TEXT PRIMARY KEY,
  value TEXT
);
```

---

#### 5. Base Folder Architecture (Day 6-7)
- [ ] Finalize folder structure
- [ ] Create README for each major directory
- [ ] Set up component documentation templates
- [ ] Configure path aliases (@components, @services, etc.)
- [ ] Create architectural decision records (ADRs)
- [ ] Document coding standards and conventions

---

### Phase 0 Deliverable

**✅ Working Prototype:**
- App loads successfully on iOS and Android
- Single ayah plays from local file
- Audio completes and triggers callback
- Basic session data saves to local storage
- Project structure is clean and documented

**Validation Criteria:**
- [ ] Audio plays without errors
- [ ] Audio playback completes successfully
- [ ] Data persists after app restart
- [ ] No memory leaks during playback
- [ ] Build time < 2 minutes

---

## 🟢 Phase 1 — Core Engine MVP
**Duration:** 2 weeks  
**Priority:** Critical  
**Team Size:** 2 developers

### Objectives

Build the intelligent playback engine that handles queue generation, repetition logic, and seamless ayah transitions. This is the brain of the application.

### Tasks

#### Week 1: Queue Building & Repetition

##### 1. Segment Model Implementation (Day 1-2)
- [ ] Define TypeScript interfaces for Segment, Session, AyahUnit
- [ ] Create Segment validator
- [ ] Implement segment CRUD operations
- [ ] Test segment creation and storage

**Segment Interface:**
```typescript
interface Segment {
  id: string;
  sessionId: string;
  surahId: number;
  startAyah: number;
  endAyah: number;
  repeatCount?: number;
  repeatDuration?: number; // in minutes
  orderIndex: number;
}

interface AyahUnit {
  id: string;
  surahId: number;
  ayahNumber: number;
  audioUrl: string;
  duration: number;
  segmentId: string;
  repeatIndex: number;
}
```

---

##### 2. Queue Builder (Day 3-5)
- [ ] Implement QueueBuilder service
- [ ] Build logic to expand segments into ayah units
- [ ] Handle partial surah ranges (e.g., ayah 1-5)
- [ ] Test queue generation for various segment configurations
- [ ] Optimize queue building performance

**Core Algorithm:**
```typescript
class QueueBuilder {
  build(segments: Segment[]): AyahUnit[] {
    const queue: AyahUnit[] = [];
    
    segments
      .sort((a, b) => a.orderIndex - b.orderIndex)
      .forEach(segment => {
        const ayahUnits = this.expandSegment(segment);
        queue.push(...ayahUnits);
      });
    
    return queue;
  }
  
  private expandSegment(segment: Segment): AyahUnit[] {
    const ayahs: AyahUnit[] = [];
    const repeatCount = segment.repeatCount || 1;
    
    for (let repeat = 1; repeat <= repeatCount; repeat++) {
      for (let ayah = segment.startAyah; ayah <= segment.endAyah; ayah++) {
        ayahs.push({
          id: generateId(),
          surahId: segment.surahId,
          ayahNumber: ayah,
          audioUrl: this.buildAudioUrl(segment.surahId, ayah),
          duration: 0, // Will be populated later
          segmentId: segment.id,
          repeatIndex: repeat
        });
      }
    }
    
    return ayahs;
  }
}
```

---

##### 3. Repeat by Count Logic (Day 5-6)
- [ ] Implement count-based repetition
- [ ] Track current repeat index
- [ ] Test repetition accuracy
- [ ] Handle edge cases (0 repeats, 100+ repeats)

**Test Cases:**
```
Segment: Al-Fatiha (1-7), repeat 3 times
Expected Queue: 21 ayah units (7 ayahs × 3 repeats)

Segment: Al-Ikhlas (full), repeat 5 times
Expected Queue: 20 ayah units (4 ayahs × 5 repeats)
```

---

##### 4. Repeat by Duration Logic (Day 7-8)
- [ ] Implement duration-based repetition
- [ ] Create timer service for tracking elapsed time
- [ ] Build dynamic queue extension logic
- [ ] Test duration accuracy (±5 seconds tolerance)

**Duration Logic:**
```typescript
class RepeatHandler {
  shouldContinueRepetition(
    segment: Segment,
    currentRepeat: number,
    elapsedTime: number
  ): boolean {
    // Count-based repetition
    if (segment.repeatCount !== null) {
      return currentRepeat < segment.repeatCount;
    }
    
    // Duration-based repetition
    if (segment.repeatDuration !== null) {
      const targetSeconds = segment.repeatDuration * 60;
      return elapsedTime < targetSeconds;
    }
    
    return false;
  }
}
```

---

#### Week 2: Playback Controller & Transitions

##### 5. Playback Controller (Day 9-11)
- [ ] Create PlaybackController service
- [ ] Implement queue orchestration logic
- [ ] Build state management for current ayah index
- [ ] Add pause/resume functionality
- [ ] Handle playback start/stop
- [ ] Implement skip forward/backward

**PlaybackController Interface:**
```typescript
class PlaybackController {
  private queue: AyahUnit[] = [];
  private currentIndex: number = 0;
  private state: 'idle' | 'playing' | 'paused' = 'idle';
  
  async startPlayback(session: Session): Promise<void>;
  async pause(): Promise<void>;
  async resume(): Promise<void>;
  async stop(): Promise<void>;
  async next(): Promise<void>;
  async previous(): Promise<void>;
  
  getProgress(): PlaybackProgress;
  getCurrentAyah(): AyahUnit | null;
}
```

---

##### 6. Ayah Transitions (Day 11-12)
- [ ] Implement automatic ayah progression
- [ ] Add configurable pause between ayahs
- [ ] Ensure gapless transitions (< 100ms)
- [ ] Handle transition errors gracefully
- [ ] Test rapid skip operations

**Transition Flow:**
```typescript
async handleAyahComplete(): Promise<void> {
  // Check if more ayahs in queue
  if (this.currentIndex + 1 < this.queue.length) {
    this.currentIndex++;
    
    // Apply pause between ayahs (if configured)
    const pauseDuration = this.settings.getPauseBetweenAyahs();
    if (pauseDuration > 0) {
      await this.delay(pauseDuration * 1000);
    }
    
    // Load and play next ayah
    const nextAyah = this.queue[this.currentIndex];
    await this.audioPlayer.loadAyah(nextAyah);
    await this.audioPlayer.play();
  } else {
    // Session complete
    this.onSessionComplete();
  }
}
```

---

##### 7. Integration Testing (Day 13-14)
- [ ] Create test sessions with mixed repeat types
- [ ] Test complete playback flows
- [ ] Verify repeat accuracy for both count and duration
- [ ] **Test Smart Stop behavior** (complete ayah vs complete segment)
- [ ] Test edge cases (single ayah, full Quran, rapid skips)
- [ ] Performance testing (queue build time, memory usage)

**Test Scenarios:**
```
1. Simple Session:
   - Al-Fatiha (1-7), repeat 2 times
   - Expected: 14 ayah units play sequentially

2. Mixed Repeat Session:
   - Al-Adiyat (1-11), repeat 3 times (count)
   - At-Takathur (1-8), repeat for 5 minutes (duration)
   - Expected: First segment deterministic, second dynamic

3. Complex Session:
   - 5 segments, mixed repeat types
   - Total estimated duration: 20 minutes
   - Expected: Smooth transitions, accurate timing

4. Smart Stop Test:
   - Session with 10-minute time limit
   - At 9:50, currently on Al-Adiyat ayah 8/11
   - Stop Behavior: Complete Ayah
   - Expected: Plays ayah 8 to completion even if exceeds 10 min
   
5. Smart Stop Test (Segment):
   - Session with 15-minute time limit
   - At 14:45, on segment 2 of 3
   - Stop Behavior: Complete Segment
   - Expected: Completes entire segment 2, then stops
```

---

### Phase 1 Deliverable

**✅ Working Engine:**
- Create session from JSON configuration
- Queue generates correctly for any segment combination
- Playback executes full session without manual intervention
- Both repeat types (count + duration) work accurately
- Ayah transitions are smooth and automatic

**Validation Criteria:**
- [ ] JSON session → automatic playback works
- [ ] Count-based repetition is accurate (±0 ayahs)
- [ ] Duration-based repetition is accurate (±5 seconds)
- [ ] No audio gaps between ayahs
- [ ] Queue builds in < 200ms for 100-ayah session

**Demo:**
```json
{
  "name": "Morning Revision",
  "segments": [
    {
      "surahId": 100,
      "startAyah": 1,
      "endAyah": 8,
      "repeatCount": 3
    },
    {
      "surahId": 101,
      "startAyah": 1,
      "endAyah": 11,
      "repeatDuration": 5
    }
  ]
}
```
→ Plays completely without intervention

---

## 🟡 Phase 2 — Session Builder UI
**Duration:** 1.5 weeks  
**Priority:** High  
**Team Size:** 1-2 developers (1 frontend, 1 backend support)

### Objectives

Enable parents to create, edit, and manage memorization sessions through an intuitive interface. This is the primary configuration tool.

### Tasks

#### 1. Session List Screen (Day 1-2)
- [ ] Design session card component
- [ ] Display all saved sessions
- [ ] Add "Create New Session" button
- [ ] Implement session deletion (with confirmation)
- [ ] Add search/filter functionality
- [ ] Show session metadata (duration, ayah count)

**UI Components:**
```typescript
<SessionListScreen>
  <SearchBar />
  <SessionCard
    name="Morning Revision"
    duration="15 minutes"
    ayahCount={42}
    lastPlayed="2 days ago"
    onPress={navigateToEditor}
    onDelete={confirmDelete}
  />
  <CreateButton onPress={createNewSession} />
</SessionListScreen>
```

---

#### 2. Session Builder Screen (Day 3-5)
- [ ] Create session editor layout
- [ ] Add session name input field
- [ ] Implement segment list with drag-to-reorder
- [ ] Add "Add Segment" button
- [ ] Display total estimated duration
- [ ] Save/cancel functionality
- [ ] Form validation

**Screen Layout:**
```
┌─────────────────────────────────────┐
│  Session Name: [Morning Revision  ] │
├─────────────────────────────────────┤
│  Segments (Drag to reorder)         │
│                                     │
│  [≡] Al-Adiyat (1-8) • 3x          │
│  [≡] At-Takathur (full) • 5 min    │
│  [≡] Al-Humazah (full) • 2x        │
│                                     │
│  [+ Add Segment]                    │
├─────────────────────────────────────┤
│  Total Duration: ~18 minutes        │
│  Total Ayahs: 64                    │
│                                     │
│  [Cancel]              [Save]       │
└─────────────────────────────────────┘
```

---

#### 3. Segment Editor Modal (Day 5-7)
- [ ] Create modal component
- [ ] Implement surah selection dropdown/picker
- [ ] Add ayah range selector (start/end)
- [ ] Build repeat configuration UI
  - [ ] Toggle between "By Count" and "By Duration"
  - [ ] Number input for count
  - [ ] Time picker for duration
- [ ] Validation rules (start ≤ end, valid ayah numbers)
- [ ] Preview segment summary

**Segment Editor UI:**
```
┌─────────────────────────────────────┐
│  Add Segment                    [×] │
├─────────────────────────────────────┤
│  Surah: [Al-Adiyat ▼]              │
│                                     │
│  Ayah Range:                        │
│  From: [1 ▼]  To: [8 ▼]            │
│                                     │
│  Repeat:                            │
│  ○ By Count  [3] times              │
│  ○ By Duration [5] minutes          │
│                                     │
│  Preview: Al-Adiyat (1-8), 3x       │
│           Estimated: ~2 minutes     │
│                                     │
│  [Cancel]              [Add]        │
└─────────────────────────────────────┘
```

---

#### 4. Surah Picker Component (Day 7-8)
- [ ] Build surah selection interface
- [ ] Display all 114 surahs with Arabic and English names
- [ ] Add search functionality
- [ ] Show ayah count for each surah
- [ ] Implement favorites/recent surahs
- [ ] Support both list and grid view

**Surah Data:**
```typescript
const surahs = [
  { id: 1, name: "Al-Fatiha", nameArabic: "الفاتحة", ayahCount: 7 },
  { id: 2, name: "Al-Baqarah", nameArabic: "البقرة", ayahCount: 286 },
  // ... 112 more
];
```

---

#### 5. Ayah Range Picker (Day 8-9)
- [ ] Create start/end ayah selector
- [ ] Dynamic ayah count based on selected surah
- [ ] Validation (start ≤ end, within bounds)
- [ ] Quick select options (Full Surah, First Half, Last Half)
- [ ] Visual ayah range preview

**Range Validation:**
```typescript
function validateAyahRange(
  surahId: number,
  start: number,
  end: number
): ValidationResult {
  const surah = getSurahById(surahId);
  
  if (start < 1 || start > surah.ayahCount) {
    return { valid: false, error: "Invalid start ayah" };
  }
  
  if (end < start || end > surah.ayahCount) {
    return { valid: false, error: "Invalid end ayah" };
  }
  
  return { valid: true };
}
```

---

#### 6. Repeat Settings UI (Day 9-10)
- [ ] Toggle between count and duration modes
- [ ] Number stepper for repeat count (1-100)
- [ ] Time picker for duration (1-60 minutes)
- [ ] Display estimated total time for segment
- [ ] Disable invalid combinations

---

#### 7. Save & Validation (Day 10-11)
- [ ] Implement save session logic
- [ ] Validate complete session before saving
- [ ] Show success/error messages
- [ ] Auto-save drafts (optional)
- [ ] Confirmation dialog for navigation away with unsaved changes

**Validation Rules:**
```typescript
function validateSession(session: Session): ValidationResult {
  if (!session.name || session.name.trim().length === 0) {
    return { valid: false, error: "Session name is required" };
  }
  
  if (session.segments.length === 0) {
    return { valid: false, error: "Add at least one segment" };
  }
  
  for (const segment of session.segments) {
    const result = validateSegment(segment);
    if (!result.valid) {
      return result;
    }
  }
  
  return { valid: true };
}
```

---

### Phase 2 Deliverable

**✅ Functional Session Builder:**
- Parent opens app → navigates to Session Builder
- Parent creates session with 3+ segments
- Parent configures mixed repeat types
- Parent saves session successfully
- Session appears in session list
- Parent can edit existing session

**Validation Criteria:**
- [ ] Session creation takes < 60 seconds
- [ ] All segment configurations are intuitive
- [ ] No crashes during session editing
- [ ] Validation provides clear error messages
- [ ] UI is responsive and polished

**User Flow Test:**
```
1. Open app
2. Tap "Create Session"
3. Enter name "Evening Revision"
4. Add segment: Al-Adiyat (1-8), repeat 3x
5. Add segment: At-Takathur (full), repeat 5 min
6. Save session
7. Verify session appears in list
8. Open session for editing
9. Modify first segment to repeat 5x
10. Save changes
```

---

## 🟣 Phase 3 — Kid Mode Player
**Duration:** 1 week  
**Priority:** High  
**Team Size:** 1 developer (frontend focus)

### Objectives

Create a child-friendly, distraction-free playback interface that requires minimal interaction. Focus on simplicity, visual feedback, and automatic operation.

### Tasks

#### 1. Player Screen Design (Day 1-2)
- [ ] Design minimal, clean UI
- [ ] Large, touch-friendly buttons
- [ ] High contrast colors
- [ ] Islamic-themed visuals (optional: calligraphy, stars)
- [ ] Ensure accessibility for children ages 5-15

**Screen Layout:**
```
┌─────────────────────────────────────┐
│                                     │
│     🌙 Morning Revision 🌙          │
│                                     │
│         ┌───────────────┐           │
│         │               │           │
│         │   Al-Adiyat   │           │
│         │   Ayah 3/8    │           │
│         │               │           │
│         └───────────────┘           │
│                                     │
│    ════════════════════════         │
│         Progress 25%                │
│                                     │
│         ┌─────────┐                 │
│         │  PLAY   │  (Big button)   │
│         │   ▶️    │                 │
│         └─────────┘                 │
│                                     │
│       ⏮  ⏸  ⏭                       │
│    (Previous) (Pause) (Next)        │
│                                     │
│     ⭐⭐⭐ Keep Going! ⭐⭐⭐           │
│                                     │
└─────────────────────────────────────┘
```

---

#### 2. Big Play Button (Day 2-3)
- [ ] Create large, prominent play button
- [ ] Animate button press
- [ ] Change state to pause when playing
- [ ] Add haptic feedback (vibration on press)
- [ ] Ensure button is always visible

**Component:**
```typescript
<PlayButton
  size="large"
  isPlaying={playbackState.isPlaying}
  onPress={togglePlayback}
  accessibilityLabel="Play session"
/>
```

---

#### 3. Minimal Controls (Day 3-4)
- [ ] Previous ayah button
- [ ] Next ayah button
- [ ] Pause button
- [ ] Optional: Speed control (0.75x, 1x, 1.25x)
- [ ] Optional: Volume slider
- [ ] Hide advanced controls by default

---

#### 4. Progress Visualization (Day 4-5)
- [ ] Linear progress bar
- [ ] Current ayah / Total ayahs display
- [ ] Current segment display
- [ ] Time remaining (optional)
- [ ] Animate progress updates smoothly

**Progress Component:**
```typescript
<ProgressIndicator
  currentAyah={15}
  totalAyahs={64}
  currentSegment="Al-Adiyat (3/3)"
  percentage={23}
/>
```

---

#### 5. Auto-Next Functionality (Day 5-6)
- [ ] Automatic progression to next ayah
- [ ] Seamless segment transitions
- [ ] Session completion screen
- [ ] Celebration animation on completion
- [ ] Option to replay session

**Auto-Next Logic:**
```typescript
useEffect(() => {
  const unsubscribe = playbackController.onAyahComplete(() => {
    // Automatically play next ayah
    playbackController.next();
  });
  
  return unsubscribe;
}, []);
```

---

#### 6. Encouraging Visuals (Day 6-7)
- [ ] Stars/badges during playback
- [ ] Completion celebration screen
- [ ] Progress milestones (25%, 50%, 75%, 100%)
- [ ] Positive reinforcement messages
- [ ] Islamic patterns/calligraphy background (subtle)

**Messages:**
```typescript
const encouragementMessages = [
  "Keep going! 🌟",
  "You're doing great! ✨",
  "Almost there! 💪",
  "Excellent work! 🎉",
  "Masha'Allah! 🌙"
];
```

---

#### 7. Session Completion (Day 7)
- [ ] Completion screen with celebration
- [ ] Display session stats (duration, ayahs completed)
- [ ] Options: Replay, Back to Home, Share Progress
- [ ] Confetti or star animation
- [ ] Encouraging message

**Completion Screen:**
```
┌─────────────────────────────────────┐
│                                     │
│         🎉 Great Job! 🎉            │
│                                     │
│     You completed:                  │
│     Morning Revision                │
│                                     │
│     📖 64 ayahs                     │
│     ⏱ 18 minutes                    │
│                                     │
│     Masha'Allah! Keep it up! 🌟     │
│                                     │
│     [Replay]    [Home]              │
│                                     │
└─────────────────────────────────────┘
```

---

### Phase 3 Deliverable

**✅ Kid-Friendly Player:**
- Child opens app → sees session list
- Child taps on session → player opens
- Child taps big play button → session starts
- Session plays automatically to completion
- Child sees celebration screen
- Child can replay or go home

**Validation Criteria:**
- [ ] One-tap to start playback
- [ ] Zero manual intervention during session
- [ ] Visual feedback is clear and encouraging
- [ ] Buttons are large and easy to tap
- [ ] No confusing options or menus

**User Flow Test:**
```
Child (8 years old):
1. Opens app
2. Sees "Morning Revision" card
3. Taps card
4. Taps big green "PLAY" button
5. Listens to entire session
6. Sees "Great Job!" screen
7. Taps "Home"
```

---

## 🟠 Phase 4 — Offline + Stability
**Duration:** 1.5 weeks  
**Priority:** High  
**Team Size:** 1-2 developers

### Objectives

Ensure the app works reliably offline, recovers from crashes, and provides a stable user experience. Focus on robustness and data integrity.

### Tasks

#### 1. Audio Caching Strategy (Day 1-3)
- [ ] Implement audio file caching system
- [ ] Download and cache ayahs on session creation
- [ ] LRU cache eviction policy
- [ ] Cache size limit (500MB default)
- [ ] Cache management UI (view size, clear cache)
- [ ] Offline indicator in UI

**Caching Logic:**
```typescript
class AudioCacheManager {
  async cacheSession(session: Session): Promise<void> {
    const ayahs = this.buildAyahList(session);
    
    for (const ayah of ayahs) {
      if (!this.isCached(ayah)) {
        await this.downloadAndCache(ayah);
      }
    }
  }
  
  async downloadAndCache(ayah: AyahUnit): Promise<void> {
    const response = await fetch(ayah.audioUrl);
    const blob = await response.blob();
    await this.storage.save(this.getCacheKey(ayah), blob);
  }
  
  isCached(ayah: AyahUnit): boolean {
    return this.storage.exists(this.getCacheKey(ayah));
  }
}
```

---

#### 2. Background Playback (Day 3-5)
- [ ] Configure background audio on iOS
- [ ] Configure background audio on Android
- [ ] Lock screen controls (play/pause, skip)
- [ ] Now Playing info display
- [ ] Handle interruptions (calls, notifications)
- [ ] Resume playback after interruption

**iOS Configuration (Info.plist):**
```xml
<key>UIBackgroundModes</key>
<array>
  <string>audio</string>
</array>
```

**Android Configuration (AndroidManifest.xml):**
```xml
<service
  android:name="com.doublesymmetry.trackplayer.service.MusicService"
  android:enabled="true"
  android:exported="false"
  android:foregroundServiceType="mediaPlayback" />
```

---

#### 3. Crash Recovery (Day 5-7)
- [ ] Implement state persistence on pause/minimize
- [ ] Save playback position every 5 seconds
- [ ] Restore playback state on app restart
- [ ] Handle corrupted state gracefully
- [ ] Error boundary components
- [ ] Crash reporting integration (optional: Sentry)

**State Persistence:**
```typescript
class PlaybackStatePersistence {
  async saveState(state: PlaybackState): Promise<void> {
    await this.storage.set('playback_state', JSON.stringify(state));
  }
  
  async loadState(): Promise<PlaybackState | null> {
    const json = await this.storage.get('playback_state');
    if (!json) return null;
    
    try {
      return JSON.parse(json);
    } catch (error) {
      console.error('Corrupted playback state:', error);
      return null;
    }
  }
}
```

---

#### 4. Resume Session Functionality (Day 7-8)
- [ ] Detect incomplete sessions on app start
- [ ] Show "Resume Session" prompt
- [ ] Restore exact playback position
- [ ] Handle edge cases (deleted session, missing audio)
- [ ] Clear completed sessions

**Resume Flow:**
```
App Start → Check for saved playback state
    ↓
State Found → Show Resume Dialog
    ↓
User Confirms → Restore session
    ↓
Load exact ayah → Resume playback
```

---

#### 5. Error Handling & Retry Logic (Day 8-9)
- [ ] Handle missing audio files
- [ ] Retry failed downloads (3 attempts)
- [ ] Skip corrupted ayahs gracefully
- [ ] Network error handling
- [ ] User-friendly error messages
- [ ] Fallback to cached audio

**Error Handling:**
```typescript
async playAyah(ayah: AyahUnit): Promise<void> {
  let attempts = 0;
  const maxAttempts = 3;
  
  while (attempts < maxAttempts) {
    try {
      await this.audioPlayer.loadAyah(ayah);
      await this.audioPlayer.play();
      return; // Success
    } catch (error) {
      attempts++;
      
      if (attempts >= maxAttempts) {
        console.error(`Failed to play ayah after ${maxAttempts} attempts`);
        this.skipToNext(); // Skip problematic ayah
      } else {
        await this.delay(1000 * attempts); // Exponential backoff
      }
    }
  }
}
```

---

#### 6. Offline Mode Testing (Day 9-10)
- [ ] Test complete workflow offline
- [ ] Verify cached sessions play without network
- [ ] Test cache size limits
- [ ] Test cache eviction
- [ ] Test resume after network loss
- [ ] Performance testing

---

#### 7. Stability & Performance Optimization (Day 10-11)
- [ ] Memory leak detection and fixes
- [ ] Optimize render performance
- [ ] Reduce battery consumption
- [ ] Optimize database queries
- [ ] Lazy load heavy components
- [ ] Profile app performance

**Performance Benchmarks:**
| Metric | Target |
|--------|--------|
| App startup time | < 2s |
| Session load time | < 500ms |
| Audio load time | < 300ms |
| Memory usage | < 150MB |
| Battery drain | < 5% per hour of playback |

---

### Phase 4 Deliverable

**✅ Robust Offline App:**
- Complete session plays offline after initial cache
- App recovers from crashes without data loss
- Background playback works reliably
- User can resume interrupted sessions
- Error handling prevents app crashes

**Validation Criteria:**
- [ ] Session plays 100% offline
- [ ] App restarts successfully after crash
- [ ] Background playback works on lock screen
- [ ] Resume functionality works accurately
- [ ] No data loss during interruptions

**Stress Tests:**
```
1. Airplane Mode Test:
   - Cache session while online
   - Enable airplane mode
   - Play session completely
   - Expected: No errors, smooth playback

2. Crash Recovery Test:
   - Start session playback
   - Force quit app mid-session
   - Reopen app
   - Expected: Resume dialog appears
   - Resume → picks up at exact position

3. Interruption Test:
   - Start playback
   - Receive phone call
   - End call
   - Expected: Playback resumes automatically

4. Low Battery Test:
   - Play 1-hour session on low battery
   - Expected: No excessive drain (< 10%)
```

---

## 🔴 Phase 5 — Pre-Public Preparation
**Duration:** Future (Post-MVP)  
**Priority:** Low (for initial release)  
**Team Size:** 2-3 developers

### Objectives

Prepare the app for public release with additional features that enhance user experience and enable growth. These are not required for MVP but are important for long-term success.

---

### Future Features

#### 1. Multi-Reciter Support
- [ ] Reciter selection UI
- [ ] Download multiple reciter audio sets
- [ ] Reciter-specific settings
- [ ] Preview reciter before downloading
- [ ] Manage reciter downloads

**Reciters to Support:**
- Abdul Basit Abdul Samad
- Mishary Rashid Alafasy
- Saad Al-Ghamdi
- Maher Al-Muaiqly
- Muhammad Siddiq Al-Minshawi

---

#### 2. Analytics Integration
- [ ] Usage tracking (sessions played, completion rate)
- [ ] Performance monitoring
- [ ] Error tracking and reporting
- [ ] User behavior insights
- [ ] Privacy-compliant analytics

**Key Metrics:**
- Daily active users
- Average session duration
- Completion rate
- Most popular surahs
- Crash-free rate

---

#### 3. User Accounts & Cloud Sync
- [ ] User authentication (email, Google, Apple)
- [ ] Cloud storage for sessions
- [ ] Multi-device sync
- [ ] Conflict resolution
- [ ] Offline-first with eventual sync

**Sync Architecture:**
```
Device A ↔ Cloud Database ↔ Device B
```

---

#### 4. Parental Dashboard
- [ ] Progress tracking per child
- [ ] Completion history
- [ ] Time spent listening
- [ ] Ayah memorization tracking
- [ ] Weekly/monthly reports
- [ ] Set goals and reminders

---

#### 5. Advanced Features
- [ ] Tajweed highlighting (future)
- [ ] Translation overlay
- [ ] Recitation speed control
- [ ] Smart scheduling (spaced repetition)
- [ ] Gamification (badges, streaks)
- [ ] Social sharing

---

#### 6. Localization
- [ ] Support multiple languages
  - Arabic (primary)
  - English
  - Urdu
  - French
  - Turkish
- [ ] RTL support for Arabic
- [ ] Culturally appropriate UI

---

#### 7. App Store Preparation
- [ ] Privacy policy
- [ ] Terms of service
- [ ] App screenshots and preview video
- [ ] App description (App Store / Play Store)
- [ ] Keyword optimization
- [ ] Beta testing program
- [ ] App Store Optimization (ASO)

---

## Architecture Decisions

### 🧠 Critical Design Principles

---

### ✅ 1. Build Queue from Ayahs, Not Segments

**Decision:**
Segments are **configuration** (user input), but ayahs are **runtime playback units**.

**Why:**
```
Segment: Al-Fatiha (1-7), repeat 3x
    ↓ Expand at build time
Queue: [
  Ayah(1,1), Ayah(1,2), ..., Ayah(1,7),  // Repeat 1
  Ayah(1,1), Ayah(1,2), ..., Ayah(1,7),  // Repeat 2
  Ayah(1,1), Ayah(1,2), ..., Ayah(1,7)   // Repeat 3
]
```

**Benefits:**
- ✅ Simplifies playback logic (just iterate queue)
- ✅ Enables precise progress tracking
- ✅ Easy pause/resume at any ayah
- ✅ Deterministic behavior

**Alternative (Rejected):**
```
❌ Keep segments and repeat at runtime
Problems:
- Complex state management
- Difficult to track progress
- Hard to resume at exact position
```

---

### ✅ 2. Separate Parent Mode & Kid Mode

**Decision:**
Create two distinct navigation stacks internally, not just different screens.

**Implementation:**
```typescript
// Parent Stack
<ParentNavigator>
  <SessionListScreen />
  <SessionBuilderScreen />
  <SettingsScreen />
  <ParentalDashboard />
</ParentNavigator>

// Kid Stack
<KidNavigator>
  <SessionSelectionScreen />
  <PlayerScreen />
  <CompletionScreen />
</KidNavigator>

// Root Navigator
<RootNavigator>
  {mode === 'parent' ? <ParentNavigator /> : <KidNavigator />}
</RootNavigator>
```

**Why:**
- ✅ Prevents accidental navigation to parent features
- ✅ Simpler mental model for children
- ✅ Different themes/styling per mode
- ✅ Easy to add parental lock (future)

**Theme Differences:**
| Aspect | Parent Mode | Kid Mode |
|--------|-------------|----------|
| Colors | Professional, muted | Bright, colorful |
| Font Size | Standard | Large, readable |
| Buttons | Normal size | Extra large |
| Complexity | Full features | Minimal options |

---

### ✅ 3. No YouTube Integration

**Decision:**
Use **only raw audio files**, never YouTube or video sources.

**Why:**
- ✅ No video distractions
- ✅ Faster loading (smaller files)
- ✅ Offline-first capability
- ✅ Precise ayah-level control
- ✅ No YouTube API dependencies
- ✅ Better battery life

**Audio Sources:**
- EveryAyah.com (ayah-level MP3s)
- Quran.com API
- Tanzil.net
- Custom CDN

**Rejected Alternative:**
```
❌ Use YouTube videos with embedded recitations
Problems:
- Requires internet
- Video playback drains battery
- Cannot extract ayah ranges
- Ads and distractions
- API rate limits
```

---

### ✅ 4. Stateless Playback Engine

**Decision:**
Playback engine should be a **pure function** with no internal state.

**Input:**
```typescript
PlaybackQueue = AyahUnit[]
```

**Output:**
```typescript
Events = {
  onAyahStart(ayah: AyahUnit)
  onAyahComplete(ayah: AyahUnit)
  onSegmentComplete(segmentId: string)
  onSessionComplete()
  onError(error: Error)
}
```

**Why:**
- ✅ Easier to test (no mocking needed)
- ✅ Predictable behavior
- ✅ Simple debugging
- ✅ No hidden state bugs

**State Management:**
All state lives in **PlaybackController**, not in the audio engine itself.

```typescript
// Good: Stateless engine
class AudioEngine {
  async play(ayah: AyahUnit): Promise<void> {
    // Just play, no state tracking
  }
}

// Good: Stateful controller
class PlaybackController {
  private queue: AyahUnit[];
  private currentIndex: number;
  
  async next(): Promise<void> {
    this.currentIndex++;
    await this.engine.play(this.queue[this.currentIndex]);
  }
}
```

**Rejected Alternative:**
```
❌ Engine manages its own queue and state
Problems:
- Hard to test
- Coupled components
- Difficult to pause/resume
- State synchronization bugs
```

---

### Additional Principles

#### 5. Offline-First Architecture
All core features must work without internet connection.

#### 6. Ayah-Level Granularity
Never use surah-level audio; always ayah-level for flexibility.

#### 7. Parent Controls Everything
Children should have zero configuration options (simplicity).

#### 8. Data Integrity First
Always validate before saving; prevent corrupted sessions.

#### 9. Performance Budget
- App startup: < 2s
- Audio load: < 500ms
- UI response: < 100ms

---

## Milestones & Deliverables

### Milestone Summary

| Phase | Duration | Key Deliverable | Success Metric |
|-------|----------|-----------------|----------------|
| Phase 0 | 1 week | Single ayah plays offline | Audio playback works |
| Phase 1 | 2 weeks | JSON session → full playback | Engine handles any session |
| Phase 2 | 1.5 weeks | Parent can create sessions | Session builder functional |
| Phase 3 | 1 week | Child runs session alone | One-tap playback |
| Phase 4 | 1.5 weeks | Offline + crash recovery | App is stable |
| **MVP TOTAL** | **~7 weeks** | **Fully functional app** | **Ready for beta testing** |
| Phase 5 | Future | Public release features | App store ready |

---

### Weekly Check-ins

**Every Friday:**
- Demo current progress
- Review completed tasks
- Identify blockers
- Plan next week's work
- Update roadmap if needed

---

## Resource Allocation

### Team Structure

#### MVP Team (Phases 0-4)
- **1 Senior Developer**: Architecture, core engine, playback logic
- **1 Frontend Developer**: UI/UX, session builder, kid mode
- **1 QA Engineer** (part-time): Testing, bug reporting
- **1 Product Manager** (part-time): Requirements, prioritization

#### Post-MVP Team (Phase 5)
- Add 1 backend developer for cloud sync
- Add 1 designer for polish and branding
- Add 1 DevOps engineer for CI/CD

---

### Technology Requirements

**Development:**
- MacBook for iOS development
- Android device for testing
- iOS device for testing
- Figma for design (optional)

**Services:**
- GitHub for version control
- Netlify/Vercel for documentation hosting
- TestFlight for iOS beta
- Google Play Beta for Android
- Firebase (optional for analytics)

---

## Risk Management

### High-Priority Risks

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Audio API unavailable | Medium | High | Cache audio locally, use multiple sources |
| Ayah boundary inaccuracy | Medium | High | Validate against multiple sources, manual QA |
| Performance issues on old devices | Medium | Medium | Performance profiling, optimize early |
| Complex parent UI confuses users | Medium | Medium | User testing, iterative design |
| Background playback bugs | Low | High | Extensive platform testing |

---

### Technical Debt Management

**Acceptable in MVP:**
- Hardcoded reciter (only 1 supported)
- No cloud sync
- Basic UI without animations

**NOT Acceptable:**
- Skipping audio caching (critical for offline)
- Ignoring crash recovery (bad UX)
- Poor performance (< 2s startup required)

---

## Success Metrics

### Development Metrics

**Phase 0-1:**
- Audio playback success rate: 100%
- Queue build time: < 200ms for 100 ayahs
- Test coverage: > 70%

**Phase 2-3:**
- Session creation time: < 60 seconds
- UI responsiveness: < 100ms
- Zero crashes in manual testing

**Phase 4:**
- Offline playback success: 100%
- Crash recovery success: 100%
- Background playback stability: 100%

---

### User Metrics (Post-Launch)

**Engagement:**
- Daily active users (DAU)
- Average sessions per day
- Session completion rate > 70%

**Retention:**
- Day 1 retention > 60%
- Day 7 retention > 40%
- Day 30 retention > 25%

**Performance:**
- App load time < 2s
- Crash-free rate > 99.5%
- Audio playback success > 99%

---

## Appendix

### Development Best Practices

1. **Commit Often**: Small, focused commits
2. **Write Tests**: Unit tests for critical logic
3. **Code Reviews**: All PRs require review
4. **Documentation**: Update docs with code changes
5. **Performance**: Profile regularly, optimize early

---

### Testing Checklist

**Per Phase:**
- [ ] Unit tests for new functions
- [ ] Integration tests for workflows
- [ ] Manual testing on iOS and Android
- [ ] Performance benchmarking
- [ ] Accessibility testing

**Pre-Release:**
- [ ] Full regression testing
- [ ] Beta user testing
- [ ] Load testing
- [ ] Security audit

---

### Version Control Strategy

**Branches:**
- `main`: Production-ready code
- `develop`: Integration branch
- `feature/*`: Feature branches
- `bugfix/*`: Bug fixes
- `hotfix/*`: Emergency fixes

**Commit Message Format:**
```
[Phase X] Brief description

Detailed explanation of changes.

- List of changes
- Related to #issue-number
```

---

### Release Cadence

**MVP (Phases 0-4):**
- Weekly internal builds
- Bi-weekly beta releases

**Post-MVP (Phase 5):**
- Monthly feature releases
- Bi-weekly patch releases

---

**Document Owner:** Engineering Team  
**Last Updated:** February 14, 2026  
**Next Review:** End of Phase 1
