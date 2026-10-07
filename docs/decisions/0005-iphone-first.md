# 0005 iPhone first

Status: accepted, amended by decision 0011 (Android supported from one codebase)

## Decision

Rendezview ships on iPhone first and supports Android afterward from the same codebase (decision 0011). Web is out of scope.

## Rationale

Focused scope for the Toronto launch. Lets the product lean on iOS capabilities: precise background location, Live Activities, Dynamic Island, widgets, CarPlay, and Apple Maps handoff.

## Consequences

- Feature specs include an iOS section covering permissions, background modes, and system integrations, and an Android note once Android work begins.
- Location needs background permission on both platforms (Always on iPhone); battery and store review wording need design.
- Maps handoff offers Apple Maps first, with Google Maps and Waze as options.

## Revisit

After v1, when demand for Android is clear.
