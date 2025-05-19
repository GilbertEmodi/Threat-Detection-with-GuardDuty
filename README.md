# Threat Detection with GuardDuty

**Author:** Gilbert Emodi  
**Email:** zemodi99@gmail.com

---

![Image](https://github.com/GilbertEmodi/Threat-Detection-with-GuardDuty/blob/main/ThreatDetect-architecture-complete.png)

---

## Introducing Today's Project!

### Tools and concepts

The services I used were GuardDuty, CloudFormation, S3, and CloudShell. Key concepts I learned include SQL & command injection, using Linux commands like wget, cat and jq, and malware protection.

### Project reflection

This project took me approximately 2 hours. The most challenging part was getting access to the victim's AWS environment using a profile. It was most rewarding to reveal the victim's protected file as the attacker.

I did this project to learn about threat detection and GuardDuty + techniques like SQL/command injection. This project met my expections and was a great challenge.

---

## Project Setup

To set up for this project, I deployed a CloudFormation template that launches an insecure web app (OWASP Juice Shop). The three main components are the web app infrastructure, an S3 bucket, and GuardDuty protecting the environment.

The web app deployed is called OWASP Juice Shop. To practice my GuardDuty skills, I will attack the Juice Shop, then visit the GuardDuty console to detect and analyze its findings - does it pick up on the attacks to the web app.

GuardDuty is an AI powered threat detection service, which means it is designed to help find security attacks or vulnerabilities that affect our AWS resources/environment. Once it detects something unusual, it's still up to the user to investigate.



![Image](https://github.com/GilbertEmodi/Threat-Detection-with-GuardDuty/blob/main/1-CloudFront%20Complete.JPG?raw=true)

---

## SQL Injection

The first attack I performed on the web app is SQL injection, which means injecting malicious SQL code that manipulates a result from the web app. SQL injection is a security risk because it allows attackers to bypass logins, or delete/edit data.

My SQL injection attack involved entering the code [' or 1=1;--] into the email field of the login page.
This means the login query will always evaluate to "true" (i.e. our database is manipulated into telling the web app this login exists)

![Image](https://github.com/GilbertEmodi/Threat-Detection-with-GuardDuty/blob/main/3-Login%20String.JPG?raw=true)

---

## Command Injection

Next, I used command injection, which manipulates the web app's web server to run code that has been entered e.g. in a form. The Juice Shop web app is vulnerable to this because it does not sanitize user inputs i.e does not block scripts.

Next, I used command injection, which manipulates the web app's web server to run code that has been entered e.g. in a form. The Juice Shop web app is vulnerable to this because it does not sanitize user inputs i.e. does not block scripts.



![Image](https://github.com/GilbertEmodi/Threat-Detection-with-GuardDuty/blob/main/4-ObjectObject%20Username.JPG?raw=true)

---

## Attack Verification

To verify the attack's success, I visited the publicly exposed credentials file. This page showed me access keys that represent my EC2 instance's access to the developer's AWS environment. Anyone can use those keys to get the same level of access.

![Image](https://github.com/GilbertEmodi/Threat-Detection-with-GuardDuty/blob/main/5-Stolen%20Credentials%20JSON.JPG?raw=true)

---

## Using CloudShell for Advanced Attacks

The attack continues in CloudShell, because this is a tool I can use to run commands that use the credentials I've stolen. CloudShell will be the medium for doing suspicious thinks like stealing data from an S3 bucket.

In CloudShell, I used "wget" to download the exposed credentials file into the CloudShell environment. Next, I ran a command using "cat" and "jq" to read the downloaded file & format it nicely so the the credentials (in JSON) are easy to understand.

I then set up a profile using all of the stolen credentials. I had to create new profile because the hacker doesn't inherently have access to the victim's AWS environment. I'll need to use the profile to switch permission settings.

![Image](https://github.com/GilbertEmodi/Threat-Detection-with-GuardDuty/blob/main/6-HackComplete.JPG?raw=true)

---

## GuardDuty's Findings

After performing the attack, GuardDuty reported a finding within 15 minutes. Findings are notifications from GuardDuty that something suspicious has happened, and they give you additional details about the who/what/when of the attack.


GuardDuty's finding was called "UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS" which means credentials belonging to my EC2 instance were being used in another account. Anomaly detection was used because this was unusal behavior.

GuardDuty's detailed finding reported that an S3 bucket was affected; the action that was done using the stolen credentials was GetObject; and the EC2 instance whose credentials were leaked. The IP address & location of the actor was also available.

![Image](https://github.com/GilbertEmodi/Threat-Detection-with-GuardDuty/blob/main/7-GuardDuty%20Findings.JPG?raw=true)

---

## Extra: Malware Protection

For my project extension, I enabled "Malware Protection for S3"
Malware is a type of software designed to harm computers. It can damage computers, steal data, or disrupt operations.

To test Malware Protection, I uploaded an EICAR test file into a protected bucket. The uploaded file won't actually cause damage because the test file is only designed to alert antivirus software.

Once I uploaded the malware, GuardDuty instantly triggered a finding called "Object:S3/MaliciousFile". This verified that GuardDuty could successfully detect malware. It also mentioned that the threat type is EICAR-TEST-File (which means not a virus)

![Image](https://github.com/GilbertEmodi/Threat-Detection-with-GuardDuty/blob/main/8-GuardDuty%20Malware%20Findings.JPG?raw=true)

---
