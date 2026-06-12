# ☁️ AWS IAM User with Administrator Access and Billing Access

## 📌 Lab Overview

This lab demonstrates how to:

- 👤 Create an IAM User  
- 🔐 Grant Administrator Access permissions  
- 💰 Enable IAM access to Billing and Cost Management  
- ✔️ Verify Billing access using the IAM User  

---

## 🧰 AWS Services Used

- 🛡️ IAM (Identity and Access Management)  
- 💳 AWS Billing and Cost Management  

---

## 🪪 Step 1: Create an IAM User

1. Sign in to the AWS Management Console  
2. Navigate to the IAM service  
3. Select **Users** from the left navigation pane  
4. Click **Create User**  
5. Enter a username  
6. Click **Next**  

### 📸 Screenshot

![Create User](photo1.png)

---

## 🔑 Step 2: Assign Administrator Permissions

1. Select **Attach Policies Directly**  
2. Search for **AdministratorAccess**  
3. Check the policy  
4. Click **Next**  
5. Review the configuration  
6. Click **Create User**  

### 📸 Screenshot

![Administrator Access](photo2.png)

---

## 💰 Step 3: Enable Billing Access for IAM Users

1. Sign in using the AWS Root Account  
2. Open the **Account** page  
3. Scroll to **IAM User and Role Access to Billing Information**  
4. Click **Activate IAM Access**  
5. Save the changes  

### 📸 Screenshot

![Billing Access](photo3.png)

---

## ✔️ Step 4: Verify Billing Access

1. Sign out from the Root Account  
2. Sign in using the IAM User  
3. Search for **Billing** in the AWS Console  
4. Open the Billing Dashboard  
5. Verify that Billing information is accessible  

### 📸 Screenshot

![Billing Dashboard](photo4.png)

---

## ✅ Validation

The following checks were performed:

- 👤 IAM User created successfully  
- 🔐 AdministratorAccess policy attached  
- 💰 Billing access enabled from the Root Account  
- ✔️ IAM User can access Billing Dashboard  

---

## 🎯 Result

The IAM User was successfully configured with **Administrator privileges** and granted access to **AWS Billing information**. The user can now manage AWS resources and view account billing details.

---

## 📚 Key Learning Outcomes

- 🧑‍💻 Understanding AWS IAM User creation  
- 🔐 Assigning AWS managed policies  
- ⚖️ Difference between Root User and IAM User permissions  
- 💰 Enabling Billing access for IAM Users  
- ✔️ Verifying access permissions in AWS  

---

## 👨‍💻 Author

Ahmed Soliman 🚀