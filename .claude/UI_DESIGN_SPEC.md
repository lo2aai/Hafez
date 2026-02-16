# UI/UX Design Specification

**Project:** HafezQuran
**Design System:** Islamic/Traditional Theme
**Implementation:** Claude frontend-design skill + Expo Theme System
**Last Updated:** 2026-02-16

---

## 🎨 Design Philosophy

### Core Principles
1. **Respectful & Culturally Appropriate:** Islamic aesthetics without distraction from Quran content
2. **Child-Friendly:** Large targets, high contrast, encouraging visuals
3. **Parent-Efficient:** Quick configuration, clear organization
4. **Minimal Distraction:** Clean layouts that prioritize content over decoration

### Target Aesthetics
- **Inspiration:** Traditional Islamic geometric art, mosque architecture, Arabic calligraphy
- **Colors:** Greens (Islamic tradition), golds (nobility), deep blues (serenity)
- **Patterns:** Subtle geometric tessellations, never overpowering
- **Typography:** Optimized for Arabic script readability

---

## 🌈 Color System

### Primary Palette (Islamic Traditional)

```typescript
// To be integrated into constants/theme.ts
export const IslamicColors = {
  light: {
    // Primary colors
    primary: '#1B7F4C',        // Deep Islamic green
    primarySoft: '#4CAF50',    // Lighter green for accents
    secondary: '#D4AF37',      // Gold for highlights
    accent: '#1A5F7A',         // Deep blue for calm focus

    // Background layers
    background: '#F5F5F0',     // Warm off-white (paper-like)
    surface: '#FFFFFF',        // Pure white for cards
    surfaceElevated: '#FAFAF7',

    // Text colors
    text: '#1C1C1C',           // Near-black for primary text
    textSecondary: '#5A5A5A',  // Gray for secondary text
    textMuted: '#8A8A8A',      // Light gray for hints

    // Semantic colors
    success: '#4CAF50',        // Completion/success
    warning: '#FF9800',        // Warnings
    error: '#E53935',          // Errors
    info: '#1A5F7A',           // Info messages

    // Kid Mode specific
    kidPrimary: '#FF6B6B',     // Playful coral for kid buttons
    kidSuccess: '#51CF66',     // Bright green for progress
    kidStar: '#FFD700',        // Gold stars for achievements
  },

  dark: {
    // Primary colors
    primary: '#4CAF50',        // Brighter green for dark mode
    primarySoft: '#66BB6A',
    secondary: '#FFD700',      // Brighter gold
    accent: '#4FC3F7',         // Lighter blue

    // Background layers
    background: '#121212',     // True dark
    surface: '#1E1E1E',        // Elevated dark
    surfaceElevated: '#2A2A2A',

    // Text colors
    text: '#E8E8E8',           // Off-white for readability
    textSecondary: '#B0B0B0',
    textMuted: '#707070',

    // Semantic colors (same as light)
    success: '#4CAF50',
    warning: '#FF9800',
    error: '#E53935',
    info: '#4FC3F7',

    // Kid Mode specific
    kidPrimary: '#FF8A8A',
    kidSuccess: '#66CF88',
    kidStar: '#FFD700',
  }
};
```

### Color Usage Guidelines
- **Primary green:** Main CTAs, active states, branding
- **Gold:** Highlights, achievements, premium features
- **Blue:** Calm focus areas (reading mode, meditation)
- **Kid Mode:** Brighter, more saturated versions for engagement

---

## 🔤 Typography System

### Font Families

```typescript
export const IslamicFonts = {
  // Arabic-optimized fonts
  arabic: {
    ios: 'GeezaPro',           // iOS system Arabic font
    android: 'Noto Naskh Arabic', // Google's excellent Arabic font
    web: 'Amiri, serif',       // Traditional Arabic calligraphic style
  },

  // Latin/English fonts
  latin: {
    ios: 'SF Pro',             // iOS system font
    android: 'Roboto',         // Android system font
    web: 'Inter, system-ui',   // Modern web font
  },

  // Display fonts (for headings, special text)
  display: {
    ios: 'SF Pro Display',
    android: 'Roboto',
    web: 'Poppins, sans-serif', // Friendly, modern
  },
};
```

### Type Scale

