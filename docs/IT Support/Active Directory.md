# Active Directory

This guide walks you through the installation and configuration of **Active Directory Domain Services (AD DS)** using the **Server Manager GUI**. We’ll cover everything from naming your machine (e.g., `mat-lap-001`) to creating users (like `lincoln.basnet`), building Organizational Units, and delegating rights — with clear, step-by-step instructions.

---

## 🧠 Core Concepts of Active Directory

### 🌳 Forest
- A **forest** is the highest-level container in Active Directory.
- It acts as a **security boundary**, defining trust boundaries.
- A forest can contain **one or more domains** that trust each other.
- Installing AD DS for the first time creates a **new forest**.

> Example: Installing AD DS with the root domain `matrix.com.np` creates the **Matrix Forest**.

---

### 🏠 Domain
- A **domain** is a **logical group of objects** (users, computers, printers, etc.) inside a forest.
- Objects in a domain share the **same database, security policies, and trust relationships**.
- Each domain is managed by **domain controllers**.

> Example: In the forest `matrix.com.np`, the domain is also `matrix.com.np`.

---

### 🏢 Organizational Unit (OU)
- An **Organizational Unit** (OU) is a container inside a domain used to group and manage AD objects.
- OUs let you organize users, computers, and groups logically (e.g., `Sales`, `HR`).
- OUs are essential for **applying Group Policies** and **delegating administration**.

> Example: Create an OU called `Sales` and place all sales department accounts inside it.

---

### 👑 Domain Controller (DC)
- A **Domain Controller** is a server running AD DS that handles:
  - User logins  
  - Authentication  
  - Access control  
  - Directory lookups  
- Every DC keeps a **copy of the AD database**, synchronized across all DCs in the domain.

---

### 🖥️ Local Domain Controller
- A **Local DC** is another domain controller installed at a branch office.
- It ensures **logins and directory services remain available** even if the main site is offline.
- Useful in multi-office or geographically distributed organizations.

---

## 🧑‍💻 Prerequisites

Before installing Active Directory, make sure you have:
- A freshly installed Windows Server (2016 or later).  
- A computer named `mat-lap-001`.  
- Static IP configuration set.  
- Administrator access.  

---

## 🏗️ Installing Active Directory (GUI Method)

### ✅ Step 1: Rename Computer & Set IP
1. Go to **Control Panel > System > Change settings**.  
2. Rename the computer to `mat-lap-001`.  
3. Open **Network and Sharing Center > Change adapter settings**.  
4. Right-click Ethernet → **Properties**.  
5. Select `IPv4` → Set a **static IP, subnet mask, and gateway**.  

---

### ✅ Step 2: Add AD DS Role
1. Open **Server Manager**.  
2. Click **Add Roles and Features**.  
3. Choose:
   - **Role-based or feature-based installation**  
   - Select your server (`mat-lap-001`)  
4. Under **Server Roles**, check:  
   - ✅ **Active Directory Domain Services**  
   - Add required features when prompted.  
5. Click **Next** and then **Install**.  

---

### ✅ Step 3: Promote Server to Domain Controller
1. After installation, click **Promote this server to a domain controller**.  
2. Select **Add a new forest** → Enter root domain name: `matrix.com.np`.  
3. Set a **DSRM (Directory Services Restore Mode) password**.  
4. Accept default DNS options.  
5. Accept default paths for:  
   - Database folder  
   - Log files  
   - SYSVOL  
6. Review and click **Install**.  
7. The server will reboot automatically.  

---

## 👥 Creating Organizational Units and Users

### ✅ Step 1: Create an OU
1. Open **Active Directory Users and Computers** (`dsa.msc`).  
2. Right-click domain `matrix.com.np` → **New > Organizational Unit**.  
3. Name the OU (e.g., `Sales`).  

---

### ✅ Step 2: Create a User
1. Right-click the OU (`Sales`) → **New > User**.  
2. Enter details:  
   - First name: `Lincoln`  
   - Last name: `Basnet`  
   - User logon: `lincoln.basnet`  
3. Set a password and user options.  

---

## 🔐 Delegating Rights (Delegation of Control)

You can delegate control so non-admin users (e.g., `lincoln.basnet`) can manage accounts within an OU.

### ✅ Steps to Delegate Control
1. Open **Active Directory Users and Computers**.  
2. Right-click the OU (e.g., `Sales`) → **Delegate Control**.  
3. The wizard opens → Click **Next**.  
4. Click **Add** → Type `lincoln.basnet` → **OK** → **Next**.  
5. Choose tasks to delegate, such as:  
   - ✅ Create, delete, and manage user accounts  
   - ✅ Reset user passwords and force password changes  
6. Click **Next** → **Finish**.  

> ✅ Now `lincoln.basnet` can manage users inside the `Sales` OU without needing domain admin rights.

---
