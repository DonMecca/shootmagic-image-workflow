# Frontend Guideline Document

This document describes the frontend architecture, design principles, and technologies used in the ShootMagic Image Workflow application. It is written in everyday language so anyone can understand how the frontend is set up, how it works, and how to maintain it.

---

## 1. Frontend Architecture

**Framework and Build Tool**
- We use **React** for building the user interface. React’s component-based model makes it easy to break the UI into small, reusable pieces.
- We chose **Vite** as our build tool. Vite starts up quickly in development and generates optimized code for production with minimal configuration.

**Language**
- The project is written in **TypeScript**. TypeScript catches mistakes early by checking types as you code. It’s especially helpful when dealing with API responses and local storage data.

**UI Library and Styling**
- We use **shadcn/ui**, a library of accessible React components, to build buttons, cards, selects, and other UI elements.
- **Tailwind CSS** is our styling framework. It provides utility classes for spacing, colors, typography, and responsive layout.

**Folder Structure**
```
src/
  api/           # API integration (api/chutes.ts)
  components/    # Reusable UI components
  pages/         # Page-level components (Dashboard.jsx)
  utils/         # Utility functions (storage.ts)
  App.tsx        # Application entry point with routing
  main.tsx       # Vite bootstrap file
```

**Scalability, Maintainability, Performance**
- **Scalability**: New features fit into the existing folders (`components`, `api`, `utils`). We can add routes in `pages` easily.
- **Maintainability**: TypeScript and clearly named files/types keep the code understandable.
- **Performance**: Vite’s code-splitting, lazy loading of components, and Tailwind’s JIT mode keep bundle sizes small and speed up page loads.

---

## 2. Design Principles

We follow three key principles:

1. **Usability**: Simple and intuitive interaction. Clear labels on buttons and inputs. Drag-and-drop and file input for image upload.
2. **Accessibility**: All controls have proper ARIA labels. We use accessible components from shadcn/ui by default. Keyboard navigation is supported.
3. **Responsiveness**: The layout adapts to any screen size—from a small phone to a large desktop—using Tailwind’s responsive utilities.

**Applying These Principles**
- Button states (hover, focus, disabled) are clearly styled.
- Informative loading indicators and error messages guide users.
- The image grid wraps to fit narrower viewports.

---

## 3. Styling and Theming

**Styling Approach**
- We follow a **utility-first** approach with Tailwind CSS.
- No global CSS files; styles are applied via class names directly in JSX.

**CSS Methodology**
- Tailwind’s atomic classes replace BEM/SMACSS.

**Theming**
- A single color palette is defined in `tailwind.config.js`. We can later extend to light/dark themes using CSS custom properties or Tailwind’s theming plugin.

**Visual Style**
- **Modern & Flat** design with subtle shadows and rounded corners, inspired by Material Design.

**Color Palette**
- Primary: #4F46E5 (Indigo 600)
- Secondary: #2563EB (Blue 600)
- Accent: #10B981 (Green 500)
- Background: #F9FAFB (Gray 50)
- Surface: #FFFFFF (White)
- Text Primary: #111827 (Gray 900)
- Text Secondary: #6B7280 (Gray 500)
- Error: #EF4444 (Red 500)

**Font**
- We use **Inter**, a clean, versatile sans-serif font. It pairs well with the modern look and improves readability.

---

## 4. Component Structure

**Organization**
- All reusable pieces live under `src/components/`.
- Page-specific or complex components can have their own subfolder.

**Reusability**
- Each component is focused on a single task (e.g., `ImageUploader`, `PresetSelector`).
- Generic UI pieces (buttons, cards) come from shadcn/ui.

**Benefits**
- **Maintainability**: Small components are easier to understand and test.
- **Consistency**: Shared components enforce a uniform look.

---

## 5. State Management

**Local React State**
- We use React’s built-in `useState` and `useEffect` hooks to track the list of images and their status.

**Context API (Optional)**
- If more global state is needed (e.g., user preferences), we can introduce React Context to share state across the app.

**Persistence**
- A small utility in `src/utils/storage.ts` abstracts `localStorage` get/set.
- On app load, we read persisted image results and hydrate state.

---

## 6. Routing and Navigation

**React Router**
- We use **React Router** for navigation. Currently, we have one main route (`/`) for the dashboard.
- Future pages (e.g., Settings, About) can be added easily in `src/pages/` and wired up in `App.tsx`.

**Navigation Flow**
1. User lands on `/` (Dashboard).
2. User uploads images, selects a preset, and clicks “Process.”
3. API calls run, and results display in the same view.

---

## 7. Performance Optimization

- **Code Splitting**: Lazy-load heavy components (e.g., large image preview) with `React.lazy` and `Suspense`.
- **Asset Optimization**: Vite optimizes images and bundles CSS.
- **Caching**: Persisting processed images in `localStorage` avoids unnecessary API calls on reload.
- **Concurrency Limits**: Process images in small batches to avoid overloading the client or API.

---

## 8. Testing and Quality Assurance

**Unit Tests**
- **Vitest** for testing utilities (`storage.ts`, `api/chutes.ts`).
- Mock `fetch` to simulate API responses.

**Component Tests**
- **@testing-library/react** to verify that `ImageUploader` handles valid and invalid files.
- Test that the dashboard shows correct loading and success states.

**End-to-End Tests (Future)**
- We can integrate **Playwright** or **Cypress** if full workflows (upload ➔ process ➔ save) need automated validation.

**Linting and Formatting**
- **ESLint** + **Prettier** enforce code consistency.

---

## 9. Conclusion and Frontend Summary

This guide lays out a clear path for building and maintaining the ShootMagic Image Workflow frontend. We use React, Vite, TypeScript, shadcn/ui, and Tailwind CSS to deliver a modern, responsive, and accessible UI. Component-based architecture, simple state management, local storage persistence, and solid testing practices create a smooth developer experience and a reliable user experience. With these guidelines, any developer can understand, extend, and optimize the frontend without confusion.