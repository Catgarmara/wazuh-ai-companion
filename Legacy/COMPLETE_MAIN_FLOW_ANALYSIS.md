# 🔄 **Complete Execution Flow Chart: `/main.py` → Full Application Startup**

## **🚀 Entry Point Flow with Decision Logic**

```
┌─────────────────────────────────────────────────────────────────┐
│                         START: python main.py                   │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                    PHASE 1: IMPORTS                             │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │   uvicorn       │  │   argparse      │  │   sys/pathlib   │ │
│  │ (web server)    │  │ (CLI parsing)   │  │ (path setup)    │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘ │
│                          │                                      │
│                          ▼                                      │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │        Add project root to Python path                     │ │
│  │        sys.path.insert(0, str(project_root))               │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                          │                                      │
│                          ▼                                      │
│  ┌─────────────────┐  ┌─────────────────────────────────────────┐ │
│  │ core.config     │  │         app.main                        │ │
│  │ get_settings()  │  │         FastAPI app                     │ │
│  └─────────────────┘  └─────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                    PHASE 2: CLI PARSING                         │
│                                                                 │
│  parser.add_argument("--host")     ←── CLI Option: Host IP     │
│  parser.add_argument("--port")     ←── CLI Option: Port        │
│  parser.add_argument("--reload")   ←── CLI Option: Dev Mode    │
│  parser.add_argument("--workers")  ←── CLI Option: Processes   │
│                          │                                      │
│                          ▼                                      │
│                   args = parser.parse_args()                   │
│                   settings = get_settings()                    │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                 PHASE 3: DECISION LOGIC                         │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │                HOST RESOLUTION                              │ │
│  │                                                             │ │
│  │  if args.host:          ←── Command line provided?         │ │
│  │      host = args.host   ←── YES: Use CLI argument          │ │
│  │  else:                  ←── NO: Fall back to config        │ │
│  │      host = settings.host  ←── Use settings.host          │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │                PORT RESOLUTION                              │ │
│  │                                                             │ │
│  │  if args.port:          ←── Command line provided?         │ │
│  │      port = args.port   ←── YES: Use CLI argument          │ │
│  │  else:                  ←── NO: Fall back to config        │ │
│  │      port = settings.port  ←── Use settings.port          │ │
│  └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                   PHASE 4: STARTUP INFO                         │
│                                                                 │
│  print(f"🚀 Starting {settings.app_name} v{settings.version}") │
│  print(f"🌐 Server: http://{host}:{port}")                     │
│  print(f"🔧 Environment: {settings.environment}")              │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│              PHASE 5: WORKERS DECISION LOGIC                    │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │                                                             │ │
│  │  if args.reload:               ←── Development mode?        │ │
│  │      workers = 1               ←── YES: Force single worker │ │
│  │  else:                         ←── NO: Production mode     │ │
│  │      workers = args.workers    ←── Use specified workers   │ │
│  │                                                             │ │
│  └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                PHASE 6: UVICORN SERVER LAUNCH                   │
│                                                                 │
│  uvicorn.run(                                                   │
│      "app.main:app",           ←── Points to FastAPI app       │
│      host=host,                ←── Resolved host               │
│      port=port,                ←── Resolved port               │
│      reload=args.reload,       ←── Development auto-reload     │
│      workers=workers,          ←── Worker processes            │
│      log_level=settings.log_level.lower()                      │
│  )                                                              │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│            UVICORN LOADS: "app.main:app"                        │
│                   ↓                                             │
│            ENTERS: app/main.py                                  │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
```

---

## **🏗️ FastAPI Application Creation Flow (`app/main.py`)**

