# AI-Powered Slack Bot with Google Calendar Integration  

## Overview  
This project implements an AI-powered Slack bot using **n8n** that interprets natural language messages, extracts appointments, checks availability, and automatically schedules them in **Google Calendar**. The workflow is designed to handle **time zones**, **busy slots**, and **invalid past dates**, ensuring reliable scheduling for international teams.  

---

## Features  
- Slack integration with real-time message interpretation.  
- Natural language processing powered by OpenAI LLMs.  
- Automatic identification of appointments (title, date, time, timezone).  
- Validation of requests (prevents scheduling in the past).  
- Availability check against Google Calendar before confirming.  
- Smart user feedback:  
  - ✅ Confirms event creation with calendar link.  
  - ❌ Notifies when the requested slot is already busy.  
- Timezone detection and offset handling using IANA identifiers.  
- Modular workflow design, adaptable to other platforms.  

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
   - Message is passed to OpenAI with a strict JSON-only prompt.  
   - The LLM outputs structured fields:  
     - `answer`: confirmation text.  
     - `busy_answer`: message to use if the time is unavailable.  
     - `action`: `compromisse` (appointment) or `none`.  
     - `title`, `start_date`, `start_hour`, `end_date`, `end_hour`.  
     - `timezone`, `offset`.  
3. **JSON Parsing** ensures valid output before continuing.  
4. **Conditional Logic**:  
   - If `action = compromisse`, check the time slot in Google Calendar.  
   - If not, reply directly to the user in Slack.  
5. **Availability Check**:  
   - If free → create Google Calendar event via API.  
   - If busy → send `busy_answer` back to Slack.  
6. **Slack Response**:  
   - Confirms the event with a **Google Calendar link**.  
   - Or explains the slot is already taken.  

---

### 4. Deployment  
- Import the provided workflow JSON file into your **n8n instance**.  
- Configure credentials for Slack, Google Calendar, and OpenAI.  
- Adjust Slack channel IDs and Calendar IDs if needed.  
- Test the workflow with sample phrases such as:  
  - "Schedule a meeting tomorrow at 6pm in San Francisco"  
  - "Marcar reunião amanhã às 15h nos Açores"  
  - "Agendar reunião ontem às 10h" → gets automatically shifted to the future.  

---

## Example  

### Input (Slack message)  
`@Bot Marcar reunião amanhã às 3h da manhã`  

### Output  
- Slack reply: *"A reunião foi marcada para as 3 da manhã do dia seguinte, uma vez que o horário solicitado está no passado."*  
- Slack follow-up: `Google Calendar Link: <event-link>`  
- Event created in Google Calendar with correct timezone.  

---

## Future Improvements  
- Support for recurring events.  
- Integration with multiple calendar providers (Outlook, iCal).  
- Multi-language support beyond Portuguese and English.  
- User-specific calendar linking.  
- Rich Slack responses (buttons to accept/decline).  

---

## License  
This project is released under the MIT License.  

<img width="1920" height="891" alt="image" src="https://github.com/user-attachments/assets/ca9ea0ba-7965-4030-aa7b-9f888ab7904f" />
![Workflow Screenshot](https://github.com/user-attachments/assets/54d701ed-0d0c-423e-ae42-b74cb3691150)  
![Workflow Screenshot](https://github.com/user-attachments/assets/222a3b7a-ae0d-4fd2-b638-7b20516c1741)  
