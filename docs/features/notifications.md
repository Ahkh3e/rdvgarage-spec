# Notifications

## Goal

Two things matter in 0.0.1: the person always knows when they are sharing their location, and they hear when a friend goes live. Everything else (RDV drops, RSVPs, invites) waits for the RDVs feature.

## In 0.0.1: local notifications while the app is running

- **You are live.** When a live session starts, a notification says "You're live" with the names of the crews that can see them. It stays until Stop (or the account is suspended or deleted), then is removed. On Android it is the persistent foreground notification that Android requires for background location, and it cannot be swiped away while live. On iPhone the system's blue location indicator is shown as well, and the notification stays in the notification list; iPhone has no truly ongoing notification outside a Live Activity (a later platform module, `docs/architecture.md`).
- **A friend goes live.** When a member of a crew you have switched on appears on the map and you are away from the app (on the home screen, another app, or the phone locked while the app is still alive), a notification says "@handle is live" with the crew name. It does not repeat for someone who is simply moving, and the same person does not notify again within ten minutes. While the app is open it stays quiet, because the map already shows them.
- Your own live state never notifies you about yourself, and crews you have switched off never notify.
- The notification permission is asked for the first time you go live, not at launch. If it is refused, sharing still works and the Android system notification still shows.

## Not yet: remote push

A friend going live while the app is closed (or suspended in the background and not sharing itself) needs a push from a server, which needs keys only the owner can supply (`docs/SETUP.md` in the code repo):

- an Apple Push key and Android Firebase credentials, registered with EAS;
- a table of each device's push token, written when a signed-in person grants permission (one row per device, removed on sign out, account deletion and token errors);
- a server function that runs when a live session starts, finds the active members of the crews the session was shared with (never anyone else), skips the person who started it and anyone with that crew switched off, and sends through Expo's push service;
- a per-person setting to mute friend-live pushes, and a short quiet period so one person going live repeatedly is not a stream of pushes.

Pushes carry only the handle and the crew name. They never carry a position, a speed or a route.

## Rules

- No notification reveals a position or speed.
- Only members of a crew the session was shared with can be notified about that session.
- A person can turn notifications off in the phone's settings; the app does not fight that.

## Open questions

- Whether friend-live pushes are on by default, or opt in per crew.
