# Real-Time AI Behavioral Anomaly & Fraud Detection Engine



## 🛠️ Environment Variables Configuration (`.env`)

Create a `.env` file in the root directory of the `behaviour-anomaly-poc` folder and add the following keys:

```env
# Core API Configurations
GEMINI_API_KEY=your_google_gemini_api_key_here

# Automated Notification Settings
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USERNAME=your_gmail_address@gmail.com
EMAIL_PASSWORD=your_gmail_app_password_here



### 🔑 How and Where to Acquire Your Keys

**GEMINI_API_KEY**
* Go to the [Google AI Studio developer console](https://aistudio.google.com/).
* Sign in with your Google account.
* Click **Get API Key** and generate a new key for your project.

**EMAIL_PASSWORD (Gmail App Password)**
* Because standard passwords are blocked by modern secure mail networks, you must use an **App Password**.
* Go to your [Google Account Settings -> Security](https://myaccount.google.com/security).
* Turn on **2-Step Verification** if it isn't enabled yet.
* Search for **App passwords** in the top account search bar.
* Select an application name (e.g., "Python Hackathon Engine") and click **Create**.
* Copy the unique **16-character code** generated and paste it directly into the `EMAIL_PASSWORD` field in your `.env`.


