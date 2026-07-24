# 🛠️ Installation & Deployment Guide

This guide provides step-by-step instructions for deploying the **Enterprise AI Knowledge Hub** on-premises using Docker, n8n, Qdrant, and Ollama.

---

## 📌 System Requirements

| Resource | Minimum Requirement | Recommended |
| :--- | :--- | :--- |
| **CPU** | 4 Cores | 8 Cores+ |
| **RAM** | 16 GB | 32 GB (Required for larger LLMs like `llama3.1`) |
| **Disk Space** | 30 GB SSD | 100 GB+ NVMe SSD |
| **OS** | Ubuntu 22.04 LTS / Debian 12 / RHEL 9 | Ubuntu 22.04 LTS |
| **Software** | Docker v24.0+ & Docker Compose v2.20+ | Latest Stable |

---

## 📁 Recommended Repository Layout

Ensure your repository directory is structured as follows:

```text
enterprise-ai-knowledge-hub/
├── .gitignore
├── LICENSE
├── README.md
├── SETUP.md
├── docker/
│   └── docker-compose.yml
├── workflows/
│   └── google-drive-rag-ollama-qdrant.json
└── scripts/
    └── init-models.sh

🚀 Deployment InstructionsStep 1: Clone the RepositoryBashgit clone [https://github.com/aslambasha557/enterprise-ai-knowledge-hub.git](https://github.com/aslambasha557/enterprise-ai-knowledge-hub.git)
cd enterprise-ai-knowledge-hub
Step 2: Configure Docker ComposeCreate or update docker/docker-compose.yml:YAMLversion: '3.8'

services:
  # --- Vector Database ---
  qdrant:
    image: qdrant/qdrant:v1.9.0
    container_name: qdrant-vectordb
    restart: always
    ports:
      - "6333:6333"
      - "6334:6334"
    volumes:
      - qdrant_data:/qdrant/storage
    networks:
      - rag-network

  # --- Local LLM & Embeddings Server ---
  ollama:
    image: ollama/ollama:latest
    container_name: ollama-engine
    restart: always
    ports:
      - "11434:11434"
    volumes:
      - ollama_data:/root/.ollama
    networks:
      - rag-network

  # --- Workflow Engine ---
  n8n:
    image: docker.n8n.io/n8nio/n8n:latest
    container_name: n8n-automation
    restart: always
    ports:
      - "5678:5678"
    environment:
      - N8N_HOST=localhost
      - N8N_PORT=5678
      - N8N_PROTOCOL=http
      - NODE_ENV=production
      - WEBHOOK_URL=http://localhost:5678/
      - GENERIC_TIMEZONE=UTC
    volumes:
      - n8n_data:/home/node/.n8n
    depends_on:
      - qdrant
      - ollama
    networks:
      - rag-network

volumes:
  qdrant_data:
  ollama_data:
  n8n_data:

networks:
  rag-network:
    driver: bridge
Start the containers in detached mode:Bashcd docker
docker compose up -d
Verify all 3 containers are healthy:Bashdocker ps
Step 3: Download Local AI ModelsDownload the required embedding and chat models inside the Ollama container:Bash# 1. Download Embedding Model (Used for Vector Storage)
docker exec -it ollama-engine ollama pull nomic-embed-text:latest

# 2. Download Chat LLM (Used for Local Reasoning)
docker exec -it ollama-engine ollama pull llama3.1:latest
Step 4: Configure n8n CredentialsOpen n8n in your browser at http://localhost:5678.Navigate to Credentials -> Add Credential and set up the following:1. Ollama APICredential Name: Ollama accountBase URL: http://ollama:11434 (Note: Use container service name ollama inside the Docker network)2. Qdrant APICredential Name: Qdrant accountHost: http://qdrant:6333 (Note: Use container service name qdrant)API Key: (Leave blank unless authentication is configured in Qdrant)3. Google Gemini APICredential Name: Google Gemini(PaLM) Api accountAPI Key: Paste your API key from Google AI Studio.4. Google Drive OAuth2Credential Name: Google Drive accountSetup OAuth2 credentials via Google Cloud Console with Google Drive API enabled. Set the OAuth Redirect URL provided by n8n.Step 5: Import and Activate WorkflowIn n8n, click Workflows -> Import from File.Upload workflows/google-drive-rag-ollama-qdrant.json.Verify that all vector, model, and tool nodes are correctly connected to their credentials.Toggle the workflow state to Active.🧪 Operational Testing & API Endpoints1. Ingest Text Document via WebhookSend a sample payload to index technical documentation into Qdrant:Bashcurl -X POST http://localhost:5678/webhook/ingest-text \
  -H "Content-Type: application/json" \
  -d '{
    "content": "Core Router R-01 IP is 10.200.1.1. BGP AS Number: 65001. Location: Datacenter Rack 4B.",
    "file_name": "network_topology_r01.txt",
    "file_path": "/infrastructure/network/"
  }'
2. Verify Vector Store RetrievalQuery your knowledge base through the interactive chat UI or Chat Trigger endpoint in n8n:Query Example: "What is the IP address and BGP AS number for Core Router R-01?"Expected Result: Clear, plain-text response citing 10.200.1.1 and AS 65001.🔧 Troubleshooting Common IssuesProblemCauseResolutionn8n cannot connect to OllamaUsing http://localhost:11434 inside DockerChange host URL in n8n credentials to http://ollama:11434Qdrant Connection FailedNetwork isolation issueEnsure all containers share rag-network in docker-compose.ymlOllama Out of Memory (OOM)Insufficient host RAM for llama3.1Allocate swap space or switch to llama3.2:1b for lower resource usage


📄 License
This project is licensed under the MIT License - see the LICENSE file for details.


---

### Commands to Push This to GitHub:

Open your terminal in your repository folder and run:

```bash
# Create and write SETUP.md or update repository files
git add SETUP.md docker/ workflows/ README.md
git commit -m "docs: add complete technical setup and deployment guide"
git push origin main
