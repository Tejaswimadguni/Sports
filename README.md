# STRIVORA — Multi-Sport Hub

STRIVORA is a multi-sport web application for exploring cricket, football, basketball and volleyball. It combines sport-specific data pages with interactive sports discovery and quiz/challenge experiences.

> **Project status:** The frontend and Express API proxy are implemented. Live data access depends on valid provider API credentials and the availability/plan limits of the connected APIs. The project ZIP reviewed for this README did not include the root `package.json`, so verify the exact frontend scripts and dependency versions in your working repository before relying on the commands below.

---

## Contents

- [Features](#features)
- [Technology](#technology)
- [Project structure](#project-structure)
- [Requirements](#requirements)
- [Getting started](#getting-started)
- [Environment variables](#environment-variables)
- [Running the application](#running-the-application)
- [API architecture](#api-architecture)
- [Frontend pages and experiences](#frontend-pages-and-experiences)
- [Caching and request handling](#caching-and-request-handling)
- [Security notes](#security-notes)
- [Troubleshooting](#troubleshooting)
- [Deployment checklist](#deployment-checklist)

---

## Features

### Multi-sport data

- **Cricket**
  - Search players and view player profiles and career statistics.
  - Browse teams and team details.
  - Browse competitions, competition details and seasons.
  - View match scorecards.
- **Football**
  - Browse countries, leagues, teams and players.
  - View team details and squads.
  - View player profiles/statistics and player team history.
  - Browse league standings.
- **Basketball**
  - Browse countries, leagues, teams and players.
  - View team details and league standings.
  - View player statistics.
- **Volleyball**
  - Browse countries, leagues and teams.
  - View team details, games/match results and standings.

### Discovery and interactive experiences

The project also includes shared sports discovery and learning experiences, including:

- Explore and sports-selection pages.
- Arena and sports preview pages.
- Quiz and challenge pages, including flag, player and statistics-related activities.
- Shared navigation, search, lists, profile and footer components.

The exact availability of live information on each page depends on the corresponding API endpoint and the selected competition, season or entity.

---

## Technology

The project uses:

- **React** for the user interface.
- **Vite** as the frontend development/build tool.
- **JavaScript (ES modules)**.
- **React Router / browser routes** for page navigation where configured in the app.
- **Node.js and Express** for the backend API proxy.
- **dotenv** for server-side environment variables.
- **cors** for cross-origin requests during development.
- External sports data APIs:
  - Cricket Database API.
  - API-SPORTS Football API.
  - API-SPORTS Basketball API.
  - API-SPORTS Volleyball API.

Check the repository's `package.json` files for the exact installed package versions.

---

## Project structure

```text
project-root/
├── public/
│   ├── flags/
│   ├── images/
│   └── logos/
├── server/
│   ├── data/
│   ├── routes/
│   ├── services/
│   ├── index.js
│   └── server.js
├── src/
│   ├── components/
│   ├── data/
│   ├── pages/
│   ├── preview/
│   ├── services/
│   ├── styles/
│   ├── App.css
│   ├── app.jsx
│   ├── index.css
│   └── main.jsx
└── README.md
```

### Important directories

| Directory | Purpose |
|---|---|
| `src/components/` | Reusable interface components such as navigation, lists, selectors and shared sections. |
| `src/pages/` | Sport pages, detail pages, discovery pages and quiz/challenge screens. |
| `src/services/` | Frontend functions that call the local Express API and process responses. |
| `src/data/` | Local data used by the interface and quiz/challenge experiences. |
| `src/styles/` | Sport- and component-specific stylesheets. |
| `src/preview/` | Preview-related application components and styles. |
| `server/index.js` | Express server, provider request helpers and API proxy endpoints. |
| `server/routes/` | Route-related server modules. |
| `server/services/` | Sport API service modules. |
| `public/logos/` and `public/flags/` | Static team, competition, country and flag assets. |

---

## Requirements

Install the following before running the project:

- Node.js (an active LTS release is recommended).
- npm.
- Git.
- Valid API credentials for the sports data providers you intend to use.

Check your Node and npm versions:

```bash
node --version
npm --version
```

---

## Getting started

### 1. Clone the repository

```bash
git clone <YOUR_GITHUB_REPOSITORY_URL>
cd <YOUR_PROJECT_FOLDER>
```

Replace the placeholders with your repository URL and local folder name.

### 2. Install frontend dependencies

From the project root:

```bash
npm install
```

### 3. Install backend dependencies

If the backend has its own `package.json`, install its dependencies from the server directory:

```bash
cd server
npm install
cd ..
```

The Express server imports `express`, `cors` and `dotenv`. If the project uses a single root package manifest instead, install dependencies according to that manifest.

---

## Environment variables

Create a `.env` file in the location from which `server/index.js` is launched (normally the project root if you start it from the root):

```env
CRICKET_API_KEY=your_cricket_database_api_key
FOOTBALL_API_KEY=your_api_sports_key
```

### Which key is used?

| Variable | Used by | Authentication |
|---|---|---|
| `CRICKET_API_KEY` | Cricket Database API | `Authorization: Bearer <key>` |
| `FOOTBALL_API_KEY` | API-SPORTS Football, Basketball and Volleyball | `x-apisports-key: <key>` |

The current backend code uses the same `FOOTBALL_API_KEY` environment variable for all three API-SPORTS sports. Confirm that your subscription and key grant access to each product you use.

### Keep credentials private

- Never put real API keys in frontend code.
- Never commit `.env` to GitHub.
- Add `.env` to `.gitignore`.
- Do not paste API keys into screenshots, issues or public support requests.
- For deployment, configure environment variables in the hosting provider's secure environment settings.

A safe example file can be committed as `.env.example` with placeholder values only:

```env
CRICKET_API_KEY=replace_with_your_cricket_api_key
FOOTBALL_API_KEY=replace_with_your_api_sports_key
```

---

## Running the application

The frontend and backend are separate processes.

### Start the backend

From the project root, run:

```bash
node server/index.js
```

The current backend source listens on port **5000**.

A simple health check is available at:

```text
http://localhost:5000/
```

The server returns a JSON message indicating that the STRIVORA Cricket API server is running.

### Start the frontend

In a second terminal, from the project root, run the Vite development script configured in your root `package.json`. It is commonly:

```bash
npm run dev
```

Open the local URL printed by Vite in the terminal.

> If `npm run dev` is not recognised, inspect the `scripts` section of your root `package.json` and use the script defined there. The ZIP reviewed while preparing this README did not contain that manifest, so the exact script could not be confirmed from the supplied archive.

---

## API architecture

The React application does not call the protected sports providers directly. The frontend service modules call the local Express backend, and the backend adds the provider credentials before forwarding requests.

```text
React frontend
     |
     | fetches local /api/... routes
     v
Express backend (port 5000)
     |
     | adds provider API key
     v
Sports data provider
     |
     | JSON response
     v
Express backend -> React frontend
```

### Provider base URLs

| Sport | Provider base URL |
|---|---|
| Cricket | `https://api.cricketdatabase.com/v1` |
| Football | `https://v3.football.api-sports.io` |
| Basketball | `https://v1.basketball.api-sports.io` |
| Volleyball | `https://v1.volleyball.api-sports.io` |

### Local backend route groups

The Express server exposes these route groups:

**Cricket**

| Local route | Purpose |
|---|---|
| `/api/players` | Search players and filter player lists. |
| `/api/players/:id` | Retrieve a player profile. |
| `/api/players/:id/stats` | Retrieve player career statistics. |
| `/api/teams` | Retrieve teams. |
| `/api/teams/:id` | Retrieve team details. |
| `/api/competitions` | Retrieve competitions. |
| `/api/competitions/:id` | Retrieve competition details. |
| `/api/competitions/:id/seasons` | Retrieve competition seasons. |
| `/api/competitions/:id/seasons/:season` | Retrieve a particular season. |
| `/api/matches/:id/scorecard` | Retrieve a match scorecard. |

**Football**

| Local route | Provider endpoint |
|---|---|
| `/api/football/countries` | `/countries` |
| `/api/football/leagues` | `/leagues` |
| `/api/football/teams` | `/teams` |
| `/api/football/players` | `/players` |
| `/api/football/player-teams` | `/players/teams` |
| `/api/football/standings` | `/standings` |
| `/api/football/squads` | `/players/squads` |

**Basketball**

| Local route | Provider endpoint |
|---|---|
| `/api/basketball/countries` | `/countries` |
| `/api/basketball/leagues` | `/leagues` |
| `/api/basketball/teams` | `/teams` |
| `/api/basketball/standings` | `/standings` |
| `/api/basketball/players` | `/players` |
| `/api/basketball/player-statistics` | `/games/statistics/players` |

**Volleyball**

| Local route | Provider endpoint |
|---|---|
| `/api/volleyball/countries` | `/countries` |
| `/api/volleyball/leagues` | `/leagues` |
| `/api/volleyball/teams` | `/teams` |
| `/api/volleyball/games` | `/games` |
| `/api/volleyball/standings` | `/standings` |

Query parameters are forwarded by the backend for the relevant list, league, team, player, season and standings requests.

---

## Frontend pages and experiences

The source includes dedicated pages for the four sports.

### Cricket

- Countries and country-player browsing.
- Player search and player details.
- Teams and team details.
- Competitions/leagues and league teams.
- Match scorecards and player statistics.

### Football

- Countries.
- Leagues.
- Teams and team details.
- Players and player details.
- Standings.

### Basketball

- Countries.
- Leagues and league details.
- Teams and team details.
- Players and player details.
- Standings.

### Volleyball

- Countries.
- Leagues and league details.
- Teams and team details.
- Games/match results.
- Standings.

The project also contains shared home, explore, arena, sports preview, profile, player-universe, quiz and challenge pages.

---

## Caching and request handling

The frontend uses `src/services/requestManager.js` to centralise JSON requests.

The request manager includes:

- In-memory response caching when a cache TTL is supplied.
- In-flight request de-duplication for identical request URLs.
- A force-refresh option.
- Cache-clearing and refresh helpers.
- User-facing handling for network failures and HTTP errors, including rate-limit responses.

The sport service modules decide which requests use the default cache TTL. Cache contents are held in memory and are cleared when the page session is restarted.

---

## Security notes

1. Keep all provider API keys on the server.
2. Do not expose `.env` through the frontend or commit it.
3. Configure production CORS to allow only the frontend origin(s) you trust. The current server uses `cors()` without an origin restriction, which is convenient for local development but should be reviewed before production.
4. Use HTTPS for deployed frontend and backend services.
5. Configure secrets using your hosting provider's environment-variable manager.
6. Avoid logging credentials or full request headers.
7. Review provider terms, subscription limits, rate limits and caching rules before public launch.

---

## Troubleshooting

### `CRICKET_API_KEY is missing from .env`

- Confirm `.env` is in the backend process's working directory.
- Check that the variable is spelled exactly `CRICKET_API_KEY`.
- Restart the backend after changing environment variables.

### `FOOTBALL_API_KEY is missing from .env`

- Confirm the variable is spelled exactly `FOOTBALL_API_KEY`.
- The current backend uses it for Football, Basketball and Volleyball.
- Restart the backend after updating `.env`.

### Frontend cannot reach the backend

- Confirm the backend is running on port `5000`.
- Open `http://localhost:5000/` to check the health route.
- Check the browser console and terminal for the actual request URL and error.
- Confirm that the frontend service base URL matches the backend host and port.

### API returns an authentication, subscription or rate-limit error

- Verify the key is active and belongs to the correct provider account.
- Confirm the account subscription includes the requested sport and endpoint.
- Check the provider dashboard for quota, rate-limit or account-status messages.
- Do not repeatedly retry a request if the provider has temporarily blocked or suspended access.

### A page shows no data

- Confirm the selected country, league, team, player ID and season are valid for that endpoint.
- Check whether the provider has data for that competition and season.
- Inspect the backend response and browser console.
- Distinguish an empty API response from a failed API request.

### Vite command is unavailable

- Run `npm install` in the frontend project directory.
- Check the root `package.json` scripts.
- Run the exact development script defined there.

---

## Deployment checklist

Before publishing a production build:

- [ ] Confirm all required provider API keys are active.
- [ ] Verify that the subscriptions permit every sport and endpoint being used.
- [ ] Configure `CRICKET_API_KEY` and `FOOTBALL_API_KEY` as secure backend environment variables.
- [ ] Ensure `.env` is ignored by Git and is not committed.
- [ ] Replace development-only frontend API URLs such as `http://localhost:5000/api/...` with a configurable production backend URL.
- [ ] Configure production CORS for the deployed frontend domain.
- [ ] Deploy the Express backend to a Node.js-compatible host.
- [ ] Deploy the Vite frontend to a static hosting provider or suitable web host.
- [ ] Test the backend health route.
- [ ] Test each sport's countries, leagues, teams, players/games and standings pages where applicable.
- [ ] Test loading, empty-data and error states.
- [ ] Check mobile and desktop layouts.
- [ ] Confirm provider rate limits and acceptable usage.
- [ ] Verify that no API key appears in browser source, network requests or Git history.

**Production configuration note:** The frontend service files in the reviewed source currently use `http://localhost:5000/api` as their backend base URL. A deployed frontend cannot use a visitor's own localhost to reach your hosted backend. Configure a production API base URL before deployment.

---

## Contributing

1. Create a feature branch.
2. Make focused changes.
3. Test the relevant frontend page and backend route.
4. Do not commit `.env`, credentials or private account data.
5. Open a pull request describing the change and the tests performed.

---

## License

No license file was present in the project archive reviewed for this README. Unless a license is added to the repository, the project should not be assumed to be open source or freely reusable.

---

## Acknowledgements

- Cricket Database API for cricket data.
- API-SPORTS for football, basketball and volleyball data.
- The open-source React, Vite, Node.js and Express ecosystems.
