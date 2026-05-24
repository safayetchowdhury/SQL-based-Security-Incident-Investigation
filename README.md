# Project: SQL-Based Security Incident Investigation

This project demonstrates the application of SQL as a core tool for technical data analysis which I completed as part of the Google Cybersecurity Professional Certificate. In any technical role, we constantly deal with massive amounts of data, and inability of filtering through that noise to find what we actually need make us stuck.

I wanted to show that I can go beyond just running basic commands and I can structure my queries to isolate specific issues, manage assets, and pull the right information out of a database under different conditions. I recently investigated a potential security incident that occurred after business hours. I used SQL queries to identify suspicious login activity and categorize employee data, surfacing patterns relevant to system security and ongoing IT updates.

---

### 1. After Hours Authentication Failure

This query is designed to pull up all the login attempts that happened after 6:00 PM and failed. To get this info, I started by selecting all the data from the `log_in_attempts` table. From there I needed to narrow things down, so I used a `WHERE` clause combined with an `AND` operator to make sure I was only seeing the specific failures I was looking for.

* **Filter 1:** `login_time > '18:00'` to isolate attempts after 6:00 PM.
* **Filter 2:** `success = FALSE` to isolate only the failed attempts.

**Search Query:**
```sql
SELECT *
FROM log_in_attempts
WHERE login_time > '18:00' AND success = FALSE;
```

<img width="975" height="473" alt="image" src="https://github.com/user-attachments/assets/3d52b173-015a-4d74-841d-dea84211cd10" />

### 2. Event Correlation: Time Based Incident Scoping

After a suspicious security event occurred on 2022-05-09, I realized I needed to widen my net. To get a better sense of what was happening, I decided to investigate any login activity that took place on the day of the incident and the day leading up to it.
To do this, I wrote a SQL query to filter for login attempts that happened on those specific dates. My query starts by selecting `SELECT *` every piece of information from the `log_in_attempts` table. I then filtered my results using a `WHERE` clause and an `OR` operator, which tells the database to pull records if either condition is met.

* **Condition 1:** `login_date = '2022-05-09'` to grab the day of the incident.
* **Condition 2:** `login_date = '2022-05-08'` to grab the day leading up to it.

**Search Query:**
```sql
SELECT *
FROM log_in_attempts
WHERE login_date = '2022-05-09' OR login_date = '2022-05-08';
```
<img width="982" height="481" alt="image" src="https://github.com/user-attachments/assets/825f4fdb-40f3-45c2-92cc-5259d21a0056" />


This query gave me a clear list of every login attempt during that 48 hour window. By looking at these side by side I could start to spot patterns like whether the same IP addresses were active on both days or if there was a sudden spike in activity right before the main event. It’s all about taking that raw, messy database data and turning it into a usable timeline.

### 3. Threat Hunting: Geographical Filtering

I've been tracking some suspicious login attempts, and our initial analysis shows that this activity definitely didn't originate from Mexico. To get a better handle on where these attempts were actually coming from, I needed to isolate all the login traffic that originated outside of that region.

To do this, I built a SQL query to filter the `log_in_attempts` table. I started by selecting all the data so I could review the full scope of the traffic. From there, I used a `WHERE` clause combined with a `NOT` operator to exclude the specific countries we wanted to ignore.

* **Condition 1:** Used `NOT LIKE 'MEX%'` to filter out any entries originating from Mexico.
* **Wildcard usage:** Since the dataset lists Mexico in different ways (`MEX`, `MEXICO`), the `%` wildcard ensures we catch any variation starting with "MEX".

**Search Query:**
```sql
SELECT *
FROM log_in_attempts
WHERE country NOT LIKE 'MEX%';
```

<img width="976" height="390" alt="image" src="https://github.com/user-attachments/assets/c3a924d6-77db-4681-9a47-69fc875641df" />

By running this, I immediately cleared away the noise of normal local traffic. It left me with a small list of international logins that didn't belong. I am now checking these against our blocked IP list to see if they are actual threats.

### 4. Asset Management:Target Security Patching

Here I'll ensure our internal assets are hardened. Our team identified that several computers in the Marketing department need urgent security updates. I need to pinpoint exactly which machines belong to staff in the East building's marketing department to get this patch rollout started.

I queried the `employees` table to pull a list of the specific devices requiring updates. Since the `office` column includes building and room numbers (like `East-170`), I used the `LIKE` operator with the `%` wildcard to grab everyone in the "East" building.

* **Department Filter:** Used `department = 'Marketing'` to narrow the focus to the specific team.
* **Building Filter:** Used `office LIKE 'East%'` to capture all offices within the East building, regardless of the specific room number.

**Search Query:**
```sql
SELECT *
FROM employees
WHERE department = 'Marketing' AND office LIKE 'East%';
```
<img width="971" height="314" alt="image" src="https://github.com/user-attachments/assets/a5ae3141-ac36-4439-b57e-69f9bd57ee7e" />

