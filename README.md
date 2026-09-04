# Customer Support Chatbot

An AI-powered customer support chatbot built on **Amazon Bedrock AgentCore**, designed to handle FAQ answering, bug-report collection, and human hand-off — all through a single conversational interface.

> Built on the AgentCore managed harness (the successor to Bedrock Agents Classic, which closed to new customers on July 30, 2026), with tools exposed through an **AgentCore Gateway**. Testing and evaluation run through **Amazon Bedrock Evaluations**.

## What it does

The chatbot routes each incoming message to one of three flows:

- **FAQ answering** — pulls responses from a reference FAQ document
- **Bug report collection** — gathers all relevant details across a multi-turn conversation before filing a structured ticket via a `create_bug_report` tool
- **Human hand-off** — politely routes to a human agent when the request falls outside what the bot can resolve

## Architecture

- **Model:** `us.amazon.nova-pro-v1:0` (region: `us-east-1`)
- **Tool layer:** Lambda + DynamoDB + IAM, deployed via CloudFormation
- **Agent harness:** Amazon Bedrock AgentCore, with tools exposed through an AgentCore Gateway
- **Testing:** Auto-generated evaluation dataset, scored with Bedrock Evaluations

## Project structure
├── starter/
│ ├── cloudformation/ # Bug-report tool stack (Lambda, DynamoDB, IAM) + testing resources (S3, eval role)
│ ├── setup_gateway.py # Provisions the AgentCore Gateway
│ ├── create_harness.py # Creates the AgentCore harness
│ ├── chat.py # Chat client for local iteration/testing
│ ├── cleanup_agentcore.py # Tears down AgentCore resources
│ ├── system_prompt.txt # Core system prompt driving routing + behavior
│ └── faq.md / eval-dataset generator / test-suite template

## How it works

1. Deploy the tool stack (CloudFormation) and provision the gateway (`setup_gateway.py`)
2. Define the system prompt — routing logic for bug reports, FAQs, and hand-off, with multi-turn detail collection before ticket creation
3. Create the harness (`create_harness.py`) and iterate locally via `chat.py`
4. Run automated tests: generate an eval dataset and score responses with Bedrock Evaluations
5. Clean up provisioned resources when done

## Tech stack

`Amazon Bedrock AgentCore` · `AWS Lambda` · `DynamoDB` · `CloudFormation` · `IAM` · `Bedrock Evaluations` · `Python`