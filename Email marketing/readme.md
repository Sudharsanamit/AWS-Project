**Introducing the AWS Project: An Email Marketing Campaign System**

This project involves building a robust email marketing campaign system using AWS services. The goal is to send emails—like weekly newsletters—on a regular schedule while gaining hands-on experience with various AWS tools and best practices.

### Services Used

The application uses the following AWS services:

* **Amazon SES** for sending emails
* **AWS Lambda** to manage email logic
* **Amazon S3** to store email templates and contact lists
* **Amazon EventBridge** to schedule email campaigns
* **AWS IAM** to manage permissions and access

### Cost Consideration

Most services are within AWS Free Tier. However, EventBridge scheduling may incur a minor cost depending on usage (typically a few cents).

### Planning with Eraser.io

Using **Eraser.io**, we create both visual diagrams and checklists to guide development. This helps manage infrastructure connections and project tasks efficiently.

### High-Level Application Requirements

* A secure and accessible storage solution for templates and contact data (S3)
* A validated email sender setup (SES)
* Scheduled automation for sending (EventBridge)
* Dynamic personalization logic (Lambda)
* Fine-grained permissions (IAM)

### S3 Setup

We create an S3 bucket (with a globally unique name) to store:

* An HTML email template with dynamic placeholders (e.g., for the recipient's name)
* A CSV file named `context.csv` containing contact information

### Uploading Email Assets

Once created, the email template and contact list are uploaded to the designated S3 bucket.

### Diagramming Infrastructure with Eraser.io

Connections are drawn between:

* Lambda ↔️ S3 (bi-directional)
* Lambda ➝ SES (unidirectional)

This visualization helps track the data flow from storage to processing to dispatch.

### Setting Up SES

* Use a **custom domain**, not a generic one like `gmail.com`.
* Validate the domain and email address by verifying DNS records and responding to AWS verification emails.
* Begin in the **SES sandbox**, then move to **production** by verifying your domain (adding CNAME records) and requesting a quota increase.

### Test Email Using SES

After setup, send a formatted or raw email from the verified sender to a validated recipient.

### Validating Recipient Addresses (SES Sandbox Requirement)

In sandbox mode, each recipient email address must also be verified.

### Lambda Email Logic

A Python-based Lambda function:

* Retrieves the email template and contacts from S3
* Personalizes the message for each contact
* Sends the email using Boto3 and SES

This function requires a specific IAM execution role with permissions to `s3:GetObject` and `ses:SendEmail`.

### Creating Lambda Execution Role Policies

* A custom policy is created and attached to the Lambda role.
* The policy grants access to both S3 and SES.

### Testing the Lambda Function

* Set up a test event with generic JSON.
* Initially, you may see an **Access Denied** error if permissions aren’t correct.
* After fixing permissions, successful test sends confirm everything is functional.

### Scheduling with EventBridge

* Create a recurring rule to invoke the Lambda function (e.g., weekly).
* Configure flexible time windows and set Lambda as the target.

### Verifying Scheduled Execution

* Check your inbox for the scheduled email.
* Use CloudWatch logs and EventBridge monitors to debug delivery or function issues.

### Enhancements

* Replace the S3-stored contacts with **DynamoDB** for more scalability and querying
* Add a UI using **API Gateway** + **Lambda** to manually trigger email campaigns

### Clean-Up

To avoid future charges, delete:

* The EventBridge rule
* The Lambda function
* The IAM policies and roles
* The contents of the S3 bucket (empty it first), then delete the bucket

