# AD311 Intermediate Dev 1 — Progress Report

**Student:** Nathaniel Davis  
**Course:** AD311 Intermediate Dev 1  
**Period:** Winter Quarter

---

## Selected Projects

From the full set of coursework, the following three projects represent the highest-level, most complex work completed this term:

1. **Building React Applications with Different Frameworks** — Four full applications across Gatsby, Expo, Next.js, and React Router 7
2. **Dog Facts API v1 Replica with Node.js and Express.js** — A custom REST API with filtering, tagging logic, and a frontend client
3. **Integrating Third-Party APIs (Flight Tracker)** — A React app consuming a real-time aviation API with interactive maps and persistent favorites

---

## Project 1: Building React Applications with Different Frameworks

### Overview

This project demonstrates that React is not a single-purpose tool — the framework you wrap around it fundamentally changes the architecture, data flow, and deployment strategy of your application. I built four separate applications to explore those differences hands-on:

| App | Framework | Purpose |
|-----|-----------|---------|
| `my-gatsby-site` | Gatsby 5 | Static portfolio with headless CMS |
| `your-expo-app` | Expo (React Native) | Cross-platform mobile app |
| `your-next-app` | Next.js 16 | SSG landing page + dynamic routes |
| `your-remix-app` | React Router 7 | Markdown-powered blog |

### My Role

Sole developer — architecture, implementation, styling, and configuration for all four applications.

### Work Completed

**Gatsby Portfolio (`my-gatsby-site`)**
- Built a multi-page portfolio site (Home, Projects, About, Contact) with programmatic page generation via Gatsby's `createPages` Node API
- Designed a complete theme system using Styled Components (colors, typography, spacing, breakpoints, shadows, transitions)
- Configured Gatsby's GraphQL data layer to source from local JSON via `gatsby-source-filesystem` and `gatsby-transformer-json`
- Integrated Contentful headless CMS as an optional data source with `gatsby-source-contentful`, including graceful schema fallbacks when CMS credentials are absent
- Implemented featured project filtering and responsive grid layouts

**Expo Mobile App (`your-expo-app`)**
- Scaffolded a React Native app with Expo SDK 52
- Implemented bottom tab navigation with React Navigation
- Configured push notification registration and foreground notification listeners

**Next.js Landing Page (`your-next-app`)**
- Built a full SaaS landing page with header, hero, features grid, about, contact, and footer sections using the Next.js 16 App Router and Tailwind CSS 4
- Implemented Static Site Generation (SSG) across all routes
- Created a `/users` listing page fetching from the JSONPlaceholder API at build time
- Built dynamic `/users/[id]` profile routes using `generateStaticParams` to pre-render pages for all 10 users
- Handled errors with Next.js `notFound()` for invalid IDs

**React Router 7 Blog (`your-remix-app`)**
- Architected a blog that reads markdown files with YAML frontmatter from a `content/posts/` directory on the server
- Built a server utility module (`blog.server.ts`) using `front-matter` for metadata parsing and `marked` for HTML conversion
- Configured nested routing in `routes.ts` with `index()`, `route()`, and `layout()` helpers for blog posts, categories, and tags
- Wrote five technical blog posts as content
- Styled with Tailwind CSS 4 and `@tailwindcss/typography` for clean prose rendering
- Created a production Dockerfile for containerized deployment
- Configured Vite 8 as the build tool

### Architecture

```
┌──────────────────────────────────────────────────────────────────────┐
│              Building React Applications with Different Frameworks    │
├─────────────────┬────────────────┬────────────────┬──────────────────┤
│  Gatsby 5       │  Expo SDK 52   │  Next.js 16    │  React Router 7  │
│  Static/CMS     │  Mobile        │  SSG           │  Full-Stack Blog │
├─────────────────┼────────────────┼────────────────┼──────────────────┤
│ GraphQL queries │ React Native   │ App Router     │ Server loaders   │
│ createPages API │ Tab Navigation │ generateSP     │ Markdown → HTML  │
│ Styled Comp.    │ Push Notifs    │ Tailwind CSS 4 │ Tailwind CSS 4   │
│ Contentful CMS  │ Expo Go        │ TypeScript     │ Vite 8 + Docker  │
├─────────────────┼────────────────┼────────────────┼──────────────────┤
│  React 18       │ React Native   │  React 19      │  React 19        │
└─────────────────┴────────────────┴────────────────┴──────────────────┘
```

### Challenges & Solutions

