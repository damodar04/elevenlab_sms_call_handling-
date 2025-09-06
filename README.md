AI-Powered Conversational SMS Agent with n8n
This repository contains the n8n workflows for building an intelligent, stateful SMS agent capable of handling two-way conversations. The agent is designed to understand the full context of a user's messages, remember past interactions, and respond intelligently.

This project was inspired by the "Build an AI Agent that can call you" tutorial by Liam Cottle on YouTube and adapts the concept for an SMS-based system.

✨ Features
Intelligent Message Batching: Uses a unique Google Sheets and Wait node combination to group multiple incoming SMS messages from a user, ensuring the AI gets the full context before replying.

Stateful Memory: Leverages a Supabase (PostgreSQL) database to store and retrieve conversation history for each phone number, enabling context-aware interactions over time.

Modular Design: Includes three distinct workflows for a clean, organized architecture:

The main AI agent for handling incoming messages.

An API endpoint to programmatically send an SMS.

An API endpoint to retrieve the full conversation history for a specific number.

Modern Tech Stack: Integrates best-in-class services for AI, communication, and data persistence.

⚙️ How It Works
The core of the project is the elevenlabs_sms_agent.json workflow.

Webhook Trigger: A Twilio webhook receives an incoming SMS message.

Log Human Message: The incoming message is immediately saved to a Supabase table, linked to the sender's phone number (session_id).

Buffer in Google Sheets: The message and sender's number are appended to a Google Sheet. This acts as a temporary holding area.

Wait: The workflow pauses for a few seconds. This is the crucial step that allows a user to send multiple texts in quick succession.

Retrieve & Combine: After the pause, the workflow reads all rows in the Google Sheet corresponding to the sender's number, combines the messages into a single text block, and then deletes those rows.

AI Agent Processing: This combined message is passed to a LangChain Agent powered by an OpenAI GPT model.

Generate & Send Response: The AI generates a response, which is sent back to the user's phone number via the Twilio API.

Log AI Message: The AI's response is also saved to the Supabase table to maintain a complete conversation history.

📂 Workflows Included
elevenlabs_sms_agent.json: The main workflow that receives, processes, and responds to user SMS messages.

elevenlabs_sms_clean.json: A simple utility workflow with a webhook trigger that sends an outbound SMS and logs it. Useful for starting conversations or sending notifications.

elevenlabs_get_conversation_clean.json: A webhook-powered workflow that acts as an API endpoint. It fetches and returns the complete chat history for a given phone number from Supabase.

🛠️ Tech Stack
Automation: n8n.io

SMS & Voice: Twilio

AI Language Model: OpenAI (GPT-4.1 used)

Database (Memory): Supabase

Temporary Data Store: Google Sheets

🚀 Setup and Installation
Clone Repository: Download the three .json workflow files from this repository.

Import to n8n: Import all three workflows into your n8n instance.

Configure Credentials: Create and configure the credentials in n8n for:

Twilio

Google Sheets (OAuth2)

OpenAI

Supabase

Set Up Supabase:

Create a new table in your Supabase project (e.g., n8n_chat_histories).

Ensure the table has at least two columns: session_id (type text) and message (type jsonb).

Update Nodes:

In all Supabase nodes, select your Supabase credential and the correct table ID.

In the Google Sheets nodes, select your credential and the correct Document/Sheet ID.

In the Twilio node, set your "From" phone number.

In the SMS AI Agent node, ensure your desired OpenAI model and credentials are set.

Activate Workflows: Activate all three workflows in the n8n UI.

Configure Twilio Webhook: In your Twilio account, configure the webhook for your phone number to point to the URL of the Webhook node in the elevenlabs_sms_agent workflow.

Usage
To start a conversation, simply send an SMS to the phone number you configured in Twilio. The AI agent will receive the message, process it, and respond.
