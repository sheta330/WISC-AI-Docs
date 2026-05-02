# WISC AI - Voice Integrated Smart Cash

> **Enterprise AI for Financial ERP Systems** | Local-First Architecture | Egyptian Arabic Natural Language Interface

[![.NET 8](https://img.shields.io/badge/.NET%208-512BD4?style=flat&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![Clean Architecture](https://img.shields.io/badge/Clean%20Architecture-00D4AA?style=flat)](https://blog.cleancoder.com/)
[![Ollama](https://img.shields.io/badge/Ollama-Local%20LLM-FF6B6B?style=flat)](https://ollama.com/)
[![Security First](https://img.shields.io/badge/Trust%20Layer-SQL%20Validator-E94560?style=flat)](./docs/security.md)

---

## Architecture at a Glance

```
┌─────────────────────────────────────────────────────────────┐
│  CLIENT LAYER                                               │
│  Angular │ Flutter │ Voice (Egyptian Arabic)              │
└────────────────────┬────────────────────────────────────────┘
                     │ POST /api/v2/UnifiedSmartQuery/query
┌────────────────────▼────────────────────────────────────────┐
│  SERVICE DECORATOR STACK (Additive Architecture)            │
│                                                             │
│  🎭 PersonaEnhancedQueryService   ← Egyptian Arabic persona │
│  ─────────────────────────────────────────────────────────  │
│  🔒 SecureSmartQueryService       ← Cross-tenant validation │
│  ─────────────────────────────────────────────────────────  │
│  ⚙️  SmartQueryServiceV2 (CORE)   ← Frozen, untouchable    │
└────────────────────┬────────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────────┐
│  ORCHESTRATION LAYER (The Brain)                            │
│  RagOrchestrator → CommandGenerator → IntentClassifier     │
└────────────────────┬────────────────────────────────────────┘
                     │ ILlmProvider
┌────────────────────▼────────────────────────────────────────┐
│  INFRASTRUCTURE LAYER                                       │
│  OllamaProvider (Local) │ SqliteVectorStore │ TokenLimiter │
└────────────────────┬────────────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────────────┐
│  DATA LAYER                                                 │
│  SQL Server (Production) │ SQLite (Offline) │ wisc_vectors.db│
└─────────────────────────────────────────────────────────────┘
```

---

## Why Decorator Pattern?

We follow **Additive-Only Architecture**. The Core (`SmartQueryServiceV2`) is frozen. New features are added via decorators that wrap the layer below:

```csharp
// Program.cs - Decorator Stack Registration
builder.Services.AddScoped<ISmartQueryServiceV2>(sp =>
{
    var core = sp.GetRequiredService<SmartQueryServiceV2>();
    
    // Layer 2: Security decorator (GAP-2)
    var securityWrapped = new SecureSmartQueryService(core, validator);