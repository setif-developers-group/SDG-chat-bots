# 🤖 SDG Chat Bots - Intelligent Multi-Bot Platform

> **Build powerful, production-ready chatbots in minutes** with built-in infrastructure, function calling, smart routing, and Docker support.

A FastAPI-based intelligent chatbot platform powered by Google's Gemini AI, designed to make creating specialized chatbots incredibly easy while handling all the complex infrastructure for you.

---

## ✨ Why This Framework is Amazing

### 🚀 **Create Chatbots in Minutes, Not Days**

This isn't just another chatbot project - it's a **complete chatbot framework** that handles all the heavy lifting:

- ✅ **Zero Infrastructure Setup** - All the complex parts are already built
- ✅ **Just Add Your Content** - Define your bot's knowledge and you're done
- ✅ **Production Ready** - Rate limiting, CORS, error handling, and Docker included
- ✅ **Smart AI Routing** - Automatically switches between AI models to overcome rate limits
- ✅ **Function Calling Built-in** - Your bots can take actions, not just answer questions
- ✅ **Static Response Caching** - Lightning-fast responses for common questions

### 🎯 **What Makes This Special**

1. **📦 Complete Infrastructure Out of the Box**
   - Rate limiting with `slowapi`
   - CORS configuration for web apps
   - Redis-backed state management
   - Docker containerization
   - Automatic error handling
   - Request/response validation with Pydantic

2. **🧠 Intelligent Model Routing**
   - Automatically tracks API usage across multiple Gemini models
   - Seamlessly switches between models when rate limits are reached
   - Supports 6 different Gemini models with automatic fallback
   - Persistent state management across restarts
   - **Never worry about hitting API limits again!**

3. **⚡ Static Bot for Instant Responses**
   - Pre-defined answers for common questions
   - Zero API calls for frequently asked questions
   - Blazing fast response times
   - Perfect for FAQs and standard information

4. **🔧 Function Calling Made Easy**
   - Built-in support for AI-powered actions
   - Example: User registration system for events
   - Easy to extend with your own functions
   - Type-safe function declarations

5. **🎨 Multiple Specialized Bots**
   - Each bot has its own personality and knowledge base
   - Shared infrastructure, different purposes
   - Easy to add new bots by following the pattern

---

## 🏗️ Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    FastAPI Application                       │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  SDG Bot     │  │  Event Bot   │  │ Podcast Bot  │      │
│  │              │  │              │  │              │      │
│  │ + Static     │  │ + Static     │  │ + Static     │      │
│  │ + AI         │  │ + AI         │  │ + AI         │      │
│  │              │  │ + Functions  │  │              │      │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘      │
│         │                 │                  │              │
│         └─────────────────┴──────────────────┘              │
│                           │                                 │
│         ┌─────────────────▼──────────────────┐              │
│         │   Models Manager (Smart Routing)   │              │
│         │  ┌──────────────────────────────┐  │              │
│         │  │ gemini-2.5-flash-lite  ✓     │  │              │
│         │  │ gemini-2.0-flash-lite  ✓     │  │              │
│         │  │ gemini-2.5-flash       ✓     │  │              │
│         │  │ gemini-2.0-flash       ✓     │  │              │
│         │  │ gemini-1.5-flash       ✓     │  │              │
│         │  └──────────────────────────────┘  │              │
│         └─────────────────┬──────────────────┘              │
│                           │                                 │
│         ┌─────────────────▼──────────────────┐              │
│         │      Redis State Persistence       │              │
│         └────────────────────────────────────┘              │
└─────────────────────────────────────────────────────────────┘
```

---

## 🎯 Key Features in Detail

### 1. **Smart Model Routing System**

The `ModelsManager` is the brain of the operation:

- **Automatic Rate Limit Tracking**: Monitors both per-minute and per-day limits for each model
- **Intelligent Fallback**: Automatically switches to the next available model when limits are reached
- **Persistent State**: Uses Redis to maintain usage counts across server restarts
- **Timezone Aware**: Tracks limits in Pacific Time (Google's timezone for API quotas)
- **Priority-Based**: Uses faster, higher-limit models first, falls back to others as needed

**Models Supported** (in priority order):
1. `gemini-2.5-flash-lite` - 1000/day, 30/min
2. `gemini-2.0-flash-lite` - 200/day, 30/min
3. `gemini-2.5-flash` - 500/day, 10/min
4. `gemini-2.0-flash` - 200/day, 15/min
5. `gemini-1.5-flash` - 50/day, 15/min (fallback)

### 2. **Static Bot for Lightning-Fast Responses**

Before hitting the AI, every message is checked against:
- **Static Responses**: Pre-defined Q&A pairs
- **Info Dictionary**: Structured information lookup
- **Instant Return**: Zero latency, zero API costs

Perfect for:
- FAQs
- Contact information
- Operating hours
- Standard greetings

### 3. **Function Calling (AI-Powered Actions)**

The Event Bot demonstrates the power of function calling:

**Example: Workshop Registration**
```python
# The AI can automatically:
1. Understand user intent to register
2. Collect required information through conversation
3. Call the registration function with proper parameters
4. Confirm successful registration