| Challenge | Solution |
|-----------|----------|
| Contentful schema errors when CMS isn't configured | Added `createSchemaCustomization` in `gatsby-node.js` to define placeholder GraphQL types when `CONTENTFUL_SPACE_ID` is missing |
| Next.js 16 breaking change to `params` as a Promise | Updated dynamic route signatures to `await params` per migration guide |
| React Router 7 vs. Remix documentation confusion | Used the programmatic `routes.ts` config approach instead of file-system conventions, referencing `@react-router/dev` directly |
| Server-only code leaking to client bundle | Used `.server.ts` file suffix convention to isolate filesystem operations |

### Key Learnings

- **Framework choice is an architecture decision**, not just a preference — it dictates data loading, rendering strategy, build pipeline, and deployment model
- Gatsby's GraphQL layer excels at aggregating diverse data sources but adds complexity; Next.js is more straightforward for API-driven SSG
- React Router 7's server loaders + nested layouts provide a powerful model for content-driven sites
- Tailwind CSS 4 requires zero configuration as a Vite or PostCSS plugin, making it frictionless to adopt across projects

---

## Project 2: Dog Facts API v1 Replica with Node.js and Express.js

### Overview

A custom REST API that serves dog facts with intelligent filtering by breed and category. The API includes a web-based frontend client that lets users query and explore the data interactively. The goal was to understand backend API design — routing, query parameter handling, JSON responses, error handling, and serving static assets — all with Node.js and Express.

### My Role

Sole developer — API design, data modeling, tagging algorithm, frontend client, and styling.

### Work Completed

- **Designed and implemented a RESTful API** with three endpoints:
  - `GET /facts` — returns dog facts with optional `number`, `breed`, and `category` query parameters
  - `GET /api/breeds` — returns all available breeds extracted from the data
  - `GET /api/categories` — returns all available categories
- **Built an automated tagging system** that classifies each fact by breed and category using keyword matching against a curated list of 40+ dog breeds and 7 category definitions (History, Health, Science, Fun Facts, Records, Pop Culture, Behavior)
- **Implemented query parameter validation** with proper HTTP status codes (400 for invalid input, 404 for unknown routes)
- **Built randomized selection logic** — when `number` is specified, facts are shuffled and sliced to return random results
- **Created a frontend web client** with vanilla JavaScript that dynamically loads breed and category options from the API and displays results as styled cards
- **Served static files** from an Express `public/` directory for the landing page
- **Used ES Modules** (`"type": "module"`) throughout for modern import/export syntax

### Architecture

```
┌─────────────────────────────────────────────┐
│              Dog Facts API                    │
├──────────────────────────────────────────────┤
│                                              │
│   Client (public/)         Server (Express)  │
│   ┌──────────────┐       ┌───────────────┐  │
│   │ index.html   │──────▶│ GET /facts    │  │
│   │ app.js       │       │ GET /api/breeds│  │
│   │ styles.css   │◀──────│ GET /api/cats │  │
│   └──────────────┘       └───────┬───────┘  │
│                                  │           │
│                          ┌───────▼───────┐   │
│                          │ dog_facts-1.js │   │
│                          │ (232 facts)    │   │
│                          │ + tagging algo │   │
│                          └───────────────┘   │
└──────────────────────────────────────────────┘
```

### Challenges & Solutions

| Challenge | Solution |
|-----------|----------|
| Facts have no built-in metadata (just raw strings) | Built a keyword-matching tagging algorithm that scans each fact for breed names and category-relevant terms, assigning classifications automatically |
| Handling edge cases (no matches, invalid numbers, unknown routes) | Implemented comprehensive error handling with appropriate HTTP status codes and descriptive JSON error messages |
| Randomizing results without modifying source data | Used array spread + `sort(() => 0.5 - Math.random())` to shuffle a copy before slicing |

### Key Learnings

- **REST API design principles** — using query parameters for filtering, returning consistent JSON shapes (`{ data, success }` pattern), and proper HTTP status codes
- **Express.js middleware chain** — static file serving, route handlers, and 404 catch-all ordering matters
- **Data enrichment at startup** — pre-processing and tagging data on server initialization avoids repeated computation on each request
- ES Modules in Node.js work cleanly with `"type": "module"` in `package.json` and `import` syntax

---

## Project 3: Integrating Third-Party APIs — Flight Tracker

### Overview

A real-time flight tracking web application that consumes the Aviationstack API to search flights, display detailed information, render flight paths on an interactive Leaflet map, and persist favorite flights to localStorage. The goal was to integrate a third-party REST API into a React application while handling real-world concerns like error states, loading UX, data visualization, and client-side persistence.

### My Role

Sole developer — API integration, component architecture, map implementation, state management, and UI/UX design.

### Work Completed

