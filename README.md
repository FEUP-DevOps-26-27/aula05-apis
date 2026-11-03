# Aula 5 — Calling, Building, and Securing APIs
### Luís Gomes · MESW0023 · 4 Nov 2026

You'll build and consume APIs from both sides: calling a third-party LLM API, and exposing your own
endpoint that wraps it. The same skills apply directly to the group project, where each group exposes and
consumes APIs across microservices.

To get credit for this lab, demonstrate your work to me during the class.

## Deliverables
- [ ] Use an API key to invoke an LLM API and generate a schema-enforced JSON travel itinerary.
- [ ] Run the API endpoint with the LLM call implemented and demonstrate that it works with an example
  invocation.
- [ ] Commit your code without committing your credentials. Be ready to explain why hard-coding credentials
  is a bad idea, and what remedial steps you'd take if credentials were accidentally leaked.

## Getting started
Clone this repository.

The code implements a Flask web application that exposes an API endpoint for generating a structured travel
itinerary for a given destination. The API accepts a destination string and returns a JSON response
containing high-level travel information.

To generate this response, you will call an LLM. We suggest OpenAI's open-weight [gpt-oss](https://openai.com/index/introducing-gpt-oss/) model, hosted
by [Groq](https://groq.com/), via [LiteLLM](https://docs.litellm.ai/docs/) to abstract client-specific
details.

Install the dependencies listed in `requirements.txt` (a virtual environment is recommended):
```bash
python3 -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Start the Flask server with:
```bash
python3 app.py
```

Once running, the API will be available at:
```
http://localhost:8000/api/v1/itinerary
```

## Generate an LLM API key
We suggest an API key from Groq (the free tier is enough for this lab), but you're free to use others
(OpenAI, Anthropic, etc.). LiteLLM abstracts the differences; see the
[LiteLLM providers docs](https://docs.litellm.ai/docs/providers).
1. Sign into your Groq account and navigate to the [API keys console](https://console.groq.com/keys).
2. Generate a new API key.
3. Update the code in [`analyze.py`](./analyze.py) with the key and test it.

## Secure your credentials
The starter code hardcodes credentials. This is bad practice.

Research and discuss best practices: never hard-code credentials, never commit them to git, rotate secrets
regularly, encrypt secrets at rest/in transit where possible, and apply least-access privilege on machines
where credentials are stored as environment variables or local files.

Rewrite the code to load credentials from a file or an environment variable and commit the code without the
credentials. [`.env.example`](./.env.example) shows the expected variable name; `.env` itself is already
in `.gitignore`.

## Implement the call to the LLM
Using LiteLLM, implement the logic in `analyze.py` to call an LLM. We suggest
`groq/openai/gpt-oss-120b`, but you're free to use others.

Your implementation should:
- Make at least one LLM call using LiteLLM.
- Request a [structured JSON response](https://docs.litellm.ai/docs/completion/json_mode).
- Enforce (or validate) the response structure against a predefined schema with these fields:
  - `destination`
  - `price_range`
  - `ideal_visit_times`
  - `top_attractions`

Expected response shape:
```json
{
    "destination": "...",
    "ideal_visit_times": [
        ...
    ],
    "price_range": ...,
    "top_attractions": [
       ...
    ]
}
```

## Calling your own API
The Flask server serves a simple documentation page at:
```
http://localhost:8000/
```
and exposes the API endpoint:
```
GET http://localhost:8000/api/v1/itinerary
```
with a required query parameter `destination`.

Use [curl](https://curl.se/) or [Postman](https://www.postman.com/) to confirm your endpoint works:
```bash
curl "http://localhost:8000/api/v1/itinerary?destination=Porto"
```
[`aula05-apis.postman_collection.json`](./aula05-apis.postman_collection.json) has a sample request you can
import into Postman. Consider writing
[Postman test scripts](https://learning.postman.com/docs/tests-and-scripts/write-scripts/test-scripts/) to
check status codes and response structure.

## Additional resources
- [Red Hat: what are APIs](https://www.redhat.com/en/topics/api/what-are-application-programming-interfaces)
- [API design best practices](https://blog.stoplight.io/crud-api-design)
- [API endpoint best practices](https://www.telerik.com/blogs/7-tips-building-good-web-api)
- [LiteLLM documentation](https://docs.litellm.ai/)

---
Adapted from the [MLIP API lab](https://github.com/KaushikKoirala/mlip-api-lab) (Kaushik Koirala,
Carnegie Mellon University, Machine Learning in Production).
