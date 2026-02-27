# n8n-flows
Repository that will consist of n8n flows for modern tasks and usecases

---

<video src="https://github.com/user-attachments/assets/ccd7343b-5634-443f-b1c9-7aabc403aef3
" controls="controls" muted autoplay loop></video>




## Workflows

---

### LC Code Flow
**Purpose:** Daily LeetCode problem delivery with AI-generated solutions and explanations

**Schedule:** Runs daily at 8:00 AM

**Flow:**
1. **Schedule Trigger** - Triggers workflow at 8 AM daily
2. **Code in JavaScript** - Generates random number (100-999) to select a random LeetCode problem
3. **HTTP Request** - Queries LeetCode GraphQL API to fetch problem details
4. **AI Agent** - Uses OpenAI GPT-4o-mini to generate comprehensive solution including:
   - Problem understanding
   - Optimal approach explanation
   - Python solution with comments
   - Complexity analysis
   - Edge cases
   - Optimization notes
5. **Markdown** - Converts AI response from Markdown to HTML
6. **Send a message** - Emails formatted solution to akashbavaanand@gmail.com via Gmail

**Requirements:**
- OpenAI API credentials
- Gmail OAuth2 credentials

---

### X Auto Tweeter
**Purpose:** AI-powered automatic tweet generation and publishing — takes any topic, researches trending tweets about it, and posts an insightful, developer-style thread directly to your X (Twitter) account.

**Trigger:** n8n Web Form (manual on-demand)

**Flow:**
1. **On Form Submission** *(Form Trigger)* — Presents a web form titled *"X Topic To Post About"* with a single input: `What's the Topic?`. Submitting the form kicks off the workflow.
2. **Search Tweets** *(X / Twitter node)* — Searches X for the top 10 recent tweets matching the submitted topic, using your connected X OAuth2 account.
3. **Code in JavaScript** *(Code node)* — Cleans the fetched tweets by stripping retweet prefixes (`RT @user:`), then bundles all tweet texts and count into a single payload for the AI agent.
4. **AI Agent** *(LangChain Agent node)* — Powered by **Google Gemini**, this agent acts as a sharp, opinionated tech blogger. It takes the aggregated tweets and writes **one punchy, insight-driven X post** with:
   - A strong technical hook or hot take as the lead
   - 2–3 concrete bullet points (what it does, why it matters, how to use it)
   - Developer-friendly language and tone (direct, nerdy, authentic)
   - Up to 10 relevant viral hashtags
   - Target length: ~600 characters
5. **Code in JavaScript1** *(Code node — Tweet Splitter)* — Splits the AI-generated post into tweet-sized blobs (default: 270 characters each). If multiple blobs are needed, auto-appends thread numbering like `(1/3)`, `(2/3)` etc.
6. **Reset Last Tweet ID** *(Code node)* — Clears any cached tweet ID from previous runs to ensure clean thread chaining.
7. **Loop Over Blobs** *(Split In Batches node)* — Iterates through each tweet blob one at a time.
8. **If First Tweet?** *(If node)* — Checks if the current blob is the first tweet (`blobIndex === 1`).
   - **True → Create First Tweet** — Posts the first blob as a standalone tweet.
   - **False → Inject lastTweetId → Create Reply Tweet** — Injects the stored tweet ID and posts subsequent blobs as threaded replies to the previous tweet.
9. **Store Tweet ID** *(Code node)* — Saves the ID of the just-posted tweet into workflow static data so the next blob can reply to it, forming the thread chain.

**Capabilities:**
- 🔍 Researches real-time X trends for any topic before writing
- 🤖 AI writes with authentic developer/indie-hacker voice (not corporate fluff)
- 🧵 Automatically handles long posts as properly chained Twitter threads
- 🏷️ Adds viral hashtags to boost reach
- ♻️ Stateful thread chaining — each reply correctly references the previous tweet
- 🚀 On-demand via a simple web form — no coding needed to trigger

**Requirements:**
- X (Twitter) OAuth2 API credentials connected in n8n (node: `X account`)
- Google Gemini (PaLM) API credentials connected in n8n (node: `Google Gemini(PaLM) Api account`)

**Setup Guide:**
1. Import `X-Auto-Tweeter-1.json` into your n8n instance via *Workflows → Import from file*.
2. Connect your **X OAuth2** credentials:
   - Go to the `Search Tweets`, `Create First Tweet`, and `Create Reply Tweet` nodes.
   - Select or create an X OAuth2 credential with read/write permissions.
