<a href="https://livekit.io/">
  <img src="./.github/assets/livekit-mark.png" alt="LiveKit logo" width="100" height="100">
</a>

# Multilingual AI Voice Agent with LiveKit & Gladia

Welcome! This repository contains the companion code for my video tutorial on building a **multilingual AI voice agent** using [LiveKit Agents](https://docs.livekit.io/agents/) and [Gladia STT](https://www.gladia.io/).

If you watched my video and want to build your own multilingual voice assistant that can seamlessly switch between languages in real-time, you're in the right place!

## What's Special About This Agent?

This voice AI agent features:

- **Multilingual speech-to-text** with [Gladia's Solaria model](https://docs.gladia.io/) supporting code-switching between languages (English and Polish in this example)
- **Multilingual turn detection** using LiveKit's advanced turn detector
- **Natural conversation flow** with contextually-aware speaker detection
- **Background noise cancellation** for crystal-clear audio
- **Production-ready setup** with Docker deployment support

## Prerequisites

Before you begin, you'll need to get API keys from the following services:

### Required API Keys

1. **LiveKit Cloud Account** (free tier available)
   - Sign up at [cloud.livekit.io](https://cloud.livekit.io/)
   - You'll need three values:
     - `LIVEKIT_URL`
     - `LIVEKIT_API_KEY`
     - `LIVEKIT_API_SECRET`
   - These can be automatically loaded using the [LiveKit CLI](https://docs.livekit.io/home/cli/cli-setup/) (see setup below)

2. **Gladia API Key** (for multilingual STT)
   - Sign up at [gladia.io](https://www.gladia.io/)
   - Get your API key from the dashboard
   - Set as `GLADIA_API_KEY` in your `.env.local` file

3. **OpenAI API Key** (optional if using LiveKit Inference)
   - Required for the LLM
   - Get it from [platform.openai.com](https://platform.openai.com/)
   - Set as `OPENAI_API_KEY` in your `.env.local` file

4. **ElevenLabs API Key** (optional if using LiveKit Inference)
   - Required for text-to-speech
   - Sign up at [elevenlabs.io](https://elevenlabs.io/)
   - Set as `ELEVEN_API_KEY` in your `.env.local` file

> **Note:** This project uses [LiveKit Inference](https://docs.livekit.io/agents/models/) which provides unified access to multiple AI models. You can also use model-specific API keys directly with [LiveKit model plugins](https://docs.livekit.io/agents/models/#plugins).

## Quick Start

### 1. Clone the Repository

```bash
git clone https://github.com/HugoPodworski/multilingual-livekit.git
cd multilingual-livekit
```

### 2. Install Dependencies

This project uses the `uv` package manager. If you don't have it installed:

```bash
# macOS/Linux
curl -LsSf https://astral.sh/uv/install.sh | sh

# Windows
powershell -c "irm https://astral.sh/uv/install.ps1 | iex"
```

Then install project dependencies:

```bash
uv sync
```

### 3. Configure Environment Variables

Create a `.env.local` file with your API keys:

```bash
cp .env.example .env.local
```

**Option A: Manual Configuration**

Edit `.env.local` and add your keys:

```env
# LiveKit Cloud credentials
LIVEKIT_URL=wss://your-project.livekit.cloud
LIVEKIT_API_KEY=your_api_key
LIVEKIT_API_SECRET=your_api_secret

# Gladia API key (required)
GLADIA_API_KEY=your_gladia_api_key

# Optional: If not using LiveKit Inference
OPENAI_API_KEY=your_openai_key
ELEVEN_API_KEY=your_elevenlabs_key
```

**Option B: Automatic Configuration with LiveKit CLI**

Install and configure the [LiveKit CLI](https://docs.livekit.io/home/cli/cli-setup/):

```bash
lk cloud auth
lk app env -w -d .env.local
```

This automatically populates your LiveKit credentials. You'll still need to manually add your Gladia API key and other service keys.

### 4. Download Required Models

Before running the agent for the first time, download the required AI models:

```bash
uv run python src/agent.py download-files
```

This downloads the [Silero VAD](https://docs.livekit.io/agents/build/turns/vad/) model and [LiveKit turn detector](https://docs.livekit.io/agents/build/turns/turn-detector/) files.

### 5. Run the Agent

**Test in your terminal:**

```bash
uv run python src/agent.py console
```

This lets you speak directly to your agent using your microphone and test the multilingual capabilities!

**Run for use with a frontend or telephony:**

```bash
uv run python src/agent.py dev
```

**Production mode:**

```bash
uv run python src/agent.py start
```

## Key Features Explained

### Multilingual Speech Recognition with Gladia

The agent uses Gladia's Solaria model configured for code-switching:

```python
stt=gladia.STT(
    model="solaria-1",
    languages=["en", "pl"],  # Add any languages you need
    region="eu-west",
    interim_results=True,
    code_switching=True  # Enables seamless language switching
)
```

### Multilingual Turn Detection

The LiveKit multilingual turn detector understands when speakers finish talking across different languages:

```python
turn_detection=MultilingualModel()
```

### Customizing Languages

To add or change languages, modify the `languages` parameter in `src/agent.py`:

```python
languages=["en", "es", "fr", "de"]  # English, Spanish, French, German
```

Check [Gladia's supported languages](https://docs.gladia.io/chapters/language/supported-languages) for the full list.

## Project Structure

```
.
├── src/
│   └── agent.py          # Main agent code
├── tests/
│   └── test_agent.py     # Agent behavior tests
├── Dockerfile            # Production deployment
├── pyproject.toml        # Dependencies
├── .env.local            # Your API keys (not tracked in git)
└── README.md             # This file
```

## Deployment

Deploy to production using the included Dockerfile:

```bash
docker build -t multilingual-agent .
docker run multilingual-agent
```

For deployment to LiveKit Cloud or other platforms, see the [production deployment guide](https://docs.livekit.io/agents/ops/deployment/).

## Useful Resources

- [LiveKit Agents Documentation](https://docs.livekit.io/agents/)
- [Gladia API Documentation](https://docs.gladia.io/)
- [LiveKit Cloud Dashboard](https://cloud.livekit.io/)
- [LiveKit Community Slack](https://livekit.io/join-slack)

## Coding with AI Assistants

This project works great with AI coding assistants like [Cursor](https://www.cursor.com/) or [Claude Code](https://www.anthropic.com/claude-code).

For the best experience, install the [LiveKit Docs MCP server](https://docs.livekit.io/mcp):

[![Install MCP Server](https://cursor.com/deeplink/mcp-install-light.svg)](https://cursor.com/en-US/install-mcp?name=livekit-docs&config=eyJ1cmwiOiJodHRwczovL2RvY3MubGl2ZWtpdC5pby9tY3AifQ%3D%3D)

See [AGENTS.md](AGENTS.md) for more details on working with this project using AI assistants.

## Troubleshooting

**"Module not found" errors:**
```bash
uv sync
```

**"No API key found":**
- Ensure `.env.local` exists and contains all required keys
- Check that you're in the project directory when running commands

**Audio not working:**
- Check microphone permissions
- Verify your audio device is working
- Try running `uv run python src/agent.py console` to test audio directly

**Multilingual switching not working:**
- Ensure `code_switching=True` in the Gladia STT configuration
- Verify you've specified the correct language codes
- Check Gladia dashboard for API usage and errors

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Questions or Issues?

If you have questions about this code or run into issues:

1. Check the [LiveKit Agents documentation](https://docs.livekit.io/agents/)
2. Review [Gladia's documentation](https://docs.gladia.io/)
3. Open an issue on this repository
4. Ask me directly in my [Voice AI HQ community](https://artiloai.link/skool)

---

Built with ❤️ using [LiveKit](https://livekit.io/) and [Gladia](https://www.gladia.io/)
