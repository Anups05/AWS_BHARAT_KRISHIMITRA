# 🌾 KrishiMitra AI – Design Document

## TABLE OF CONTENTS
1. [Executive Summary](#executive-summary)
2. [System Architecture](#system-architecture)
3. [Database Design](#database-design)
4. [API Design](#api-design)
5. [Security Design](#security-design)
6. [UI/UX Design](#uiux-design)
7. [Component Design](#component-design)
8. [Data Flow](#data-flow)
9. [Technology Stack](#technology-stack)
10. [Performance & Scalability](#performance--scalability)
11. [User Workflows](#user-workflows)
12. [Competitive Analysis](#competitive-analysis)
13. [Success Metrics](#success-metrics)

---

## 1. Executive Summary

### Project Vision
KrishiMitra AI is an AI-powered farming assistant designed to empower Indian farmers with intelligent decision-making tools for disease detection, price prediction, weather alerts, and financial management.

### Key Design Principles
- **Simplicity First:** Voice-enabled interface for low-literacy users
- **Offline-First:** SMS fallback for basic phone users
- **AI-Driven:** Predictive intelligence for proactive farming decisions
- **Serverless:** Cost-effective AWS architecture within Free Tier
- **Scalable:** Auto-scaling to handle growing user base

### Design Goals
- Response time < 3 seconds for all AI operations
- 90%+ accuracy in disease detection
- Support for local languages (Hindi, regional languages)
- Mobile-first responsive design
- Minimal data usage for rural connectivity

---

## 2. System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                          │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  Web App     │  │  Mobile App  │  │  SMS Gateway │      │
│  │  (React)     │  │  (Future)    │  │  (Twilio)    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      API GATEWAY LAYER                       │
│              Amazon API Gateway (REST + WebSocket)           │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                    APPLICATION LAYER                         │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │   Disease    │  │    Price     │  │   Weather    │      │
│  │   Detection  │  │  Prediction  │  │    Alerts    │      │
│  │   Lambda     │  │   Lambda     │  │    Lambda    │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │    Voice     │  │   Expense    │  │     Auth     │      │
│  │  Assistant   │  │   Tracker    │  │    Lambda    │      │
│  │   Lambda     │  │   Lambda     │  │              │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      AI/ML LAYER                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  SageMaker   │  │   Bedrock    │  │  Transcribe  │      │
│  │  (CNN Model) │  │   (LLM)      │  │  + Polly     │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                      DATA LAYER                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐      │
│  │  DynamoDB    │  │      S3      │  │   RDS/Aurora │      │
│  │  (NoSQL)     │  │  (Images)    │  │  (Optional)  │      │
│  └──────────────┘  └──────────────┘  └──────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

### Architecture Components

**Frontend Layer**
- React.js web application
- Progressive Web App (PWA) for mobile
- SMS interface via Twilio

**API Layer**
- Amazon API Gateway for REST APIs
- WebSocket for real-time updates
- Rate limiting and throttling

**Compute Layer**
- AWS Lambda (serverless functions)
- Auto-scaling based on demand
- Cold start optimization

**AI/ML Layer**
- Amazon SageMaker for disease detection model
- Amazon Bedrock for conversational AI
- Amazon Transcribe for speech-to-text
- Amazon Polly for text-to-speech

**Data Layer**
- DynamoDB for user data and transactions
- S3 for image storage
- ElastiCache for caching (optional)

**External Services**
- Weather API integration
- Mandi price data sources
- SMS gateway (Twilio/SNS)

---

## 3. Database Design

### DynamoDB Tables

#### Table 1: Users
```
Table Name: krishi-users
Partition Key: userId (String)
Sort Key: -

Attributes:
- userId: String (UUID)
- phoneNumber: String
- name: String
- language: String (hi, en, regional)
- location: Object { state, district, village }
- crops: Array[String]
- registeredAt: Number (timestamp)
- lastActive: Number (timestamp)
```

#### Table 2: DiseaseDetections
```
Table Name: krishi-disease-detections
Partition Key: userId (String)
Sort Key: detectionId (String)

Attributes:
- detectionId: String (UUID)
- userId: String
- imageUrl: String (S3 path)
- cropType: String
- disease: String
- confidence: Number
- treatment: String
- detectedAt: Number (timestamp)
```

#### Table 3: PricePredictions
```
Table Name: krishi-price-predictions
Partition Key: crop (String)
Sort Key: date (String)

Attributes:
- crop: String
- location: String
- date: String (YYYY-MM-DD)
- predictedPrice: Number
- actualPrice: Number (nullable)
- confidence: Number
- trend: String (up, down, stable)
```

#### Table 4: Expenses
```
Table Name: krishi-expenses
Partition Key: userId (String)
Sort Key: expenseId (String)

Attributes:
- expenseId: String (UUID)
- userId: String
- category: String (seeds, fertilizer, labor, etc.)
- amount: Number
- description: String
- date: Number (timestamp)
- season: String
```

#### Table 5: WeatherAlerts
```
Table Name: krishi-weather-alerts
Partition Key: location (String)
Sort Key: alertId (String)

Attributes:
- alertId: String (UUID)
- location: String
- alertType: String (rain, storm, heat, frost)
- severity: String (low, medium, high)
- message: String
- validFrom: Number (timestamp)
- validTo: Number (timestamp)
- sentAt: Number (timestamp)
```

#### Table 6: VoiceInteractions
```
Table Name: krishi-voice-interactions
Partition Key: userId (String)
Sort Key: interactionId (String)

Attributes:
- interactionId: String (UUID)
- userId: String
- audioUrl: String (S3 path)
- transcript: String
- intent: String
- response: String
- timestamp: Number
```

### S3 Bucket Structure
```
krishi-mitra-storage/
├── crop-images/
│   ├── {userId}/
│   │   └── {detectionId}.jpg
├── voice-recordings/
│   ├── {userId}/
│   │   └── {interactionId}.mp3
└── ml-models/
    └── disease-detection-model/
```

---

## 4. API Design

### REST API Endpoints

#### Authentication APIs
```
POST /api/auth/register
Request: { phoneNumber, name, language, location }
Response: { userId, token }

POST /api/auth/login
Request: { phoneNumber, otp }
Response: { userId, token }

POST /api/auth/verify-otp
Request: { phoneNumber, otp }
Response: { verified: boolean }
```

#### Disease Detection APIs
```
POST /api/disease/detect
Headers: Authorization: Bearer {token}
Request: multipart/form-data { image, cropType }
Response: {
  detectionId,
  disease,
  confidence,
  treatment,
  preventiveMeasures
}

GET /api/disease/history
Headers: Authorization: Bearer {token}
Response: { detections: Array }
```

#### Price Prediction APIs
```
GET /api/price/predict
Headers: Authorization: Bearer {token}
Query: ?crop={crop}&location={location}&days={7-30}
Response: {
  crop,
  location,
  predictions: [
    { date, price, confidence, trend }
  ],
  bestSellingDate
}

GET /api/price/current
Query: ?crop={crop}&location={location}
Response: { crop, location, currentPrice, lastUpdated }
```

#### Voice Assistant APIs
```
POST /api/voice/query
Headers: Authorization: Bearer {token}
Request: multipart/form-data { audio }
Response: {
  transcript,
  intent,
  response,
  audioUrl (Polly generated)
}

POST /api/voice/text-query
Headers: Authorization: Bearer {token}
Request: { query, language }
Response: { response, audioUrl }
```

#### Weather APIs
```
GET /api/weather/current
Headers: Authorization: Bearer {token}
Query: ?location={location}
Response: {
  temperature,
  humidity,
  rainfall,
  forecast: Array
}

GET /api/weather/alerts
Headers: Authorization: Bearer {token}
Response: { alerts: Array }
```

#### Expense Tracker APIs
```
POST /api/expense/add
Headers: Authorization: Bearer {token}
Request: { category, amount, description, date }
Response: { expenseId, success }

GET /api/expense/list
Headers: Authorization: Bearer {token}
Query: ?season={season}&startDate={date}&endDate={date}
Response: { expenses: Array, totalAmount }

GET /api/expense/profit-prediction
Headers: Authorization: Bearer {token}
Response: { predictedProfit, expenses, expectedRevenue }
```

### WebSocket Events
```
Connection: wss://api.krishimitra.com/ws

Events:
- weather.alert: Real-time weather alerts
- price.update: Price changes for tracked crops
- system.notification: General notifications
```

---

## 5. Security Design

### Authentication & Authorization

**AWS Cognito Integration**
- User pool for authentication
- JWT tokens for API authorization
- OTP-based phone verification
- Token refresh mechanism

**Security Flow**
```
1. User registers with phone number
2. OTP sent via SMS
3. User verifies OTP
4. Cognito issues JWT token
5. Token included in API requests
6. Lambda authorizer validates token
```

### API Security

**Rate Limiting**
- 100 requests per minute per user
- 1000 requests per hour per user
- Throttling for abuse prevention

**Input Validation**
- Schema validation for all inputs
- File type validation for images
- Size limits: Images (5MB), Audio (10MB)

**Data Encryption**
- TLS 1.3 for data in transit
- S3 encryption at rest
- DynamoDB encryption enabled

### CORS Configuration
```javascript
{
  allowedOrigins: ['https://krishimitra.com'],
  allowedMethods: ['GET', 'POST', 'PUT', 'DELETE'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  maxAge: 3600
}
```

### IAM Policies
- Least privilege access for Lambda functions
- Separate roles for each service
- S3 bucket policies for image access
- DynamoDB fine-grained access control

---

## 6. UI/UX Design

### Design Principles

**Voice-First Interface**
- Large microphone button on home screen
- Visual feedback during voice input
- Text fallback for all voice features

**Simplicity**
- Maximum 3 taps to any feature
- Large touch targets (min 44px)
- High contrast colors for readability

**Localization**
- Hindi as primary language
- Regional language support
- Icon-based navigation

### Color Palette
```
Primary: #2E7D32 (Green - Agriculture)
Secondary: #FFA726 (Orange - Energy)
Accent: #1976D2 (Blue - Trust)
Success: #4CAF50
Warning: #FF9800
Error: #F44336
Background: #F5F5F5
Text: #212121
```

### Key Screens

#### 1. Home Dashboard
```
┌─────────────────────────────────┐
│  🌾 KrishiMitra AI              │
│  नमस्ते, राजेश जी               │
├─────────────────────────────────┤
│  ┌───────────────────────────┐  │
│  │   🎤  मुझसे पूछें         │  │
│  │   (Voice Assistant)       │  │
│  └───────────────────────────┘  │
│                                 │
│  ⚠️ Weather Alert               │
│  Heavy rain expected tomorrow   │
│                                 │
│  📊 Today's Prices              │
│  Tomato: ₹25/kg ↑               │
│  Onion: ₹18/kg ↓                │
│                                 │
│  ┌──────┐ ┌──────┐ ┌──────┐    │
│  │ 🌿   │ │ 💰   │ │ 🌤️   │    │
│  │Disease│ │Price │ │Weather│   │
│  └──────┘ └──────┘ └──────┘    │
└─────────────────────────────────┘
```

#### 2. Disease Detection Screen
```
┌─────────────────────────────────┐
│  ← Crop Disease Detection       │
├─────────────────────────────────┤
│  ┌───────────────────────────┐  │
│  │                           │  │
│  │   📷  Upload Photo        │  │
│  │   or                      │  │
│  │   📸  Take Photo          │  │
│  │                           │  │
│  └───────────────────────────┘  │
│                                 │
│  Recent Detections:             │
│  • Tomato - Late Blight         │
│  • Wheat - Rust Disease         │
└─────────────────────────────────┘
```

#### 3. Disease Result Screen
```
┌─────────────────────────────────┐
│  ← Detection Result             │
├─────────────────────────────────┤
│  [Leaf Image]                   │
│                                 │
│  🔴 Late Blight Detected        │
│  Confidence: 94%                │
│                                 │
│  💊 Treatment:                  │
│  • Apply Mancozeb fungicide     │
│  • Remove infected leaves       │
│  • Improve air circulation      │
│                                 │
│  🛡️ Prevention:                 │
│  • Avoid overhead watering      │
│  • Use resistant varieties      │
│                                 │
│  [🎤 Explain in Voice]          │
└─────────────────────────────────┘
```

#### 4. Price Prediction Screen
```
┌─────────────────────────────────┐
│  ← Price Forecast               │
├─────────────────────────────────┤
│  Crop: [Tomato ▼]               │
│  Location: [Delhi ▼]            │
│  Period: [7 Days ▼]             │
│                                 │
│  📈 Price Trend                 │
│  [Line Chart]                   │
│                                 │
│  Current: ₹25/kg                │
│  Predicted (7 days): ₹32/kg ↑   │
│                                 │
│  💡 Best Time to Sell:          │
│  March 20, 2026 (₹35/kg)        │
│                                 │
│  [Set Price Alert]              │
└─────────────────────────────────┘
```

#### 5. Voice Assistant Screen
```
┌─────────────────────────────────┐
│  🎤 Voice Assistant             │
├─────────────────────────────────┤
│                                 │
│      ┌─────────────┐            │
│      │             │            │
│      │   🎤 ●●●    │            │
│      │  Listening  │            │
│      │             │            │
│      └─────────────┘            │
│                                 │
│  You: "आज का मौसम कैसा है?"    │
│                                 │
│  🤖: "आज हल्की बारिश की         │
│      संभावना है। तापमान          │
│      28°C रहेगा।"               │
│                                 │
│  [🔊 Play Response]             │
│  [⌨️ Type Instead]              │
└─────────────────────────────────┘
```

#### 6. Expense Tracker Screen
```
┌─────────────────────────────────┐
│  ← Expense Tracker              │
├─────────────────────────────────┤
│  Total Expenses: ₹45,000        │
│  Expected Revenue: ₹75,000      │
│  Predicted Profit: ₹30,000 📈   │
│                                 │
│  [+ Add Expense]                │
│                                 │
│  Recent Expenses:               │
│  • Seeds - ₹5,000               │
│  • Fertilizer - ₹8,000          │
│  • Labor - ₹12,000              │
│  • Pesticide - ₹3,000           │
│                                 │
│  [📊 View Report]               │
└─────────────────────────────────┘
```

### Responsive Design
- Mobile-first approach (320px - 768px)
- Tablet support (768px - 1024px)
- Desktop view (1024px+)

---

## 7. Component Design

### Frontend Components (React)

```
src/
├── components/
│   ├── common/
│   │   ├── Button.jsx
│   │   ├── Card.jsx
│   │   ├── Input.jsx
│   │   ├── VoiceButton.jsx
│   │   └── LanguageSelector.jsx
│   ├── disease/
│   │   ├── ImageUpload.jsx
│   │   ├── DiseaseResult.jsx
│   │   └── TreatmentCard.jsx
│   ├── price/
│   │   ├── PriceChart.jsx
│   │   ├── CropSelector.jsx
│   │   └── PricePrediction.jsx
│   ├── voice/
│   │   ├── VoiceRecorder.jsx
│   │   ├── VoiceResponse.jsx
│   │   └── TranscriptDisplay.jsx
│   ├── weather/
│   │   ├── WeatherCard.jsx
│   │   ├── AlertBanner.jsx
│   │   └── ForecastList.jsx
│   └── expense/
│       ├── ExpenseForm.jsx
│       ├── ExpenseList.jsx
│       └── ProfitChart.jsx
```

### Backend Components (Lambda Functions)

```
lambdas/
├── auth/
│   ├── register.js
│   ├── login.js
│   └── verifyOtp.js
├── disease/
│   ├── detectDisease.js
│   ├── getHistory.js
│   └── preprocessImage.js
├── price/
│   ├── predictPrice.js
│   ├── getCurrentPrice.js
│   └── fetchMandiData.js
├── voice/
│   ├── processVoiceQuery.js
│   ├── transcribeAudio.js
│   └── generateSpeech.js
├── weather/
│   ├── getCurrentWeather.js
│   ├── getAlerts.js
│   └── sendSmsAlert.js
└── expense/
    ├── addExpense.js
    ├── listExpenses.js
    └── predictProfit.js
```

### ML Model Components

**Disease Detection Model**
```python
# SageMaker Training Script
model/
├── train.py
├── inference.py
├── preprocessing.py
└── requirements.txt

Architecture: CNN (ResNet50 Transfer Learning)
Input: 224x224 RGB image
Output: Disease class + confidence score
Classes: 20+ common crop diseases
```

**Price Prediction Model**
```python
# LSTM Time Series Model
price_model/
├── train_lstm.py
├── predict.py
├── data_preprocessing.py
└── requirements.txt

Architecture: LSTM with attention
Input: Historical price data (30 days)
Output: 7-30 day price forecast
Features: Price, season, weather, demand
```

---

## 8. Data Flow

### Disease Detection Flow
```
1. User uploads crop image
   ↓
2. Frontend → API Gateway → Lambda
   ↓
3. Lambda uploads image to S3
   ↓
4. Lambda invokes SageMaker endpoint
   ↓
5. CNN model processes image
   ↓
6. Model returns disease + confidence
   ↓
7. Lambda fetches treatment from DynamoDB
   ↓
8. Response sent to frontend
   ↓
9. Result displayed to user
```

### Voice Query Flow
```
1. User speaks query
   ↓
2. Frontend records audio
   ↓
3. Audio sent to Lambda
   ↓
4. Lambda → Transcribe (speech-to-text)
   ↓
5. Text → Bedrock (intent detection + response)
   ↓
6. Response → Polly (text-to-speech)
   ↓
7. Audio URL returned to frontend
   ↓
8. Frontend plays audio response
```

### Price Prediction Flow
```
1. User selects crop + location
   ↓
2. Frontend → API Gateway → Lambda
   ↓
3. Lambda fetches historical data
   ↓
4. Lambda invokes LSTM model
   ↓
5. Model predicts future prices
   ↓
6. Lambda calculates best selling date
   ↓
7. Response with predictions sent
   ↓
8. Frontend displays chart + recommendation
```

### Weather Alert Flow
```
1. Scheduled Lambda runs every hour
   ↓
2. Fetches weather data from API
   ↓
3. Analyzes for risk conditions
   ↓
4. If alert needed:
   ↓
5. Stores alert in DynamoDB
   ↓
6. Sends SMS via SNS/Twilio
   ↓
7. Pushes notification via WebSocket
   ↓
8. User receives alert
```

---

## 9. Technology Stack

### Frontend
- **Framework:** React.js 18+
- **State Management:** Redux Toolkit / Zustand
- **UI Library:** Material-UI / Tailwind CSS
- **Charts:** Recharts / Chart.js
- **Voice:** Web Speech API / MediaRecorder API
- **PWA:** Workbox for offline support
- **Build Tool:** Vite
- **Language:** TypeScript

### Backend
- **Compute:** AWS Lambda (Node.js 18.x / Python 3.11)
- **API:** Amazon API Gateway (REST + WebSocket)
- **Authentication:** AWS Cognito
- **Storage:** Amazon S3
- **Database:** Amazon DynamoDB
- **Caching:** Amazon ElastiCache (optional)

### AI/ML Services
- **Disease Detection:** Amazon SageMaker (TensorFlow/PyTorch)
- **LLM:** Amazon Bedrock (Claude/Llama)
- **Speech-to-Text:** Amazon Transcribe
- **Text-to-Speech:** Amazon Polly
- **Translation:** Amazon Translate

### DevOps & Monitoring
- **IaC:** AWS CDK / Terraform
- **CI/CD:** GitHub Actions / AWS CodePipeline
- **Monitoring:** Amazon CloudWatch
- **Logging:** CloudWatch Logs
- **Tracing:** AWS X-Ray
- **Alerts:** Amazon SNS

### External Services
- **Weather:** OpenWeatherMap API / Weather.gov
- **SMS:** Amazon SNS / Twilio
- **Mandi Prices:** Government APIs / Web scraping
- **Maps:** Google Maps API (optional)

---

## 10. Performance & Scalability

### Performance Targets

**Response Times**
- API Gateway latency: < 50ms
- Lambda cold start: < 1s
- Lambda warm execution: < 500ms
- Disease detection: < 3s
- Price prediction: < 2s
- Voice processing: < 3s
- Page load time: < 2s

**Throughput**
- 1000 concurrent users
- 10,000 requests per minute
- 100 disease detections per minute
- 500 voice queries per minute

### Optimization Strategies

**Frontend Optimization**
- Code splitting and lazy loading
- Image optimization (WebP format)
- CDN for static assets (CloudFront)
- Service worker for offline caching
- Compression (Gzip/Brotli)

**Backend Optimization**
- Lambda provisioned concurrency for critical functions
- DynamoDB on-demand capacity
- S3 Transfer Acceleration
- API Gateway caching (5 minutes)
- Connection pooling for external APIs

**ML Model Optimization**
- Model quantization for faster inference
- SageMaker endpoint auto-scaling
- Batch prediction for price forecasts
- Model caching in Lambda

**Database Optimization**
- DynamoDB GSI for query patterns
- Composite sort keys for efficient queries
- TTL for temporary data
- DynamoDB Streams for real-time updates

### Scalability Design

**Horizontal Scaling**
- Lambda auto-scales to demand
- API Gateway handles unlimited requests
- DynamoDB auto-scales read/write capacity
- S3 unlimited storage

**Vertical Scaling**
- Lambda memory: 512MB - 3GB based on function
- SageMaker instance: ml.t3.medium → ml.c5.xlarge

**Cost Optimization**
- Lambda: Pay per invocation
- DynamoDB: On-demand pricing
- S3: Lifecycle policies (move to Glacier after 90 days)
- CloudWatch: Log retention (7 days)
- SageMaker: Spot instances for training

### Caching Strategy
```
Layer 1: Browser cache (static assets)
Layer 2: CloudFront CDN (images, JS, CSS)
Layer 3: API Gateway cache (price data)
Layer 4: Lambda in-memory cache (model results)
Layer 5: ElastiCache (optional, for frequent queries)
```

---

## 11. User Workflows

### Workflow 1: New User Registration
```
1. User opens app
2. Selects language (Hindi/English/Regional)
3. Enters phone number
4. Receives OTP via SMS
5. Enters OTP
6. Fills profile (name, location, crops)
7. Completes onboarding tutorial
8. Lands on home dashboard
```

### Workflow 2: Disease Detection
```
1. User taps "Disease Detection" card
2. Chooses "Take Photo" or "Upload Photo"
3. Captures/selects crop leaf image
4. Selects crop type (optional)
5. Taps "Detect Disease"
6. Loading indicator shows (2-3s)
7. Results displayed:
   - Disease name
   - Confidence score
   - Treatment steps
   - Prevention tips
8. User can:
   - Listen to voice explanation
   - Save to history
   - Share with expert
```

### Workflow 3: Voice Query
```
1. User taps microphone button on home screen
2. Speaks query in local language
   Examples:
   - "आज का मौसम कैसा है?"
   - "टमाटर का भाव क्या है?"
   - "गेहूं में कौन सी बीमारी है?"
3. Voice visualizer shows recording
4. Processing indicator (1-2s)
5. Response displayed as text
6. Audio response auto-plays
7. User can:
   - Replay audio
   - Ask follow-up question
   - View related information
```

### Workflow 4: Price Prediction
```
1. User taps "Price Forecast" card
2. Selects crop from dropdown
3. Selects location (auto-filled from profile)
4. Chooses forecast period (7/15/30 days)
5. Taps "Get Prediction"
6. Chart displays with:
   - Historical prices
   - Predicted prices
   - Trend line
7. Recommendation shown:
   - Best selling date
   - Expected price
8. User can:
   - Set price alert
   - View market analysis
   - Share with others
```

### Workflow 5: Daily Usage
```
1. User opens app (morning)
2. Dashboard shows:
   - Weather alert (if any)
   - Today's crop prices
   - Pending tasks
3. User asks voice query about weather
4. Checks price forecast for tomato
5. Logs fertilizer expense (₹2000)
6. Receives SMS alert about rain
7. Uploads diseased leaf photo
8. Gets treatment recommendation
9. Closes app
```

### Workflow 6: Expense Tracking
```
1. User taps "Expense Tracker"
2. Taps "+ Add Expense"
3. Options:
   - Voice input: "बीज में पांच हजार रुपये"
   - Manual form: Category, Amount, Date
4. Expense saved
5. Dashboard updates:
   - Total expenses
   - Category breakdown
   - Profit prediction
6. User views monthly report
7. Exports data (optional)
```

---

## 12. Competitive Analysis

### Feature Comparison Matrix

| Feature | KrishiMitra | Agri Weather Apps | Mandi Apps | Crop Doctor Apps |
|---------|-------------|-------------------|------------|------------------|
| Disease Detection | ✅ AI-powered | ❌ | ❌ | ✅ Basic |
| Price Prediction | ✅ ML-based | ❌ | ⚠️ Current only | ❌ |
| Weather Alerts | ✅ Hyperlocal | ✅ Basic | ❌ | ❌ |
| Voice Assistant | ✅ Local language | ❌ | ❌ | ❌ |
| Expense Tracking | ✅ | ❌ | ❌ | ❌ |
| SMS Support | ✅ | ⚠️ Limited | ❌ | ❌ |
| Offline Mode | ✅ PWA | ❌ | ❌ | ❌ |
| Multi-language | ✅ | ⚠️ English only | ⚠️ Hindi only | ⚠️ Limited |
| Profit Prediction | ✅ | ❌ | ❌ | ❌ |
| All-in-One | ✅ | ❌ | ❌ | ❌ |

### Competitive Advantages

**1. Unified Platform**
- Single app for entire farming lifecycle
- No need to switch between multiple apps
- Consistent user experience

**2. AI-First Approach**
- Predictive intelligence, not just data
- Proactive recommendations
- Learning from user behavior

**3. Accessibility**
- Voice-first for low-literacy users
- SMS fallback for basic phones
- Local language support

**4. Cost-Effective**
- Free for farmers
- AWS Free Tier keeps costs low
- No premium features paywall

**5. Rural-Focused Design**
- Works on slow networks
- Minimal data usage
- Offline capabilities

### Market Positioning
```
High Tech ↑
         │
         │  [KrishiMitra AI]
         │  (AI + Voice + All-in-One)
         │
         │  [Crop Doctor Apps]
         │  (Disease only)
         │
         │  [Mandi Apps]     [Weather Apps]
         │  (Price only)     (Weather only)
         │
Low Tech ↓
         └────────────────────────────→
         Single Feature    Multi-Feature
```

---

## 13. Success Metrics

### Key Performance Indicators (KPIs)

#### User Acquisition
- **Target:** 10,000 users in 6 months
- **Metric:** New registrations per week
- **Goal:** 400 new users/week

#### User Engagement
- **Daily Active Users (DAU):** > 60%
- **Weekly Active Users (WAU):** > 80%
- **Monthly Active Users (MAU):** > 90%
- **Session Duration:** > 5 minutes
- **Sessions per User:** > 3 per day

#### Feature Adoption
- **Voice Usage:** > 50% of interactions
- **Disease Detection:** > 30% of users weekly
- **Price Prediction:** > 40% of users weekly
- **Expense Tracking:** > 25% of users weekly

#### Technical Performance
- **API Success Rate:** > 99.5%
- **Average Response Time:** < 3 seconds
- **Disease Detection Accuracy:** > 90%
- **Price Prediction Accuracy:** > 75%
- **App Crash Rate:** < 0.1%

#### Business Impact
- **Crop Loss Reduction:** 20% decrease
- **Income Increase:** 15% average increase
- **User Satisfaction:** > 4.5/5 rating
- **Retention Rate:** > 70% after 3 months

#### Cost Metrics
- **AWS Cost per User:** < $0.50/month
- **Total Monthly Cost:** < $5,000
- **Cost per Transaction:** < $0.01

### Analytics Implementation

**Event Tracking**
```javascript
// User events
- user_registered
- user_login
- feature_used (disease/price/voice/expense)
- voice_query_submitted
- disease_detected
- price_predicted
- expense_added
- alert_received

// Technical events
- api_call_success
- api_call_failure
- model_inference_time
- page_load_time
```

**Dashboards**
- Real-time user activity
- Feature usage trends
- Error rate monitoring
- Cost tracking
- Model performance

**Tools**
- Amazon CloudWatch for logs
- AWS X-Ray for tracing
- Custom dashboard in React
- Weekly email reports

### A/B Testing Strategy

**Test 1: Voice vs Text Input**
- Variant A: Voice button prominent
- Variant B: Text input prominent
- Metric: Engagement rate

**Test 2: Disease Detection Flow**
- Variant A: Auto-detect crop type
- Variant B: Manual crop selection
- Metric: Completion rate

**Test 3: Price Alert Frequency**
- Variant A: Daily alerts
- Variant B: Weekly alerts
- Metric: User retention

### Feedback Collection

**In-App Surveys**
- Post-detection satisfaction
- Feature request form
- Bug report form

**User Interviews**
- Monthly calls with 10 active users
- Understand pain points
- Gather feature ideas

**Analytics Review**
- Weekly team review
- Monthly stakeholder report
- Quarterly strategy adjustment

---

## Appendix

### Glossary
- **Mandi:** Agricultural market/marketplace
- **Kharif:** Monsoon crop season
- **Rabi:** Winter crop season
- **Hyperlocal:** Very specific geographic area

### References
- AWS Architecture Best Practices
- Material Design Guidelines
- WCAG 2.1 Accessibility Standards
- Indian Agricultural Data Sources

### Version History
- v1.0 - Initial design document (Feb 2026)

---

**Document Status:** Draft  
**Last Updated:** February 15, 2026  
**Next Review:** March 1, 2026  
**Owner:** KrishiMitra AI Team
