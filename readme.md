# 🌾 KrishiMitra AI

### AI-Powered Farming Assistant for Smarter Agricultural Decisions

KrishiMitra AI is an AI-powered farming assistant designed to help Indian farmers make informed decisions through crop disease detection, mandi price forecasting, weather alerts, voice-based assistance, and farm expense tracking. The platform focuses on accessibility, multilingual support, and simplified agricultural information for rural communities.

## 🌟 Key Features

### 🌿 Crop Disease Detection
- Analyze crop images to identify potential diseases.
- Display predicted disease classes and confidence scores.
- Provide treatment suggestions and preventive measures.
- Maintain a history of previous detections.

### 📈 Mandi Price Prediction
- Display current crop prices based on location.
- Forecast price trends over the next 7–30 days.
- Visualize historical and predicted prices using interactive charts.
- Help farmers compare market trends when planning crop sales.

### 🌦️ Weather Monitoring & Alerts
- Retrieve current weather conditions and forecasts.
- Provide location-based alerts for potentially harmful weather conditions.
- Support notifications through the application and SMS integrations.

### 🎙️ Multilingual Voice Assistant
- Accept agricultural queries through voice and text.
- Convert speech to text and generate spoken responses.
- Support queries related to weather, crop prices, and farming.
- Provide localized interactions for supported languages.

### 💰 Farm Expense Tracking
- Record expenses for seeds, fertilizers, labour, pesticides, and other farming activities.
- Organize expenses by category, date, and season.
- Summarize expenditure and estimated revenue.
- Calculate estimated profit to support farm financial planning.

### 📱 Accessible User Interface
- Responsive React-based web application.
- Progressive Web App (PWA) support.
- Offline capabilities for selected features.
- Mobile-first interface designed for low-bandwidth environments.
- Multilingual support with Hindi and regional languages.

---

## 🏗️ System Architecture

KrishiMitra AI follows a proposed serverless architecture using AWS services for application processing, AI inference, authentication, and data storage.

```text
┌──────────────────────────────────┐
│       React Web App / PWA        │
└────────────────┬─────────────────┘
                 │
┌────────────────▼─────────────────┐
│        Amazon API Gateway        │
└────────────────┬─────────────────┘
                 │
┌────────────────▼─────────────────┐
│         AWS Lambda Layer         │
│                                  │
│  Authentication                  │
│  Disease Detection               │
│  Price Prediction                │
│  Weather Alerts                  │
│  Voice Assistant                 │
│  Expense Tracking                │
└────────────────┬─────────────────┘
                 │
┌────────────────▼─────────────────┐
│           AI / ML Layer          │
│                                  │
│  Amazon SageMaker                │
│  Amazon Bedrock                  │
│  Amazon Transcribe               │
│  Amazon Polly                    │
└────────────────┬─────────────────┘
                 │
┌────────────────▼─────────────────┐
│           Data Layer             │
│                                  │
│  Amazon DynamoDB                 │
│  Amazon S3                       │
└──────────────────────────────────┘
```

## 🛠️ Technology Stack

| Component | Technologies |
|---|---|
| Frontend | React.js, Vite, Tailwind CSS |
| State Management | Redux Toolkit / Zustand |
| Backend | AWS Lambda, Node.js, Python |
| API Management | Amazon API Gateway |
| Authentication | Amazon Cognito, JWT |
| Database | Amazon DynamoDB |
| File Storage | Amazon S3 |
| Disease Detection | CNN, TensorFlow / PyTorch, Amazon SageMaker |
| Price Prediction | LSTM-based time-series modelling |
| Conversational AI | Amazon Bedrock |
| Speech Processing | Amazon Transcribe, Amazon Polly |
| Translation | Amazon Translate |
| Monitoring | Amazon CloudWatch, AWS X-Ray |
| Infrastructure | AWS CDK / Terraform |
| CI/CD | GitHub Actions / AWS CodePipeline |

---

## 🔄 System Workflows

### 1. Crop Disease Detection
1. The user captures or uploads a crop image.
2. The image is sent to the backend through the API Gateway.
3. AWS Lambda processes the request and invokes the disease detection model.
4. The CNN model predicts the disease class and confidence score.
5. The system retrieves relevant treatment information.
6. Results and preventive measures are displayed to the user.

### 2. Price Forecasting
1. The user selects a crop and location.
2. The system retrieves historical market-price data.
3. The LSTM model generates price forecasts.
4. Predicted trends are returned to the frontend.
5. The user views price charts and potential selling opportunities.

### 3. Voice-Based Assistance
1. The user submits a voice query.
2. Amazon Transcribe converts speech into text.
3. Amazon Bedrock processes the query and generates a response.
4. Amazon Polly converts the response into speech.
5. The application displays and plays the response.

### 4. Weather Alerts
1. A scheduled process retrieves weather information.
2. Weather conditions are evaluated for potential risks.
3. Relevant alerts are stored and notifications are triggered.
4. Users receive notifications through supported channels.

### 5. Expense Tracking
1. The user enters expenses manually or through voice input.
2. Expense records are organized by category and date.
3. The system calculates total expenditure.
4. Estimated revenue and expenses are used to calculate projected profit.

---

## 🔐 Security & Privacy

- OTP-based phone verification and token-based authentication.
- API rate limiting and request validation.
- Encryption for data in transit and at rest.
- Least-privilege IAM policies for AWS services.
- Restricted access to stored crop images and other user data.
- File type and size validation for uploaded images and audio.

## ⚡ Performance & Scalability

- Serverless compute through AWS Lambda.
- Automatic scaling to accommodate changing workloads.
- DynamoDB indexing and capacity optimization.
- Caching for frequently requested information.
- Frontend code splitting and image optimization.
- CloudFront CDN for static assets.
- CloudWatch monitoring and AWS X-Ray tracing.
- Storage lifecycle policies and resource usage monitoring for cost optimization.

## 📊 Success Metrics

The proposed evaluation metrics include:

- Disease detection accuracy above 90%.
- Price prediction accuracy above 75%.
- Average response time below 3 seconds.
- API success rate above 99.5%.
- Monitoring of feature adoption and user engagement.
- Tracking model inference time, application errors, and operating costs.

These are design targets and require validation through implementation and testing.

## 🔮 Future Scope

- Extend support for additional regional languages.
- Improve disease detection using diverse crop-image datasets.
- Evaluate forecasting models using historical mandi prices.
- Expand offline functionality for rural connectivity conditions.
- Integrate additional agricultural information sources.
- Improve analytics for model performance and application usage.

## ⚠️ Disclaimer

KrishiMitra AI is intended to provide agricultural information and decision-support tools. Disease predictions, treatment recommendations, weather forecasts, and price estimates may be inaccurate and should not be treated as guaranteed outcomes. Important farming decisions should be verified with reliable local information or qualified agricultural experts.

## 📄 License

This project is intended to support accessible, AI-assisted agricultural decision-making for Indian farming communities.
