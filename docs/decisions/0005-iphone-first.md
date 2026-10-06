# 0005 iPhone first

Status: accepted

## Decision

RDV Garage is an iPhone app first. Android and web are out of scope until iPhone ships.

## Rationale

Focused scope for the Toronto launch. Lets the product lean on iOS capabilities: precise background location, Live Activities, Dynamic Island, widgets, CarPlay, and Apple Maps handoff.

## Consequences

- Feature specs include an iOS section covering permissions, background modes, and system integrations.
- Location uses Core Location with Always authorization; battery and App Store review wording need design.
- Maps handoff offers Apple Maps first, with Google Maps and Waze as options.

## Revisit

After v1, when demand for Android is clear.
