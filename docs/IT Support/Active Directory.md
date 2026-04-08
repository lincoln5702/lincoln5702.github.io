# Active Directory

Active Directory Domain Services (AD DS) is how Windows manages users, computers, and resources across a network. Once you set it up, you get a central place to control who can log in, what they can access, and how policies get applied across machines.

This guide walks through installing AD DS using Server Manager, promoting a server to a domain controller, creating users, managing printer access through security groups, and delegating rights — all through the GUI.

---

## How Active Directory is Structured

Before jumping into the install, it helps to understand what you're actually building. AD has a hierarchy, and knowing where each piece fits saves confusion later.

**Forest**

The forest is the top-level container in Active Directory — the widest security boundary. A fresh AD install creates a new forest. A forest can contain multiple domains that trust each other, but from a security perspective, the forest boundary is what matters most.

When you install AD DS with root domain `matrix.com.np`, you're creating the Matrix forest.

**Domain**

A domain lives inside the forest. It's a logical group of objects — users, computers, printers — that share the same database, security policies, and trust relationships. Domain controllers manage everything inside it.

In most small setups, the forest and the domain share the same name: `matrix.com.np`.

**Organizational Unit (OU)**

An OU is a container inside a domain. Think of it as a folder for organising AD objects by department, location, or function — Sales, HR, IT, whatever makes sense for the environment.

OUs matter for two reasons: applying Group Policy Objects (GPOs) and delegating admin rights. If you want IT helpdesk staff to reset passwords for the Sales team without giving them full domain admin access, OUs are how you do that.

**Domain Controller**

The domain controller is the server running AD DS. It handles authentication, logins, and access control — and keeps a copy of the AD database. In a production environment you always want at least two, so there's no single point of failure.

**Branch Office / Local DC**

If an organisation has multiple offices, a local domain controller at each branch keeps logins and directory services working even if the link back to the main site goes down. Users don't notice the difference — which is the point.

---

## Prerequisites

- Windows Server 2016 or later, freshly installed
- Computer named `mat-lap-001`
- Static IP address configured
- Local administrator access

---

## Installing Active Directory

### Step 1 — Rename the computer and set a static IP

Go to **Control Panel > System > Change settings** and rename the machine to `mat-lap-001`. Then open **Network and Sharing Center > Change adapter settings**, right-click the Ethernet adapter, go to **Properties**, select IPv4, and set your static IP, subnet mask, and default gateway.

Reboot after renaming if you haven't already.

### Step 2 — Add the AD DS role

Open **Server Manager** and click **Add Roles and Features**. Walk through the wizard:

- Installation type: Role-based or feature-based installation
- Server selection: `mat-lap-001`
- Server roles: check **Active Directory Domain Services**, and add the required features when prompted

Click through to **Install** and wait for it to finish. Don't close Server Manager.

### Step 3 — Promote the server to a domain controller

After the role installs, you'll see a notification flag in Server Manager. Click it and select **Promote this server to a domain controller**.

In the wizard:

- Deployment operation: **Add a new forest**
- Root domain name: `matrix.com.np`
- Leave DNS options as default
- Accept the default paths for the database, log files, and SYSVOL

Review the summary, click **Install**, and the server will reboot automatically. After reboot, log in with the domain admin credentials.

---

## Creating Organisational Units and Users

### Create an OU

Open **Active Directory Users and Computers** (run `dsa.msc`). Right-click the domain `matrix.com.np`, go to **New > Organizational Unit**, and name it — for example, `Sales`.

### Create a user

Right-click the `Sales` OU, select **New > User**, and fill in:

- First name: Lincoln
- Last name: Basnet
- User logon name: `lincoln.basnet`

Set a password and configure the account options as needed. Standard practice is to require a password change on first login.

---

## Managing Printer Access with Security Groups

In Active Directory, printers are shared resources — and controlling who can use which printer is best handled through security groups rather than assigning permissions to individual users. This scales much better once you have more than a handful of people.

The idea is straightforward: create a security group for each printer, share the printer, assign the group permissions on that share, then add users to the group as needed. When someone moves departments you update group membership — the printer permissions themselves never change.

### Step 1 — Add the Print and Document Services role

Open **Server Manager**, click **Add Roles and Features**, and under Server Roles select **Print and Document Services**. Add the required features when prompted, then install.

### Step 2 — Share the printer

Once the role is installed, open the **Print Management** console (run `printmanagement.msc`). Expand your server, right-click **Printers**, and add the printer. After adding it, right-click the printer and select **List in Directory** — this publishes it to AD so users can find it through search.

