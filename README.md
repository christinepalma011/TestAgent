# TestAgent

Run browser-based QA scenarios from GitHub Issues. Each issue describes what to test and what the browser should show. The AI agent performs the steps and reports **Pass**, **Fail**, or **Blocked** with its observations.

## Try the Google smoke test

Create a GitHub issue titled **QA | Search Google and verify results** and use this test plan.

| Step | Expected result |
| --- | --- |
| Open https://www.google.com. | The page loads and a search field is visible. |
| Search for `Philippines weather`. | The results page shows the submitted query. |
| Review the visible results. | At least one result relates to weather in the Philippines. |

If a CAPTCHA or consent prompt prevents verification, report the affected step as **Blocked** and describe what appeared.

### Run from ChatGPT Work

Copy the issue body into a ChatGPT Work conversation and ask it to execute each step in the browser. Request a Markdown table with the status and observed result for each check, then paste the report back into the issue as a comment. This path uses your ChatGPT allowance and requires no API key.

### Run from GitHub Actions

1. Put the workflow and runner files listed below on the repository's default branch.
2. In the repository, go to **Settings → Secrets and variables → Actions → New repository secret**. Add `OPENAI_API_KEY` using a key from the [OpenAI API platform](https://platform.openai.com/api-keys). The API project needs available usage. API billing is separate from ChatGPT Plus.
3. Set `STAGING_URL` in the workflow to `https://www.google.com` for this smoke test.
4. Create the repository label `qa-agent-run` and apply it to the issue. If the label was already present before setup, remove it and add it again.
5. Open **Actions → Run AI QA agent** to check progress. When the workflow finishes, look for the report and run link in the issue comments.

## Repository files

| Path | Purpose |
| --- | --- |
| `.github/workflows/qa-agent.yml` | Starts the run when an issue gets `qa-agent-run` and posts the report. |
| `scripts/run-qa-agent.mjs` | Reads the issue from the GitHub event payload, runs the hosted browser agent, and writes `qa-report.md`. |
| `package.json` and `package-lock.json` | Define and lock the Node.js dependencies, including the OpenAI SDK. |
| `.gitignore` | Keeps `node_modules/`, `.env`, and generated `qa-report.md` out of Git. |

Keep API keys and test credentials in Actions secrets.

## Write a QA issue

Include the target environment, the test goal, any required test data, and an expected result for each step. Make the expected results observable. For example, ask the agent to reopen a saved record and verify its values after refresh.

Use **Pass** when the expected result is observed, **Fail** when the application behaves differently, and **Blocked** when access, missing data, or an interruption prevents verification. For a failure, include the reproduction steps and actual result.

## Troubleshooting

| Symptom | What to check |
| --- | --- |
| No run appears | Confirm the workflow is on the default branch and the label was added after setup. |
| Dependency installation fails | Confirm both npm package files are committed and in sync. |
| The run reports Blocked | Open the Actions log and review the agent's observations. Check API access and whether the target page can be reached. |
| No issue comment appears | Check the reporting step and confirm the job has `issues: write` permission. |