# You just define the function and its declaration!
```

**Built-in Features**:
- Type-safe function declarations
- Automatic parameter validation
- Error handling and user feedback
- Easy to extend with new functions

### 4. **Production-Ready Infrastructure**

#### Rate Limiting
```python
# Automatic rate limiting on all endpoints
- Root endpoint: 10 requests/hour
- Chatbot endpoints: 100 requests/hour + 10 requests/minute
```

#### CORS Configuration
```python
# Pre-configured for common scenarios
- Production domains
- Development localhost
- Credentials support
```

#### Error Handling
- Model unavailability gracefully handled
- Function call errors caught and reported
- User-friendly error messages
- Detailed logging for debugging

### 5. **Docker Support**

**One-Command Deployment**:
```bash
docker-compose up --build
```

**Features**:
- Lightweight Alpine-based image
- Gunicorn + Uvicorn for production
- Environment variable support
- Port 8000 exposed by default

---

## 🚀 Quick Start Guide

### **Creating Your First Bot (In 3 Steps!)**

#### Step 1: Define Your Bot's Knowledge
```python
# In infos.py
my_bot_info = {
    "hello": "Hi! I'm your custom bot!",
    "help": "I can help you with..."
}

my_bot_static_replay = {
    "what is your name": "I'm CustomBot, nice to meet you!"
}
```

#### Step 2: Create System Instructions
```python
# In system_instructions.py
MY_BOT_INSTRUCTION = """You are a helpful assistant for...
Keep responses short and focused.
Only answer based on provided information."""
```

#### Step 3: Add Your Bot Function
```python
# In chat_bots.py
async def MyCustom_chatbot(msg: Massage) -> Massage:
    return await chatbot(
        msg, 
        MY_BOT_INSTRUCTION, 
        'MyBot', 
        my_bot_info,
        my_bot_static_replay,
        None  # Or add function declarations here
    )
```

#### Step 4: Add API Endpoint
```python
# In main.py
@app.post("/api/chatbot/custom")
@limiter.limit("100/hour")
@limiter.limit("10/minute")
async def custom_chatbot(request: Request, msg: Massage) -> Massage:
    return await MyCustom_chatbot(msg)
```

**That's it! Your bot is ready! 🎉**

---

## 📋 Prerequisites

- Python 3.13+
- Redis instance (for state management)
- Google Gemini API key
- Docker (optional, for containerized deployment)

---

## 🛠️ Installation & Setup

### 1. Clone the Repository
```bash
git clone https://github.com/setif-developers-group/SDG-chat-bots.git
cd SDG-chat-bots
```

### 2. Create Virtual Environment
```bash
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

### 4. Configure Environment Variables

Create a `.env` file in the root directory:

