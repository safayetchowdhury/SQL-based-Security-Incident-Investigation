# Project: SQL-Based Security Incident Investigation

This project demonstrates the application of SQL as a core tool for technical data analysis, completed as part of the Google Cybersecurity Professional Certificate. In any technical role, we constantly dealing with massive amounts of data, and inability of filtering through that noise to find what we actually need make us stuck.

I wanted to show that I can go beyond just running basic commands and I can structure my queries to isolate specific issues, manage assets, and pull the right information out of a database under different conditions. I recently investigated a potential security incident that occurred after business hours. I used SQL queries to identify suspicious login activity and categorize employee data, surfacing patterns relevant to system security and ongoing IT updates.

---

### Incident 1: After-Hours Authentication Failure

This query is designed to pull up all the login attempts that happened after 6:00 PM and failed. To get this info, I started by selecting all the data from the `log_in_attempts` table. From there, I needed to narrow things down, so I used a `WHERE` clause combined with an `AND` operator to make sure I was only seeing the specific failures I was looking for.

* **Filter 1:** `login_time > '18:00'` to isolate attempts after 6:00 PM.
* **Filter 2:** `success = FALSE` to isolate only the failed attempts.

**Search Query:**
```sql
SELECT *
FROM log_in_attempts
WHERE login_time > '18:00' AND success = FALSE;
```

<img width="975" height="473" alt="image" src="https://github.com/user-attachments/assets/3d52b173-015a-4d74-841d-dea84211cd10" />

### 2. Event Correlation: Time-Based Incident Scoping

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