```typescript
export const TypeScale = {
  // Parent Mode (standard sizing)
  parent: {
    h1: 32,      // Screen titles
    h2: 24,      // Section headers
    h3: 20,      // Card titles
    body: 16,    // Standard text
    caption: 14, // Secondary text
    small: 12,   // Labels, hints
  },

  // Kid Mode (larger, more readable)
  kid: {
    h1: 48,      // Session title
    h2: 36,      // Current surah name
    h3: 28,      // Ayah number
    body: 24,    // Instructions
    caption: 20, // Secondary info
    small: 16,   // Minimal use
  },
};
```

### Arabic Text Rendering
- **Line height:** 1.8x (Arabic requires more vertical space)
- **Letter spacing:** 0 (Arabic doesn't use letter spacing)
- **Text alignment:** RTL (right-to-left) when Arabic content is primary
- **Font weight:** Regular (400) for body, SemiBold (600) for headings

---

## 🖼️ Splash Screen Design

### Specifications

**Duration:** 1.5-2 seconds (only as long as necessary for initial load)

**Layout:**
```
┌─────────────────────────┐
│                         │
│      [App Logo]         │  ← Islamic geometric icon
│                         │
│      HafezQuran         │  ← App name in elegant font
│   حافظ القرآن الكريم    │  ← Arabic subtitle
│                         │
│    [Subtle Pattern]     │  ← Geometric background
│                         │
└─────────────────────────┘
```

**Visual Elements:**
1. **Logo:** Geometric Islamic star/mandala pattern
   - Subtle fade-in animation (0.5s)
   - Color: Primary green with gold accent

2. **App Name:**
   - English: "HafezQuran"
   - Arabic: "حافظ القرآن الكريم"
   - Font: Display font, centered

3. **Background:**
   - Subtle geometric Islamic pattern (very light opacity: 5%)
   - Gradient: Warm off-white to light green tint

4. **Animation:**
   - Logo: Fade + gentle scale (0.95 → 1.0)
   - Text: Fade in after logo (0.3s delay)
   - Pattern: Static (no motion to avoid distraction)

5. **Transition:**
   - Crossfade to main app (0.3s)
   - No abrupt cuts

**Implementation:**
- Use Expo's `expo-splash-screen` module
- Static image + controlled fade-out (avoid over-engineering)
- Design asset to be created using Claude frontend-design skill

---

## 📱 Screen Layouts

### 1. Session List Screen (Parent Mode)

**Purpose:** Browse and select memorization sessions

**Layout:**
```
┌─────────────────────────┐
│ ← Sessions         [+]  │  ← Navigation + Create button
├─────────────────────────┤
│ 📚 My Sessions          │  ← Section header
│                         │
│ ┌─────────────────────┐ │
│ │ Al-Baqarah 1-5     │ │  ← Session card
│ │ 5 ayahs · 3×       │ │
│ │ █████░░░░░ 50%     │ │  ← Progress bar
│ └─────────────────────┘ │
│                         │
│ ┌─────────────────────┐ │
│ │ Juz Amma Review    │ │
│ │ 37 ayahs · 2×      │ │
│ │ ███░░░░░░░ 30%     │ │
│ └─────────────────────┘ │
└─────────────────────────┘
```

**Components:**
- **Header:** Title + Create button (gold accent)
- **Session Cards:**
  - White surface with subtle shadow
  - Green accent stripe on left
  - Title in h3 size
  - Metadata in caption size (gray)
  - Progress bar with primary green fill
- **Empty State:**
  - Islamic geometric illustration
  - "Create your first session" CTA

---

### 2. Session Builder Screen (Parent Mode)

**Purpose:** Create/edit memorization sessions

**Layout:**
```
┌─────────────────────────┐
│ ← New Session      [✓]  │  ← Back + Save
├─────────────────────────┤
│ Session Name            │
│ ┌─────────────────────┐ │
│ │ Al-Baqarah Review  │ │  ← Text input
│ └─────────────────────┘ │
│                         │
│ Segments                │
│ ┌─────────────────────┐ │
│ │ Al-Baqarah 1-5     │ │  ← Segment card
│ │ 5 ayahs · 3 reps   │ │
│ │           [Edit]    │ │
│ └─────────────────────┘ │
│ [+ Add Segment]         │  ← Gold button
│                         │
│ Settings                │
│ Pause between: [2s]     │
│ Auto-advance: [✓]       │
└─────────────────────────┘
```

**Components:**
- **Text Input:** Clean, bordered, focus state with green accent
- **Segment Cards:** Similar to session cards, swipeable to delete
- **Add Button:** Outlined button with gold text
- **Settings:** Toggle switches with green active state

---

### 3. Surah Picker (Parent Mode)

**Purpose:** Select surah and ayah range

**Layout:**
```
┌─────────────────────────┐
│ ← Select Surah          │
├─────────────────────────┤
│ [Search...]             │  ← Search bar
│                         │
│ ┌─────────────────────┐ │
│ │ 1. Al-Fatihah      │ │  ← List item
│ │    7 ayahs          │ │
│ └─────────────────────┘ │
│ ┌─────────────────────┐ │
│ │ 2. Al-Baqarah      │ │
│ │    286 ayahs        │ │
│ └─────────────────────┘ │
│                         │
│ [Selected: Al-Baqarah]  │  ← Bottom sheet
│ From: [1]  To: [5]      │  ← Ayah range
│ Quick: [Full] [Half]    │  ← Preset buttons
│ [Confirm]               │  ← Primary CTA
└─────────────────────────┘
```

**Components:**
- **Search Bar:** Rounded, light gray background
- **List Items:** Clean, separator lines, Arabic + transliteration
- **Bottom Sheet:** Slides up, contains range picker
- **Number Inputs:** Bordered, center-aligned
- **Quick Buttons:** Chip-style, outlined
- **Confirm Button:** Full-width, primary green

---

### 4. Kid Player Screen (Kid Mode)

**Purpose:** Distraction-free playback for children

**Layout:**
```
┌─────────────────────────┐
│                         │
│   Al-Baqarah Review     │  ← Session name (large)
│                         │
│        ┌─────┐          │
│        │  ▶  │          │  ← Giant play/pause button
│        └─────┘          │
│                         │
│     Ayah 3 of 15        │  ← Current position
│                         │
│   ★ ★ ★ ★ ★ ☆ ☆ ☆      │  ← Progress stars
│                         │
│   Rep 2 of 3            │  ← Repetition counter
│                         │
│ [<] [Speed] [⏹]        │  ← Minimal controls
└─────────────────────────┘
```

**Design Details:**
- **Background:** Subtle Islamic pattern (5% opacity)
- **Play Button:**
  - Huge circular button (120px diameter)
  - Bright coral color (#FF6B6B)
  - Drop shadow for depth
  - Haptic feedback on press
- **Progress Stars:**
  - 8 stars total (representing progress)
  - Filled stars in gold (#FFD700)
  - Empty stars in light gray
  - Subtle glow animation on new star fill
- **Text:**
  - All text very large (kid type scale)
  - High contrast (dark text on light bg)
  - Arabic + English if needed
- **Controls:**
  - Minimized to bottom
  - Icon-only buttons
  - Disabled/hidden if session is locked

---

### 5. Completion Screen (Kid Mode)

**Purpose:** Celebrate session completion

**Layout:**
```
┌─────────────────────────┐
│                         │
│    🎉 Amazing Work! 🎉  │  ← Celebration header
│                         │
│   ┌─────────────────┐   │
│   │                 │   │
│   │   [Trophy/Star  │   │  ← Animated illustration
│   │    Animation]   │   │
│   │                 │   │
│   └─────────────────┘   │
│                         │
│ You memorized 5 ayahs!  │  ← Achievement summary
│                         │
│  [Play Again] [Done]    │  ← Action buttons
└─────────────────────────┘
```

**Animation:**
- Particle confetti (brief, 2 seconds)
- Trophy/star bounces in
- Text fades in with slight bounce
- Sound effect (optional, can disable in settings)

**Colors:**
- Bright, celebratory palette
- Gold stars, green checkmarks
- Avoid overstimulation

---

## 🎭 Component Library

### Buttons

```typescript
// Button Variants
export const ButtonStyles = {
  primary: {
    background: IslamicColors.light.primary,
    text: '#FFFFFF',
    fontSize: TypeScale.parent.body,
    padding: '12px 24px',
    borderRadius: 8,
    shadow: 'medium',
  },

  secondary: {
    background: 'transparent',
    text: IslamicColors.light.primary,
    border: `2px solid ${IslamicColors.light.primary}`,
    fontSize: TypeScale.parent.body,
    padding: '12px 24px',
    borderRadius: 8,
  },

  kidPrimary: {
    background: IslamicColors.light.kidPrimary,
    text: '#FFFFFF',
    fontSize: TypeScale.kid.h3,
    padding: '20px 40px',
    borderRadius: 16,
    shadow: 'large',
  },
};
```

### Cards

```typescript
export const CardStyles = {
  sessionCard: {
    background: IslamicColors.light.surface,
    padding: 16,
    borderRadius: 12,
    shadow: 'small',
    borderLeft: `4px solid ${IslamicColors.light.primary}`,
  },

  segmentCard: {
    background: IslamicColors.light.surfaceElevated,
    padding: 12,
    borderRadius: 8,
    border: `1px solid ${IslamicColors.light.textMuted}20`, // 20% opacity
  },
};
```

### Progress Indicators

```typescript
export const ProgressStyles = {
  bar: {
    height: 8,
    borderRadius: 4,
    background: IslamicColors.light.textMuted + '20',
    fill: IslamicColors.light.primary,
    transition: 'width 0.3s ease',
  },

  stars: {
    size: 32, // Kid mode
    filled: IslamicColors.light.kidStar,
    empty: IslamicColors.light.textMuted + '30',
    glow: true, // Subtle glow animation
  },

  circle: {
    size: 120,
    strokeWidth: 8,
    track: IslamicColors.light.textMuted + '20',
    fill: IslamicColors.light.primary,
  },
};
```

---

## 🌙 Islamic Design Elements

### Geometric Patterns

**Usage:** Subtle backgrounds, decorative accents (never overpowering)

**Patterns to Use:**
1. **Tessellation:** Repeating geometric shapes (8-point star, hexagons)
2. **Arabesque:** Flowing plant-inspired patterns (minimal use)
3. **Calligraphy:** Bismillah, Mashallah (only where contextually appropriate)

**Implementation:**
- SVG patterns with 5-10% opacity
- Background-only (never interfere with text)
- Consistent across screens for cohesion

**Example SVG Pattern:**
```xml
<!-- Simple 8-point star pattern -->
<pattern id="islamic-star" patternUnits="userSpaceOnUse" width="100" height="100">
  <path d="M50,10 L60,40 L90,40 L65,60 L75,90 L50,70 L25,90 L35,60 L10,40 L40,40 Z"
        fill="none"
        stroke="#1B7F4C"
        stroke-width="1"
        opacity="0.05"/>
</pattern>
```

### Calligraphy

**Where to Use:**
- Splash screen (Bismillah)
- Completion screen (Mashallah, Alhamdulillah)
- Empty states (encouraging phrases)

**Guidelines:**
- High-quality vector assets (not pixel-based)
- Respectful placement (never as decoration alone)
- Arabic + transliteration/translation

---

## 🧩 Integration with Expo Theme

### Extending `constants/theme.ts`

The existing Expo project has a theme system in `constants/theme.ts`. We will **extend** it, not replace it.

**Strategy:**
1. Keep existing `Colors` object as fallback
2. Add `IslamicColors` as a new export
3. Create theme variants: `parentTheme` and `kidTheme`
4. Update `useThemeColor` hook to support both themes

**Updated Theme Structure:**
```typescript
// constants/theme.ts (extended)

export const Colors = {
  // Original Expo theme colors (keep as-is)
  light: { /* existing */ },
  dark: { /* existing */ },
};

export const IslamicColors = {
  // Islamic theme (defined above)
  light: { /* ... */ },
  dark: { /* ... */ },
};

export const Fonts = {
  // Extended font system
  latin: { /* existing */ },
  arabic: { /* new */ },
  display: { /* new */ },
};

// Theme variants
export const Themes = {
  parent: {
    colors: IslamicColors,
    fonts: Fonts.latin,
    spacing: 8, // 8px base unit
    borderRadius: 8,
  },

  kid: {
    colors: {
      ...IslamicColors,
      // Override with brighter kid-specific colors
      primary: IslamicColors.light.kidPrimary,
    },
    fonts: Fonts.display,
    spacing: 12, // Larger spacing for kids
    borderRadius: 12,
  },
};
```

### Usage in Components

```typescript
import { Themes } from '@/constants/theme';
import { useColorScheme } from '@/hooks/use-color-scheme';

export function SessionCard() {
  const colorScheme = useColorScheme();
  const theme = Themes.parent;
  const colors = theme.colors[colorScheme];

  return (
    <View style={{
      backgroundColor: colors.surface,
      borderLeftColor: colors.primary,
      borderRadius: theme.borderRadius,
    }}>
      {/* ... */}
    </View>
  );
}
```

---

## ✅ Design Checklist (Phase 0 Tasks)

Before starting Phase 1, use Claude frontend-design skill to create:

### Core Design Assets
- [ ] Complete color palette definition (integrate into `constants/theme.ts`)
- [ ] Typography system (fonts, scales, weights)
- [ ] Icon set (playback controls, navigation, decorative)
- [ ] Islamic pattern SVGs (3-5 variants for backgrounds)

### Splash Screen
- [ ] App logo design (vector SVG)
- [ ] Splash screen layout (with Bismillah)
- [ ] Animation timing definition
- [ ] Export assets for iOS/Android

### Component Designs
- [ ] Session card component (parent mode)
- [ ] Segment editor UI mockup
- [ ] Surah picker interface
- [ ] Kid player screen (complete layout)
- [ ] Progress visualization (stars, bars, circles)
- [ ] Completion celebration screen

### Deliverables Format
For each component, Claude frontend-design should provide:
1. **React Native code** (ready to copy into `components/` directory)
2. **Theme integration** (uses Expo theme system)
3. **Accessibility** (ARIA labels, screen reader support)
4. **Responsiveness** (works on various screen sizes)
5. **Islamic aesthetics** (respectful, culturally appropriate)

---

## 🎯 Design Validation Criteria

Before moving to Phase 1, validate:

### Visual Quality
- [ ] All screens have consistent color usage
- [ ] Islamic patterns are subtle (not overwhelming)
- [ ] Typography is readable (test with Arabic text)
- [ ] Buttons are large enough for touch (min 44x44px)

### Accessibility
- [ ] Color contrast meets WCAG AA standards (4.5:1 for text)
- [ ] All interactive elements have labels
- [ ] Text scales correctly with system font size
- [ ] Dark mode works correctly

### Cultural Appropriateness
- [ ] Islamic elements are respectful (verified by Muslim team members)
- [ ] Arabic text is grammatically correct
- [ ] No music or inappropriate sounds (use nature sounds or silence)

### Kid-Friendliness
- [ ] Large touch targets (80x80px minimum in kid mode)
- [ ] High contrast for visibility
- [ ] Encouraging, positive language
- [ ] No frustration-inducing UI patterns

---

## 📝 Next Steps

1. **Immediate (This Week):**
   - Use Claude frontend-design skill to generate initial designs
   - Create splash screen assets
   - Build reusable component library
   - Integrate with Expo theme system

2. **Phase 1 (Weeks 2-3):**
   - Implement session list screen
   - Implement session builder screen
   - Test with real content (Arabic text)

3. **Phase 2 (Weeks 4-5):**
   - Implement kid player screen
   - Add progress visualizations
   - Polish animations and transitions

4. **Ongoing:**
   - Collect user feedback (especially from parents and kids)
   - Iterate on designs based on usability testing
   - Refine Islamic aesthetic based on cultural feedback

---

## 🔗 Resources

### Design Inspiration
- Islamic Art Museum collections (geometric patterns)
- Mosque architecture (color schemes)
- Modern Quran apps (for UI patterns to avoid or improve upon)

### Technical References
- [Expo Theme Documentation](https://docs.expo.dev/develop/user-interface/color-themes/)
- [React Native Paper](https://callstack.github.io/react-native-paper/) (Material Design)
- [SF Symbols](https://developer.apple.com/sf-symbols/) (iOS icons)
- [Material Icons](https://fonts.google.com/icons) (Android icons)

### Fonts
- [Amiri Font](https://fonts.google.com/specimen/Amiri) (Arabic calligraphic)
- [Noto Naskh Arabic](https://fonts.google.com/noto/specimen/Noto+Naskh+Arabic) (Arabic sans-serif)
- [Inter](https://rsms.me/inter/) (Latin sans-serif)
- [Poppins](https://fonts.google.com/specimen/Poppins) (Display font)

---

**Document Status:** Draft (Ready for implementation)
**Owner:** Frontend Developer (Claude frontend-design skill)
**Review Required:** Product Owner + Muslim Community Member