```env
# Google Gemini API
GOOGLE_API_KEY=your_gemini_api_key_here

# Redis Configuration
REDIS_HOST=your_redis_host
REDIS_PORT=6379
REDIS_USERNAME=default
REDIS_PASSWORD=your_redis_password

# API Configuration (for function calling)
API_URL=https://your-api-url.com
CHATBOT_API_KEY=your_api_key_here
```

### 5. Run the Application

#### Development Mode
```bash
uvicorn main:app --reload
```

#### Production Mode with Docker
```bash
docker-compose up --build
```

The application will be available at `http://localhost:8000`

---

## 📡 API Endpoints

### Health Check
```http
GET /
```

### SDG Information Bot
```http
POST /api/chatbot/sdg
Content-Type: application/json

{
  "message": "What is SDG?",
  "history": [],
  "summary": ""
}
```

### Event Management Bot (with Registration)
```http
POST /api/chatbot/event
Content-Type: application/json

{
  "message": "I want to register for the AI workshop",
  "history": [],
  "summary": ""
}
```

### Podcast Information Bot
```http
POST /api/chatbot/podcast
Content-Type: application/json

{
  "message": "Tell me about the latest episode",
  "history": [],
  "summary": ""
}
```

---

## 📊 Request/Response Schema

### Request Schema
```typescript
{
  message: string;           // User's message
  history: Array<{          // Conversation history
    role: "user" | "model";
    content: string;
  }>;
  summary?: string;         // Optional conversation summary
}
```

### Response Schema
```typescript
{
  message: string;          // Bot's response
  history: Array<{          // Updated conversation history
    role: "user" | "model";
    content: string;
  }>;
  summary?: string;         // Updated conversation summary
}
```

---

## 🔧 Advanced Features

### Adding Function Calling to Your Bot

#### 1. Define Your Function
```python
# In tools.py
async def my_custom_function(param1: str, param2: int) -> str:
    # Your logic here
    result = do_something(param1, param2)
    return result
```

#### 2. Create Function Declaration
```python
# In tools_declarations.py
my_function_declaration = {
    "name": "my_custom_function",
    "description": "What this function does...",
    "parameters": {
        "type": "object",
        "properties": {
            "param1": {
                "type": "string",
                "description": "Description of param1"
            },
            "param2": {
                "type": "integer",
                "description": "Description of param2"
            }
        },
        "required": ["param1", "param2"]
    }
}
```

#### 3. Add Function Execution Logic
```python
# In chat_bot.py - function_call_execution()
if function_call.name == "my_custom_function":
    args = function_call.args
    result = await my_custom_function(**args)
    return f"Function executed successfully: {result}"
```

#### 4. Enable in Your Bot
```python
# In chat_bots.py
async def MyBot_chatbot(msg: Massage) -> Massage:
    return await chatbot(
        msg, 
        MY_INSTRUCTION, 
        'MyBot', 
        my_info,
        my_static,
        [my_function_declaration]  # Enable function calling
    )
```

---

## 🐳 Docker Deployment

### Build and Run
```bash
# Build the image
docker build -t sdg-chatbots .

# Run with docker-compose
docker-compose up -d

# View logs
docker-compose logs -f

# Stop
docker-compose down
```

### Environment Variables in Docker
The `docker-compose.yaml` automatically loads your `.env` file.

---

## 📚 Project Structure

```
SDG-chat-bots/
├── main.py                    # FastAPI app & routes
├── chat_bots.py              # Bot implementations
├── chat_bot.py               # Core AI response generation
├── models_manager.py         # Smart model routing system
├── static_bot.py             # Static response handler
├── tools.py                  # Function implementations
├── tools_declarations.py     # Function declarations for AI
├── system_instructions.py    # Bot personalities & rules
├── infos.py                  # Knowledge bases & static data
├── schemas.py                # Pydantic models
├── util.py                   # Utility functions
├── sdg_exceptions.py         # Custom exceptions
├── requirements.txt          # Python dependencies
├── Dockerfile                # Docker configuration
├── docker-compose.yaml       # Docker Compose setup
└── README.md                 # This file
```

