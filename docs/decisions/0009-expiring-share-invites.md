# 0009 Share invites expire after 24 hours

Status: accepted

## Decision

Invites are created per share through Share invite. Each invite link is active for 24 hours, then expires. There is no permanent personal invite link.

## Rationale

Keeps referral-only access (decision 0003) tight: a leaked link stops working on its own. Each invite is a deliberate act by a member.

## Relation to other decisions

Refines decision 0003 by settling invite expiry and revocation. It replaces the earlier unlimited, reusable personal link described in invites.md, which was a spec choice and not a recorded decision.

## Consequences

- Members create a new invite whenever they want to bring someone in.
- Each invite is tracked, so the referral chain and who-joined list are per invite.
- Invites are multi-use within 24 hours, bounded by a per-invite signup limit and a per-member active-invite limit (values to be decided).
- Revisit single-use if multi-use proves too loose.
