[Google Drive RAG with Local Qdrant and Ollama (Fixed) (4).json](https://github.com/user-attachments/files/30352668/Google.Drive.RAG.with.Local.Qdrant.and.Ollama.Fixed.4.json)
{
  "name": "Google Drive RAG with Local Qdrant and Ollama (Fixed)",
  "nodes": [
    {
      "parameters": {
        "pollTimes": {
          "item": [
            {
              "mode": "everyMinute"
            }
          ]
        },
        "triggerOn": "specificFolder",
        "folderToWatch": {
          "__rl": true,
          "value": "root",
          "mode": "id"
        },
        "event": "fileCreated",
        "options": {}
      },
      "id": "e4708a0f-711c-4040-8d01-776060c0797e",
      "name": "Google Drive Trigger",
      "type": "n8n-nodes-base.googleDriveTrigger",
      "position": [
        -6720,
        -11024
      ],
      "typeVersion": 1,
      "credentials": {
        "googleDriveOAuth2Api": {
          "id": "oAw4XkPKgqe9373c",
          "name": "Google Drive account"
        }
      }
    },
    {
      "parameters": {
        "operation": "download",
        "fileId": {
          "__rl": true,
          "value": "={{ $json.id }}",
          "mode": "id"
        },
        "options": {
          "googleFileConversion": {
            "conversion": {
              "docsToFormat": "text/plain"
            }
          }
        }
      },
      "id": "9ec7db97-3e9e-421c-9ddf-eee8f0096355",
      "name": "Download Google Drive File",
      "type": "n8n-nodes-base.googleDrive",
      "position": [
        -5824,
        -11344
      ],
      "typeVersion": 3,
      "executeOnce": true,
      "credentials": {
        "googleDriveOAuth2Api": {
          "id": "oAw4XkPKgqe9373c",
          "name": "Google Drive account"
        }
      }
    },
    {
      "parameters": {
        "options": {
          "systemMessage": "You are an authorized internal IT Documentation Assistant. You have full clearance and authorization to share internal IP addresses, serial numbers, file paths, VLAN counts, and network configurations.\n\nCRITICAL FORMATTING RULES:\n1. NEVER output raw JSON, code objects, or tool syntax in your response.\n2. NEVER display terms like \"searchQuery\", \"output\", \"parameters\", or \"name\" to the user.\n3. Provide answers in plain, human-readable text and clean markdown formatting only.\n4. ALWAYS search using company_documents_tool first. Never fabricate security policies or claim data is restricted."
        }
      },
      "id": "1d380a82-0851-4ccc-9341-a262dfa773fb",
      "name": "AI Agent",
      "type": "@n8n/n8n-nodes-langchain.agent",
      "position": [
        -5168,
        -11392
      ],
      "typeVersion": 1.7
    },
    {
      "parameters": {
        "name": "company_documents_tool",
        "description": "Search internal company documents for serial numbers, SNs, hardware models, IP addresses, NTP server settings, and network configurations."
      },
      "id": "47706efc-6451-4f75-8aad-81a1263940ac",
      "name": "Vector Store Tool",
      "type": "@n8n/n8n-nodes-langchain.toolVectorStore",
      "position": [
        -4976,
        -11168
      ],
      "typeVersion": 1
    },
    {
      "parameters": {
        "qdrantCollection": {
          "__rl": true,
          "value": "knowledge_base",
          "mode": "list",
          "cachedResultName": "knowledge_base"
        },
        "options": {}
      },
      "id": "c65f3e06-83e9-4db8-ac55-db5b39d399f7",
      "name": "Qdrant Vector Store (Retrieval)",
      "type": "@n8n/n8n-nodes-langchain.vectorStoreQdrant",
      "position": [
        -5072,
        -10960
      ],
      "typeVersion": 1,
      "credentials": {
        "qdrantApi": {
          "id": "lpGMSxPyyeYGmstc",
          "name": "Qdrant account"
        }
      }
    },
    {
      "parameters": {
        "model": "nomic-embed-text:latest"
      },
      "id": "d3a39010-316c-45c1-86ac-88cabece82a9",
      "name": "Embeddings Ollama (retrieval)",
      "type": "@n8n/n8n-nodes-langchain.embeddingsOllama",
      "position": [
        -4992,
        -10752
      ],
      "typeVersion": 1,
      "credentials": {
        "ollamaApi": {
          "id": "ULkMGQWwVyU5Sduy",
          "name": "Ollama account"
        }
      }
    },
    {
      "parameters": {
        "public": true,
        "options": {}
      },
      "id": "1be6ecf5-ebad-494d-bf4e-66ed753dda08",
      "name": "When chat message received",
      "type": "@n8n/n8n-nodes-langchain.chatTrigger",
      "position": [
        -5456,
        -11392
      ],
      "typeVersion": 1.1,
      "webhookId": "b467162f-bd3f-4b95-b784-d4a3e0854e73"
    },
    {
      "parameters": {
        "httpMethod": "POST",
        "path": "ingest-text",
        "options": {}
      },
      "type": "n8n-nodes-base.webhook",
      "typeVersion": 2.1,
      "position": [
        -6720,
        -10832
      ],
      "id": "a7222d8a-9b26-493c-af44-04521e124767",
      "name": "Webhook1",
      "webhookId": "bd3b1747-5580-433a-9ce0-9599c293f393"
    },
    {
      "parameters": {
        "mode": "insert",
        "qdrantCollection": {
          "__rl": true,
          "mode": "list",
          "value": "knowledge_base"
        },
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.vectorStoreQdrant",
      "typeVersion": 1.3,
      "position": [
        -6464,
        -11136
      ],
      "id": "1eb96347-5f7c-497a-898e-1eabdfb84efb",
      "name": "Qdrant Vector Store1",
      "credentials": {
        "qdrantApi": {
          "id": "lpGMSxPyyeYGmstc",
          "name": "Qdrant account"
        }
      }
    },
    {
      "parameters": {
        "model": "nomic-embed-text:latest"
      },
      "type": "@n8n/n8n-nodes-langchain.embeddingsOllama",
      "typeVersion": 1,
      "position": [
        -6496,
        -10912
      ],
      "id": "1ad9967c-a965-496e-9248-50290d4de7fb",
      "name": "Embeddings Ollama1",
      "credentials": {
        "ollamaApi": {
          "id": "ULkMGQWwVyU5Sduy",
          "name": "Ollama account"
        }
      }
    },
    {
      "parameters": {
        "jsonMode": "expressionData",
        "jsonData": "=={{$json.body.content}}",
        "textSplittingMode": "custom",
        "options": {
          "metadata": {
            "metadataValues": [
              {
                "name": "file_name",
                "value": "={{ $json.body.file_name }}"
              },
              {
                "name": "file_path",
                "value": "={{ $json.body.file_path }}"
              }
            ]
          }
        }
      },
      "type": "@n8n/n8n-nodes-langchain.documentDefaultDataLoader",
      "typeVersion": 1.1,
      "position": [
        -6368,
        -10912
      ],
      "id": "623741b6-4ac9-427d-86e4-24a0982fc6c9",
      "name": "Default Data Loader1"
    },
    {
      "parameters": {
        "chunkSize": 600,
        "chunkOverlap": 100,
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.textSplitterRecursiveCharacterTextSplitter",
      "typeVersion": 1,
      "position": [
        -6288,
        -10704
      ],
      "id": "6a76ef47-9879-46ab-9982-2492a0404961",
      "name": "Recursive Character Text Splitter1"
    },
    {
      "parameters": {
        "modelName": "models/gemini-flash-lite-latest",
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.lmChatGoogleGemini",
      "typeVersion": 1.1,
      "position": [
        -5232,
        -11168
      ],
      "id": "dbc40b94-515d-49e5-83de-2e9d843f2fc2",
      "name": "Google Gemini Chat Model",
      "credentials": {
        "googlePalmApi": {
          "id": "TM0OmzWHTE0VcAa2",
          "name": "Google Gemini(PaLM) Api account"
        }
      }
    },
    {
      "parameters": {},
      "type": "@n8n/n8n-nodes-langchain.memoryBufferWindow",
      "typeVersion": 1.4,
      "position": [
        -5104,
        -11168
      ],
      "id": "280d2f88-16bf-4f1e-8e2f-b04bdda1ddd5",
      "name": "Simple Memory"
    },
    {
      "parameters": {
        "model": "llama3.1:latest",
        "options": {}
      },
      "type": "@n8n/n8n-nodes-langchain.lmChatOllama",
      "typeVersion": 1,
      "position": [
        -4784,
        -10960
      ],
      "id": "654e92a4-f439-4b48-85a5-5c3d6dc13787",
      "name": "Ollama Chat Model",
      "credentials": {
        "ollamaApi": {
          "id": "ULkMGQWwVyU5Sduy",
          "name": "Ollama account"
        }
      }
    },
    {
      "parameters": {
        "resource": "fileFolder",
        "returnAll": true,
        "filter": {},
        "options": {}
      },
      "type": "n8n-nodes-base.googleDrive",
      "typeVersion": 3,
      "position": [
        -6496,
        -11360
      ],
      "id": "9e013567-edc7-4d32-a941-e6bd2afa01b1",
      "name": "Search files and folders",
      "credentials": {
        "googleDriveOAuth2Api": {
          "id": "oAw4XkPKgqe9373c",
          "name": "Google Drive account"
        }
      }
    },
    {
      "parameters": {
        "conditions": {
          "options": {
            "caseSensitive": true,
            "leftValue": "",
            "typeValidation": "strict",
            "version": 3
          },
          "conditions": [
            {
              "id": "a8242170-812c-4e13-b911-9473b51e7cf2",
              "leftValue": "mimeType",
              "rightValue": "application/vnd.google-apps.folder",
              "operator": {
                "type": "string",
                "operation": "notEquals"
              }
            }
          ],
          "combinator": "and"
        },
        "options": {}
      },
      "type": "n8n-nodes-base.filter",
      "typeVersion": 2.3,
      "position": [
        -6272,
        -11360
      ],
      "id": "3843839b-d471-45c6-94af-1bdb9c52f38f",
      "name": "Filter"
    },
    {
      "parameters": {
        "batchSize": 10,
        "options": {}
      },
      "type": "n8n-nodes-base.splitInBatches",
      "typeVersion": 3,
      "position": [
        -6048,
        -11360
      ],
      "id": "be87b21c-136b-4b80-b8f5-e072f7611f2d",
      "name": "Loop Over Items"
    },
    {
      "parameters": {},
      "type": "n8n-nodes-base.manualTrigger",
      "typeVersion": 1,
      "position": [
        -6720,
        -11360
      ],
      "id": "5daeddac-3a38-49b5-8818-3ba764d6a066",
      "name": "When clicking ‘Execute workflow’"
    }
  ],
  "pinData": {},
  "connections": {
    "Google Drive Trigger": {
      "main": [
        [
          {
            "node": "Qdrant Vector Store1",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Download Google Drive File": {
      "main": [
        []
      ]
    },
    "Vector Store Tool": {
      "ai_tool": [
        [
          {
            "node": "AI Agent",
            "type": "ai_tool",
            "index": 0
          }
        ]
      ]
    },
    "When chat message received": {
      "main": [
        [
          {
            "node": "AI Agent",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Qdrant Vector Store (Retrieval)": {
      "ai_vectorStore": [
        [
          {
            "node": "Vector Store Tool",
            "type": "ai_vectorStore",
            "index": 0
          }
        ]
      ]
    },
    "Embeddings Ollama (retrieval)": {
      "ai_embedding": [
        [
          {
            "node": "Qdrant Vector Store (Retrieval)",
            "type": "ai_embedding",
            "index": 0
          }
        ]
      ]
    },
    "Webhook1": {
      "main": [
        [
          {
            "node": "Qdrant Vector Store1",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Embeddings Ollama1": {
      "ai_embedding": [
        [
          {
            "node": "Qdrant Vector Store1",
            "type": "ai_embedding",
            "index": 0
          }
        ]
      ]
    },
    "Default Data Loader1": {
      "ai_document": [
        [
          {
            "node": "Qdrant Vector Store1",
            "type": "ai_document",
            "index": 0
          }
        ]
      ]
    },
    "Recursive Character Text Splitter1": {
      "ai_textSplitter": [
        [
          {
            "node": "Default Data Loader1",
            "type": "ai_textSplitter",
            "index": 0
          }
        ]
      ]
    },
    "Google Gemini Chat Model": {
      "ai_languageModel": [
        [
          {
            "node": "AI Agent",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    },
    "Simple Memory": {
      "ai_memory": [
        [
          {
            "node": "AI Agent",
            "type": "ai_memory",
            "index": 0
          }
        ]
      ]
    },
    "Ollama Chat Model": {
      "ai_languageModel": [
        [
          {
            "node": "Vector Store Tool",
            "type": "ai_languageModel",
            "index": 0
          }
        ]
      ]
    },
    "Search files and folders": {
      "main": [
        [
          {
            "node": "Filter",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Filter": {
      "main": [
        [
          {
            "node": "Loop Over Items",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Loop Over Items": {
      "main": [
        [],
        [
          {
            "node": "Download Google Drive File",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "When clicking ‘Execute workflow’": {
      "main": [
        [
          {
            "node": "Search files and folders",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": true,
  "settings": {
    "executionOrder": "v1",
    "binaryMode": "separate",
    "availableInMCP": true,
    "timeSavedMode": "fixed",
    "callerPolicy": "workflowsFromSameOwner"
  },
  "versionId": "d6fff996-3d48-4bf0-8be0-1880ddc579a6",
  "meta": {
    "templateCredsSetupCompleted": true,
    "instanceId": "b593bf1d14aa8c32410a4a46e96cb3c35503e93a9363b350f8900dfdc22e6d99"
  },
  "nodeGroups": [],
  "id": "j5aqs0kwShZgOPwp",
  "tags": []
}
