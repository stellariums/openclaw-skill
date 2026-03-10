# Web Tools Reference

Web tools provide search and fetch capabilities for the AI agent. Multiple search providers are supported.

## How It Works

- `web_search`: Sends a query to a search provider and returns results.
- `web_fetch`: Fetches a web page and returns extracted content.

## Choosing a Search Provider

### Auto-Detection

OpenClaw detects the provider from available API keys:
1. Brave Search API (`BRAVE_API_KEY`)
2. Perplexity (via OpenRouter or direct)
3. Gemini (Google Search grounding)

Recent behavior notes:
- Onboarding/configure now exposes explicit provider selection instead of relying only on key auto-detection.
- Provider lists in pickers are kept neutral/alphabetized in newer releases; runtime behavior should not assume the UI order implies recommendation priority.

### Explicit Provider

```json5
{
  tools: {
    web: {
      search: {
        provider: "brave",      // "brave" | "perplexity" | "gemini"
        apiKey: "${BRAVE_API_KEY}",
      },
    },
  },
}
```

## Brave Search API

### Getting a Brave API Key

1. Go to [Brave Search API](https://brave.com/search/api/).
2. Create an account and get an API key.
3. Free tier available (limited queries/month).

### Where to Set the Key

### Brave `llm-context` Mode

Newer releases add an opt-in Brave mode that returns extracted grounding snippets with source metadata instead of the standard result shape.

```json5
{
  tools: {
    web: {
      search: {
        provider: "brave",
        apiKey: "${BRAVE_API_KEY}",
        brave: { mode: "llm-context" },
      },
    },
  },
}
```

Use `llm-context` when the user wants grounded snippets for answer synthesis; leave it unset for normal search result formatting.


```json5
// In ~/.openclaw/openclaw.json
{
  tools: {
    web: {
      search: {
        apiKey: "${BRAVE_API_KEY}",
      },
    },
  },
}
```

Or via environment variable:

```bash
export BRAVE_API_KEY="your-api-key"
```

## Using Perplexity (Direct or via OpenRouter)

### Getting an OpenRouter API Key

1. Go to [OpenRouter](https://openrouter.ai/).
2. Create an account and get an API key.

### Setting Up Perplexity Search

Recent compatibility notes:
- Perplexity can be used directly or through compatible OpenRouter/Sonar-style setups in older deployments.
- Newer releases add language / region / time filters, so provider-specific search tuning should happen here instead of being hard-coded into prompts.


```json5
{
  tools: {
    web: {
      search: {
        provider: "perplexity",
        // Configure via OpenRouter or direct Perplexity API
      },
    },
  },
}
```

### Available Perplexity Models

Check the [OpenRouter](https://openrouter.ai/) model catalog for current Perplexity models.

## Using Gemini (Google Search Grounding)

### Getting a Gemini API Key

1. Go to [Google AI Studio](https://aistudio.google.com/).
2. Generate an API key.

### Setting Up Gemini Search

```json5
{
  tools: {
    web: {
      search: {
        provider: "gemini",
      },
    },
  },
}
```

Notes:
- Gemini search uses Google Search grounding.
- Requires a valid Gemini API key.

## web_search

### Requirements

- A search API key (Brave, Perplexity, or Gemini).

### Config

```json5
{
  tools: {
    web: {
      search: {
        provider: "brave",
        apiKey: "${BRAVE_API_KEY}",
      },
    },
  },
}
```

### Tool Parameters

| Parameter | Description |
|---|---|
| `query` | Search query string |
| `provider` | Optional provider override (`brave`, `perplexity`, `gemini`) |
| `searchLang` | Language hint when supported by the provider |
| `region` / `timeRange` | Perplexity-style localization / recency filters in newer releases |
| Additional parameters depend on provider |

## web_fetch

### Requirements

- No API key needed (fetches pages directly).

### Config

```json5
{
  tools: {
    web: {
      fetch: {
        // Optional config
      },
    },
  },
}
```

### Tool Parameters

| Parameter | Description |
|---|---|
| `url` | URL to fetch |
| Additional options for content extraction |
