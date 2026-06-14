# 🚀 Task 2: Cross-Account S3 Bucket Access

## 🎯 Objective
The goal of this task is to establish a secure **Two-Way Trust** between two different AWS accounts. 
An IAM user in **Account A** will be granted permissions to access and upload files to an S3 Bucket located in **Account B**.


## 🏗️ Account Structure
* **Account A (The IAM User Account):** `Ahmed-Admin`
* **Account B (The S3 Bucket Account):** `test`

---

## 🛠️ Step-by-Step Implementation

### Step 1: Create the S3 Bucket (Account B)
1. Logged into Account B (`test`).
2. Navigated to **S3** -> **Create bucket**.
3. Configured the bucket with a globally unique name: `level1-task2-backet` in the `eu-north-1` region.
4. Disabled ACLs (Object Ownership) to manage access strictly via policies.
5. Enabled **Block all public access** (Cross-account access does not require public exposure).
6. Enabled **Server-side encryption** with Amazon S3 managed keys (SSE-S3) and Bucket Key.

*Screenshot for Step 1:*

<img width="946" height="377" alt="s3-bucket-creation" src="https://github.com/user-attachments/assets/e4929938-d8a2-4ca4-88f3-50ce4e7be531" />


---

### Step 2: Get IAM User ARN (Account A)
1. Logged into Account A (`Ahmed-Admin`).
2. Navigated to **IAM** -> **Users**.
3. Copied the User ARN to be used in the Bucket Policy.
   * `arn:aws:iam::***********:user/Ahmed-Admin`

---

### Step 3: Attach Bucket Policy (Account B)
To allow the IAM user from Account A to access the bucket, I added the following **Bucket Policy** to `level1-task2-backet` in Account B:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowCrossAccountAccess",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::*****************:user/Ahmed-Admin"
            },
            "Action": [
                "s3:ListBucket",
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::level1-task2-backet",
                "arn:aws:s3:::level1-task2-backet/*"
            ]
        }
    ]
}
```
Screenshot for Step 3:
<img width="954" height="375" alt="policy-of-s3-bucket" src="https://github.com/user-attachments/assets/dad9b204-a008-4245-a8d7-f45f357096c4" />

Step 4: Attach IAM Policy (Account A)
To grant the user the ability to utilize the cross-account access, I attached the following Inline Policy to the user Ahmed-Admin in Account A:

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowAccessToCrossAccountBucket",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetObject",
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::level1-task2-backet",
                "arn:aws:s3:::level1-task2-backet/*"
            ]
        }
    ]
}
```
Screenshot for Step 4:
<img width="959" height="326" alt="inline-poicy-for-account1-to-acc-bucket" src="https://github.com/user-attachments/assets/c33dfcf7-45ec-424c-b760-bb7f2b21ab7e" />


✅ Validation (AWS CLI)
To verify the cross-account trust, I used the AWS CLI configured with the credentials of Ahmed-Admin (Account A) to interact with the bucket in Account B.

<img width="515" height="298" alt="validation from cli -acc1-can-access-s3" src="https://github.com/user-attachments/assets/44c8df0c-ae3a-4bc5-803c-88fb235af2e7" />

Created a test file locally:

Bash
   echo "Hello from Cross Account" > test.txt
Uploaded the file to the cross-account bucket:

Bash
   aws s3 cp test.txt s3://level1-task2-backet/
Listed the contents of the bucket:

Bash
   aws s3 ls s3://level1-task2-backet/
The commands executed successfully without any Access Denied errors, proving the Two-Way Trust is fully functional.

Screenshot of successful validation:




<img width="450" height="116" alt="final-validation" src="https://github.com/user-attachments/assets/0fc0ead5-071d-4b3b-9eb5-b3c397118345" />

