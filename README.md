# TripMate AI - Multi-Agent Travel Planner

[![Live Web App](https://img.shields.io/badge/Live_App-tripmate--ai.onrender.com-46E3B7?style=for-the-badge&logo=render)](https://tripmate-ai-multi-agent-travel-planner-1hha.onrender.com/)

👉 **Live Demo Web App**: [https://tripmate-ai-multi-agent-travel-planner-1hha.onrender.com/](https://tripmate-ai-multi-agent-travel-planner-1hha.onrender.com/)

TripMate AI is an advanced, interactive multi-agent travel planning system built with **LangGraph**, **LangChain**, **FastAPI**, and **Model Context Protocol (MCP)** tools.

---

## 🌟 Features

- **Multi-Agent Orchestration**: Managed by a central Supervisor agent that routes requests to specialized agents (Flight Finder, Hotel & Stay, Itinerary Planner, Weather Forecast, Budget Estimator).
- **Model Context Protocol (MCP)**: Dynamic tool integrations for real-time web search (Tavily), flight data (AviationStack), and weather info (OpenWeather).
- **State Persistence**: Full state check-pointing backed by **PostgreSQL** (`PostgresSaver`).
- **Human-in-the-Loop (HITL)**: Interactive approval workflow before finalizing travel itineraries.
---

## 🏗️ Multi-Agent Architecture & Workflow Diagram

```mermaid
flowchart TD
    User["👤 User Request"] --> WebUI["🌐 FastAPI Web UI (app.py)"]
    WebUI --> Supervisor["🧠 Supervisor Agent & Input Guardrail"]
    
    Supervisor -->|Invalid / Off-topic| GuardrailBlock["⛔ Blocked Response"]
    Supervisor -->|Valid Travel Request| SpecialistRouter["🔀 Specialist Agent Router"]
    
    subgraph MCP_Tools ["🛠️ Specialist Agents & Model Context Protocol (MCP) Tools"]
        SpecialistRouter --> FlightAgent["✈️ Flight Agent (AviationStack MCP)"]
        SpecialistRouter --> HotelAgent["🏨 Hotel Agent (Tavily Search MCP)"]
        SpecialistRouter --> WeatherAgent["☀️ Weather Agent (OpenWeather MCP)"]
        SpecialistRouter --> BudgetAgent["💰 Budget Analyst Agent"]
    end
    
    FlightAgent --> ItineraryAgent["📋 Itinerary Synthesis Agent"]
    HotelAgent --> ItineraryAgent
    WeatherAgent --> ItineraryAgent
    BudgetAgent --> ItineraryAgent
    
    ItineraryAgent --> HITL["👤 Human-in-the-Loop Review (interrupt)"]
    
    HITL -->|User Request Revision| ItineraryAgent
    HITL -->|User Approved Draft| FinalAgent["✨ Final Polished Plan Generator"]
    
    FinalAgent --> PostgresDB[("💾 PostgreSQL Checkpointer (PostgresSaver State Persistence)")]
    PostgresDB --> UserResponse["🎉 Final Formatted Travel Plan"]
```

---

## 🚀 Quick Setup Guide

### 1. Prerequisites
- **Python 3.11+**
- **PostgreSQL Database** (Local instance or hosted on Render/Supabase/Neon)

### 2. Environment Setup

1. **Virtual Environment**:
   ```bash
   python -m venv .venv
   ```

2. **Activate Virtual Environment**:
   - **Windows (PowerShell)**:
     ```powershell
     .\.venv\Scripts\Activate.ps1
     ```
   - **Linux / macOS**:
     ```bash
     source .venv/bin/activate
     ```

3. **Install Dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

---

## 🔑 Environment Variables Configuration

Copy `.env.example` to `.env` and fill in your credentials:

```bash
cp .env.example .env
```

Edit `.env` with your API keys:

```env
# 1. Groq API Key (Required)
GROQ_API_KEY=your_groq_api_key

# 2. PostgreSQL Connection String (Required)
DATABASE_URL=postgresql://postgres:password@localhost:5432/tripmate_db

# 3. Tool API Keys
TAVILY_API_KEY=your_tavily_api_key
AVIATION_STACK_API_KEY=your_aviationstack_api_key
OPENWEATHER_API_KEY=your_openweather_api_key
```

---

## 🏃 Running the Application

### Option A: Direct Python Execution
```bash
python app.py
```
Or with `uvicorn`:
```bash
uvicorn app:app --reload --host 0.0.0.0 --port 8000
```
Open your browser and navigate to `http://localhost:8000`.

### Option C: Deploy on Render.com (1-Click Blueprint)
1. Push this repository to GitHub.
2. Log in to [Render.com](https://render.com) and click **New +** -> **Blueprint**.
3. Connect your GitHub repository. Render will automatically detect [render.yaml](file:///c:/Users/nidhi/OneDrive/Desktop/Multi-Agent-System/render.yaml), set up a free PostgreSQL database, and build your FastAPI web app.
4. Input your `GROQ_API_KEY`, `TAVILY_API_KEY`, `OPENWEATHER_API_KEY`, and `AVIATION_STACK_API_KEY` when prompted in the Render dashboard!

---

## 🛠 Project Structure

- `app.py`: FastAPI server handling web routes and API endpoints.
- `backend.py`: LangGraph state machine, agent supervisor, and agent nodes.
- `mcp_client.py`: Client wrappers for MCP servers (Tavily, AviationStack, Weather).
- `custom_weather_mcp_server.py`: FastMCP stdio weather server.
- `templates/`: HTML templates for frontend.
- `static/`: CSS and JavaScript assets.
