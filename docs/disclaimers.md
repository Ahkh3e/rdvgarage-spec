# Disclaimers

RDV Garage lets people share location and compete on top speed, so disclaimers are prominent and repeated. The wording below is a working draft. It must be reviewed by a lawyer before launch.

## Canonical text

Short (used in the Go live sheet and leaderboard footer):

> Drive safely and obey all laws and speed limits. Don't use your phone while driving. Speeds are GPS estimates for fun, not a challenge to speed. You are responsible for how you drive.

Full (shown at onboarding and under Me, Legal):

1. **You are responsible for your driving.** Obey traffic laws, posted speed limits, and road conditions. RDV Garage does not encourage speeding, racing, stunts, or any unsafe or illegal driving.
2. **Don't operate the app while driving.** Set up Go live before you start. Once you are live, the map follows you and needs no input. Do not look at or touch your phone while driving. Passengers may use the app.
3. **Top speed is not a contest to break the law.** Speeds are estimates measured by your phone's GPS. They can be wrong. The leaderboard is for fun and carries no prize, reward, or endorsement. Never drive unsafely to improve a ranking. If you want to test speed, use a closed course where it is legal.
4. **Your location is shared.** When you Go live, the crews you choose can see where you are. Only share with people you trust. Anyone in a crew you choose can see your live position and your top speeds for sessions shared with that crew. You can stop at any time.
5. **Meets and cruises are organized by users.** RDV Garage does not organize, supervise, or insure any gathering. You attend at your own risk and are responsible for your own conduct and safety.
6. **Accuracy is not guaranteed.** Maps, positions, and speeds may be delayed or wrong. The app is not a navigation or safety tool.
7. **Your account is your responsibility.** Keep your password private. You are responsible for activity on your account. If you forget your password, reset it by email; if you lose access to your email, we may not be able to restore your account.
8. **Your email is for your account only.** We use it for confirmation and recovery. We do not show it to other users.
9. **You must be 18 or older.** Use of the app is limited to adults who are licensed to drive where they drive.
10. **Use at your own risk.** To the extent the law allows, RDV Garage is not liable for injury, loss, fines, or damage arising from your use of the app or from the actions of other users.

## Placement

| Where | Text | Behavior |
|---|---|---|
| Onboarding, before the account is created | Full | Must accept and confirm being 18 or older; the accepted version and time are stored |
| Go live sheet | Short | Visible every time, above the Start button |
| Board footer | Short | Every view of the leaderboard |
| Me, Legal | Full | Always available |
| Invite landing page | Short, plus a link to the full text | Footer |
| Store listings | Summary | See `docs/store-submission.md` |

A new terms version prompts existing users to accept again on next open.

## Reducing liability

Disclaimers lower risk but do not remove it, and none of this is legal advice. These product choices and steps are meant to keep exposure as small as practical, and a lawyer should confirm them for Ontario and Canada before launch.

Product choices already in the spec:
- 18 or older only, with acceptance and the terms version stored per account.
- Crew-only location sharing, always started by the user, never public (decision 0004).
- Speed shown after the session, never live; no prizes, rewards, or promotion of speed; the leaderboard sits behind a flag so it can be pulled at once (decision 0007).
- Minimal personal data: handle, email, optional avatar. Passwords are handled by Supabase Auth and never seen by us (decision 0013).
- No route history stored; session summaries only (privacy.md).
- Account and data deletion in the app.

To arrange before launch:
- Terms of use with limitation of liability, assumption of risk, indemnity by the user, governing law and venue, and a clear statement that RDV Garage organizes no events.
- A privacy policy covering email, handle, avatar, and location summaries under Canadian privacy law (PIPEDA).
- A breach response plan and a named contact for privacy requests.
- Business liability and cyber insurance, and a business entity so personal assets are separate from the app.

The largest exposure is the top speed leaderboard, because it ranks speed. A lower-risk alternative is to rank something else, such as distance driven or meets attended. The owner has chosen to keep top speed for 0.0.1 with the safeguards above.