3. Connect your **Google Gemini API** credentials:
   - Go to the `Google Gemini Chat Model` node.
   - Add your Google PaLM/Gemini API key.
4. Activate the workflow.
5. Open the form URL (visible in the `On form submission` node under *Test URL* or *Production URL*).
6. Enter any topic (e.g., `Claude Code`, `RAG pipelines`, `LangGraph`) and submit.
7. The workflow will search X, generate the post, and publish it (or thread) automatically.

**Customisation Tips:**
- **Change tweet chunk size:** In `Code in JavaScript1`, adjust `maxCharsPerBlob` (default: `270`) to a value up to `280`.
- **Change the AI persona/tone:** Edit the prompt in the `AI Agent` node to match your personal style.
- **Swap the LLM:** Replace the `Google Gemini Chat Model` sub-node with any other LangChain-compatible model (OpenAI, Claude, Mistral, etc.).
- **Disable thread numbering:** Set `addNumbering` to `false` in `Code in JavaScript1` if you prefer clean posts without `(1/2)` suffixes.

---
### Text To Speech (Google)
**Purpose:** Converts any text string into a high-quality MP3 audio file using the **Google Cloud Text-to-Speech API** (WaveNet voices), outputting a downloadable binary audio file directly from n8n.

**Trigger:** Manual — click *"Execute workflow"* in n8n

**Flow:**
1. **When clicking 'Execute Workflow'** *(Manual Trigger)* — Starts the workflow on demand from the n8n editor.
2. **HTTP Request** *(POST to Google TTS API)* — Sends a JSON payload to `https://texttospeech.googleapis.com/v1/text:synthesize` with:
   - `input.text` — The text to be converted to speech
   - `voice.languageCode` — `en-US` (American English)
   - `voice.name` — `en-US-Wavenet-D` (a high-quality WaveNet male voice)
   - `audioConfig.audioEncoding` — `MP3`
   - Authenticated via **Google OAuth2**
3. **Convert to File** *(Convert to File node)* — Reads the `audioContent` field (base64-encoded audio) from the API response and converts it into a binary MP3 file ready for download or downstream use.

**Capabilities:**
- 🎙️ Converts any text to natural-sounding speech using Google WaveNet voices
- 🎵 Outputs a ready-to-use MP3 binary file within the n8n workflow
- 🌍 Supports all Google TTS languages and voice profiles (easily configurable)
- ⚡ Lightweight 3-node pipeline — simple, fast, and extensible
- 🔗 Output can be chained to further nodes (e.g., save to Google Drive, send via email/Telegram, etc.)

**Requirements:**
- Google OAuth2 credentials connected in n8n (node: `Google account`) with **Cloud Text-to-Speech API** enabled in Google Cloud Console

**Setup Guide:**
1. Import `TextToSpeech-Google.json` into your n8n instance via *Workflows → Import from file*.
2. Connect your **Google OAuth2** credentials:
   - Go to the `HTTP Request` node and select or create a `Google OAuth2` credential.
   - Ensure the **Cloud Text-to-Speech API** is enabled in your [Google Cloud Console](https://console.cloud.google.com/apis/library/texttospeech.googleapis.com).
3. Edit the text input:
   - In the `HTTP Request` node, update the `jsonBody` field — change the `"text"` value under `"input"` to your desired text.
4. Click **Execute workflow** to run.
5. The output binary MP3 file will be available in the `Convert to File` node's output.

**Customisation Tips:**
- **Change the voice:** Update `voice.name` in the JSON body to any supported WaveNet or Neural2 voice (see [Google TTS voice list](https://cloud.google.com/text-to-speech/docs/voices)).
- **Change the language:** Update `voice.languageCode` (e.g., `en-GB`, `hi-IN`, `fr-FR`) along with a matching voice name.
- **Change output format:** Swap `audioEncoding` to `OGG_OPUS` or `LINEAR16` (WAV) if needed.
- **Dynamic text input:** Replace the hardcoded text with an n8n expression like `{{ $json.text }}` to feed text from a previous node (e.g., a form, webhook, or AI agent output).
- **Save the audio:** Chain a **Google Drive**, **S3**, or **Write Binary File** node after `Convert to File` to persist the MP3.