```
┌─────────────────────────────────────────────────────────────────┐
│                   FASTAPI STARTUP SEQUENCE                      │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                  PHASE 7: LIFESPAN MANAGER                      │
│                                                                 │
│  @asynccontextmanager                                           │
│  async def lifespan(app: FastAPI):                              │
│      # STARTUP SEQUENCE                                         │
│      settings = get_settings()        ←── Load configuration   │
│      container = get_container()       ←── Dependency injection │
│      init_database()                  ←── Database setup       │
│      metrics.set_app_info()           ←── Metrics setup        │
│                                                                 │
│      yield  ←── APP RUNS HERE                                  │
│                                                                 │
│      # SHUTDOWN SEQUENCE                                        │
│      shutdown_database()              ←── Clean database       │
│      container.clear_scoped()         ←── Clean DI container   │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                PHASE 8: FASTAPI APP CREATION                    │
│                                                                 │
│  app = FastAPI(                                                 │
│      title=settings.app_name,         ←── "Wazuh AI Companion" │
│      version=settings.version,        ←── "2.0.0"              │
│      debug=settings.debug,            ←── True/False           │
│      lifespan=lifespan                ←── Startup/shutdown     │
│  )                                                              │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                 PHASE 9: MIDDLEWARE SETUP                       │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │                CORS Middleware                              │ │
│  │  allow_origins=settings.cors_origins                       │ │
│  │  allow_credentials=True                                     │ │
│  │  allow_methods=["*"]                                        │ │
│  │  allow_headers=["*"]                                        │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                          │                                      │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │              Audit Logging Middleware                       │ │
│  │  setup_audit_middleware(app)                               │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                          │                                      │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │               Security Middleware                           │ │
│  │  setup_middleware(app)                                      │ │
│  └─────────────────────────────────────────────────────────────┘ │
│                          │                                      │
│  ┌─────────────────────────────────────────────────────────────┐ │
│  │               Metrics Middleware                            │ │
│  │  setup_metrics_middleware(app)                             │ │
│  └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│               PHASE 10: ROUTE REGISTRATION                      │
│                                                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │  /api/v1/auth   │  │  /api/v1/chat   │  │  /api/v1/logs   │ │
│  │  (auth_router)  │  │  (chat_router)  │  │  (logs_router)  │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘ │
│                                                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │ /api/v1/audit   │  │/api/v1/analytics│  │  /api/v1/ai     │ │
│  │ (audit_router)  │  │(analytics_router│  │  (ai_router)    │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘ │
│                                                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │  /api/v1/siem   │  │  /api/v1/alerts │  │  /api/v1/threat │ │
│  │  (siem_router)  │  │  (alert_mgmt)   │  │ (threat_correl) │ │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘ │
│                                                                 │
│  ┌─────────────────┐  ┌─────────────────┐                     │
│  │     /ws/        │  │   /ws/siem      │                     │
│  │ (websocket)     │  │ (siem_websocket)│                     │
│  └─────────────────┘  └─────────────────┘                     │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│              PHASE 11: HEALTH CHECK ENDPOINTS                   │
│                                                                 │
│  @app.get("/health")                    ←── Basic health       │
│  @app.get("/health/detailed")           ←── Full system status │
│  @app.get("/health/{service_name}")     ←── Individual service │
│  @app.get("/metrics")                   ←── Prometheus metrics │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                 PHASE 12: APP INSTANCE                          │
│                                                                 │
│                  app = create_app()                             │
│                         │                                       │
│                    ✅ READY TO SERVE                           │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│                   SERVER RUNNING                                │
│                                                                 │
│  🌐 Listening on http://host:port                              │
│  📡 WebSocket connections ready                                 │
│  🗄️ Database connected                                         │
│  🧠 AI services initialized                                     │
│  📊 Metrics collection active                                   │
│  🔐 Authentication ready                                        │
└─────────────────────────────────────────────────────────────────┘
```

---

## **🎯 Key Decision Points Summary:**

| **Decision Point** | **Logic** | **File Location** |
|-------------------|-----------|------------------|
| **Host Selection** | `args.host or settings.host` | `/main.py:47` |
| **Port Selection** | `args.port or settings.port` | `/main.py:48` |
| **Worker Count** | `args.workers if not args.reload else 1` | `/main.py:59` |
| **Settings Creation** | `try: AppSettings() except: test_defaults` | `core/config.py:210-217` |
| **Environment Mode** | `settings.environment` (dev/staging/prod) | `core/config.py:170` |
| **Debug Mode** | `settings.debug` (True/False) | `core/config.py:171` |

---

## **🔄 Complete Function Call Chain:**

```
python main.py
├── main() 
│   ├── argparse.ArgumentParser()
│   ├── get_settings() → core/config.py:207
│   │   ├── AppSettings() → core/config.py:165
│   │   │   ├── DatabaseSettings()
│   │   │   ├── RedisSettings() 
│   │   │   ├── SecuritySettings()
│   │   │   ├── AISettings()
│   │   │   └── LogSettings()
│   │   └── Field validations (port ranges, secret key length, etc.)
│   └── uvicorn.run("app.main:app")
│       └── app/main.py
│           ├── lifespan() async context manager
│           │   ├── get_settings()
│           │   ├── get_container() → core/container.py:190
│           │   ├── init_database() → core/database.py
│           │   └── metrics.set_app_info()
│           ├── create_app()
│           │   ├── FastAPI() initialization
│           │   ├── CORSMiddleware setup
│           │   ├── setup_audit_middleware()
│           │   ├── setup_middleware()
│           │   ├── setup_metrics_middleware()
│           │   ├── 11 router inclusions (auth, chat, logs, etc.)
│           │   └── Health check endpoints
│           └── Return configured FastAPI app instance
```

---

## **📊 Configuration Loading Sequence:**

### **Core Settings Hierarchy (core/config.py)**

