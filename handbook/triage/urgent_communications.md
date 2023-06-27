# Urgent vs Non-Urgent Communications

Things will always break in a software and technology company.  How we react and communicate broke things to both internal and external clients is important!

## Non-Urgent Communication

All bug/issue communication should occur within a well documented ticket on the [Triage Board](/handbook/3rd_party_tools/jira.md), along with the correct template for the issue type.

## Emergency Communication

All **degradations** and **outages** must be immediately announced

### Ticket Creation

Board: [Jira Triage Board](#) 
Template: [Postmortem](/handbooks/communication/triage/postmortems.md)

Please fill out as many of the details as possible at the beginning of the issue.  It is likely that very little is known at the beginning, that is OK!  Fill out what is already know.

### Escalation Email

- An email must be sent to eng-escalations@pantheon.com using the [Pantheon Escalation Template Email](/handbooks/communication/triage/urgent_communications.md#escalation-email-template)

### Chat Communication Alerts

Slack: Create a message in the Slack **#cse** channel including link to the empty/new Postmortem ticket created above in [Ticket Creation](/handbooks/communication/triage/urgent_communications.md#ticket-creation)

---

## Escalation Email Template

```markdown
# Subject: Engineering Incident Report

Incident Summary
----------------
Business Impact: ... details here ...
User Impact: ... details here ...
How the incident was first reported: Engineering discovery? Email from external client? etc.

Incident Details
----------------
Incident Time Start:	August 20, 2020 - 11:23am
Incident Time End:	August 24, 2020 - 4:58pm
Incident Description:	TLDR; engineering description
Triage Ticket:  JIRA LINK

```
**NOTE**: Please remove `Subject: ` from the actual email subject line.


## Escalation Email Followup

When followup communications around an emergency incident is needed, please use the email template below: 

```markdown
# Subject: Engineering Incident Update

UPDATE: TBD

STATUS: ... (possibilities include but are not limited to resolution of the incident, further investigation, code fix in next week, etc.)

[WHAT'S BEEN DONE SINCE ORIGINAL COMMUNICATION TO MINIMIZE IMPACT] (did we do something manually, communicate with internal/external customers, etc.)

[LINK TO EXISTING GITHUB POSTMORTEM TICKET WITH MORE DETAILS] (an invitation to follow along in more detail at a link to postmortem)
```
**NOTE**: Please remove `Subject: ` from the actual email subject line.
