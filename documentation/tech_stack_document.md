# Tech Stack Document

This document explains the key technology choices behind the ShootMagic Image Workflow tool. It uses everyday language so anyone—technical or not—can understand why each piece was chosen and how it fits together.

## 1. Frontend Technologies

Our application lives entirely in the browser, and here’s what makes it tick:

- React (via Vite)  
  • A popular library for building user interfaces.  
  • Chosen for its component approach, letting us break the UI into small, reusable pieces like our image uploader and dashboard cards.
- Vite  
  • A fast build tool that sets up React projects in seconds.  
  • Provides instant reloads as you change code, so developers see updates immediately.
- TypeScript  
  • A version of JavaScript that checks types as you code.  
  • Helps catch mistakes early—especially useful when talking to an external API and saving things in your browser.
- shadcn/ui  
  • A ready-made library of styled UI components (buttons, cards, dropdowns).  
  • Speeds up development and ensures everything looks consistent and modern.
- Tailwind CSS  
  • A utility-first styling approach: small building blocks like `p-4` (padding) or `grid-cols-2` (two-column grid).  
  • Makes it easy to create responsive layouts, so the tool looks good on phones, tablets, and desktops.

**How these choices enhance the experience:**  
They let us deliver a snappy, polished interface with minimal custom styling. Users get instant feedback—drag-and-drop, loading indicators, and a clean grid of before-and-after images.

## 2. Backend Technologies

This is a purely client-side app, which means there’s no traditional server or database to manage. Instead, we rely on two simple components:

- Browser Local Storage  
  • Built into every web browser—no setup required.  
  • Stores processed image URLs so your work stays visible even if you reload the page.
- Chutes.ai API  
  • A third-party service that does the heavy lifting of transforming images.  
  • We send it a photo and a style preset; it returns a new, styled version of the image.

**How they work together:**  
When you upload an image and click “Process,” the app sends it to Chutes.ai. Once the styled image returns, we display it immediately and save the result in your browser for later.

## 3. Infrastructure and Deployment

Even though the app runs in your browser, we still need a way to build, store, and deliver it:

- Git + GitHub  
  • Version control for tracking every change and collaborating.  
  • GitHub hosts the code and keeps your history safe.
- GitHub Actions (CI/CD)  
  • Automatically builds and tests the app whenever code is updated.  
  • Ensures each new version is error-free before it goes live.
- Static Hosting (e.g., Vercel, Netlify)  
  • Easy platforms that serve the final, built files to users around the world.  
  • Automatically pull from GitHub and redeploy on every merge—no manual steps.
- (Optional) Docker + Nginx  
  • For teams who prefer containerization, we provide a simple Dockerfile with Nginx to serve static content.  
  • Ensures consistency across development and production machines.

**Benefits:**  
Reliable, repeatable builds; automatic deployments; and global performance—your users get the latest version delivered quickly.

## 4. Third-Party Integrations

We rely on a single, powerful external service:

- Chutes.ai Image Processing API  
  • Applies creative “presets” (Amazon catalog style, etc.) to any photo.  
  • We call it directly from the browser, passing our secret key in an environment-managed variable.

**Why it matters:**  
Outsourcing the image transformation to Chutes.ai means we don’t have to build or maintain complex machine-learning code. You get high-quality results with minimal effort.

## 5. Security and Performance Considerations

We’ve taken steps to keep your data safe and the app running smoothly:

Security:
- Environment Variables  
  • The Chutes.ai API key lives in a `.env` file and is never hardcoded in the code you see.  
  • During deployment, the hosting platform injects the key securely.
- API-Key Exposure  
  • For production, consider adding a simple serverless proxy to avoid exposing the key in client code.

Performance:
- Batch Processing with State Tracking  
  • Each image tracks its own status: queued, processing, completed, or error.  
  • Users see individual progress indicators rather than a single spinner for all images.
- Local Storage Caching  
  • Once processed, images are saved locally so we don’t call the API again on page refresh.  
  • Speeds up repeat visits and reduces unnecessary API calls.
- Code Splitting and Fast Builds  
  • Vite handles splitting the app into small chunks, so the initial load is quick and each feature loads only when needed.

## 6. Conclusion and Overall Tech Stack Summary

We chose a client-focused, modern stack that meets our goals: a fast, easy-to-use image workflow with minimal overhead.

- **Frontend:** React + Vite, TypeScript, shadcn/ui, Tailwind CSS  
- **Backend:** None—uses browser Local Storage  
- **Integrations:** Chutes.ai API for image styling  
- **Infrastructure:** GitHub + GitHub Actions, deployed to Netlify/Vercel (or Docker+Nginx)  
- **Security & Performance:** Environment variables, local caching, batch status handling.

This combination ensures we deliver a polished, responsive tool that users can trust and developers can maintain with ease. Enjoy transforming your images!