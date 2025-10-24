n8n Workflow: UniFi Device Uptime Monitor & Alerting
This n8n workflow monitors your UniFi network devices, detects when they go offline or come back online, and sends email alerts. It includes a 4-hour cooldown period to prevent excessive notifications for devices that remain offline. 🕰️

Overview
The workflow runs automatically every 5 minutes (or on your configured schedule):

Fetches Device Status: It connects to your UniFi Controller API to get the current status (connected, offline, etc.) of all network devices.

Checks History: It looks up each device in an n8n Data Table to see its last known status and when the last alert was sent.

Analyzes Changes: It compares the current status with the historical status.

Sends Alerts:

If a device goes offline for the first time, it sends an "Offline Alert" email and records the time.

If a device is still offline after 4 hours, it sends another "Still Offline" alert and resets the 4-hour timer. ⏳

If a device comes back online after being offline, it sends a "Recovery Alert" email and clears the alert timestamps, readying it for the next outage. ✅

If there's no change in status (still online or still offline within the cooldown), it silently updates the database and sends no alert. 👍

Updates History: It saves the latest status and alert timestamp back to the Data Table for the next run.

Prerequisites
Before you can use this workflow, you'll need:

n8n Instance: A running n8n setup (Cloud or self-hosted).

UniFi Controller & API Key: Access to your UniFi Network Application and an API key. You can usually generate API keys under your UniFi OS user profile settings if you have admin access.

SMTP Credentials: An SMTP service configured in your n8n credentials for sending emails.

n8n Data Table: You need to create an n8n Data Table (within the same project or globally) with the following exact column names and types:

hostname (String)

state (String)

alert_start (DateTime)

last_alert_sent (DateTime) Give this table a memorable name (e.g., unifi_alerts).

Setup Instructions
Import Workflow: Download the Unifi Offline Alerts - Template.json file from this repository and import it into your n8n instance (New Workflow -> Import from File).

Configure HTTP Nodes:

Open the HTTP Request - UniFi Hosts node.

Under "Header Parameters", replace "REPLACE WITH API KEY" with your actual UniFi API key.

Important: Open the HTTP Request - UniFi Hosts1 node (part of the disabled manual run section) and update the API key there too.

Configure Data Table Nodes:

Open the Read unifi_alerts node.

In the "Data table" parameter, select the Data Table you created in the prerequisites.

Repeat this for the four other Data Table nodes: Upsert unifi_alerts, Update unifi_alerts (Recovery), Update unifi_alerts (No Alert), and Insert Initial Rows.

Configure Email Nodes:

Open the Send Offline Email node.

Under "Credentials", select your configured SMTP credential.

Update the "From Email" and "To Email" fields.

Scroll down in the "HTML" field and replace "YOUR COMPANY NAME" at the bottom.

Repeat this process for the Send Recovery Email node.

Initial Data Load (Run Once!):

Find the group of nodes at the top labeled "ONLY RUN TO PREFILL...".

Click the "play" icon ▶️ on the Manual Trigger (1hr disabled) node. This will run only this section, fetch all your devices, and add their current hostname and state to your Data Table. This gives the main workflow its starting point.

Verify: Check your Data Table to ensure it's populated with device hostnames.

Activate Workflow: Save the workflow and toggle the "Active" switch in the top-left corner to ON.

Your UniFi monitoring is now live! ✨

⚠️ Disclaimer:
This workflow is provided “as is,” without any warranty or guarantee of performance. Use at your own discretion.

💡 Use Case:
Perfect for MSPs, network admins, and Unifi enthusiasts who manage remote sites and need reliable offline alerting beyond the standard Unifi notifications.
