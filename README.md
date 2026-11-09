# AI-Powered Customer Review Insights Pipeline

A no-code **n8n** automation that turns raw customer reviews into structured insights. It summarizes each review, classifies its sentiment and the summary's tone (with confidence scores), logs everything to Google Sheets, and emails the team lead whenever a review is negative.

Built for **TalentSprint GenAI, Module 2 Mini-Project 1**, using the *XSound Pro Headphones* as the example product.

## Why this project

Manually reading hundreds of reviews is slow, inconsistent, and biased. This workflow:

- Reduces review processing from hours to minutes
- Applies consistent sentiment and tone detection to every review
- Sends structured results to a central Google Sheet for trend monitoring
- Alerts the team lead immediately about negative feedback

## Workflow

```
On form submission
   -> Summarization               (Basic LLM Chain + OpenAI gpt-4o-mini)
   -> Sentiment Analysis          (Basic LLM Chain + OpenAI gpt-4o-mini)
   -> Summary Tone Classification (Basic LLM Chain + OpenAI gpt-4o-mini)
   -> Append row in sheet         (Google Sheets)
   -> Is Review Negative?         (If)
         true  -> Send message to Team Lead (Gmail)
         false -> end
```

| Node | Purpose | Output format |
|---|---|---|
| On form submission | Collects **Product Name** and **Product Review** | Form fields |
| Summarization | Summarizes the review in 2-3 sentences | Plain summary text |
| Sentiment Analysis | Classifies the review | `Sentiment: <Positive/Negative/Neutral> \| Score: <0-1>` |
| Summary Tone Classification | Classifies the summary's tone | `Tone: <Informative/Persuasive/Friendly> \| Score: <0-1>` |
| Append row in sheet | Logs all results | One row per review |
| Is Review Negative? | Checks if sentiment contains "Negative" | True / False |
| Send message to Team Lead | Emails product name and summary | Text email |

## Screenshots

### Workflow
![n8n workflow canvas]

### Google Sheet output
![Google Sheet results]

### Negative review email alert
![Gmail alert]

## Tech stack

- **n8n** (workflow automation, Form Trigger, LangChain nodes)
- **OpenAI gpt-4o-mini** (works with Mistral Cloud Chat Model too)
- **Google Sheets** and **Gmail** integrations

## Setup

1. Create a workflow in [n8n](https://n8n.io) and import `review-insights-workflow.json` (paste with Ctrl+V on the canvas, or use Import from File).
2. Add credentials: OpenAI (used by the 3 chat model nodes), Google Sheets, and Gmail.
3. Create a Google Sheet with these exact headers in row 1:

   | Product Name | Product Review | Product Review Summary | Sentiment and Score | Tone and Score |
   |---|---|---|---|---|

4. In **Append row in sheet**, select your sheet and tab, then click the refresh icon next to **Values to Send**.
5. In **Send message to Team Lead**, set the recipient email.
6. Click **Execute workflow** to test, then **Save and Activate**.
7. Submit reviews through the **Production URL** of the form node.

## Sample test reviews

- **Negative** (triggers the email): "The XSound Pro stopped working after two weeks and support never replied. Very disappointed."
- **Positive**: "Amazing sound quality and battery life. Best headphones I have owned."

## Sample email sent to the team lead

```
Subject: Negative Customer Review Received – XSound Pro

Dear Team Lead,
We have received a negative customer review for the product - XSound Pro.
Please find the feedback summary below:
<AI-generated summary>

Best Regards,
Workflow Automation Team
```

## Troubleshooting

| Problem | Fix |
|---|---|
| "Column names were updated after the node's setup" | Make sure row 1 of the sheet has the 5 exact headers, then refresh the columns in the Google Sheets node |
| Email bounces | Check the recipient has no duplicated `@gmail.com` |
| Gmail branch never fires | Test with a clearly negative review; the If node checks for the word "Negative" |

## Repository contents

```
review-insights-pipeline/
├── README.md
├── review-insights-workflow.json
└── images/
```

## Notes

- Credentials are not stored in the exported workflow JSON. Never commit API keys.
- Replace the placeholder sheet ID and email in the JSON with your own before running.

## Author

**Konijeti Sai Sukanya**
GitHub: [saisuukaanyaa-dev](https://github.com/saisuukaanyaa-dev)
