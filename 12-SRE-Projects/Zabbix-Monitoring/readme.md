# **Automated Network Event Notification System: Zabbix to Telegram Integration**

Real-time infrastructure alerting and event-driven webhook notifications bridging enterprise network monitoring with instant incident response.

## **Overview / What It Does**

In production enterprise networks, Mean Time to Detect (MTTD) and Mean Time to Respond (MTTR) are heavily reliant on immediate notification pipelines. This project establishes an automated, event-driven alerting integration between **Zabbix** and **Telegram** via a custom JavaScript webhook media type.

When interface state transitions (e.g., Cisco IOS link-down events) or host metric thresholds are crossed, Zabbix evaluates trigger conditions, invokes an automated Action, and dispatches structured event payloads directly to incident response engineering channels in Telegram.

### **Key Features**

* **Automated Incident Dispatch**: Direct asynchronous event forwarding from Zabbix server daemons to target Telegram chat endpoints.  
* **Webhook Parameter Sanitization**: Structured mapping of native Zabbix runtime macros ({ALERT.MESSAGE}, {EVENT.TAGSJSON}, {EVENT.NSEVERITY}, etc.) into compliant JSON payloads.  
* **Granular Escalation Policies**: Configured via Zabbix Operations pipelines to target role-based groups (Zabbix administrators) and filter by severity tiers.  
* **Recovery & Update Tracking**: Full lifecycle event alerting, including problem generation, status updates, and automatic resolution/recovery broadcasts.

## **Demo / Screenshots**

* **Zabbix Problem Dashboard**: Interface Down state trigger.  
  \!\[Zabbix Interface Alert\](docs/screenshots/zabbix-problem-dashboard.png)  
* **Webhook Media Type Configuration**: Parameter definition and test modal.  
  \!\[Media Type Setup\](docs/screenshots/zabbix-mediatype-config.png)  
* **Telegram Alert Delivery**: Live alert received on mobile/desktop client.  
  \!\[Telegram Alert Output\](docs/screenshots/telegram-alert-sample.png)

## **Tech Stack**

* **Monitoring Core**: Zabbix Server 7.x / 6.x LTS  
* **Integration Interface**: Zabbix Webhook Engine (Embedded Duktape JavaScript)  
* **API Protocol**: Telegram Bot HTTP API (HTTPS POST / JSON)  
* **Target Network Hardware**: Cisco IOS / IOS-XE Network Devices (SNMPv2c/SNMPv3 polling)  
* **Operating System**: Ubuntu Server LTS

## **How It Works / Architecture**

&nbsp;

&nbsp;

&nbsp;

Plaintext

\+-------------------+       SNMP Trap / Polling        \+-------------------+  
|  Cisco R1 Router  | \==============================\> |   Zabbix Server   |  
| (Interface Gi0/1) |                                 |  (Poller Engine)  |  
\+-------------------+                                 \+---------+---------+  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Trigger Fires: "Link Down"  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;v  
\+-------------------+          HTTPS Webhook           \+-------------------+  
|   Telegram API    | \<============================== |   Zabbix Action   |  
|  (Bot Endpoint)   |       JSON Event Payload        |   (Operations)    |  
\+---------+---------+                                 \+-------------------+  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;v  
\+-------------------+  
|  Network Support  |  
|  Telegram Client  |  
\+-------------------+

### **Architecture & Payload Flow**

> 1. **Detection**: Zabbix agent or SNMP poller detects interface operstatus change (IF-MIB::ifOperStatus \= down).  
> 2. **Evaluation**: Trigger action assesses criteria (Host Group, Severity: Average or higher).  
> 3. **Execution**: Action dispatches an operation directed to the target user group.  
> 4. **Webhook Processing**: Zabbix's built-in webhook JavaScript parses parameter macros, validates JSON structures, and constructs an HTTP POST payload directed at \[https://api.telegram.org/bot\](https://api.telegram.org/bot)\<TOKEN\>/sendMessage.  
> 5. **Ingestion**: The Telegram bot pushes the message to the specified engineering chat\_id.

## **Installation & Getting Started**

### **Prerequisites**

