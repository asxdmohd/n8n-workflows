# n8n Workflows

Automation workflows I'm building with [n8n](https://n8n.io), self-hosted in Docker.

| Workflow | What it does | Status |
|---|---|---|
| [Feedback form → Google Sheets](workflows/feedback-form-to-google-sheets.json) | Collects customer feedback through an n8n form, flags positive responders for a discount, and appends every response to a Google Sheet | ✅ Working |
| [Gmail code commenter](workflows/gmail-code-commenter.json) | Watches Gmail for emails containing code and uses an OpenAI model to add line-by-line explanatory comments | 🚧 In progress |

---

## 1. Feedback form → Google Sheets

```
Form submission ──► If (Feedback = "Positive")
                        ├─ true  ──► Set Give_Discount = "Yes" ─┐
                        └─ false ──► Set Give_Discount = "no"  ─┴─► Append row to Google Sheet
```

- **Trigger:** n8n Form with fields Email, Name, Age and Feedback (Positive / Neutral / Negative)
- **Logic:** an `If` node routes positive feedback to a discount branch; `Set` nodes add a `Give_Discount` column
- **Output:** each submission is appended as a row to a Google Sheet (columns: Email_Id, Name, Age, Feedback, Give_Discount)

**Nodes:** Form Trigger · If · Set · Google Sheets

## 2. Gmail code commenter (in progress)

Planned flow:

1. Read incoming emails containing code (Gmail Trigger, polls every minute, filtered by sender)
2. Summarise the code with an LLM
3. Generate explanatory comments for the code without changing the original lines
4. Run steps 2 and 3 in parallel
5. Merge both outputs
6. Email the result back to me

**Done so far:** Gmail trigger, OpenAI chat model (`gpt-5-mini`) and the LLM chain with the commenting prompt.
**Next:** connect the nodes, add the summary branch, merge, and send the reply via Gmail.

**Nodes:** Gmail Trigger · Basic LLM Chain · OpenAI Chat Model

---

## How to use

1. Run n8n locally:
   ```bash
   docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n docker.n8n.io/n8nio/n8n
   ```
2. Open http://localhost:5678, create a new workflow, then use **⋯ → Import from file** and choose a JSON file from `workflows/`.
3. Add your own credentials (Google Sheets / Gmail / OpenAI). Credentials are not included in this repo.
4. Replace the placeholders: `YOUR_GOOGLE_SHEET_ID` in the Google Sheets node, and `sender@example.com` in the Gmail trigger.
