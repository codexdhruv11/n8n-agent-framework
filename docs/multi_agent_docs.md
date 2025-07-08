# 📘 Multi-agent Workflow Documentation

## 1. Workflow Title

**Multi\_agent**

---

## 2. Workflow Purpose

The `Multi_agent` workflow creates an AI-powered, multichannel conversational assistant capable of receiving user messages via Slack, Telegram, or WhatsApp. It processes those messages using LangChain agents, performs vector-based document retrieval via Pinecone, and responds back to the respective platforms. It also supports uploading and embedding PDFs into the knowledge base for future queries.

This is part of a larger **multi-agent architecture**, where knowledge ingestion and conversational querying are handled within a unified n8n pipeline.

---

## 3. Trigger Type

This workflow is triggered by **three main inputs**:

* **Slack Trigger** (`slackTrigger`): Listens to any event (e.g., mentions) in a Slack channel.
* **Telegram Trigger** (`telegramTrigger`): Captures incoming Telegram messages.
* **WhatsApp Trigger** (`whatsAppTrigger`): Listens for WhatsApp message updates.

There is also a **form-based trigger** (`formTrigger`) used to **upload a PDF document** for knowledge base ingestion.

---

## 4. Node Breakdown

### 📥 Input & Routing

* **On form submission** (`formTrigger`)

  * Type: `formTrigger`
  * Purpose: Accepts uploaded PDF files via a user-facing form
  * Output: Binary data for the file
  * No credentials required

* **Slack Trigger**

  * Type: `slackTrigger`
  * Purpose: Triggers on any Slack message event
  * Requires: `Slack API` credential

* **Telegram Trigger**

  * Type: `telegramTrigger`
  * Purpose: Captures Telegram messages
  * Requires: `Telegram Bot API` credential

* **WhatsApp Trigger**

  * Type: `whatsAppTrigger`
  * Purpose: Listens for incoming messages from WhatsApp
  * Requires: `WhatsApp Business API` credential

* **If is message1**

  * Type: `if`
  * Purpose: Checks if the Slack message contains a specific mention and is of type `app_mention`
  * Output: Routes only relevant Slack messages

### 🧠 Message Extraction & Channel Tagging

Each of the three platforms routes messages to a `set` node to:

* Extract the message
* Tag the channel type (`slack`, `telegram`, `whatsapp`)

Nodes involved:

* `Slack1`
* `Telegram`
* `Whatsapp`

→ All messages are then funneled through `Channel`, which standardizes the structure as:

```json
{
  "message": "Your input message",
  "channel": "telegram|slack|whatsapp"
}
```

---

### 🧠 AI Agent Core

* **AI Agent**

  * Type: `langchain.agent`
  * Agent Type: `conversationalAgent`
  * Input: `message` from user
  * Purpose: Executes dynamic reasoning using tools and language models
  * Connected to:

    * `OpenAI Chat Model2` → provides LLM reasoning
    * `Vector Store Tool` → vector-based document retrieval

* **OpenAI Chat Model2**

  * Type: `langchain.lmChatOpenAi`
  * Model: `gpt-4.1`
  * Used by: `AI Agent`
  * Requires: `OpenAI API` credential

* **Vector Store Tool**

  * Type: `langchain.toolVectorStore`
  * Connects to: `Pinecone Vector Store1` for retrieval
  * Used by: `AI Agent`

* **OpenAI Chat Model1**

  * Type: `langchain.lmChatOpenAi`
  * Used to drive vector search tool
  * Requires: `OpenAI API`

* **Pinecone Vector Store1**

  * Type: `langchain.vectorStorePinecone`
  * Provides semantic search interface
  * Requires: `Pinecone API` credential

---

### 📤 Channel-Specific Output

* **Switch**

  * Type: `switch`
  * Routes response to the correct channel:

    * WhatsApp → `WhatsApp Business Cloud`
    * Slack → `Send a message`
    * Telegram → `Telegram1`

* **WhatsApp Business Cloud**

  * Type: `whatsApp`
  * Sends message back via WhatsApp
  * Requires: `WhatsApp Business API` credential

* **Send a message**

  * Type: `slack`
  * Sends message to Slack using channel ID
  * Requires: `Slack API`

* **Telegram1**

  * Type: `telegram`
  * Sends message to Telegram using chat ID
  * Requires: `Telegram Bot API`

---

### 📄 Knowledge Base Creation Flow

* **Extract from File**

  * Type: `extractFromFile`
  * Extracts text from uploaded PDF
  * Input: Binary file from form
  * Output: Text content

* **Embeddings OpenAI**

  * Type: `langchain.embeddingsOpenAi`
  * Model: `text-embedding-3-large`
  * Creates vector embeddings
  * Requires: `OpenAI API`

* **Recursive Character Text Splitter**

  * Type: `textSplitterRecursiveCharacterTextSplitter`
  * Splits documents into manageable chunks
  * Output: Structured text segments

* **Default Data Loader**

  * Type: `documentDefaultDataLoader`
  * Converts split text into LangChain documents

* **Pinecone Vector Store**

  * Type: `vectorStorePinecone`
  * Stores the documents and their embeddings
  * Requires: `Pinecone API`

---

## 5. Credential Requirements

| Credential Name       | Used By Nodes                                                   |
| --------------------- | --------------------------------------------------------------- |
| OpenAI API            | `OpenAI Chat Model1`, `OpenAI Chat Model2`, `Embeddings OpenAI` |
| Pinecone API          | `Pinecone Vector Store`, `Pinecone Vector Store1`               |
| Slack API             | `Slack Trigger`, `Send a message`                               |
| Telegram Bot API      | `Telegram Trigger`, `Telegram1`                                 |
| WhatsApp Business API | `WhatsApp Trigger`, `WhatsApp Business Cloud`                   |

---

## 6. Inputs/Outputs

### Inputs:

* Incoming messages from Slack, Telegram, WhatsApp
* PDF files via form for knowledge ingestion

### Outputs:

* Responses back to the same channel (Slack, WhatsApp, Telegram)
* Stored vector embeddings in Pinecone for future use

---

## 7. How This Workflow is Called

* This is a **top-level workflow** with multiple triggers.
* It may be indirectly called via a form submission (embedding data) or direct chat messages.

No `Execute Workflow` nodes are used—this runs standalone but serves as the *main agent interface* for the system.

---

## 8. Notes for Extension

### 🔧 Possible Extensions

* Add fallback response node for AI failures.
* Add throttling to limit excessive message queries per user.
* Add summarization or classification nodes before storing knowledge.
* Add support for image or audio document ingestion.

### ✅ Assumptions

* All API credentials are pre-configured and valid.
* Pinecone index named `open` already exists.
* Channels (Slack, Telegram, WhatsApp) are properly set up and integrated.
* LangChain nodes are supported in your n8n version.

---

## 📎 Example JSON Flow (Telegram Message In → AI Agent → Telegram Response)

```json
{
  "channel": "telegram",
  "message": "What's our refund policy?"
}
```

Response is generated by AI Agent → routed via `Switch` → sent to user via `Telegram1`.

