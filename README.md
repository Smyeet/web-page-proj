# web-page-proj - Smit Patel (L00213626)

# Static web-page hosted through AWS Cloud formation using s3 services 

# For S3 setup
- Uploaded "index.html" into the s3 Bucket and configured the necessary configurations that would make the website available publically.
- 'Enabled' ACLs(Access control List(s)) to let objects be accessed by other AWS Accounts and visible to use or owned.
- 'Disabled' "Block all public access" to make the contents of the buckets and objects visible to public.
- Keeping the rest as default, "created" the bucket.
- Later, selected the newly created bucket, and in "properties" > "Static website hosting" > "Edit" > with enabling the "Static Website hosting" > Adding the index document as per the file name > Save Changes (http://atu-web-proj.s3-website-eu-west-1.amazonaws.com)

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# For S3 CDN
- Navigate to CloudFront > "Create Distribution" > Select "origin type" as it would be a source for the website > Select "S3 Origin" and choose an existing bucket
- Keep the rest default in "Enable Security" (no need to change unless specified or as per the use case)
- Create it, and wait for sometime to get it provisioned. The "Cloudfront" link would be there once the distribution is created or navigate to distribution and click over it to see more details along with the link.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# For Three-tier-architecture
# VPC
- Navigate to VPC > Create VPC > Selected "VPC and more" to configure the network resources required for the three-tier architecture.
- Provided a name for the VPC (atu-three-tier-vpc) and configured the IPv4 CIDR block as 10.0.0.0/16.
- Selected 2 Availability Zones, with 2 public and 2 private subnets to distribute the resources across different network segments.
- Added a Zonal NAT Gateway to allow resources within the private subnets to access the internet for software installation, package updates and other outbound requests.
- Kept the remaining settings as required and created the VPC along with its networking resources.

# Apache Web Server
- Navigate to EC2 > Launch Instance and provided a suitable instance name (atu-web-server).
- Select 'Amazon Linux 2023' as the OS and t3.micro as the instance type.
- In Network Settings, choose the existing three-tier VPC and one of its public subnets.
- Select the previously created atu-web-sg security group.
- Under Advanced Details > User Data, added a Bash script to automate the installation and configuration of Apache HTTP Server.
- Launched the instance and verified its running status through the EC2 dashboard.
- Accessed the webpage using the public IPv4 address of the instance over HTTP to verify that Apache was serving the HTML content .
- Performed additional checks using curl to verify that the server was responding successfully with an HTTP 200 OK status.

# Flask 
- Navigated to EC2 > Launch Instance and created another instance named atu-app-server.
- Selected Amazon Linux 2023 with a t3.micro instance type, similar to the web-tier configuration.
- Disabled automatic public IPv4 assignment and attached the previously configured atu-app-sg security group.
- Attached the IAM instance profile configured for Systems Manager access.
- Under Advanced Details > User Data, added a Bash script intended to automate the Python environment and Flask application setup.
- The script included installing Python dependencies, creating a Python virtual environment, and installing Flask along with Waitress as the application server.
- Configured a custom systemd service (atu-flask.service) to manage the application and enable automatic startup (Used LLM to get the configured scripts because venv was not working hence the flask api was not able to get its dependencies).
- During initial testing, the service was found to be inactive because the User Data initialization had not completed successfully.
- Connected to the instance through Session Manager, inspected the service configuration and repaired the missing application dependencies and files.
- Restarted the service and verified that "Waitress" was running on port 5000.
- Used curl within the application instance to test the API endpoint and confirm that the expected JSON response was being returned successfully.

# Database - MySQL
- Navigated to Amazon RDS > Subnet Groups > Create DB Subnet Group to configure the network placement for the database tier.
- Provided a suitable name (atu-db-subnet-group) and selected the previously created atu-three-tier-vpc.
- Selected two private subnets belonging to different Availability Zones (eu-west-1a and eu-west-1b) to configure the database subnet group. Navigated to RDS > Databases > "Create Database" and selected MySQL as the relational database engine.
- Selected Full Configuration with the Dev/Test template to customise the database settings based on the project requirements.
- Configured a Single-AZ DB instance deployment.
- Selected a burstable db.t3.micro instance class and configured General Purpose SSD storage to keep the infrastructure suitable for a small development environment.
- Created the MySQL RDS instance using the selected configuration.
- The database tier was made to communicate with the private Flask application server through internal VPC networking, without requiring an additional EC2 instance for database hosting.

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
"ilities" achieved : 
(Word document with relevant screenshots and some comments - https://docs.google.com/document/d/140LNAs8YzuK4vQbFUPGxYbqxSAYRAzkYDI2n8jZz7QA/edit?usp=sharing) {anyone can access enabled}
Scalability — Limited 
Availability Basic (Single-AZ) 
Security 
Maintainability 
Repeatability — CloudFormation
Infrastructure as Code 
Network Isolation — Private subnets