This query gave me a clean list of the three employees in the East building's Marketing department whose devices need patching. Having this targeted list makes the update process much faster than manually checking every single machine, ensuring our internal systems are protected against potential vulnerabilities.

### 5. Privilege & Role Segmentation
Following the updates in the Marketing department, our team needs to address security requirements for the Finance and Sales departments. My goal here is to pull a consolidated list of all employees within these two specific departments to prepare for the next phase of the patch rollout.

To gather this data, I queried the `employees` table. Because I needed records for *either* department rather than a single specific one, I used the `WHERE` clause combined with an `OR` operator to combine both criteria into one result set.

* **Criteria 1:** `department = 'Finance'` to isolate all staff within the Finance team.
* **Criteria 2:** `department = 'Sales'` to isolate all staff within the Sales team.


**Search Query:**
```sql
SELECT *
FROM employees
WHERE department = 'Finance' OR department = 'Sales';
```
<img width="975" height="341" alt="image" src="https://github.com/user-attachments/assets/e27ee0f7-21db-40d7-b316-c2ec241fcfa9" />

This query efficiently returned a combined list of all employees across both departments. Having this broader view allows us to verify asset coverage across these roles, ensuring that no devices are missed as we continue our security hardening process.

### 6. Compliance Auditing: Identifying Non IT Assets

In this step I need to address all devices outside of the IT department. To ensure I don't miss any endpoints during this rollout, I need to generate a comprehensive list of all staff members who fall outside the Information Technology team.

I wrote a SQL query to filter the `employees` table, specifically excluding the IT department to focus purely on the non-technical staff assets.

* **Filter Logic:** I used the `WHERE NOT` clause to explicitly exclude any records where the `department` is listed as 'Information Technology'.


**Search Query:**
```sql
SELECT *
FROM employees
WHERE NOT department = 'Information Technology';
```

<img width="978" height="398" alt="image" src="https://github.com/user-attachments/assets/8f6c7117-3163-4258-8310-1acfcd286a11" />

This query gave me a complete, filtered view of all non-IT staff. By isolating this group, I can now confidently proceed with the final security upgrades, knowing that all necessary departments have been identified and prepared for the maintenance window.

### Professional Context for Scaling to Enterprise Environments

While I performed lab was performed on a static dataset to master SQL logic, in a production I would refine these queries with the following enterprise security principles:

#### 1. Principle of Least Privilege (PoLP)
In this lab, I used `SELECT *` for full visibility. In a production database, I would explicitly select only the required columns (e.g., `SELECT username, login_timestamp`). This minimizes the exposure of PII (Personally Identifiable Information) and follows the Principle of Least Privilege, reducing the "blast radius" if a query is intercepted or viewed by unauthorized personnel.

#### 2. SecOps Metrics & Incident Response
Here I identified anomalies manually. At an enterprise level I would map these detection queries to formal SecOps metrics like **MTTD (Mean Time to Detect)**. As an architect, my goal is to reduce dwell time. Tracking how quickly an anomaly is flagged by these queries helps us measure the effectiveness of our incident response program and report performance to leadership.

#### 3. Transitioning to Real-Time Awareness
In this lab I ran manual SQL queries on static data. In production I would convert these filters into **Correlation Rules** within a SIEM (e.g., Splunk, Microsoft Sentinel) because security threats are dynamic.

### Final Summary & Report

Throughout this project I’ve used SQL as a core tool to investigate security anomalies and support IT maintenance. By applying various filtering strategies specifically `AND`, `OR`, and `NOT` operators I was able to isolate specific datasets to uncover meaningful insights, such as:

* **Security Analysis:** Tracking failed login attempts after business hours and identifying suspicious activity outside of authorized regions.
* **Operational Maintenance:** Efficiently segmenting employee data by department and office location to streamline security patch rollouts.
* **Pattern Matching:** Utilizing the `LIKE` operator and the `%` wildcard to search for flexible data patterns across large, complex tables.

These methods demonstrate how SQL is essential for cybersecurity operations, allowing us to organize, evaluate, and act on massive datasets effectively. 

---

### Looking Ahead

**GitHub**: Thank you for following along! This lab is part of my ongoing commitment to mastering incident response and forensic analysis. I’m constantly adding new labs and documentation—check back soon for upcoming projects! Check out my other works below: 
*  [**Wireshark-and-Nmap-Packet-Analysis-and-Network-Reconnaissance**](https://github.com/safayetchowdhury/Wireshark-and-Nmap-Packet-Analysis-and-Network-Reconnaissance)
*  [**Penetration Testing & Forensic Audit**](https://github.com/safayetchowdhury/Penetration-Testing-Forensic-Audit-Using-Kali-Ubuntu-Metasploit)
*  [**Incident Response with Splunk**](https://github.com/safayetchowdhury/Incident-Response-with-Splunk)

  
**LinkedIn:** I’d love to **[connect](https://www.linkedin.com/in/chowdhurysafayet/)**! If you’re working in information security or are interested in these methodologies, let's discuss how we can improve security posture together.