- **Integrated the Aviationstack API** for real-time flight data, handling API keys via Vite environment variables (`import.meta.env`)
- **Built a search interface** (`SearchForm`) supporting four search types: departure airport, arrival airport, airline code, and exact flight number, plus status filtering (scheduled, active, landed, cancelled, diverted)
- **Implemented a flight results grid** (`FlightResults`) with card-based layout showing route visualization, times, airline, and status badges with color coding
- **Created a detailed flight view** (`FlightDetail`) displaying comprehensive flight data: departure/arrival terminals, gates, delays, aircraft registration, codeshare info, and live tracking data (altitude, speed, coordinates)
- **Built an interactive map component** (`FlightMap`) using React-Leaflet with:
  - Custom airport dot markers and plane position markers
  - Dashed polyline flight paths between departure and arrival
  - Live aircraft position when available from the API
  - Automatic bounds fitting with padding
  - A curated lookup table of 100+ major airport coordinates worldwide
- **Implemented a favorites system** (`Favorites`) with localStorage persistence — users can save and remove flights, and the list survives page reloads
- **Handled all API edge cases** — loading spinners, error messages, empty states, and graceful fallbacks when data fields are null
- **Made the app fully accessible** — keyboard navigation (`onKeyDown` handlers), ARIA labels on interactive cards, semantic HTML structure
- **Styled the entire application** with custom CSS including status color coding, responsive card layouts, and map container styling

### Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                     Flight Tracker (React + Vite)                 │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  App.jsx (State Management)                                      │
│  ├── flights[], selectedFlight, favorites[], loading, error      │
│  ├── searchFlights() → Aviationstack API                        │
│  ├── addFavorite() / removeFavorite() → localStorage            │
│  │                                                               │
│  ├── SearchForm ──── User Input ──────────────────┐              │
│  │                                                │              │
│  ├── FlightResults ── Card Grid ◀─────────────────┘              │
│  │   └── onClick → setSelectedFlight                             │
│  │                                                               │
│  ├── FlightDetail ── Full Info Panel                             │
│  │   └── FlightMap ── Leaflet + OpenStreetMap                    │
│  │       ├── Airport markers (100+ coords)                       │
│  │       ├── Flight path polyline                                │
│  │       └── Live position marker                                │
│  │                                                               │
│  └── Favorites ── localStorage persistence                       │
│                                                                  │
├──────────────────────────────────────────────────────────────────┤
│  External Services:                                              │
│  • Aviationstack API (flight data, free tier: 100 req/month)    │
│  • OpenStreetMap (map tiles, free, no key required)              │
└──────────────────────────────────────────────────────────────────┘
```

### Challenges & Solutions

| Challenge | Solution |
|-----------|----------|
| Aviationstack doesn't return airport coordinates | Built a curated lookup table of 100+ major airport IATA codes mapped to lat/lng coordinates, with fallback to API-provided coords when available |
| Free API tier limited to 100 requests/month and HTTP only | Designed the search form to encourage specific queries (exact flight numbers, airport codes) to minimize wasted calls; documented the HTTP limitation |
| Flight data has many nullable fields | Used optional chaining (`?.`) and fallback values (`|| '—'`) throughout every component to gracefully handle missing data |
| Map needing to dynamically resize based on route distance | Implemented a `FitBounds` component using Leaflet's `useMap()` hook to automatically adjust zoom and center when markers change |

### Key Learnings

- **Third-party API integration patterns** — environment variable management, query parameter construction, error response handling, and rate limit awareness
- **React-Leaflet** is a clean declarative wrapper around Leaflet — custom markers with `DivIcon`, polylines, and programmatic bounds control all work through React components
- **localStorage for client-side persistence** is effective for features like favorites where server-side storage would be overkill
- **Defensive programming** is essential when working with external APIs — you can never trust that fields will be present, so every data access needs a fallback
- Building a coordinate lookup table was more reliable than depending on the API to provide geo data for airports

---

## Overall Reflection

These three projects represent a progression from backend API design (Dog Facts), to frontend API consumption and data visualization (Flight Tracker), to full multi-framework architectural comparison (React Frameworks). Together they cover:

- **Backend:** Node.js, Express.js, REST API design, data processing, middleware
- **Frontend:** React 18/19, React Native, component architecture, state management, conditional rendering
- **Data:** GraphQL (Gatsby), REST APIs (JSONPlaceholder, Aviationstack), local JSON, Markdown with frontmatter
- **Styling:** Styled Components, Tailwind CSS 4, vanilla CSS
- **Tools:** Vite, Gatsby CLI, Expo CLI, Next.js Turbopack, Docker
- **Maps & Visualization:** Leaflet, React-Leaflet, OpenStreetMap
- **Infrastructure:** Headless CMS (Contentful), environment-based configuration, push notifications, SSG, server-side rendering

The common thread is understanding that building real applications requires making deliberate choices about architecture, data flow, and tooling — and this term's work gave me hands-on experience making and justifying those choices across very different problem domains.
