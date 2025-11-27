# AWS Newsletter Subscription — README

## Project Overview
An AWS Serverless newsletter subscription app that uses **4 AWS services**: **API Gateway**, **AWS Lambda (Python)**, **Amazon SNS**, and **IAM**. A simple HTML page allows users to subscribe with their email. The backend validates the email and registers it with an SNS topic which sends a confirmation email.

---

## Repository Structure
```
newsletter/
├─ app.py                # Lambda handler (Python)
├─ index.html            # Simple frontend page
├─ template.yaml         # (Optional) SAM template for full deployment
├─ README.md             # This README
└─ docs/
   └─ architecture.png   # Architectural diagram (generated)
```

---

## Quick Start
1. Create an SNS Topic in AWS console. Copy the Topic ARN.
2. Create a Lambda function (Python 3.11). Paste `app.py` code and set environment variable `TOPIC_ARN` to the SNS Topic ARN.
3. Give the Lambda execution role an IAM policy that allows `sns:Subscribe` on the topic (or `*` for initial testing).
4. Create an API Gateway REST API, add resource `/subscribe`, create POST method integrated with your Lambda. Enable CORS and deploy to stage `prod`.
5. Update `index.html` with the API endpoint `https://<api-id>.execute-api.<region>.amazonaws.com/prod/subscribe`.
6. Open `index.html` (double-click or host on S3) and test. Confirm subscription by clicking link in SNS confirmation email.

---

## Files & Key Code References
### `app.py` (Lambda)
The Lambda includes simple email validation, SNS `subscribe()` call, and CORS response headers. Keep `TOPIC_ARN` as an environment variable.

### `index.html` (Frontend)
A tiny HTML page with a form that `fetch()` POSTs JSON `{ "email": "..." }` to `/subscribe`.

### `template.yaml` (optional)
A SAM template for automated deployment (creates SNS topic, Lambda, API Gateway, and IAM permissions). Use `sam build` + `sam deploy --guided`.

---

## Deployment Options
- **Manual (Console)** — Good for beginners: Create SNS → Lambda → IAM → API Gateway through the Console (step-by-step guides included in docs).
- **SAM (CLI)** — Recommended for repeatable deploys. Use `template.yaml` shipped in repo.

---

## Security & Production Notes
- Replace wildcard CORS `*` with your production domain.
- Use least-privilege IAM policies (limit `sns:Subscribe` to the specific Topic ARN).
- Add rate-limiting or CAPTCHA to your frontend to avoid spam.
- Consider SES for sending templated welcome emails after SNS confirmation.

---

## Troubleshooting
- If you do not receive confirmation email: check spam, CloudWatch logs for Lambda, verify `TOPIC_ARN`, and ensure IAM policy grants `sns:Subscribe`.
- Use `aws sns list-subscriptions-by-topic --topic-arn <arn>` to see pending/confirmed subs.

### Mermaid diagram (paste into mermaid.live to render):

```mermaid
flowchart TD
    A[User Browser] -->|POST /subscribe| B[API Gateway]
    B --> C[AWS Lambda (Python)]
    C --> D[Amazon SNS Topic]
    D -->|Confirmation Email| A
```
