
# Power BI Integration

To start communicating with Power BI APIs, an Application has to be created and given permissions to PowerBI APIs.

Create Application and assign permissions: https://dev.powerbi.com/Apps

For SPA make it a Native application. 

After selecting "Register" an Application ID will be generated. Save this.

Customer Portal PowerBI Integration - DEV: fa0db634-ffd5-40e9-9782-e6a348baae6a

# Access Admin Interface

Attunity Replicate is installed on a server. The admin interface is also installed on the local server which cannot be accessed, by default, from outside the server.

- Remote Desktop into the server
- Go to URL, https://{ec2-servername}/attunityreplicate/login/
- Put in the EC2 Remote Desktop login credentials
    - L: Administrator
    - P: xxxxxxxxxxxxx

# Manage Endpoint Connections

To replicate between database servers, sources and endpoints need to be setup first. 

## AWS RDS Aurora
