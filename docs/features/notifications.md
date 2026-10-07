# Notifications

## Goal

Two things matter in 0.0.1: the person always knows when they are sharing their location, and they hear when a friend goes live. RDV notifications are described in rdvs.md (local reminders now, drops and changes need remote push); RSVP and invite notifications are not specified yet.

## In 0.0.1: local notifications while the app is running

- **You are live.** While a live session runs there is a persistent indicator that says "You're live" and that the person's crews can see their location. It does not name crews, because a person can be live to several crews at once. On iPhone it is a Live Activity on the lock screen and in the Dynamic Island, which stays for the whole session and cannot be swiped away like a notification; on an iPhone that cannot run one it falls back to an ordinary notification. On Android it is the persistent foreground notification that Android requires for background location. It ends on Stop, when the account is suspended or deleted, and an indicator left behind by a killed app is cleared at the next launch. The iPhone also shows the system's blue location indicator. The indicator does not depend on the notification permission.
- **A friend goes live.** When a member of a crew you have switched on appears on the map and you are away from the app (on the home screen, another app, or the phone locked while the app is still alive), a notification says "@handle is live". It names the crew only when the friend shares exactly one switched-on crew with you, since someone can share several. It does not repeat for someone who is simply moving, and the same person does not notify again within ten minutes. While the app is open it stays quiet, because the map already shows them.
- Your own live state never notifies you about yourself, and crews you have switched off never notify.
- The notification permission, which only the friend notifications need, is asked for the first time you go live, not at launch. If it is refused, sharing still works and the live indicator still shows.

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