* A running Zabbix Server instance (v6.0 LTS or v7.0+).  
* A valid Telegram Bot Token generated via [@BotFather](https://www.google.com/search?q=https://t.me/botfather).  
* Target Telegram Chat ID (obtainable via [@userinfobot](https://t.me/userinfobot) or the getUpdates API endpoint).

### **1\. Telegram Bot Setup**

> 1. Open Telegram, start a chat with @BotFather, and send /newbot.  
> 2. Follow prompts to name your bot and obtain the HTTP API Token (e.g., 123456789:AA...).  
> 3. Start a conversation with your new bot or add it to your network operations group.

### **2\. Zabbix Media Type Configuration**

> 1. In the Zabbix Web Frontend, navigate to **Alerts** \> **Media types**.  
> 2. Select **Telegram** (Webhook).  
> 3. Set the required parameters:  
   * api\_token: \<YOUR\_TELEGRAM\_BOT\_TOKEN\>  
   * api\_chat\_id: \<YOUR\_CHAT\_ID\> (Default fallback, optional if mapped per-user)  
   * event\_tags: {EVENT.TAGSJSON} *(Ensure this parses valid JSON or leave empty array \[\] during manual tests)*  
   * event\_source: {EVENT.SOURCE}  
   * event\_value: {EVENT.VALUE}  
   * event\_update\_status: {EVENT.UPDATE.STATUS}  
> 4. Click **Update**.

### **3\. User Media Provisioning**

> 1. Navigate to **Users** \> **Users**, and select the engineering user (e.g., Admin).  
> 2. Click the **Media** tab and choose **Add**:  
   * **Type**: Telegram  
   * **Send to**: \<YOUR\_TARGET\_CHAT\_ID\>  
   * **When active**: 1-7,00:00-24:00  
   * **Use if severity**: Check appropriate tiers (Warning, Average, High, Disaster).  
> 3. Click **Add**, then click **Update** on the user configuration form.

### **4\. Configure Trigger Action**

> 1. Navigate to **Alerts** \> **Actions** \> **Trigger actions**.  
> 2. Edit or create an action (e.g., Report problems to Zabbix administrators).  
> 3. In the **Operations** tab, configure:  
   * **Send to user groups**: Zabbix administrators  
   * **Send to media type**: Telegram  
> 4. Apply the same settings under **Recovery operations** and **Update operations**.  
> 5. Save the Action.

## **Usage Examples**

### **Manual Webhook Testing**

You can validate connectivity and token authentication directly inside Zabbix:

> 1. Go to **Alerts** \> **Media types**.  
> 2. Click **Test** on the Telegram row.  
> 3. Supply dummy values for the runtime parameters:  
   * event\_source: 0  
   * event\_value: 1  
   * event\_update\_status: 0  
   * event\_tags: \[\]  
   * alert\_message: TEST: Core Interface Gi0/1 down  
   * alert\_subject: PROBLEM: Core-Router-1  
> 4. Click **Test** and verify receipt in the target Telegram chat.

### **Real-world Fault Simulation**

To test end-to-end telemetry:

&nbsp;

&nbsp;

&nbsp;

Code snippet

R1-Core-Router\# configure terminal  
R1-Core-Router(config)\# interface GigabitEthernet0/1  
R1-Core-Router(config-if)\# shutdown

* **Expected Result**: Within the configured poller step interval, Zabbix registers the incident under **Monitoring** \> **Problems** and fires an instant Telegram message containing the device hostname, interface name, severity level, and trigger timestamp.

## **Future Improvements / Roadmap**

* \[ \] **Interactive Acknowledgment**: Implement Telegram inline keyboard buttons (\[Acknowledge\], \[Close\]) using Zabbix API callbacks.  
* \[ \] **Dynamic Graph Attachments**: Integrate automated chart generation to render and upload interface metric snapshots directly in the Telegram chat during problem events.  
* \[ \] **Template Modularization**: Export complete configuration as a native Zabbix YAML template for version-controlled deployment via CI/CD pipelines.  
* \[ \] **Dual-Stack Notification Fallback**: Add secondary failover webhooks (e.g., Slack / PagerDuty / Webex) if the primary API endpoint experiences a timeout.

## **License**

Distributed under the MIT License. See LICENSE for more information.