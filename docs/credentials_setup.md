# 🔐 Credentials Setup Guide

This guide explains how to set up the required API credentials for running the **Multi-## 📋 Required Credentials

| Platform   | Credential Name        | Where Used (n8n Nodes)                   |
|------------|------------------------|------------------------------------------|
| OpenAI     | `OPENAI_API_KEY`       | `OpenAI Chat Model`, `Embeddings OpenAI` |
| Pinecone   | `PINECONE_API_KEY`     | `Pinecone Vector Store`, `Vector Tool`   |
| Slack      | `SLACK_BOT_TOKEN`      | `Slack Trigger`, `Slack Message Sender`  |
| Telegram   | `TELEGRAM_BOT_TOKEN`   | `Telegram Trigger`, `Telegram Sender`    |
| WhatsApp   | `WHATSAPP_TOKEN`       | `WhatsApp Trigger`, `WhatsApp Cloud API` |

---

## 🔑 1. OpenAI API Key

**Used for:**
- Embeddings
- GPT-4 based chat models via LangChain

**Steps:**
1. Go to [https://platform.openai.com/account/api-keys](https://platform.openai.com/account/api-keys)
2. Create a new secret key.
3. Copy the key into your `.env` file:

```env
OPENAI_API_KEY=sk-...
````

In n8n:

* Add a new credential: `OpenAI API`
* Paste your key

---

## 🌲 2. Pinecone Vector Store

**Used for:** Storing document embeddings and retrieving them via similarity search.

**Steps:**

1. Sign up at [https://www.pinecone.io](https://www.pinecone.io)
2. Create a project and index (e.g., `open`)
3. Go to **API Keys** in Pinecone Console
4. Save the following in your `.env`:

```env
PINECONE_API_KEY=your-key
PINECONE_ENVIRONMENT=your-env-id (e.g. us-east4-gcp)
```

In n8n:

* Add a `Pinecone API` credential with:

  * **API Key**
  * **Environment**

---

## 💬 3. Slack Bot

**Used for:** Receiving and sending messages via Slack.

**Steps:**

1. Go to [https://api.slack.com/apps](https://api.slack.com/apps)
2. Create a new app
3. Under **OAuth & Permissions**, add:

   * Scopes: `chat:write`, `channels:history`, `app_mentions:read`
4. Install to workspace → Copy Bot Token

```env
SLACK_BOT_TOKEN=xoxb-...
```

In n8n:

* Add a `Slack API` credential with your token

---

## 📲 4. Telegram Bot

**Used for:** Receiving and replying to Telegram messages.

**Steps:**

1. Open Telegram
2. Search for `@BotFather`
3. Create a new bot: `/newbot`
4. Save the **Bot Token**

```env
TELEGRAM_BOT_TOKEN=123456:ABCDEF...
```

In n8n:

* Add a `Telegram API` credential
* Use the Bot Token

---

## 📞 5. WhatsApp Business Cloud API

**Used for:** Receiving and sending messages via WhatsApp.

**Steps:**

1. Go to [https://developers.facebook.com/apps](https://developers.facebook.com/apps)
2. Create a Meta app
3. Under **WhatsApp**, follow setup:

   * Generate a test number & token
   * Save the phone number ID and token

```env
WHATSAPP_TOKEN=EAAG...
WHATSAPP_PHONE_NUMBER_ID=1234567890
```

In n8n:

* Create `WhatsApp Trigger API` and `WhatsApp API` credentials using your token

---

## ✅ After Setting Credentials

1. Go to `n8n > Credentials`
2. Create credentials for each service listed
3. Assign them to the respective nodes in your workflow
4. Ensure environment variables match those used in `.env` (if using CLI mode)

---

## 🛠️ Troubleshooting

* Double-check webhook URLs (especially for WhatsApp and Telegram)
* For Pinecone, verify index name matches what's configured in your workflow
* If Slack messages don’t trigger: ensure your bot is in the correct channel

---

Feel free to submit a GitHub Issue if you face trouble with credential setup.

