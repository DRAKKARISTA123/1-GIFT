# Private AI Hub — Streamlit starter

Combines the user-facing ideas from two supplied repositories into one Streamlit app:
- **MentalChat16K / QLoRA repository**: represented as a dedicated wellbeing conversation mode. The supplied repository is training/evaluation code and does **not** itself include a ready-to-use mental-health model checkpoint.
- **Multi-route chatbot**: represented as a keyword-routed expert section and general assistant.

## Run locally (privacy-first)

1. Install Python 3.10+.
2. Install Ollama from https://ollama.com/
3. Pull a model, for example:
   ```bash
   ollama pull llama3.2
   ```
4. In this folder:
   ```bash
   python -m venv .venv
   # Windows:
   .venv\Scripts\activate
   # macOS/Linux:
   source .venv/bin/activate
   pip install -r requirements.txt
   streamlit run app.py
   ```

The app defaults to Ollama at `http://localhost:11434/api/chat`. Change the model or endpoint in the sidebar if needed.

## Optional Groq cloud mode

Choose **Groq cloud API** in the sidebar and enter your Groq API key. This sends prompts and recent conversation context to Groq. Do not use cloud mode for sensitive conversations unless you have reviewed the provider's current terms and privacy policy.

## Privacy and efficiency choices

- Local Ollama is the default; no external inference call is made in local mode.
- No LangSmith/LangChain tracing is enabled.
- Conversation history is held in Streamlit session state, not intentionally written to a database or file.
- Only the last 12 messages are sent as conversation context to bound request size.
- One shared model/provider is used across sections, avoiding duplicate model loading inside this app.
- Clear-history buttons are provided per section.

This is a privacy-conscious starter, not a guarantee of absolute privacy. Streamlit session state is not durable storage, but the host OS, browser, network, model manager, and deployment environment can still matter. Do not expose the app publicly without authentication and appropriate deployment security.

## Important limitations

- The first supplied repository is QLoRA fine-tuning code/data, not a plug-and-play mental-health model. This app uses a wellbeing system prompt; it does not reproduce the trained model's exact performance.
- The routing uses keyword heuristics for efficiency, so ambiguous questions may route imperfectly.
- The three sections share one model. Different expert prompts change response style, not the underlying model's capabilities.
- For clinical or crisis situations, this app is not a substitute for professional care or emergency services.

## Sources

- Streamlit docs: https://docs.streamlit.io/
- Ollama API docs: https://github.com/ollama/ollama/blob/main/docs/api.md
- Groq API docs: https://console.groq.com/docs/quickstart
- QLoRA repository: https://github.com/artidoro/qlora
- Supplied multi-route chatbot repository: https://github.com/Dhru3/multi-route-chatbot-using-langchain-framework


## Deploy from GitHub to Streamlit Community Cloud

GitHub stores the code; it does not run the app by itself. To host it:
1. Create a GitHub repository and upload `app.py`, `requirements.txt`, and `README.md` (you can upload the extracted files; do not upload a real API key).
2. Sign in at https://share.streamlit.io/ and deploy the repository, selecting `app.py` as the main file.
3. In the deployed app's **Settings → Secrets**, add:
   ```toml
   GROQ_API_KEY = "your_real_groq_api_key"
   ```
4. Reboot/redeploy the app. When this secret exists, the app selects Groq cloud mode by default.

**Hosting/privacy trade-off:** Streamlit Community Cloud cannot use the Ollama server running on your personal computer through `localhost`. For a hosted deployment, use a cloud inference provider such as Groq, which means prompts and recent conversation context are sent to that provider. If you need local-only inference, clone the repository and run Streamlit on the same machine as Ollama instead. Never commit API keys to GitHub; use Streamlit Secrets: https://docs.streamlit.io/deploy/streamlit-community-cloud/deploy-your-app/secrets-management
