# Quizzel

Quizzel is a browser-based quiz platform for live, host-led games and self-paced assessments. It is a static Firebase application: the UI is plain HTML, Tailwind CSS is loaded from its CDN, and Firebase provides anonymous authentication, Firestore data storage, Cloud Storage media uploads, and Hosting.

**Live site:** https://quizzel0.web.app

## Features

- Admin dashboard to create, edit, delete, and launch quizzes.
- Question editor with four answer options, per-question timers, and optional images.
- CSV question import using the columns `Question, Answer A, Answer B, Answer C, Answer D, Correct Answer`.
- Two quiz modes:
  - **Synchronous:** the host advances each question and controls the shared pace.
  - **Asynchronous:** players progress independently; an optional overall time limit can be set.
- Player registration with name, email, contact number, and school.
- Real-time lobby, questions, leaderboards, player progress, and results.
- CSV result exports for both quiz modes.

## Project layout

```text
.
├── firebase.json            # Firebase Hosting configuration
├── .firebaserc              # Default Firebase project (quizzel0)
└── public/
    ├── index.html           # Role-selection landing page
    ├── admin.html           # Quiz administration and game creation
    ├── host.html            # Live game controls and result exports
    ├── player-join.html     # PIN and player-details form
    ├── player-wait.html     # Pre-game lobby
    ├── player-game.html     # Quiz experience and scoring
    └── 404.html             # Hosting fallback page
```

## Run locally

Prerequisites: Node.js and the Firebase CLI.

```bash
npm install -g firebase-tools
firebase login
firebase emulators:start --only hosting
```

Open the local URL printed by the emulator. The app currently connects to the Firebase project configured in each HTML page, so Firebase Authentication, Firestore, and Storage must be available for the full workflow.

## Deploy

The checked-in Hosting target is `quizzel0`.

```bash
firebase deploy --only hosting
```

Firebase publishes the `public/` directory to `https://quizzel0.web.app`.

## Firebase setup

The configured Firebase project must have the following services enabled:

1. **Authentication** — enable the **Anonymous** sign-in provider.
2. **Cloud Firestore** — stores `quizzes`, game sessions, player records, answers, and scores.
3. **Cloud Storage** — stores question images under `quiz-images/`.
4. **Firebase Hosting** — serves the files in `public/`.

The Firebase web configuration is intentionally present in the client pages; Firebase web API keys identify a project and are not secrets. Protect the project with restrictive Firebase Security Rules, allowed origins/API restrictions where appropriate, and least-privilege access. This repository does not yet contain Firestore or Storage rule files, so rules must be maintained and deployed from the Firebase project until they are added here.

## Data model

- `quizzes/{quizId}`: quiz metadata, owner, mode, optional time limit, and questions.
- `games/{pin}`: a launch instance, its state, active question, and host information.
- `games/{pin}/players/{playerId}`: player profile, score, progress, and answers.

## Notes for maintainers

- This is deliberately build-free; editing a page in `public/` changes the deployed application directly.
- External dependencies are loaded from CDNs: Tailwind CSS, Papa Parse, Google Fonts, and Firebase JavaScript SDK 10.12.2.
- Player details and quiz results may contain personal data. Apply a retention policy and ensure collection/processing complies with the applicable privacy requirements.