```
AppSettings
├── app_name: "Wazuh AI Companion"
├── version: "2.0.0"
├── environment: DEVELOPMENT|STAGING|PRODUCTION
├── debug: True|False
├── host: "0.0.0.0"
├── port: 8000
├── log_level: DEBUG|INFO|WARNING|ERROR|CRITICAL
├── cors_origins: ["*"] or comma-separated list
├── api_prefix: "/api/v1"
│
├── DatabaseSettings
│   ├── host: "localhost"
│   ├── port: 5432
│   ├── name: "wazuh_chat"
│   ├── user: "postgres"
│   ├── password: ""
│   ├── pool_size: 10
│   └── max_overflow: 20
│
├── RedisSettings
│   ├── host: "localhost"
│   ├── port: 6379
│   ├── db: 0
│   ├── password: None
│   └── max_connections: 20
│
├── SecuritySettings
│   ├── secret_key: (required, min 32 chars)
│   ├── jwt_algorithm: "HS256"
│   ├── access_token_expire_minutes: 30
│   ├── refresh_token_expire_days: 7
│   ├── password_min_length: 8
│   └── bcrypt_rounds: 12
│
├── AISettings
│   ├── ollama_host: "localhost"
│   ├── ollama_port: 11434
│   ├── ollama_model: "llama3"
│   ├── embedding_model: "all-MiniLM-L6-v2"
│   ├── chunk_size: 500
│   ├── chunk_overlap: 50
│   ├── max_tokens: 2048
│   └── temperature: 0.7
│
└── LogSettings
    ├── wazuh_logs_path: "/var/ossec/logs/archives"
    ├── default_days_range: 7
    ├── max_days_range: 365
    ├── ssh_timeout: 10
    └── log_batch_size: 1000
```

---

## **🛡️ Validation Rules Applied:**

### **Port Validations:**
- Database port: 1-65535
- Redis port: 1-65535
- Application port: 1-65535
- Ollama port: 1-65535

### **Security Validations:**
- Secret key: Minimum 32 characters
- Access token expiry: 1-1440 minutes (24 hours max)
- Bcrypt rounds: 4-20 (security vs. performance balance)
- Password minimum length: 8 characters

### **AI/ML Validations:**
- Chunk size: 100-2000 characters
- Temperature: 0.0-2.0 (creativity control)
- Redis DB: 0-15 (Redis supports 16 databases)

### **Log Processing Validations:**
- Default days range: 1-365 days
- Max days range: 1-365 days

---

## **🔧 Environment Variable Support:**

All settings can be overridden via environment variables:

```bash
# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=wazuh_chat
DB_USER=postgres
DB_PASSWORD=secretpassword

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_DB=0

# Security
SECRET_KEY=your-32-character-secret-key-here
JWT_ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30

# AI/Ollama
OLLAMA_HOST=localhost
OLLAMA_PORT=11434
OLLAMA_MODEL=llama3
EMBEDDING_MODEL=all-MiniLM-L6-v2

# Application
APP_NAME="Wazuh AI Companion"
APP_VERSION=2.0.0
ENVIRONMENT=production
DEBUG=false
HOST=0.0.0.0
PORT=8000
LOG_LEVEL=INFO
CORS_ORIGINS=http://localhost:3000,https://yourdomain.com
```

---

## **⚡ Command Line Examples:**

```bash
# Basic startup
python main.py

# Development mode with auto-reload
python main.py --reload

# Custom host and port
python main.py --host 127.0.0.1 --port 9000

# Production with multiple workers
python main.py --workers 4

# Full production setup
python main.py --host 0.0.0.0 --port 8080 --workers 8
```

---

## **🏁 Startup Sequence Summary:**

The system follows a **12-phase startup sequence** with **6 key decision points** that determine host, port, workers, environment settings, and fallback configurations. Every decision has a clear if/else path with sensible defaults!

1. **Import Phase** - Load dependencies and set Python path
2. **CLI Parsing** - Process command line arguments
3. **Decision Logic** - Resolve host/port with CLI override capability
4. **Startup Info** - Display configuration details
5. **Workers Logic** - Determine worker count based on mode
6. **Server Launch** - Start uvicorn with resolved settings
7. **Lifespan Manager** - Handle startup/shutdown procedures
8. **FastAPI Creation** - Initialize web application framework
9. **Middleware Setup** - Configure security, CORS, audit, metrics
10. **Route Registration** - Register all API endpoints and WebSocket handlers
11. **Health Endpoints** - Set up monitoring and health check endpoints
12. **Ready to Serve** - Application fully initialized and ready for requests

**Key Features:**
- ✅ Graceful fallback for all configuration options
- ✅ Comprehensive validation with clear error messages  
- ✅ Environment variable support for all settings
- ✅ Development vs. production mode handling
- ✅ Proper async lifecycle management
- ✅ Full dependency injection container setup
- ✅ Security-first middleware configuration
- ✅ Health monitoring and metrics collection
- ✅ WebSocket support for real-time features