---

## 🎨 Built-in Bots

### 1. **SDG Bot** (`/api/chatbot/sdg`)
- Provides information about Setif Development Group
- Answers questions about mission, activities, membership
- Uses static responses for common queries

### 2. **Event Bot** (`/api/chatbot/event`)
- Information about SDG Skills Lab events
- **Function Calling**: Can register users for workshops
- Collects: name, email, phone, attendance type, workshop selection
- Integrates with external registration API

### 3. **Podcast Bot** (`/api/chatbot/podcast`)
- Information about SDG Podcast
- Details about episodes, guests, topics
- Links to podcast platforms

---

## 🔒 Security Features

- **Rate Limiting**: Prevents abuse with configurable limits
- **CORS Protection**: Whitelist-based origin control
- **Input Validation**: Pydantic schemas validate all requests
- **Error Sanitization**: User-friendly errors without exposing internals
- **API Key Protection**: Secure function calling with API keys

---

## 📈 Monitoring & Debugging

### Model Usage Tracking
```python
# Automatic logging of model usage
print(f"Using model: {current_model}")
print(f"Model usage: day_count={day_count}/{day_limit}, minute_count={minute_count}/{minute_limit}")
```

### Function Call Debugging
```python
# Detailed logging of function calls
print(f"Function call detected: {function_name} with args {args}")
print(f"Function response: {response}")
```

### State Persistence
- Usage counts saved to Redis on shutdown
- Automatically loaded on startup
- Survives server restarts

---

## 🚀 Performance Optimizations

1. **Static Response Cache**: Instant responses for common questions
2. **Async/Await**: Non-blocking I/O for better concurrency
3. **Model Prioritization**: Uses fastest models first
4. **Redis State**: Fast in-memory state management
5. **Lightweight Docker**: Alpine-based image for smaller footprint

---

## 📖 API Documentation

Once running, access interactive API documentation:

- **Swagger UI**: `http://localhost:8000/docs`
- **ReDoc**: `http://localhost:8000/redoc`

---

## 🤝 Contributing

We welcome contributions! Here's how:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Add tests if applicable
5. Commit your changes (`git commit -m 'Add amazing feature'`)
6. Push to the branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request

---

## 🐛 Troubleshooting

### Model Unavailable Error
- Check your Gemini API key in `.env`
- Verify you haven't exceeded all model limits
- Check Redis connection

### Function Call Errors
- Verify function declarations match function signatures
- Check API_URL and CHATBOT_API_KEY in `.env`
- Review function execution logs

### Rate Limit Issues
- Adjust limits in `main.py` if needed
- Check if Redis is properly persisting state
- Review model usage logs

---

## 📝 License

This project is open source and available under the [MIT License](LICENSE).

---

## 🌟 Why You'll Love This Framework

- ✅ **5 Minutes to First Bot**: Seriously, that's all it takes
- ✅ **Production Ready**: Used in real production environments
- ✅ **Scales Automatically**: Smart routing handles growth
- ✅ **Extensible**: Add features without touching core code
- ✅ **Well Documented**: Clear examples and patterns
- ✅ **Active Development**: Continuously improved

---

## 📞 Support & Contact

- **GitHub Issues**: [Report bugs or request features](https://github.com/setif-developers-group/SDG-chat-bots/issues)
- **Organization**: [Setif Development Group](https://github.com/setif-developers-group)

---

## 🎉 Get Started Now!

```bash
git clone https://github.com/setif-developers-group/SDG-chat-bots.git
cd SDG-chat-bots
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
# Add your .env file
uvicorn main:app --reload
```

**Your intelligent chatbot platform is ready in under 2 minutes!** 🚀

---

<div align="center">
  <strong>Built with ❤️ by the Setif Development Group</strong>
  <br>
  <sub>Making AI chatbots accessible to everyone</sub>
</div>
