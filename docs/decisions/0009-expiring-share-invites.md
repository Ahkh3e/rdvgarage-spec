# 0009 Share invites expire after 24 hours

Status: accepted

## Decision

Invites are created per share through Share invite. Each invite link is active for 24 hours, then expires. There is no permanent personal invite link.

## Rationale

Keeps referral-only access (decision 0003) tight: a leaked link stops working on its own. Each invite is a deliberate act toward a specific person.

## Supersedes

The earlier unlimited, reusable personal link in invites.md.

## Consequences

- Members create a new invite whenever they want to bring someone in.
- Each invite is tracked, so the referral chain and who-joined list are per invite.
- Whether a link is single-use or multi-use within 24 hours is open.
