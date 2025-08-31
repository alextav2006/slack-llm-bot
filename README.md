# AI-Powered Slack Bot with Google Calendar Integration

## Overview
This project implements an AI-powered Slack bot using **n8n** that interprets natural language messages, extracts appointments, and automatically schedules them in **Google Calendar**. The workflow is designed to handle multiple time zones reliably, ensuring accurate scheduling for international users.

---

## Features
- Slack integration with real-time message interpretation.
- Natural language processing powered by OpenAI LLMs.
- Automatic identification of appointments, dates, times, and locations.
- Timezone detection and offset handling using IANA identifiers.
- Google Calendar integration for creating events with correct start and end times.
- Scalable and modular workflow design, adaptable to other platforms.

---

## Standard Operating Procedure (SOP)

### 1. Prerequisites
- An active **Slack workspace** with permission to install apps.
- A **Google Calendar account** with API access enabled.
- **n8n** installed and running (self-hosted or cloud).
- API credentials:
  - Slack Bot Token
  - Google Calendar OAuth2 credentials
  - OpenAI API Key

---

### 2. Setup Instructions

#### 2.1 Slack
1. Create a Slack App in your workspace.
2. Enable Bot Token Scopes (`chat:write`, `app_mentions:read`).
3. Install the app in the target workspace and retrieve the Bot Token.
4. Configure the **Slack Trigger** node in n8n with the token.

#### 2.2 Google Calendar
1. Enable Google Calendar API in Google Cloud Console.
2. Create OAuth2 credentials and download the client configuration.
3. Configure the **Google Calendar OAuth2 API** credentials in n8n.
4. Grant required scopes for calendar access.

#### 2.3 OpenAI
1. Generate an API key from the OpenAI dashboard.
2. Add the key to n8n as credentials for the **OpenAI Chat Model** node.

---

### 3. Workflow Logic
1. **Slack Trigger** listens for `app_mention` events in the selected channel.
2. **LLM Processing**:  
   - The message is passed to an OpenAI model with a strict prompt.  
   - The LLM returns only JSON with structured fields:  
     - `resposta` (confirmation text)  
     - `acao` (type of action: `compromisso` or `nenhum`)  
     - `titulo` (title of appointment)  
     - `data`, `hora` (date and time in ISO format)  
     - `timezone`, `offset` (IANA timezone and ISO offset)
3. **Validation**: JSON is parsed and validated to ensure correct formatting.
4. **Conditional Logic**:
   - If `acao = compromisso`, proceed to create a Google Calendar event.
   - If not, reply to the user only in Slack.
5. **Google Calendar**:  
   - Event is created with correct `summary`, `start`, `end`, and `timezone`.
6. **Slack Response**:  
   - Confirmation message is posted back to the channel.

---

### 4. Deployment
- Import the provided workflow JSON file into your **n8n instance**.
- Configure credentials for Slack, Google Calendar, and OpenAI.
- Adjust Slack channel IDs and Calendar IDs if needed.
- Test the workflow with sample phrases such as:  
  - "Schedule a meeting tomorrow at 6pm in San Francisco"  
  - "Marcar reunião amanhã às 15h nos Açores"

---

## Example
### Input (Slack message)
@Bot Schedule a meeting tomorrow at 3pm in Macau

### Output
- Slack reply: "Your appointment has been scheduled for tomorrow at 15:00 in Macau."
- Google Calendar event created with correct timezone conversion.

---

## Future Improvements
- Support for recurring events.
- Integration with multiple calendar providers (Outlook, iCal).
- Multi-language support beyond Portuguese and English.
- User-specific calendar linking.

---

## License
This project is released under the MIT License.
