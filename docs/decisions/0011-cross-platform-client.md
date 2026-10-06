# 0011 Cross-platform client

Status: accepted

## Decision

Build the mobile app once in React Native with Expo (TypeScript). Ship iPhone first; Android follows from the same codebase.

## Rationale

Android is a stated goal. One codebase avoids a second app. TypeScript matches the Supabase SDK and suits solo, fast iteration.

## Consequences

- Module boundaries stay the same; each module is a workspace package.
- Background location is the riskiest capability and is built and tested first on both platforms.
- Platform-only features (Live Activity, CarPlay, Android Auto) are separate modules added later.
- A native module is the fallback if a plugin cannot meet a platform's background-location needs.
- Development builds are needed; Expo Go is not enough.

## Revisit

If cross-platform location or battery behavior cannot meet the product's needs.
