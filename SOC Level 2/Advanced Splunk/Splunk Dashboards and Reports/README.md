# TryHackMe Room: Splunk: Dashboards and Reports

This room was focused on the critical aspects of Splunk visualization and automation, specifically covering how to create effective reports, build intuitive dashboards, and set up dynamic alerts. I learned how to move beyond basic search queries and use the results for ongoing monitoring and proactive security defense.

---

## Section: Organizing Data in Splunk

### Task 1: Which search term will show us results from all indices in Splunk?

The universal search term used in Splunk to retrieve events from every configured index is the wildcard: index=*.

---

## Section: Creating Reports for Recurring Searches

### Task 1: Create a report from the network-server logs host that lists the ports used in network connections and their count. What is the highest number of times any port is used in network connections?

To analyze the network activity on the network-server host, I constructed a simple query and then summarized the data by the port field:

index=* host=network-server | stats count by port | sort -count

By examining the result set, I could immediately see the port that recorded the highest usage count.

[Screenshot 01: Highest port usage count](./screenshots/01-highest-port-count.png)

### Task 2: While creating reports, which option do we need to enable to get to choose the time range of the report?

To allow users flexibility when viewing or generating a report, the option that must be enabled is the Time Range Picker.

---

## Section: Creating Dashboards for Summarizing Results

### Task 1: Create a dashboard from the web-server logs that show the status codes in a line chart. Which status code was observed for the least number of times?

I searched the logs for the web-server host and used the stats command to group the event counts by status_code. I then built a line chart visualization from the results. Sorting the data helped to identify the status code that was observed the fewest times.

host=web-server | stats count by status_code | sort count

[Screenshot 02: Dashboard status code line chart](./screenshots/02-dashboard-status-codes.png)

### Task 2: What is the name of the traditional Splunk dashboard builder?

While modern Splunk uses a more streamlined interface, the classic method for creating and editing dashboards is known as the Classic dashboard builder.

---

## Section: Alerting on High Priority Events

### Task 1: What feature can we use to make Splunk take some actions on our behalf?

The Splunk feature that allows for automated responses—such as sending an email, writing to a file, or running a script—when an alert is triggered is trigger actions.

### Task 2: Which alert type will trigger the instant an event occurs?

The alert scheduling option that continuously monitors log data and triggers an action immediately upon finding a match is the Real-time alert type.

### Task 3: Which option, when enabled, will only send a single alert in the specified time even if the trigger conditions re-occur?

To prevent alert fatigue and ensure the security team receives only one notification for repeated activity within a defined window, the Throttle option must be enabled.

---

# Summary

This room was a practical guide to leveraging Splunk's reporting and automation capabilities. I learned how to:

-Reports and Dashboards: Create persistent searches (Reports) and visually compelling summaries (Dashboards) for continuous monitoring.
-Visualization: Use the stats command to prepare data for visualization, enabling analysis of key metrics like port usage and HTTP status code distribution.
-Alerting Strategy: Configure Real-time alerts for immediate threat response and use Throttle settings to manage alert volume and prevent false positives.

These skills are vital for turning raw log data into an effective, automated monitoring system.