# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

HafezQuran is an Expo/React Native mobile application built with TypeScript and React 19. The app uses Expo Router for file-based navigation and supports iOS, Android, and web platforms.

## Development Commands

### Starting the app
```bash
npm install              # Install dependencies
npx expo start           # Start the development server
npm run ios              # Run on iOS simulator
npm run android          # Run on Android emulator
npm run web              # Run on web browser
```

### Code quality
```bash
npm run lint             # Run ESLint
```

### Project reset
```bash
npm run reset-project    # Moves current app/ to app-example/ and creates blank app/
```

## Architecture & File Structure

### Routing System
This project uses **Expo Router** with file-based routing:
- `app/_layout.tsx` - Root layout with navigation theme provider and stack configuration
- `app/(tabs)/` - Tab-based navigation group (parentheses denote layout routes)
- `app/(tabs)/_layout.tsx` - Tab bar configuration
- `app/(tabs)/index.tsx` - Home screen (first tab)
- `app/(tabs)/explore.tsx` - Explore screen (second tab)
- `app/modal.tsx` - Modal screen example

Route files export React components. Layout files configure navigation structure. The `unstable_settings` export in `_layout.tsx` defines the anchor route for navigation.

### Component Organization

**Core Components** (`components/`):
- `themed-text.tsx` - Text component with theme support and type variants (title, subtitle, link, etc.)
- `themed-view.tsx` - View component with automatic background color theming
- `parallax-scroll-view.tsx` - Scroll view with parallax header effect
- `haptic-tab.tsx` - Tab button wrapper with haptic feedback
- `external-link.tsx` - External link handler
- `hello-wave.tsx` - Animated wave component

**UI Components** (`components/ui/`):
- `icon-symbol.tsx` - Cross-platform icon component (Material Icons for Android/web)
- `icon-symbol.ios.tsx` - iOS-specific icon using SF Symbols via expo-symbols
- `collapsible.tsx` - Collapsible/expandable component

Platform-specific files use `.ios.tsx` or `.web.tsx` extensions. The bundler automatically selects the correct file per platform.

### Theming System

**Theme Configuration** (`constants/theme.ts`):
- `Colors` object defines light/dark color schemes
- `Fonts` object provides platform-specific font families (iOS system fonts, web fonts)
- Theme is accessed via the `useThemeColor` hook

**Theme Hooks** (`hooks/`):
- `use-color-scheme.ts` - Returns current color scheme ('light' or 'dark')
- `use-color-scheme.web.ts` - Web-specific color scheme detection
- `use-theme-color.ts` - Returns themed color value with fallback support

All themed components accept `lightColor` and `darkColor` props to override default theme colors.

### TypeScript Configuration

The project uses strict TypeScript with path aliases:
- `@/*` maps to repository root for cleaner imports
- Example: `@/components/themed-text` instead of `../../components/themed-text`
- Configured in `tsconfig.json` with `"strict": true`

### Key Dependencies

- **expo-router** (v6.x) - File-based routing with typed routes
- **react-native-reanimated** - Animations and gestures
- **expo-symbols** - SF Symbols support on iOS
- **@expo/vector-icons** - Material Icons and other icon sets
- **react-native-gesture-handler** - Gesture recognition

## Important Configuration

### App Configuration (`app.json`)
- App name: "HafezQuran"
- New Architecture enabled (`newArchEnabled: true`)
- Typed routes enabled (`experiments.typedRoutes: true`)
- React Compiler enabled (`experiments.reactCompiler: true`)
- Custom URL scheme: `hafezquran://`
- Edge-to-edge display on Android

### ESLint
Uses `eslint-config-expo` with flat config format. Ignores `dist/` directory.

## UI/UX Development Workflow

### CRITICAL RULE: Always Use frontend-design Skill for UI Work

**When creating ANY UI component, screen, or visual element, you MUST use the `frontend-design` skill.**

