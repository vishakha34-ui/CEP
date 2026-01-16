# Quick Start Guide

## 🚀 Get Started in 5 Minutes

### Prerequisites
- Python 3.8+
- pip

### Step 1: Install Dependencies
```bash
cd c:\Users\User\Desktop\cep
pip install -r requirements.txt
```

### Step 2: Start Backend (Terminal 1)
```bash
python -m uvicorn backend.main:app --host 0.0.0.0 --port 8000 --reload
```

### Step 3: Start Dashboard (Terminal 2)
```bash
streamlit run dashboard/app.py --server.port 8501
```

### Step 4: Open in Browser
- **Dashboard**: http://localhost:8501
- **API Docs**: http://localhost:8000/docs

## 💡 Try a Sample Query

In the Streamlit dashboard:
1. Go to sidebar → "Sample Queries"
2. Select a disaster type
3. Click "Load Sample Query"
4. Click "Analyze Disaster"
5. View results in tabs

## 📊 System Overview

**Architecture**:
- 7 AI Agents working together
- Multi-Agent Orchestrator
- FastAPI Backend
- Streamlit Frontend
- RAG Knowledge Base

**Supported Disasters**:
- Earthquake
- Flood
- Wildfire
- Hurricane
- Tornado
- Tsunami
- Landslide
- Severe Storm
- Chemical Spill
- Nuclear Incident
- Pandemic

## 🔍 Key Features

✅ Intent Classification - Identifies disaster type and urgency  
✅ Risk Assessment - Analyzes severity and vulnerable populations  
✅ Information Retrieval - Fetches relevant disaster procedures  
✅ Guidance Generation - Creates actionable instructions  
✅ Verification - Ensures accuracy and compliance  
✅ Safety Checks - Prevents harmful advice  
✅ Orchestration - Coordinates all components  

## 📚 Documentation

- **Full Docs**: See README.md
- **API Docs**: http://localhost:8000/docs (when running)
- **Code Comments**: Comprehensive inline documentation

## ⚠️ Important

**In emergencies, always call 911 first!**

This system provides advisory information only. Follow official government guidance and emergency responders' instructions.

## 🆘 Troubleshooting

**API not connecting?**
- Ensure backend is running on Terminal 1
- Check http://localhost:8000/health

**Module errors?**
- Run: `pip install -r requirements.txt`
- Ensure activated virtual environment

**Port already in use?**
- Backend: `python -m uvicorn backend.main:app --port 8001`
- Dashboard: `streamlit run dashboard/app.py --server.port 8502`

## 📊 System Health

Monitor in sidebar or visit: http://localhost:8000/api/v1/system/status

## 🚨 Emergency Contact

- **Emergency Services**: 911
- **Red Cross**: 1-800-733-2767
- **FEMA**: 1-800-621-3362
- **Crisis Line**: 988

---

**Enjoy using the Disaster Management System! 🛡️**
