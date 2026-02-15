# 🌾 KrishiMitra AI – Requirements Document

## 1. Project Overview

### Vision
An AI-powered farming assistant that helps farmers detect crop diseases early, predict crop prices, receive weather alerts, and manage farm finances using a single voice-enabled platform.

### Target Audience
- Small & marginal farmers
- Farmers with low digital literacy
- Rural communities in India
- Smartphone & basic phone users

### Core Value Proposition
Unlike fragmented agriculture apps, KrishiMitra AI provides end-to-end farming support from sowing to selling in one simple platform.

---

## 2. Problem Statement

### Current Challenges

**Delayed Disease Detection**
- Crop diseases are identified too late → major crop loss

**Market Information Gap**
- Farmers sell crops without knowing future prices

**Weather Vulnerability**
- Lack of hyperlocal alerts leads to crop damage

**Digital Divide**
- Existing apps are complex and English-only

**Fragmented Tools**
- Farmers must use multiple apps for different needs

### Our Solution
A unified AI platform that:
- Detects crop disease from images
- Predicts mandi prices
- Sends weather risk alerts via SMS
- Tracks expenses & predicts profit
- Provides voice assistant in local language

---

## 3. Competitive Analysis

### vs Agri Weather Apps

| Feature | KrishiMitra | Weather Apps |
|---------|-------------|--------------|
| Disease Detection | ✅ | ❌ |
| Price Prediction | ✅ | ❌ |
| Expense Tracking | ✅ | ❌ |
| Voice Assistant | ✅ | ❌ |
| Focus | Farming Lifecycle | Weather Only |

### vs Mandi Apps

| Feature | KrishiMitra | Mandi Apps |
|---------|-------------|------------|
| Future Price Prediction | ✅ | ❌ |
| Weather Alerts | ✅ | ❌ |
| Disease Detection | ✅ | ❌ |
| Financial Tracking | ✅ | ❌ |

**Key Difference:** Others give data. We give AI decisions.

---

## 4. Unique Selling Propositions (USPs)

**USP 1 – All-in-One Farming Companion 🌾**
- Supports entire farming lifecycle: Plant → Protect → Predict → Profit

**USP 2 – Voice-First for Rural India 🎤**
- Farmers can speak instead of typing

**USP 3 – Predictive AI 🤖**
- Predicts disease, price, profit & weather risk

**USP 4 – Works Without Internet 📩**
- SMS alerts for basic phone users

**USP 5 – Financial Empowerment 💰**
- Helps farmers plan expenses & profits

---

## 5. Core Features

### Feature 1: Crop Disease Detection

**Description:** Upload leaf image → detect disease + treatment

**Requirements:**
- Image upload
- Disease classification
- Treatment suggestions
- Preventive tips

**Success Criteria:**
- Detection accuracy > 90%
- Result within 3 seconds

**Tech:** CNN model (SageMaker)

---

### Feature 2: Crop Price Prediction

**Description:** Predict future mandi prices

**Requirements:**
- Select crop + location
- 7–30 day forecast
- Best time to sell

**Success Criteria:**
- Accurate trend prediction
- Helps farmers choose selling time

**Tech:** Time Series ML (LSTM)

---

### Feature 3: Voice Assistant

**Description:** Farmers ask questions via voice

**Examples:**
- "Aaj ka weather?"
- "Tomato price kya hai?"

**Tech:** Amazon Transcribe + Polly + Bedrock

---

### Feature 4: Expense & Profit Tracker

**Description:**
- Track expenses via voice/manual entry
- Predict seasonal profit

**Success Criteria:**
- Easy to use for non-tech users

---

### Feature 5: Weather Risk Alerts

**Description:**
- Hyperlocal weather alerts
- SMS notifications

**Alerts:**
- Heavy rain
- Heat wave
- Storm
- Frost

---

## 6. User Workflows

### Workflow 1 – Disease Detection
1. Open app
2. Upload crop photo
3. AI detects disease
4. Farmer receives solution

### Workflow 2 – Daily Usage
1. Open dashboard
2. Check weather alert
3. Ask voice query
4. View price forecast
5. Log expenses

---

## 7. Non-Functional Requirements

### Performance
- App load < 3 seconds
- AI response < 3 seconds

### Security
- AWS Cognito authentication
- Secure APIs

### Scalability
- AWS Serverless architecture
- Auto-scaling Lambda

### Accessibility
- Local language voice support
- Simple UI for rural users

---

## 8. Success Metrics

### Impact
- Crop loss reduction
- Income increase
- Daily active users growth

### Usage
- Daily active users > 60%
- Voice usage > 50% of sessions

---

## 9. Future Enhancements
- Regional language support
- IoT sensor integration
- Marketplace integration
- Mobile app

---

## 10. Constraints
- AWS Free Tier
- 5-day hackathon timeline
- Limited datasets