To share it, right-click the printer, go to **Printer Properties > Sharing**, check **Share this printer**, and give it a share name like `Sales-HP-LaserJet`.

### Step 3 — Create a security group for the printer

Open **Active Directory Users and Computers**, right-click the appropriate OU — or create a dedicated `Printers` OU to keep things tidy — and go to **New > Group**. Configure it as:

- Group name: `GRP-Print-Sales-HP`
- Group scope: **Global**
- Group type: **Security**

A naming convention like `GRP-Print-[Location/Dept]-[Device]` keeps things readable as the environment grows.

If you want a separate group with management rights over the printer — to pause jobs, adjust settings — create a second group like `GRP-Print-Sales-HP-Manage`.

### Step 4 — Set permissions on the printer share

Back in **Print Management**, right-click the printer and open **Printer Properties > Security**. Remove **Everyone** from the list if it's there — it shouldn't have print access by default. Click **Add**, type the group name `GRP-Print-Sales-HP`, and grant it **Print** permission.

If you created a management group, add `GRP-Print-Sales-HP-Manage` and grant it **Manage Documents** and **Manage Printers**.

### Step 5 — Add users to the printer group

Open **Active Directory Users and Computers**, find the group `GRP-Print-Sales-HP`, right-click it, and go to **Properties > Members > Add**. Type the username — `lincoln.basnet` — click **Check Names** to confirm, then **OK**.

From that point, `lincoln.basnet` can connect to the printer. They can find it through **Control Panel > Devices and Printers > Add a printer > Search Active Directory**, or by browsing directly to `\\mat-lap-001\Sales-HP-LaserJet`.

Adding someone to a different department's printer group works exactly the same way — find the relevant group, add the user. That's the value of this setup. The access model is just group membership.

---

## Delegating Control

Delegation is one of the most useful things about OUs. Instead of giving helpdesk staff domain admin rights just to reset a password, you can give them exactly the rights they need within a specific OU.

Open **Active Directory Users and Computers**, right-click the `Sales` OU, and select **Delegate Control**. The wizard will open:

1. Click **Next**
2. Click **Add**, type `lincoln.basnet`, click **OK**, then **Next**
3. Select the tasks to delegate — for a helpdesk scenario, the typical choices are:
   - Create, delete, and manage user accounts
   - Reset user passwords and force password changes
4. Click **Next**, then **Finish**

`lincoln.basnet` can now manage users inside the Sales OU without touching anything else in the domain. The principle of least privilege in practice.

---

## What Could Go Wrong

### DNS issues

AD relies heavily on DNS. If DNS records don't get created correctly after promotion, domain joins will fail silently or with confusing errors. Check Event Viewer under `Applications and Services Logs > Directory Service` if something seems off.

A quick sanity check from a client machine:

```
nslookup matrix.com.np
```

If that doesn't resolve to the domain controller's IP, DNS is the problem, not AD itself.

---

### "The trust relationship between this workstation and the primary domain failed"

This is one of the more frustrating errors in AD environments, and it comes up often enough that it's worth knowing how to deal with it quickly.

**What it means**

Every domain-joined machine maintains a secure channel with the domain controller — a machine account with its own password that rotates automatically every 30 days. When that password gets out of sync between the machine and the DC, the trust breaks. The machine still thinks it's on the domain, but the DC no longer recognises its credentials.

The result: users can't log in with their domain accounts. Local accounts still work, which is how you get back in.

The most common causes are:

- The machine was offline or shut down for more than 30 days
- A VM was restored from an old snapshot, so it rolled back to an outdated machine account password
- The machine was cloned from another domain-joined machine without being properly sysprep'd

**How to fix it — PowerShell method (preferred)**

Log in with a local administrator account, then run:

```powershell
Test-ComputerSecureChannel -Repair -Credential (Get-Credential)
```

When prompted, enter domain admin credentials. This resets the secure channel without removing the machine from the domain, so local profiles and settings are preserved. Most of the time this is all you need.

Verify it worked with:

```powershell
Test-ComputerSecureChannel
```

It should return `True`.

**How to fix it — disjoin and rejoin (fallback)**

If the PowerShell method doesn't work, the manual approach is to remove and re-add the machine to the domain:

1. Log in with a local admin account
2. Go to **System Properties > Computer Name > Change**
3. Switch membership to **Workgroup** — use anything, like `WORKGROUP`
4. Reboot
5. Go back to **System Properties > Computer Name > Change**
6. Switch back to **Domain**, enter `matrix.com.np`
7. Provide domain admin credentials when prompted
8. Reboot

