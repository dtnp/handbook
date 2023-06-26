# Triagable Events

## System/Service Outage

An outage occurs if **ANY** part of the system is not accessible to either internal and external customers when it should have been.

**Examples**:

- scheduled maintenance downtimes
- an unresponsive database
- a page not loading on an admin dashboard

## System/Service Degradations

A degradation is when a part of the system is still accessible and returning correct results, but doing so at times that do not meet SLA/SLO's.

**Examples**:

- nightly automations expected to run in 2 hours, but taking 6+ hours
- response times expected to be under 300ms, but taking 5+ seconds (5,000ms)
- infinite loop on a page causing the website to run really slowly, or never finish loading

## Service Requests

Service Requests are defined as a formal request from a user for something to be provided, often in the form of a request for information, advice, or task to be completed by engineers on behalf of the internal clients.

**Examples**:

- information from the database that isn't available through the Portal, or platform APIs
- a demo or deep dive explanation of a tool or service
- new user account to the portal website

## Bug reports

"A software bug is a problem causing a program to crash or produce invalid output. The problem is caused by insufficient or erroneous logic. A bug can be an error, mistake, defect or fault, which may cause failure or deviation from expected results"

Defects on actively open engineering tickets should be fixed through the already open tickets and should not have a bug ticket created for it on the Triage board

## Feature Requests

Feature requests should be forwarded to [Productboard](#), not put into Triage Board tickets.  If a feature ticket is created on the board, a comment should be created to post it on Productboard, labeled with a **feature** label, and closed.
