# Ultra-Fast Voice AI Agent (LiveKit + Murf Falcon)

Real-time speech conversation powered by Deepgram STT, Google Gemini LLM, and Murf Falcon TTS — optimized with multilingual turn detection, VAD, BVC noise cancellation, and usage metrics.

## Features

- LiveKit Agents voice framework
- Deepgram `nova-3` streaming transcription (`backend/src/agent.py:68`)
- Google Gemini `gemini-2.5-flash` for responses (`backend/src/agent.py:71-73`)
- Murf Falcon TTS voice `en-US-matthew` (`backend/src/agent.py:76-81`)
- Multilingual turn detector + Silero VAD (`backend/src/agent.py:84-86`, `backend/src/agent.py:53-55`)
- Background Voice Cancellation BVC (`backend/src/agent.py:128-131`)
- Usage metrics logging (`backend/src/agent.py:103-114`)
- Production-ready backend `Dockerfile` (`backend/Dockerfile:42-69`)
- Next.js frontend with secure token endpoint (`frontend/app/api/connection-details/route.ts:12-16, 66-90`)

## Architecture

- `AgentSession` composes STT → LLM → TTS with turn detection and VAD (`backend/src/agent.py:64-89`)
- `Assistant` encapsulates the agent’s persona (`backend/src/agent.py:26-33`)
- Frontend calls `/api/connection-details` to obtain a participant token and LiveKit server URL (`frontend/app/api/connection-details/route.ts:20-57`)
- Client joins room and connects to the agent (`frontend/hooks/useRoom.ts:37-45`)

## Quick Start

Prerequisites:

- `python 3.9+`, `uv`, `node 18+`, `pnpm`
- LiveKit Cloud project with API key/secret
- Vendor keys for Murf, Google, Deepgram

1) Configure environment files

- `backend/.env.local`
  - `LIVEKIT_URL=wss://<your-livekit-cloud-url>`
  - `LIVEKIT_API_KEY=<your_api_key>`
  - `LIVEKIT_API_SECRET=<your_api_secret>`
  - `MURF_API_KEY=<your_murf_key>`
  - `GOOGLE_API_KEY=<your_google_key>`
  - `DEEPGRAM_API_KEY=<your_deepgram_key>`
- `frontend/.env.local`
  - `LIVEKIT_URL=https://<your-livekit-cloud-url>`
  - `LIVEKIT_API_KEY=<your_api_key>`
  - `LIVEKIT_API_SECRET=<your_api_secret>`

2) Install dependencies

```bash
# Backend
cd backend
uv sync
uv run python src/agent.py download-files

# Frontend
cd ../frontend
pnpm install
```

3) Run locally

```bash
# Backend (dev)
cd backend
uv run src/agent.py dev

# Frontend (dev)
cd ../frontend
pnpm dev
# open http://localhost:3000/
```

## Usage

- Open `http://localhost:3000/`
- Click Start and grant microphone permission
- Speak; you should hear Murf Falcon responses in real time

## Configuration Notes

- Env is loaded at backend start (`backend/src/agent.py:23`)
- Frontend token signing uses `LIVEKIT_API_KEY` and `LIVEKIT_API_SECRET` (`frontend/app/api/connection-details/route.ts:66-90`)
- Noise cancellation is enabled via `RoomInputOptions` (`backend/src/agent.py:125-132`)

## Testing

- Backend tests via `pytest` (`backend/tests/test_agent.py`)

```bash
cd backend
uv run pytest
```

## Deployment

- Build and run the backend container with the included `Dockerfile` (`backend/Dockerfile:42-69`)
- Deploy the worker to LiveKit Cloud and point the frontend to your Cloud URL

## Screenshots

- Frontend Start screen
- In-session UI with agent connected
- Backend terminal showing worker registration
- Metrics summary after session end

Add images to `docs/images/` and reference them here:

```markdown
![Start Call](docs/images/start-call.png)
![Session](docs/images/session.png)
![Worker Registered](docs/images/worker-registered.png)
![Metrics](docs/images/metrics.png)
```

## Credits

- LiveKit Agents and starter templates
- Murf Falcon TTS
- Deepgram STT
- Google Gemini LLM
