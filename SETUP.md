# Kindle Club Arcade — setup and update guide

This is a single-page site. It does not need Node.js, a build step, or a package manager. `index.html` is the website; `firebase-rules.json` contains the suggested Realtime Database rules.

## What works before setup

- Four rooms with local, same-browser demo chat
- Guest nickname/profile stored in the browser
- Word Scramble, Memory Match, and Number Guess
- Responsive, low-animation layout intended for small screens

## What needs Firebase

- Live room chat shared across devices
- Shared multiplayer Tic-Tac-Toe boards, one board per chat room
- Shared Tic-Tac-Toe win leaderboard

Profiles are guest profiles, not full accounts. Anonymous identity is tied to a browser/app session and should not be treated as a permanent cross-device login. The client-side scoreboard is suitable for a hobby club, not a competitive or fraud-resistant system.

## Part 1 — create the real-time backend

1. Open [Firebase Console](https://console.firebase.google.com/) and create a project for Kindle Club Arcade.
2. In **Build → Authentication → Sign-in method**, enable **Anonymous** sign-in.
3. In **Build → Realtime Database**, create a database. Pick a region close to most of your users. Copy its database URL.
4. In **Realtime Database → Rules**, replace the starter rules with the contents of `firebase-rules.json`, then publish the rules. Keep the default deny-all root rules; do not set the entire database to public read/write.
5. In **Project settings → General → Your apps**, add a Web app. Firebase shows a configuration object with `apiKey`, `authDomain`, `databaseURL`, `projectId`, and `appId`.
6. Open `index.html` in a text editor. Near the top of the final script, find `var FIREBASE_CONFIG = { ... }`. Replace each placeholder with the values from your Firebase Web app configuration. Make sure `databaseURL` matches the database you created, and keep the quotes and commas.
7. Save `index.html` and open it in a modern browser. It should say **Connected · live** after Firebase anonymous sign-in succeeds. Test with a second browser/device. Use the same URL when testing on a Kindle.

Firebase's client API key is not a server secret; database access is controlled by Authentication and Realtime Database Security Rules. Never add service-account JSON, private keys, or admin credentials to `index.html`.

## Part 2 — publish the site online with GitHub Pages

GitHub Pages can serve this static HTML page over HTTPS. You need a GitHub account.

1. Go to [GitHub](https://github.com/) and create a repository named `kindle-club-arcade`.
2. Upload `index.html`, `firebase-rules.json`, and `SETUP.md` to the repository's top level. Keep the filename exactly `index.html`.
3. Open the repository's **Settings → Pages**. Under the build/deployment source, select **Deploy from a branch**, choose `main` and the `/ (root)` folder, then save.
4. Wait for the deployment to finish. GitHub will show the site's URL on the Pages settings screen. Open it on a second device and on your Kindle browser.
5. Keep `firebase-rules.json` in your repository as the rules backup. You publish its contents separately in Firebase Console; GitHub Pages does not deploy database rules.

## How to update the website later

**The main file to edit is `index.html`.** You can update text, room labels, colours, layout, mini-games, or club prompts without rebuilding the project.

Easy update method in GitHub:

1. Open your repository on GitHub and select `index.html`.
2. Choose the pencil **Edit this file** button.
3. Make your changes, then choose **Commit changes**.
4. Wait for GitHub Pages to publish the new version, then reload the site. Deployment can take a few minutes.

You can also edit `index.html` on your computer and upload/commit the changed file. If you modify Firebase settings or data paths, update `firebase-rules.json` and publish its rules in Firebase Console as well.

## Kindle browser notes

The site avoids frameworks, large images, animation-heavy effects, and build tooling. It uses a Firebase compatibility SDK from Google's CDN for online mode. Some older e-ink Kindle browser versions may not support the Firebase SDK, current TLS/CDN requirements, or modern web features; in that case, the local demo may still work, but live chat/multiplayer may not. Kindle Fire tablets generally have more capable browsers than older e-ink Kindles. Test on the exact device/browser you plan to use.

## Safety and moderation notes before sharing widely

- Use nicknames and don't post personal information.
- The current project is a starter hobby site. It does not include report/block controls, moderation queue, spam throttling, age controls, or robust account recovery.
- Guest users can choose display names; the starter scoreboard can be manipulated by users who understand the client code. Don't use it for prizes or anything important.
- The security rules restrict profile writes to the current authenticated UID and message creation to authenticated users; game state is intentionally a shared room resource. Review and harden the rules before a large public launch.
- Consider adding a moderator/admin workflow and rate limiting if you open it to lots of people.
