# Lab 02a – Manage Subscriptions and RBAC

**Course:** AZ-104: Microsoft Azure Administrator (Scott Duffy, Udemy)  
**Date Completed:** June 27, 2026  
**Estimated Duration:** 30 minutes  
**Lab Reference:** [Official Microsoft Lab Instructions](https://github.com/MicrosoftLearning/AZ-104-MicrosoftAzureAdministrator/blob/master/Instructions/Labs/LAB_02a_Manage_Subscriptions_and_RBAC_Entra.md)

---

## Scenario

Your organization needs to streamline Azure administration. The task is to create a management group to organize subscriptions, assign a built-in RBAC role to a group scoped to that management group, and then create a custom RBAC role with least-privilege permissions for support requests.

---

## Environment

Working inside the Default Directory tenant with Microsoft Entra ID Free.

![Default Directory overview](screenshots/01-Task1-properties-overview.png)

| Detail | Value |
|---|---|
| Tenant name | Default Directory |
| Primary domain | vikramkm114cgmail.onmicrosoft.com |
| Tenant ID | 6ea7cd29-9aab-41fd-a026-602b66435a16 |
| License | Microsoft Entra ID Free |
| Users | 10 |
| Groups | 3 |

---

## Task 1 – Create a Management Group

### 1a. Enabled Access Management for Azure Resources

Navigated to **Microsoft Entra ID → Properties** and enabled the **Access management for Azure resources** toggle. This grants the Global Administrator role at the root management group scope, which is required to create and manage management groups.

![Default Directory Properties](screenshots/02-Task1-properties.png)

### 1b. Created the management group

Navigated to **All Services → Management groups**. The initial state showed only the Tenant Root Group with Azure subscription 1 nested beneath it.

![Management groups initial state](screenshots/03-Task1-management-groups.png)

Clicked **Create** and filled in the management group details.

![Create management group panel](screenshots/04-Task1-create-management-group.png)

| Setting | Value |
|---|---|
| Management group ID | az104-mg1 |
| Management group display name | az104-mg1 |
| Parent | Tenant Root Group |

**Result:** `az104-mg1` confirmed in the management groups list nested under Tenant Root Group.

![Management groups list after creation](screenshots/05-Task1-management-groups-list.png)

---

## Task 2 – Assign RBAC Roles at Management Group Scope

### 2a. Created the Help Desk security group

Before assigning roles, navigated to **Microsoft Entra ID → Groups** to create the group that would be assigned the role.

![All groups before Help Desk creation](screenshots/06-Task2-groups.png)

Created a new Security group with 1 owner and 3 members.

![New Help Desk group form](screenshots/07-Task2-create-new-helpdesk-group.png)

| Setting | Value |
|---|---|
| Group type | Security |
| Group name | Help Desk |
| Description | Help Desk Group |
| Membership type | Assigned |
| Members | 3 members selected |

### 2b. Assigned the Virtual Machine Contributor role

Navigated to **az104-mg1 → Access control (IAM)**. Reviewed the available built-in roles on the Roles tab.

![az104-mg1 overview](screenshots/08-Task2-az104-mg1.png)
![IAM Roles tab](screenshots/09-Task2-IAM-roles.png)

Clicked **Add → Add role assignment**.

![Add dropdown menu](screenshots/10-Task2-add-roles.png)

On the Role tab, searched for and selected **Virtual Machine Contributor**.

![Add role assignment - Role tab](screenshots/11-Task2-add-role-assignment.png)

On the Members tab, clicked **Select members** and chose the **Help Desk** group.

![Select members panel](screenshots/12-Task2-select-members.png)
![Members tab with Help Desk confirmed](screenshots/13-Task2-select-members-overview.png)

On the Conditions tab, left condition as None (not required for this role).

![Conditions tab](screenshots/14-Task2-conditions-tab.png)

Reviewed and clicked **Review + assign**.

![Review + assign tab](screenshots/15-Task2-review-and-assign.png)

| Setting | Value |
|---|---|
| Role | Virtual Machine Contributor |
| Scope | /providers/Microsoft.Management/managementGroups/az104-mg1 |
| Member | Help Desk (Group) |
| Condition | None |

**Result:** Role assignments tab confirmed 3 assignments — Help Desk group now has Virtual Machine Contributor at the az104-mg1 scope.

![Role assignments confirmed](screenshots/16-Task2-confirm-role.png)

> **Note:** The lab instructions reference an **Assignment type** tab (for Eligible vs Active PIM assignments). This tab does not appear on the free Entra ID license — it requires Entra ID Premium P2 and Privileged Identity Management (PIM). The role was assigned as Active permanently, which achieves the same end result for lab purposes.

---

## Task 3 – Create a Custom RBAC Role

### 3a. Initiated custom role creation

From **az104-mg1 → Access control (IAM)**, clicked **Add → Add custom role**.

![Add custom role dropdown](screenshots/17-Task3-add-custom-role.png)

### 3b. Configured the Basics tab

Named the role and selected **Clone a role**, using **Support Request Contributor** as the baseline.

![Create custom role - Basics tab](screenshots/18-Task3-create-custom-role-basics.png)

| Setting | Value |
|---|---|
| Custom role name | Custom Support Request |
| Description | A custom contributor role for support requests. |
| Baseline permissions | Clone a role |
| Role to clone | Support Request Contributor |

### 3c. Configured Permissions

The cloned role inherited 3 actions from Support Request Contributor.

![Permissions tab - initial state](screenshots/19-Task3-create-custom-role-permissions.png)

Clicked **Exclude permissions** and searched for `.support` to find Microsoft.Support permissions to subtract from the wildcard.

![Exclude permissions - search](screenshots/20-Task3-create-custom-role-exclude-permissions.png)

Selected **Microsoft.Support/register/action** (Registers Support Resource Provider) as a NotAction to exclude it from the role.

![Microsoft.Support permissions panel](screenshots/21-Task3-Microsoft_support-permissions.png)

**Result:** Permissions tab updated with 3 Actions and 1 NotAction.

![Permissions tab - final state](screenshots/22-Task3-create-custom-role-permissions-new.png)

| Permission | Type |
|---|---|
| Microsoft.Authorization/*/read | Action |
| Microsoft.Resources/subscriptions/resourceGroups/read | Action |
| Microsoft.Support/* | Action |
| Microsoft.Support/register/action | NotAction |

### 3d. Configured Assignable Scopes

The assignable scope was automatically set to az104-mg1 based on where the custom role was initiated.

![Assignable scopes tab](screenshots/23-Task3-create-custom-role-assignable-scope.png)

### 3e. Reviewed JSON definition

Verified the full role definition in JSON format before creating.

![JSON tab](screenshots/24-Task3-create-custom-role-json.png)

### 3f. Created the custom role

Reviewed the final configuration and clicked **Create**.

![Review + create tab](screenshots/25-Task3-create-custom-role-review-and-create.png)

---

## Task 4 – Review Activity Logs

Navigated to **az104-mg1 → Activity Log** to verify all operations completed successfully.

![Activity log - all events](screenshots/26-Task4-activity-log.png)

The unfiltered log showed 3 successful events within the last 6 hours:
- Create or update custom role definition
- Create role assignment
- Create or Update (management group creation)

Applied a filter for **Create role assignment** to isolate the RBAC assignment events.

![Activity log - filtered by role assignment](screenshots/27-Task4-filter-activity.png)

Both role assignment events confirmed as **Succeeded** — one initiated by the admin account and one by Azure Management Groups (the system-level assignment made when access management was enabled).

> **Note:** Activity logs are a key auditing tool in Azure. They record all control plane operations at the management group, subscription, and resource group level, making it possible to track who made changes, when, and whether they succeeded.

---

## Key Takeaways

- **Management groups** provide a governance layer above subscriptions, allowing you to apply policies and RBAC roles that inherit down to all child subscriptions and resource groups.
- **RBAC roles should be assigned to groups, not individuals.** This is a best practice — it reduces administrative overhead and ensures access is managed at scale.
- **Built-in roles** cover most scenarios but may grant more permissions than needed. When a built-in role is too broad, create a **custom role** using least-privilege principles.
- **Custom roles** are created by cloning an existing role and
