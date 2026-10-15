# Phishing Incident Response Activity

## Activity Overview

In this activity, you will respond to a phishing incident involving a malicious file hash. This is the same SHA-256 file hash that you investigated and verified as malicious in a previous activity. You will follow playbook instructions to investigate and resolve the incident's alert ticket.

Playbooks outline the step-by-step actions necessary to respond to security incidents effectively. Coordinated, effective, and timely action is critical during incident response. Playbooks help security teams minimize the impact of an incident and reduce response time.

## Scenario

You are a Level-One Security Operations Center (SOC) analyst at a financial services company. Previously, you received a phishing alert about a suspicious file downloaded on an employee's computer. After investigating the email attachment's file hash, you verified that the attachment was malicious.

Now, you must follow your organization's procedures to complete the investigation and resolve the alert.

Your organization's security policies and procedures describe how to respond to specific alerts, including phishing alerts.

The phishing playbook contains a flowchart and written instructions to help you investigate and resolve the alert. At the end of your investigation, you will update the alert ticket with your findings.

**Note:** Use the incident handler's journal you started in a previous activity to record notes and track your findings.

## Step-by-Step Instructions

### Step 1: Access the Template

1. Open the Alert Ticket template.
2. Select **Use Template**.
3. If you do not have a Google account, download the template from the provided attachment.

### Step 2: Access Supporting Materials

Open the following supporting material and keep it available during the investigation:

- Phishing Playbook (with Flowchart)

If you do not have a Google account, download the supporting material from the provided attachment.

Review both resources before beginning:

- **Phishing Playbook:** Provides detailed written instructions for each response step.
- **Phishing Flowchart:** Provides a visual overview of the response process.

**Note:** The playbook is not a definitive guide for every phishing incident. Organizations have their own policies, standards, and procedures.

### Step 3: Begin the Investigation

In the Alert Ticket template, update the **Ticket Status** dropdown to **Investigating**.

Begin with Step 2 of the playbook, **Evaluate the Alert**, because you have already received and accessed the phishing alert ticket.

Create a new entry in your incident handler's journal. Record the incident details, initial observations, and questions requiring further investigation.

### Step 4: Evaluate the Alert

Review the Alert Ticket, including the **Additional Information** section.

#### Alert Severity

According to the playbook, an alert severity of Medium or High may indicate that the ticket requires escalation.

#### Sender Details

Analyze the sender's information for inconsistencies that could indicate phishing. For example, a mismatch between the sender's display name and email address may indicate an attempt to impersonate a trusted entity.

#### Message Body and Subject Line

Review the subject line and message body for suspicious wording, grammatical errors, unusual requests, or other indicators of phishing.

#### Attachments and Links

Determine whether the email contains attachments or links. Malicious attachments and links may be used to steal sensitive information or download malware onto a recipient's device.

### Step 5: Answer the Five W's

Use the information available in the alert ticket to answer these questions:

1. **Who** caused the incident?
2. **What** happened?
3. **When** did the incident take place?
4. **Where** did the incident occur?
5. **Why** did it happen?

Do not assume details that have not been provided.

Identify two or three reasons why you believe the phishing alert is legitimate or not legitimate.

### Step 6: Determine Whether to Escalate

Use Steps 3.0 and 3.1 of the Phishing Playbook to determine whether the email contains links or attachments and whether they are malicious.

The email attachment has already been verified as malicious through its SHA-256 file hash.

Follow the playbook:

- If the alert meets the escalation criteria, proceed to Step 3.2.
- If the alert does not meet the escalation criteria, proceed to Step 4.

Follow the organization's escalation procedures when making your decision.

### Step 7: Update the Alert Ticket

Complete the final step of the playbook by updating the Alert Ticket template.

#### Ticket Status

Select the appropriate status from the **Ticket Status** dropdown:

- **Escalated:** If the incident meets the playbook's escalation criteria.
- **Closed:** If the playbook indicates that the alert can be resolved without escalation.

Use the playbook and available evidence to determine the correct outcome.

#### Ticket Comments

In the **Ticket Comments** column, document your findings.

Include the following:

1. A sentence briefly describing the alert and what happened.
2. Two or three sentences explaining why you chose to escalate or close the ticket.
3. Specific details from the alert ticket that support your decision.
4. A brief summary of the investigation steps completed.

Keep your comments factual, concise, and consistent with the evidence collected.

### Example Ticket Comments

The following is an example for an incident that meets the escalation criteria. Replace the bracketed details with information from your actual alert ticket.

> A phishing alert was triggered after an employee received an email containing a suspicious attachment. The attachment's SHA-256 hash was verified as malicious, confirming that the file poses a security risk. The incident is being escalated because the attachment is confirmed malicious and may have exposed the employee's device to malware. The alert details and investigation findings have been documented for further investigation by the appropriate security team.

**Note:** Use this example only if escalation is supported by the playbook and your alert evidence.

## What to Include in Your Response

- [ ] Update the **Ticket Status** column using the dropdown list.
- [ ] Add a sentence in **Ticket Comments** describing the alert and what happened.
- [ ] Add two or three sentences explaining why you chose to escalate or close the ticket.
- [ ] Support your decision with specific details from the alert ticket.
- [ ] Record your findings in your incident handler's journal.

## Expected Outcome

By completing this activity, you will demonstrate your ability to evaluate a phishing alert, verify malicious indicators, follow an incident response playbook, make an escalation decision, and document your findings in an alert ticket.

After completing the activity, proceed to the next course item to compare your work with the completed exemplar.