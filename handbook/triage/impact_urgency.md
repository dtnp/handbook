# Impact vs Urgency

## When to Triage Based on Issue Severity

Informed by communications with requesters, and using the [Priority Matrix](#), Engineers should make an initial prioritization of a request as per the table below. Once the issue is prioritized, it is to be assiged to an engineer or team with appropriate labels. 


| Level       | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| **Urgent**  | Urgent issues are to be brought to teams and prioritized to be worked on immediately/on the day the request is made |
| **Highest** | Highest issues are to be brought to the teams and prioritized and labeled as **unscheduled**  for pickup within the current sprint |
| **High**    | High issues should be scheduled next sprint unless unexpected bandwidth exists in current sprint |
| **Medium**  | Medium issues are to be moved to the "Product Backlog", or "Engineering Backlog", on the Product Engineering board.  Scheduling will be prioritized by product and engineering stakeholders |
| **Low**     | Low issues are to be moved to the "Product Backlog", or "Engineering Backlog", on the Product Engineering board.  Scheduling will be prioritized by product and engineering stakeholders |

Note: *All issues should be evaluated against the [Priority Matrix](#) and routed accordingly*

-----

## Severity Calculations
![Priority Matrix](/handbook/triage/images/priority-matrix.png)

## Impact

Business impact of an incident is measured by how large the interruption is to the enterprise and/or customers.

To better discover how severe a ticket's impact is, a few calculations are required between stakeholders and those knowledgable with the systems. The priority given the ticket is measured by scales of urgency and impact on an Impact vs Urgency [Priority Matrix](#) in line with the same definitions used by IT, with each rated low-high (simplified to 1-5 on the chart, with 1 being critical, 5 being low):

- **Urgent [1]:**
	- Affects Entire Enterprise: Entire enterprise degraded/outage, all users impacted
	- Multiple VIP users or VIP customers impacted
- **Highest [2]**
	- Affects Most Teams and/or Services: Most teams and/or services impacted, or most users impacted, with one or more service degradations and/or outages
	- At least one VIP user or VIP customers impacted
- **High [3]:** 
	- Affects Multiple Teams or Services: Multiple teams and/or services impacted, or a significant service is unavailable to the public
- **Medium [4]:**
	- Affects Team or Single Service: Low business impact, may be single or multi-user issue where the service is not significant
- **Low [5]:**
	- Affects Single Person: Single-user business impact, general break/fix issue with no or little business impact or service request.

## Urgency

Business urgency of an incident is measured by how quickly the incident needs to be resolved.

- **Urgent [1]:**
	- The team/business area cannot provide a critical service to the enterprise or customers.
	- Service Level Objectives violated.
	- Revenue impacted.
- **Highest [2]:**
	- A team or teams cannot provide multiple critical services to the other internal team(s) or customers.
	- Service Level Objectives violated.
	- Service Level Objectives at risk.
	- Revenue at risk.
- **High [3]:**
	- A team/business area cannot provide a non-critical service to the public, users cannot perform a critical job function.
	- Service Level Objectives at risk.
- **Medium [4]:**
	- Single-user cannot complete a job function
	- A team/business area cannot provide a non-critical service to the public, users cannot perform a critical job function; work around exists.
- **Low [5]:**
	- No urgency/impact to business services, single-user "how to" or related to a service request
	- Single-user cannot complete a job function; work around exists.
