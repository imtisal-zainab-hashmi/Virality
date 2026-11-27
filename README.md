# VIRALITY

VIRALITY is a demo app for estimating how a short-form video might perform before it is posted. Choose TikTok, Instagram Reels, or YouTube Shorts, preview a clip, and review a score, reach estimates, retention, simulated audience reactions, and suggested edits.

The app can run with built-in demo data or call the Fastn `analyze-virality` workflow. Demo predictions are illustrative and are not guarantees of real-world performance.

<p align="center">
  <img src="images/xug7.png" width="250">
  <img src="images/xug1.png" width="250">
</p>

## Contents

- [Features](#features)
- [Run locally](#run-locally)
- [Use the demo](#use-the-demo)
- [Connect to Fastn](#connect-to-fastn)
- [Fastn request and response](#fastn-request-and-response)
- [Current video-upload limitation](#current-video-upload-limitation)
- [Project structure](#project-structure)
- [Scripts](#scripts)
- [Troubleshooting](#troubleshooting)

## Features

- Drag and drop a video or choose one from the file picker for a local preview.
- Try three sample videos: **Street Food Flop**, **Tech Gadget Unboxing**, and **Fitness Quick Tip**.
- Switch between TikTok, Instagram Reels, and YouTube Shorts.
- Watch an analysis progress screen with changing analysis milestones.
- Review a viral score and verdict, predicted views/likes/comments/shares, and scores for five areas: hook, retention, pacing, audio/trend, and emotional trigger.
- Compare a second-by-second retention curve with a viral benchmark.
- See simulated audience sentiment and example comments.
- Select suggested edits in **Fix & Recalculate** to preview a local score and metric boost. This does not re-run the Fastn workflow.
- Configure and test a Fastn endpoint from the app’s settings dialog.

## Run locally

<p align="center">
  <img src="images/xug7.png" width="250">
  <img src="images/xug1.png" width="250">
</p>

### Requirements

- Node.js with npm, or Bun.
- An internet connection to load the sample video clips and, when configured, call Fastn.

### Install and start

With Bun:

```sh
bun install
bun run dev
```

With npm:

```sh
npm install
npm run dev
```

Open the local address printed by the development server (the default Vite port in this project is `8080`).

## Use the demo

1. Choose a platform.
2. Select a sample clip, or choose/drop in a video file.
3. Select **Predict virality**.
4. Review the report. Choose suggested edits to see their cumulative illustrative score boost, or select **New analysis** to start over.

With no Fastn endpoint configured, analysis uses deterministic built-in demo data based on the video title and platform. Sample clips are hosted by Mixkit and need an internet connection to play.

## Connect to Fastn

The app includes a Fastn settings dialog; this README does not contain a workspace URL, API key, or Space ID. Each person using the demo must enter their own permitted Fastn connection details in the app.

1. Open **Fastn** in the top-right corner.
2. Enter the **Workflow Endpoint URL** from the workflow’s Integrate / Contract tab.
3. Enter an **API Key / Auth Token** from Fastn Settings → API Keys or the Integrate panel.
4. Enter the **Space ID** for live calls. Fastn shows it in the flow-builder URL or in the Integrate panel’s request headers.
5. Select **Test connection**. A successful response confirms the endpoint answered; the test uses `use_mock: true`.
6. Leave **use_mock** on to use the workflow’s mock path. Turn it off when you are ready for a live workflow run; live runs may consume Fastn credits.
7. Select **Save**, then analyze a sample clip.

The API key is stored in this browser’s local storage, not in the source files or a shared project setting. Avoid saving it in a public or shared browser, and never commit credentials to the repository. Settings are browser-specific and will need to be entered again in another browser or account.

The server-side Fastn proxy sends the required `Authorization: Bearer <token>` and `x-fastn-api-key` headers. Keys beginning with `fsk_test_` also add `X-fastn-Test-Mode: true`. When supplied, the Space ID is sent as `x-fastn-space-id`. A missing token is rejected before the request is sent; Fastn authorization errors are surfaced in the app.

## Fastn request and response

The app sends a JSON input with these fields:

| Field | Type | Meaning |
| --- | --- | --- |
| `title` | string | Video title (the file name without its extension for a local upload). |
| `platform` | string | `tiktok`, `instagram`, or `youtube`. |
| `use_mock` | boolean | Whether the Fastn workflow should use its mock path. |
| `video_url` | string | A URL for the workflow to analyze. See the upload limitation below. |

The Fastn workflow slug used by the demo is `analyze-virality`. The response normalizer accepts a full response or a response nested under `data`, and can use fields such as `viral_score`/`score`, `verdict`, `predicted_metrics`, `pillar_scores`, `retention_curve`, `reaction_simulator`, and `actionable_fixes`/`fixes`. If a response omits optional report data, the app fills those sections with demo values so the report remains complete.

When a workflow call fails, the app shows an error and falls back to the built-in demo prediction for that analysis. The report indicates whether its source was a live Fastn response or demo data.

## Current video-upload limitation

A local upload is only previewed in the browser; it is **not uploaded or transmitted to Fastn**. For a locally uploaded file, the current app sends the Tech Gadget Unboxing sample clip URL as `video_url`. Choosing one of the three sample clips sends that sample’s URL instead. To analyze the actual uploaded file with Fastn, the app would need an upload/storage flow that provides Fastn with a reachable video URL.

## Project structure

```text
src/
  components/virality/
    Dashboard.tsx       Prediction report and Fix & Recalculate controls
    SettingsModal.tsx   Fastn settings, connection test, browser-local settings
  lib/
    fastn.functions.ts  Server-side Fastn request proxy and validation
    virality.ts         Prediction types, sample clips, demo generator, response mapping
  routes/
    __root.tsx          Shared app shell, document metadata, and notifications
    index.tsx           Upload hub and upload → analysis → report flow
  styles.css            Global styles and design tokens
  test/                 Vitest tests
```

The app uses TanStack Start for routing and server functions, React and TypeScript for the interface, Tailwind CSS for styling, Recharts for the retention chart, and shadcn/Radix UI primitives.

## Scripts

| Command | Purpose |
| --- | --- |
| `bun run dev` / `npm run dev` | Start the local development server. |
| `bun run test` / `npm test` | Run the Vitest test suite. |
| `bun run build` / `npm run build` | Create a production build. |
| `bun run lint` / `npm run lint` | Run ESLint. |

## Troubleshooting

- **Fastn rejects the request:** Check the endpoint, API key, and Space ID. The Space ID is required for the live Fastn API path. A test key beginning with `fsk_test_` is automatically marked as test mode.
- **The app shows demo data instead of a live report:** Confirm that the endpoint is saved and that the connection test succeeds. If the workflow call fails, the app deliberately falls back to demo data and displays an error.
- **The sample clip does not play:** Check the internet connection; the sample videos are hosted externally.
- **Your uploaded video is not analyzed by Fastn:** This is the current limitation described above; local uploads are preview-only.
- **Fastn settings disappeared:** Settings are saved in the current browser’s local storage. Re-enter them when using another browser or device.

## Continue the project

Open the project in Lovable to continue editing, or work on the source locally and push changes to the connected repository. Keep Fastn credentials out of source control
