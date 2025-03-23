# **Mandatory Access Control (`MAC`)**  

**Mandatory Access Control (MAC)** is a strict security model where **`access control decisions are enforced by the system, not by individual users or resource owners`**. Unlike **DAC (Discretionary Access Control)**, where users can grant permissions, MAC applies **predefined security policies** that users **cannot override**.

---

# **1. What is MAC?**  
MAC is a **highly restrictive access control model** commonly used in environments requiring **high security**, such as **military, government, and critical infrastructure systems**.

## **Key Characteristics of MAC**
1. **System-Enforced Security** → The **operating system or application enforces rules**, not users.  
2. **Security Labels & Classifications** → Resources and users have **security labels** like "Confidential," "Secret," and "Top Secret."  
3. **No User Discretion** → Users **cannot modify** permissions on resources they own.  
4. **Hierarchical Security Model** → Access follows strict **security clearance levels**.  

---

# **2. How MAC Works - Conceptual Flow**
1. **Each resource (file, database, network request) is assigned a classification label** (e.g., "Confidential," "Top Secret").  
2. **Each user is assigned a clearance level** (e.g., "Secret," "Unclassified").  
3. **The system enforces access rules** based on the user's clearance level and the resource’s classification.  

---

# **3. Example: MAC in Action**
## **Scenario: Military Document Classification**
In a military setting, documents are labeled with **security classifications**, and users have **clearance levels**.

| **Document**      | **Classification** |
|-------------------|-------------------|
| battle_plan.pdf  | Top Secret |
| mission_report.doc | Secret |
| general_orders.txt | Unclassified |

| **User**   | **Clearance Level** |
|-----------|-----------------|
| **Alice** | Top Secret |
| **Bob**   | Secret |
| **Charlie** | Unclassified |

## **Access Evaluation:**
✅ **Alice (Top Secret) tries to read "battle_plan.pdf"** → **Access Granted**  
✅ **Bob (Secret) tries to read "mission_report.doc"** → **Access Granted**  
❌ **Charlie (Unclassified) tries to read "mission_report.doc"** → **Access Denied**  

---

# **4. MAC Components**
## **1. Security Labels**
- Resources (files, records, network packets) are assigned **classification labels** such as:
  - **Unclassified**
  - **Confidential**
  - **Secret**
  - **Top Secret**

- Users are assigned **clearance levels** that match the classification hierarchy.

## **2. Security Policy Enforcement Mechanisms**
- **Bell-LaPadula Model** → **No Read Up (NRU), No Write Down (NWD)**
  - Users **cannot read information at a higher classification** level.
  - Users **cannot write data to a lower classification** level.

- **Biba Model** → **No Read Down (NRD), No Write Up (NWU)**
  - Ensures **data integrity** by preventing low-privileged users from modifying high-integrity resources.

---

# **5. MAC in Operating Systems**
MAC is built into **high-security operating systems** like **SELinux (Security-Enhanced Linux), AppArmor, and Windows Mandatory Integrity Control (MIC).**

## **Example: SELinux MAC in Linux**
SELinux enforces MAC policies using **security contexts**.

### **Checking File Security Labels:**
```bash
ls -Z /var/www/html/index.html
```
🔹 **Output Example:**
```plaintext
-rw-r--r--. root root system_u:object_r:httpd_sys_content_t:s0 /var/www/html/index.html
```
| **Field** | **Meaning** |
|-----------|------------|
| `system_u` | SELinux user |
| `object_r` | Role |
| `httpd_sys_content_t` | File type (Apache content) |
| `s0` | Security Level |

### **Changing a File’s Security Context:**
```bash
chcon -t httpd_sys_content_t /var/www/html/index.html
```

### **Checking a Process Security Context:**
```bash
ps -Z | grep httpd
```

---

# **6. MAC in Databases**
MAC can be applied in **database security** to restrict access based on classification levels.

## **Example: PostgreSQL Row-Level Security (RLS) with MAC**
PostgreSQL allows row-level policies to enforce MAC-like restrictions.

### **Step 1: Create Users with Security Levels**
```sql
CREATE USER alice WITH PASSWORD 'secretpass';
CREATE USER bob WITH PASSWORD 'securepass';
```

### **Step 2: Create a Table with Security Levels**
```sql
CREATE TABLE classified_data (
    id SERIAL PRIMARY KEY,
    content TEXT,
    classification TEXT CHECK (classification IN ('Unclassified', 'Confidential', 'Secret', 'Top Secret')),
    owner TEXT
);
```

### **Step 3: Enforce MAC-Based Access Policy**
```sql
CREATE POLICY security_policy ON classified_data
FOR SELECT USING (classification = current_user);
```
Now, **users can only access rows that match their clearance level.**

---

# **7. Advantages of MAC**
| **Advantage** | **Description** |
|--------------|----------------|
| **High Security** | Prevents unauthorized access by enforcing strict policies. |
| **Enforced by System** | Users cannot override security settings, reducing risks. |
| **Prevents Data Leaks** | Stops unintentional sharing of sensitive information. |
| **Ideal for Critical Systems** | Used in military, government, and healthcare. |

---

# **8. Disadvantages & Security Challenges**
| **Challenge** | **Risk** |
|--------------|----------------|
| **Complex Implementation** | Requires advanced configuration and administration. |
| **Less Flexibility** | Users cannot modify permissions, which may slow down workflow. |
| **Difficult to Scale** | Managing security classifications at scale can be challenging. |
| **Not Suitable for All Use Cases** | Overkill for environments where user discretion is acceptable. |

---

# **9. MAC vs. Other Access Control Models**
| Feature | MAC (Mandatory) | DAC (Discretionary) | RBAC (Role-Based) |
|---------|----------------|----------------|-----------------|
| **Control** | System-enforced | User-enforced | Role-based |
| **Security Risk** | Low | High (users can override) | Medium |
| **Flexibility** | Low | High | Medium |
| **Common Use Cases** | Military, Government | Personal File Systems | Enterprises, Cloud Security |
| **Example** | SELinux, MIC | Windows NTFS, Linux Permissions | AWS IAM Roles, Kubernetes RBAC |

---

# **10. MAC Use Cases**
| **Industry** | **Use Case** |
|-------------|-------------|
| **Government & Military** | Enforcing security clearance levels for classified information. |
| **Healthcare** | Protecting patient records based on clearance levels (HIPAA compliance). |
| **Banking & Finance** | Preventing unauthorized access to financial transactions. |
| **Secure Cloud Computing** | Applying strict access control in **AWS, Azure, and Google Cloud**. |

---

# **11. Key Takeaways**
- **MAC enforces strict access control rules** set by the system, not users.  
- **Security Labels & Classifications** define access rights.  
- **Users cannot override system-enforced policies**, reducing security risks.  
- **Used in military, government, healthcare, and high-security environments.**  
- **More secure than DAC but less flexible.**  
- **Implemented in SELinux, Windows MIC, and high-security database systems.**  

---