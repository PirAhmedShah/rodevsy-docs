# 🗺️ User Journey Map

## Persona: [e.g., "Sam the SRE"]
## Scenario: [e.g., "Diagnosing and resolving a production outage"]

| Phase | Actions | Thoughts | Feelings/Emotions | Pain Points | Opportunities |
|---|---|---|---|---|---|
| **1. Alerting** | - Receives PagerDuty alert. <br/> - Opens laptop. | "Ugh, again? Which system is it this time?" | 😫 Anxious, Stressed | - Alert is vague. <br/> - Woken up at 3 AM. | - Provide richer context in alerts. |
| **2. Triage** | - Logs into dashboard. <br/> - Checks monitoring graphs. | "Where is the spike? CPU? Memory? It's not clear." | 🤔 Confused, Frustrated | - Too many dashboards. <br/> - Data is lagging. | - Create a single "triage" dashboard. |
| **3. Diagnose** | - Finds error logs. <br/> - Identifies bad deploy. | "Aha! It's the `auth-service` deploy from 10 mins ago." | 💡 Relieved, Focused | - Logs are hard to search. | - Better log aggregation (ELK). |
| **4. Resolve** | - Rolls back the deploy. <br/> - Verifies service health. | "Okay, rollback started. Now we wait. Looks like it's recovering." | 😌 Calm, Hopeful | - Rollback script is slow. | - Automate rollbacks on failure. |
| **5. Post-Mortem** | - Opens a new incident doc. <br/> - Starts writing timeline. | "I need to make sure this doesn't happen again." | 😒 Weary, Diligent | - Manually creating post-mortems is tedious. | - Template or automate incident reports. |
