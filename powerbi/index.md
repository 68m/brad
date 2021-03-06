
# Power BI Integration

To start communicating with Power BI APIs, an Application has to be created and given permissions to PowerBI APIs.

Create Application and assign permissions: https://dev.powerbi.com/Apps

For SPA make it a Native application. 

After selecting "Register" an Application ID will be generated. Save this.

Customer Portal PowerBI Integration - DEV: fa0db634-ffd5-40e9-9782-e6a348baae6a

# Grant Access to Power BI w/ New Application

In order for the API calls to work, permissions need to be granted on the Application to Power BI. Login to the Azure Portal, and go to the following:

Home > Rubicon Global Holdings > App Registrations > APPLICATION > Settings > Required Permissions

There will be a button called "Grant permissions". Select it and grant them.

# API - Generate Access Token

GET: https://login.windows.net/common/oauth2/token

BODY

x-www-form-urlencoded

client_id: {APPLICATION_ID}
resource: https://analysis.windows.net/powerbi/api
grant_type: password
username: {AD_USERNAME}
password: {AD_PASSWORD}

# API - Generate Reports Embed Token

POST: https://api.powerbi.com/v1.0/myorg/groups/{group_id}/reports/{report_id}/GenerateToken

POST: https://api.powerbi.com/v1.0/myorg/groups/eeb49ce7-bef2-443c-a815-c28c5612b35b/reports/01d7aaf8-b199-4600-b1d6-33508f92c263/GenerateToken

Authorization: Bearer {access_token}

Body:

{
  "accessLevel": "View"
}