This applies to:
- ✅ New screens (session list, player screen, settings, etc.)
- ✅ New components (buttons, cards, progress bars, modals)
- ✅ Visual elements (splash screen, icons, animations)
- ✅ Layouts and navigation structures
- ✅ Styling updates or visual redesigns

**How to use the skill:**
1. Reference `.claude/UI_DESIGN_SPEC.md` for design system guidelines
2. Invoke the skill: Use the `Skill` tool with `skill: "frontend-design"`
3. Provide context from UI_DESIGN_SPEC.md (Islamic theme, colors, typography)
4. The skill will generate production-ready React Native code with:
   - Proper Expo theme integration (`constants/theme.ts`)
   - Accessibility support (ARIA labels, screen readers)
   - Responsive design (works on all screen sizes)
   - Islamic aesthetic (culturally appropriate, respectful)

**Example:**
```
User asks: "Create the session list screen"
Your response: Use the frontend-design skill with the Islamic design system
specs to create the SessionListScreen component
```

**DO NOT:**
- ❌ Write UI code manually without using the skill
- ❌ Create generic-looking components (use Islamic aesthetic)
- ❌ Skip accessibility features
- ❌ Ignore the design system in UI_DESIGN_SPEC.md

**Design System Reference:**
- Colors: Islamic greens (#1B7F4C), golds (#D4AF37), blues (#1A5F7A)
- Typography: Arabic-optimized (Amiri, Noto Naskh Arabic, Geeza Pro)
- Patterns: Geometric Islamic designs (subtle, 5% opacity)
- Kid Mode: Larger sizes, brighter colors, playful elements

---

## Development Notes

### Adding New Icons
When adding icons to `IconSymbol`:
1. iOS uses SF Symbols (check SF Symbols app for names)
2. Android/web uses Material Icons (check icons.expo.fyi)
3. Map SF Symbol names to Material Icon names in `MAPPING` object in `icon-symbol.tsx`

### Adding New Screens
1. Create new file in `app/` directory
2. File name becomes the route (e.g., `app/settings.tsx` → `/settings`)
3. For grouped routes, use parentheses: `app/(tabs)/new-tab.tsx`
4. For dynamic routes, use brackets: `app/user/[id].tsx`

### Theme Customization
Modify `constants/theme.ts` to change app-wide colors. The theme automatically switches based on system color scheme unless overridden in themed components with `lightColor`/`darkColor` props.

---

## Project Documentation

This project has comprehensive documentation in the `.claude/` directory. **Always consult these files before starting work:**

### Core Documentation
- **`.claude/PRD.md`** - Product Requirements Document
  - Product vision and user stories
  - Core features and user flows
  - Success criteria and constraints

- **`.claude/ARCHITECTURE.md`** - System Architecture Document
  - Layer breakdown (UI, Business Logic, Data, Services)
  - Component design and data flow
  - Technology stack and design decisions

- **`.claude/TASK_LIST.md`** - Comprehensive Task Tracker
  - 266 MVP tasks across 5 development phases
  - Progress tracking with checkboxes
  - Dependencies and blockers
  - Daily/weekly schedules

- **`.claude/UI_DESIGN_SPEC.md`** - UI/UX Design Specification
  - Islamic design system (colors, typography, patterns)
  - Complete component library specs
  - Screen layouts and user flows
  - Accessibility and cultural guidelines

- **`.claude/Quran_Memorization_Development_Road_Map (1).md`** - Development Roadmap
  - 7-week timeline to MVP
  - Phase-by-phase implementation plan
  - Critical design principles
  - Performance benchmarks

### Workflow
1. **Before starting any task:** Check TASK_LIST.md for context and dependencies
2. **Before writing UI code:** Review UI_DESIGN_SPEC.md and use frontend-design skill
3. **Before making architectural decisions:** Consult ARCHITECTURE.md
4. **For feature questions:** Reference PRD.md for product vision

### Task Management
- Use TASK_LIST.md to track progress
- Mark tasks complete with `[x]` when finished
- Add blockers to the "Blockers & Issues" section
- Update progress percentages at end of each phase
