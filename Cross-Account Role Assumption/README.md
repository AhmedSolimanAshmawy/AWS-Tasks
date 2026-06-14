**🚀 Ultimate Guide: Cross-Account Access via IAM Roles ☁️🔐**



🎯 Objective: To securely grant a user (test) in one account access to an S3 Bucket located in another host account (Ahmed-Admin) using the powerful AssumeRole mechanism. This is the gold standard for AWS security and Role-Based Access Control (RBAC)! 🏆

**🏢 Part 1: Host Environment Setup (Ahmed-Admin Account)**


Step 1: Create the S3 Bucket & Upload 🪣📁

👤 Log into your host account (Ahmed-Admin).

➕ Create a brand new S3 bucket.

📄 Upload a test file to the bucket so we have something to look at later!

<img width="725" height="334" alt="creating a bucket" src="https://github.com/user-attachments/assets/15c8d3ce-6a0d-492d-91fe-857826a48f4f" />



Step 2: Verify the User's Initial State 🛑🕵️‍♂️


🔄 Switch over to the test user.

🚫 Notice that this user currently does not have permission to access the S3 service.

❌ An "Access Denied" error will pop up when trying to open S3. (This is perfect! AWS is doing its job protecting your data).

<img width="959" height="380" alt="test-doesnot have S3 permission" src="https://github.com/user-attachments/assets/216e3202-e8b3-493a-9027-f24d4b1f967c" />


🛠️ Part 2: Creating the Gateway (IAM Role Configuration)


Step 3: Start the Role Creation 🎭✨


🔙 Jump back to the host account (Ahmed-Admin) where the S3 bucket lives.

⚙️ Navigate to the IAM dashboard and click Create Role.

<img width="959" height="377" alt="screen 3" src="https://github.com/user-attachments/assets/c9cb1c1a-6fcd-4359-b535-45879dec0917" />


Step 4: Choose the Trusted Entity Type 🤝🌐


✅ Select AWS account.

🧠 Why? Because we want to allow another AWS account (or user) to use this role, not an internal AWS service like EC2.

🛡️ Pro Security Tips:

🔑 External ID: A secret token used to prevent the "Confused Deputy" problem when a third party assumes your role.

📱 MFA: A security requirement that forces the user to type a code from their phone app before they can put on the "Role mask".


Step 5: Add Permissions 📜✅


🔍 Search for and attach the exact permission this role needs (e.g., AmazonS3FullAccess).

<img width="940" height="382" alt="screen 5" src="https://github.com/user-attachments/assets/64baa3a4-b2b3-499d-9c79-f63d04bee40a" />



Step 6: Name the Role 🏷️✍️


⌨️ Type a clear, descriptive name for your new role so you can easily spot it later.

<img width="939" height="338" alt="screen 6" src="https://github.com/user-attachments/assets/f25dd4f6-bf7f-40d1-83fd-6d4fbfb71c89" />



Step 7: Complete Role Creation 🎉✅



🥳 Boom! The role is successfully created in the host account.

📋 CRITICAL: Copy the Role ARN right now. You're going to need it in the next part!

<img width="950" height="380" alt="screen 7" src="https://github.com/user-attachments/assets/c1683b9c-f697-420a-8638-a9e29638f4c7" />

🔑 Part 3: Granting the VIP Pass (test Account)


Step 8: Add an Inline Policy 📝🔐



🔄 Switch your environment back to the test user account.

⚙️ Go to the IAM settings for the test user and click Add permissions -> Create inline policy. We need to give them access to the STS (Security Token Service).

<img width="950" height="370" alt="screen 8" src="https://github.com/user-attachments/assets/0bd05336-8b60-459f-8cde-be7a6d694be2" />


Step 9: Write the Policy & Attach the ARN 💻🔗


🟢 Set the Action to sts:AssumeRole.

🎯 In the Resource section, paste that Role ARN you copied from the Ahmed-Admin account.

<img width="940" height="377" alt="screen 9" src="https://github.com/user-attachments/assets/bac2aaa6-b547-4ce7-a40a-36e33979cf2c" />



Step 10: Verify Attachment 📌👍


💾 Save it! The policy is now successfully attached to the test user. They officially have their VIP ticket! 🎟️

<img width="948" height="377" alt="screen 10" src="https://github.com/user-attachments/assets/5ef152b7-08a1-4434-ad92-80a8dbf67d60" />

🎇 Part 4: The Magic Moment (Switch Role)


Step 11: Initiate the Switch 🔄🎩


👤 While logged in as the test user, click on your account name in the top right corner of the console.

🖱️ Click on Switch Role.

<img width="942" height="416" alt="screen 11" src="https://github.com/user-attachments/assets/c0bdaaed-5bcd-4799-880b-89e53bd0fe95" />


Step 12: Enter the Details 📝🔍


📋 Fill in the required fields:

🏢 Account: Type the exact account-num of the host account.

🎭 Role: Type the exact role name you created earlier.

🚀 Smash that Switch Role button!

<img width="914" height="424" alt="screen 12" src="https://github.com/user-attachments/assets/5f7f331a-2be6-4043-a9bc-957863b22c2c" />


Step 13: Mission Accomplished! 🏆🎊


🪄 Success! The test account has now assumed the role and has full access to the S3 bucket.

👀 The user can successfully view the bucket and the test file inside it. You've officially mastered Cross-Account Access! 👑

<img width="959" height="406" alt="final screen" src="https://github.com/user-attachments/assets/0745781d-eea5-4f83-9d36-3324d1d1e99e" />
