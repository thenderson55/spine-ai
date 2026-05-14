# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## App Purpose

SpinAI is a spine/medical React Native app. The primary UI involves body anatomy visualization — `react-native-body-highlighter` renders interactive body diagrams (front/back views) used to select or highlight specific body parts/muscles. `react-native-svg` backs the SVG rendering, and `react-native-reanimated` + `react-native-worklets` handle gesture-driven animations.

## Navigation Architecture

The root Stack in `app/_layout.tsx` wraps the entire app. The `(tabs)` group is the default route and renders a bottom tab bar. Modals are pushed onto the root Stack above the tab group (not inside it), which is why `app/modal.tsx` appears at the Stack level rather than inside `(tabs)/`.

When adding a new tab screen, add the file to `app/(tabs)/` and register it in `app/(tabs)/_layout.tsx` — the `<Tabs.Screen>` order controls tab bar order.

## Expo Config Notes

- **Orientation**: portrait-only (set in `app.json`; do not add landscape layouts)
- **Typed routes**: enabled via `expo-router` experiments — `href` props in `<Link>` components are TypeScript-checked against actual route paths
- **New Architecture**: enabled — some third-party libraries may not yet support it; verify before adding new RN packages

## Component Patterns

**Theme-aware components**: `components/Themed.tsx` exports `Text` and `View` with `lightColor`/`darkColor` props and auto-applies them via `useColorScheme`. Use these everywhere a component needs to respond to light/dark mode.

**`useClientOnlyValue(serverValue, clientValue)`** (`components/useClientOnlyValue.ts`): returns `serverValue` during SSR and `clientValue` after hydration. Use this for any value that would cause a hydration mismatch on web (e.g., the tab navigator header visibility in `app/(tabs)/_layout.tsx`).

**Color tokens**: all theme colors live in `constants/Colors.ts`. Add new semantic tokens there rather than inlining hex values.

**Fonts**: SpaceMono is loaded in `app/_layout.tsx` and applied via `components/StyledText.tsx`'s `MonoText`. FontAwesome icons load from the same place via `@expo/vector-icons`.

## Tests

Snapshot tests live in `components/__tests__/` and use `react-test-renderer`. Run from the repo root via turbo (no dedicated test script in this package's `package.json`).
