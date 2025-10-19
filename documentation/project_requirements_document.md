# Project Requirements Document (PRD)

## 1. Project Overview

**shootmagic-image-workflow** is a client-side web application that lets users upload one or more images, choose an enhancement preset, and see transformed "before" and "after" pairs in a responsive gallery. Instead of maintaining its own backend, it calls the external Chutes.ai API directly from the browser and stores results in `localStorage` to preserve user sessions without a database. This streamlined approach reduces complexity, eliminates server-side infrastructure, and focuses entirely on delivering a smooth, modern image processing experience.

We’re building this tool to provide a polished, self-contained image enhancement workflow that is easy to deploy and maintain. Key success criteria include: 
- Reliable integration with the Chutes.ai API (correct presets and error handling)
- A sleek, responsive UI built with shadcn/ui and Tailwind CSS
- Instant feedback and per-image progress indicators
- Persistent storage of processed images across sessions

## 2. In-Scope vs. Out-of-Scope

### In-Scope (Version 1)
- Full client-side implementation using a Vite + React + TypeScript stack
- Components:
  - **ImageUploader**: drag-and-drop and file picker with type/size validation
  - **PresetSelector**: dropdown menu listing enhancement presets (e.g., "Amazon Catalog")
  - **Dashboard**: main page showing upload area, preset selector, “Process” button, and results grid
- API Integration:
  - `src/api/chutes.ts`: handles converting `File` to Base64, building and sending POST requests with the API key, parsing responses
- Persistence:
  - `src/utils/storage.ts`: simple `getItem`/`setItem` wrappers around `localStorage` for saving image pairs
- UI & Styling:
  - shadcn/ui primitives (Button, Card, Select, Progress)
  - Tailwind CSS for layout, spacing, typography, and responsive grid
- Environment Variables:
  - `VITE_CHUTES_API_KEY` stored in `.env`
- Basic unit tests for API module and storage utility using Vitest

### Out-of-Scope (Later Phases)
- Any server-side code or backend framework (authentication, database, custom API routes)
- User accounts or multi-user separation
- Proxying or hiding the API key behind a serverless function
- Advanced image editing features beyond presets
- Third-party integrations (e.g., payment gateways, social media sharing)

## 3. User Flow

A new user lands on the dashboard and immediately sees two main areas: a drag-and-drop zone (or file picker) for uploading images and a preset dropdown. At first visit, there are no images displayed, so the user clicks or drags one or more image files into the uploader. The user then selects an enhancement preset (for example, "Amazon Catalog") from the dropdown.

Once images and a preset are selected, the user clicks the "Process" button. Each image enters a queued state and shows an individual loading indicator. As the Chutes.ai API returns enhanced images, the dashboard updates in real time, replacing the loader with the processed image. Both the original and transformed images appear side by side in a responsive grid. All processed pairs are saved to `localStorage` so that on page reload or revisit, the user sees their past results instantly.

## 4. Core Features

- **ImageUploader Component**  
  - Drag-and-drop area and file input fallback  
  - Validates extensions (JPEG, PNG) and size limits  
  - Emits file list to parent

- **PresetSelector Component**  
  - Renders a styled `Select` dropdown  
  - Lists predefined presets (e.g., "Amazon Catalog", "Magazine Style")  
  - Emits selected preset to parent

- **Dashboard Page**  
  - Imports `ImageUploader` and `PresetSelector`  
  - Maintains React state array of `{ id, file, status, outputUrl }`  
  - “Process” button triggers API calls per image  
  - Renders status indicators and before/after Card pairs in a grid

- **API Module (`src/api/chutes.ts`)**  
  - `processImage(file: File, preset: string): Promise<{ outputUrl: string }>`  
  - Converts file to Base64, builds POST request, attaches `Authorization` header  
  - Parses JSON response and returns image URL or throws error

- **Storage Utility (`src/utils/storage.ts`)**  
  - `getProcessedImages(): ImagePair[]` and `saveProcessedImages(data: ImagePair[]): void`  
  - Abstracts `localStorage` operations, handles JSON serialization

- **Testing**  
  - Vitest tests for API module (mock fetch) and storage utility

## 5. Tech Stack & Tools

- **Build Tool & Framework**: Vite + React + TypeScript  
- **UI Library**: shadcn/ui (Button, Card, Select, Progress)  
- **Styling**: Tailwind CSS  
- **API**: Chutes.ai (external) via browser `fetch`  
- **State Management**: React `useState` and custom hooks  
- **Persistence**: `localStorage` via utility module  
- **Testing**: Vitest + React Testing Library  
- **Environment Variables**: Vite’s `import.meta.env.VITE_CHUTES_API_KEY`  
- **IDE Integrations** (optional): VSCode with ESLint, Prettier, Tailwind CSS IntelliSense, ts-node for type checks

## 6. Non-Functional Requirements

- **Performance**:  
  - Initial page load under 1.5s (production build)  
  - API call latency target: <2s per image (dependent on Chutes.ai)  
- **Security**:  
  - Do not commit `VITE_CHUTES_API_KEY` to source control  
  - Use HTTPS for all network requests  
- **Usability**:  
  - Responsive design for desktop and tablet  
  - Clear loading and error messages per image  
- **Reliability**:  
  - Retry logic for transient network failures (up to 2 retries)  
- **Accessibility**:  
  - All interactive elements keyboard-navigable  
  - Use semantic HTML and proper ARIA labels

## 7. Constraints & Assumptions

- The Chutes.ai API is publicly reachable and the key has sufficient quota.  
- Modern browsers with ES202X support and `localStorage` availability.  
- No backend or serverless layer to hide the API key—key is exposed client-side.  
- TypeScript is retained to reduce runtime errors; switching to JavaScript is possible but not recommended.  
- Docker and CI/CD pipelines (if any) will serve only the static build—no server processes.

## 8. Known Issues & Potential Pitfalls

- **API Rate Limits**: Chutes.ai may throttle requests—implement exponential backoff and display rate-limit errors clearly.  
- **Large File Uploads**: Very large images can slow down Base64 conversion and network transfer. Consider limiting file size or compressing images before upload.  
- **localStorage Quota**: Browsers limit storage size (~5–10 MB). If users process many images, older entries may be evicted. Mitigation: store only URLs, not image data, and cap stored entries (e.g., last 50).  
- **Exposed API Key**: Client-side keys can be discovered. For production, a serverless proxy is recommended but out of scope for v1.  
- **Network Failures**: Implement retry logic and fallback UIs so users can re-process failed images manually.

---

This PRD provides a clear, unambiguous blueprint for building a client-side image workflow application using Vite, React, TypeScript, and the Chutes.ai API. All major features, flows, and constraints are spelled out so the AI model can generate subsequent technical documents without missing details.