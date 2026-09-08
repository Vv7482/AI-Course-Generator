# AI Course Generator

AI Course Generator is a full-stack application that uses Google Gemini to create structured courses from a topic. Users can register, generate courses, browse modules and lessons, view AI-enriched lesson content, search for related YouTube videos, and export lessons as PDF files.

## Features

- JWT-based registration and login
- AI-generated course outlines
- Lazy AI generation of lesson content
- Course, module, and lesson navigation
- Rich lesson blocks for headings, paragraphs, code, videos, and MCQs
- YouTube video lookup for video lesson blocks
- Client-side PDF export for lessons
- MongoDB persistence through Mongoose

## Project Structure

```text
AI-Course-Generator/
├── client/    # React, Vite, Tailwind CSS frontend
└── server/    # Express, MongoDB, and Gemini API backend
```

## Prerequisites

- Node.js 18 or later
- npm
- MongoDB database, local or hosted
- Google Gemini API key
- YouTube Data API v3 key for video blocks

## Installation

Clone the repository and install dependencies for both applications:

```bash
cd AI-Course-Generator

cd server
npm install

cd ../client
npm install
```

## Environment Variables

### Server

Create `server/.env`:

```env
MONGO_URI=mongodb://127.0.0.1:27017/ai-course-generator
JWT_SECRET=replace_with_a_long_random_secret
GEMINI_API_KEY=your_gemini_api_key
PORT=5000
```

`PORT` is optional and defaults to `5000`.

### Client

Copy `client/.env.example` to `client/.env`:

```env
VITE_API_URL=http://localhost:5000/api
VITE_YOUTUBE_API_KEY=your_youtube_api_key
```

Because Vite variables are bundled into the browser, restrict the YouTube API key by API, quota, and allowed referrer in Google Cloud.

## Running Locally

Start the backend in one terminal:

```bash
cd server
npm run dev
```

Start the frontend in a second terminal:

```bash
cd client
npm run dev
```

Open the Vite URL shown in the client terminal, normally:

```text
http://localhost:5173
```

The API health endpoint is available at:

```text
http://localhost:5000/api/health
```

## Available Scripts

### Client

| Command | Description |
| --- | --- |
| `npm run dev` | Start the Vite development server |
| `npm run build` | Create a production build |
| `npm run preview` | Preview the production build |
| `npm run lint` | Run the configured ESLint command |

### Server

| Command | Description |
| --- | --- |
| `npm start` | Start the Express server |
| `npm run dev` | Start the server with Nodemon |

## API Overview

All protected endpoints require a JWT bearer token in the `Authorization` header.

### Authentication

| Method | Endpoint | Description |
| --- | --- | --- |
| `POST` | `/api/auth/register` | Create a user account |
| `POST` | `/api/auth/login` | Authenticate a user |

### Courses

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/api/courses` | List the current user’s courses |
| `POST` | `/api/courses/generate` | Generate and save a course from a topic |
| `GET` | `/api/courses/:id` | Retrieve a course and its modules |
| `DELETE` | `/api/courses/:id` | Delete a course and its contents |

### Modules and Lessons

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/api/modules/:id` | Retrieve a module and its lessons |
| `GET` | `/api/lessons/:id` | Retrieve a lesson and enrich it with Gemini when needed |

## Generation Flow

1. The client sends a course topic to `POST /api/courses/generate`.
2. The server requests a JSON course outline from Gemini.
3. The course, modules, and placeholder lessons are saved to MongoDB.
4. When a lesson is opened, the server requests detailed lesson content from Gemini.
5. The client renders the returned content blocks.

## Production Notes

- Use a strong, private `JWT_SECRET`.
- Restrict CORS to the deployed frontend origin.
- Validate and schema-check Gemini responses before persisting them.
- Add ownership checks when reading modules and lessons through their parent course.
- Add rate limiting and generation quotas to control Gemini usage.
- Use a transaction or cleanup strategy during multi-document course creation.
- Do not commit `.env` files or API keys.
