# Duplicate Bug Detector

A full-stack web application that detects duplicate bugs using AI-powered semantic similarity. It integrates with Azure DevOps (TFS) to fetch existing bugs and uses OpenAI embeddings to find matches.

## Architecture

```
duplicate-bug-detector/
├── client/                    # React frontend
│   ├── public/
│   │   └── index.html
│   ├── src/
│   │   ├── components/
│   │   │   ├── BugForm.js     # Input form for bug title & description
│   │   │   ├── BugForm.css
│   │   │   ├── ResultsList.js # Displays similar bugs with scores
│   │   │   ├── ResultsList.css
│   │   │   ├── Spinner.js     # Loading indicator
│   │   │   ├── Spinner.css
│   │   │   ├── ErrorMessage.js
│   │   │   └── ErrorMessage.css
│   │   ├── App.js
│   │   ├── App.css
│   │   ├── index.js
│   │   └── index.css
│   └── package.json
├── server/                    # Node.js + Express backend
│   ├── routes/
│   │   └── bugRoutes.js       # POST /api/check-duplicate
│   ├── services/
│   │   ├── azureDevOpsService.js  # Azure DevOps REST API integration
│   │   ├── embeddingService.js    # OpenAI / Azure OpenAI embeddings
│   │   └── similarityService.js   # Cosine similarity computation
│   ├── .env.example
│   ├── index.js
│   └── package.json
└── README.md
```

## How It Works

1. User enters a bug **title** and **description** in the React form
2. Frontend sends a `POST /api/check-duplicate` request to the Express server
3. Server fetches existing bugs from **Azure DevOps** via REST API (WIQL query)
4. Server generates **OpenAI embeddings** for both the user input and each existing bug
5. **Cosine similarity** is computed between the user input embedding and each bug embedding
6. Top 5 bugs exceeding the similarity threshold (default: 0.75) are returned
7. Frontend displays results with similarity scores and visual indicators

## Prerequisites

- **Node.js** >= 18.x
- **Azure DevOps** account with a Personal Access Token (PAT)
- **OpenAI API key** or **Azure OpenAI** deployment

## Setup

### 1. Configure Environment Variables

```bash
cd server
cp .env.example .env
```

Edit `server/.env` with your credentials:

| Variable | Description |
|---|---|
| `AZURE_DEVOPS_ORG_URL` | e.g. `https://dev.azure.com/your-org` |
| `AZURE_DEVOPS_PROJECT` | Your Azure DevOps project name |
| `AZURE_DEVOPS_PAT` | Personal Access Token with Work Items read scope |
| `OPENAI_API_KEY` | OpenAI API key (Option A) |
| `AZURE_OPENAI_API_KEY` | Azure OpenAI key (Option B) |
| `AZURE_OPENAI_ENDPOINT` | Azure OpenAI endpoint URL (Option B) |
| `AZURE_OPENAI_DEPLOYMENT` | Embedding deployment name (Option B) |
| `SIMILARITY_THRESHOLD` | Minimum similarity score, default `0.75` |

### 2. Install Dependencies & Run

**Server:**
```bash
cd server
npm install
npm run dev      # Development with auto-reload
# npm start      # Production
```

**Client (in a separate terminal):**
```bash
cd client
npm install
npm start
```

The React app runs on `http://localhost:3000` and the API server on `http://localhost:5000`.

## API Reference

### `POST /api/check-duplicate`

**Request Body:**
```json
{
  "title": "Login button not working on mobile",
  "description": "When tapping the login button on iOS Safari, nothing happens..."
}
```

**Response:**
```json
{
  "duplicates": [
    {
      "id": 12345,
      "title": "Login button unresponsive on mobile Safari",
      "description": "Users report that the login button does not respond...",
      "similarity": 0.9231
    }
  ]
}
```

## Security

- PAT and API keys stored in `.env` (never committed)
- HTTP security headers via Helmet
- Rate limiting (100 requests / 15 min)
- Input validation with length limits
- CORS restricted to the React dev server origin
- Request body size limited to 1 MB

## Accessibility

- WCAG 2.1 AA compliant
- Semantic HTML with proper `aria-*` attributes
- Keyboard navigable with visible focus indicators
- Screen reader announcements for loading and error states
- Respects `prefers-reduced-motion`

## License

MIT
