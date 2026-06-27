# Lab 01 – Manage Microsoft Entra ID Identities

**Course:** AZ-104: Microsoft Azure Administrator (Scott Duffy, Udemy)  
**Date Completed:** June 26, 2026  
**Estimated Duration:** 30 minutes  
**Lab Reference:** [Official Microsoft Lab Instructions](https://github.com/MicrosoftLearning/AZ-104-MicrosoftAzureAdministrator/blob/master/Instructions/Labs/LAB_01-Manage_Entra_ID_Identities.md)

---

## Scenario

A new lab environment is being built for pre-production testing. Engineers need to authenticate using Microsoft Entra ID. The task is to provision internal users, invite an external guest, and organize them into a security group.

---

## Task 1 – Create and Configure User Accounts

### 1a. Created an internal user (`az104-user1`)

| Setting | Value |
|---|---|
| User principal name | az104-user1@vikramkm114cgmail.onmicrosoft.com |
| Display name | az104-user1 |
| Password | Auto-generated |
| Job title | IT Lab Administrator |
| Department | IT |
| Usage location | United States |

![Create new user - Basics](screenshots/01-create-user-basics.png)
![Create new user - Properties](screenshots/02-create-user-properties.png)
![Create new user - Review](screenshots/03-create-user-review.png)

### 1b. Invited an external guest user

Used a secondary Gmail address to simulate a B2B guest scenario. The guest account was provisioned with the same job title and department as the internal user.

| Setting | Value |
|---|---|
| Email | vikramkm114a@gmail.com |
| Display name | Vikram Malhotra |
| User type | Guest |
| Job title | IT Lab Administrator |
| Department | IT |
| Usage location | United States |

![Invite external user - Basics](screenshots/04-invite-external-basics.png)
![Invite external user - Properties](screenshots/05-invite-external-properties.png)
![Invite external user - Review](screenshots/06-invite-external-review.png)

**Result:** User list confirms 10 users — `az104-user1` as Member and guest `Vikram Malhotra` as Guest.

![All users list](screenshots/07-all-users-list.png)

> **Note:** My first invite attempt failed because I used the same email already associated with my admin account. Azure treated it as an existing identity and did not create a new guest entry. Re-inviting with a different email resolved this. Key lesson: Entra ID deduplicates identities across Microsoft accounts — you cannot invite yourself as a guest.

---

## Task 2 – Create a Group and Add Members

Created a Security group with Assigned membership and added both the internal user and the guest.

| Setting | Value |
|---|---|
| Group type | Security |
| Group name | IT Lab Administrators |
| Description | Administrators that manage the IT lab |
| Membership type | Assigned |
| Owner | Vikram Malhotra (admin account) |
| Members | az104-user1 (Member), Vikram Malhotra (Guest) |

![New group creation form](screenshots/08-new-group-form.png)
![All groups list](screenshots/09-all-groups-list.png)
![Group overview](screenshots/10-group-overview.png)
![Group members](screenshots/11-group-members.png)
![Group owners](screenshots/12-group-owners.png)

**Result:** `IT Lab Administrators` confirmed with 2 direct members, 1 owner, Type: Security, Source: Cloud.

---

## Key Takeaways

- A **tenant** is a dedicated instance of Microsoft Entra ID representing your organization. All users and groups live within a tenant.
- **Internal users** (Members) are provisioned directly in the directory. **Guest users** are external identities invited via B2B collaboration — they authenticate with their own identity provider but are granted scoped access to your tenant.
- Entra ID deduplicates identities — inviting an email already registered in the tenant will not create a new guest account.
- **Security groups** can contain a mix of Members and Guests and are used to manage access to resources at scale.
- **Dynamic membership** (auto-updating based on user attributes like job title) requires Entra ID Premium P1 or P2. With the free license, only **Assigned** membership is available.

---

## Directory Summary (Post-Lab)

| Metric | Value |
|---|---|
| Total users | 10 |
| Total groups | 3 |
| License | Microsoft Entra ID Free |
| Primary domain | vikramkm114cgmail.onmicrosoft.com |
