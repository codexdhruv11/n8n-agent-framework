# 🤖 Multi-Agent Conversational AI System

This project is a multi-channel conversational AI agent built using **n8n**, **LangChain**, **OpenAI**, and **Pinecone**. It receives messages from **Slack**, **Telegram**, and **WhatsApp**, processes them via LangChain agents, retrieves relevant knowledge from a vector store, and sends intelligent responses back to the user.

---

## ⚙️ Features

- 🌐 Works across Slack, WhatsApp, and Telegram
- 🧠 Uses GPT-4 and LangChain agents for reasoning
- 📄 Accepts PDF uploads and adds them to a vector-based knowledge base (Pinecone)
- 🔁 Dynamically routes messages to appropriate channels
- 🧩 Modular and extendable via n8n workflows

---

## 🚀 Quick Start
1. Clone the repo:
   ```bash
   git clone https://github.com/your-username/multi-agent.git
2. Set up environment variables:

   ```
   cp .env.example .env
   ```

3. Import the workflows in `n8n` and configure your credentials.

---

## 📂 Repo Structure

```
multi-agent/
├── workflows/               # Cleaned n8n workflows
├── docs/                    # Credential setup and system diagrams
├── assets/                  # Architecture and demo images
├── .env.example             # Sample config
└── README.md
```

---

## 📄 Documentation

* [Credentials Setup Guide](docs/credentials_setup.md)
* [Workflow Architecture](docs/architecture.md)

---

## 🧠 Built With

* [n8n](https://n8n.io/)
* [LangChain](https://www.langchain.com/)
* [OpenAI](https://platform.openai.com/)
* [Pinecone](https://www.pinecone.io/)

---

## 📜 License

MIT License

---

> Created by Dhruv Kaushik — Feel free to fork, extend, and explore!
