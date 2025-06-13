# 🔐 AWS IAM Tutorial – Creating a Secure User Group and IAM User

This guide walks you through creating a secure AWS Identity and Access Management (IAM) **User Group** with limited permissions, and assigning an **IAM User** to that group. This approach follows best practices for securely managing AWS access.

---

## 📌 Use Case

The goal of this tutorial is to:

* Create a **User Group** named `SupportEngineers` with **read-only access to EC2**.
* Add an IAM User `support-engineer-1` to the group.
* Provide that user with **secure console access**, including **password**, **tags**, and **restricted permissions**.
* Demonstrate what happens when restricted permissions are enforced by attempting to terminate an EC2 instance.

---

## 🧱 AWS Services Involved

| Service                                  | Description                                                                                                                             |
| ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| **IAM (Identity and Access Management)** | Enables you to securely control access to AWS services and resources. You can create and manage users, groups, and permission policies. |
| **Amazon EC2**                           | Provides scalable virtual servers (instances). IAM policies restrict which users or groups can manage instances.                        |

---

## 🚀 Step-by-Step Walkthrough

### 🧑‍🤝‍🧑 1. Create a User Group – `SupportEngineers`

* Go to the **IAM Console**.
* Navigate to **User groups** > **Create group**.
* Enter the group name: `SupportEngineers` *(case-sensitive)*.
* Attach the **AmazonEC2ReadOnlyAccess** policy.
* Click **Create group**.

🔒 This group will only have **read-only access** to EC2 resources.

---

### 👤 2. Create a User – `support-engineer-1`

* Go to **Users** > **Create user**.
* Enter the user name: `support-engineer-1`.
* Enable AWS Management Console access.
* Set a custom password: `supportPassword!123` *(example only – use secure passwords in production)*.

### 👥 Add the User to the Group

* In the **Permissions** step, choose **Add user to group**.
* Select the `SupportEngineers` group.
* Proceed to the next step.

### 🏷️ Add Tags (Metadata)

* Add a tag with:

  * **Key**: `job-title`
  * **Value**: `Support Engineer`

Tags help in organizing and identifying IAM users in audits and billing.

---

### 🧾 Finalize and Access Details

* On the final screen, note the **Console Sign-in URL**.
* Optionally, **Download the .csv** file containing user credentials.
* Use the credentials to test login via an **incognito/private browser**.

---

## 🔐 IAM Security Credentials Overview

Once the user is created:

* Navigate to the **Security credentials** tab for `support-engineer-1`.
* Review:

  * **Multi-Factor Authentication (MFA)** – enhances login security.
  * **Access Keys** – used for programmatic access via AWS CLI or SDKs.

---

## 🧪 Permission Test (Read-Only Validation)

Login as `support-engineer-1`, then:

1. Search and open the **EC2 Dashboard**.
2. Select the **WebServer instance**.
3. Attempt to **terminate the instance**.

🚫 The action will **fail** because the user has only **read-only** EC2 permissions.

This confirms that the IAM policy is working correctly.

---

## ✅ What This Demonstrates

* **Principle of Least Privilege**: Users should only have the access they need.
* **IAM Group Management**: Efficiently manage permissions for multiple users.
* **Tagging and Metadata**: Track users with meaningful labels.
* **Security First**: Console password, MFA, and access key visibility all align with AWS security practices.
