- Search aws service for `AWS Billing` and click on it. 
- By default Access to Billing Information is not enabled to IAM users, only accessible by root user.
- To enable the access of Billing information to IAM user, follow following steps ^ed361d
	- Login as root user
	- Click on top-right root user dropdown menu
	- Click on My Account
	- New page will open, scroll down to `IAM User and Role Access to Billing Information`
	- In this section, click on `edit` button
	- You'll see a checkbox with `Activate IAM Access`, mark that checked
	- Update.
	- Now the IAM user with the admin access, can see the billing information.

### AWS Budget Setup
> [!info]
> If not already, enable AWS billing dashboard for IAM users, steps [](.md#^ed361d%7Chere)).

- Click on your-name dropdown on top right corner
- Click on `My Billing Dashboard`
- You'll see the dashboard enabled if you have the correct permissions

### To see the charges on the services
- Go to Billing dashboard
- On the sidebar, there is an option called `Bills`
- Go to Bills
- Under bills, there are tabs, go to `Charges by services`
- There you can see `Active services` and charges for the services.
- To see more details about the charge, you can click on the service name
- You'll see the link for the page of that service charges
- You can go to that page and see the service names, description and more details about the usage and charges.

### To check the costs of the free tier usage
- Go to billing dashboard
- Go to `free tier` from sidebar
- There you can see the service name, usage, how much you have used from free tier and how much remaining. There is other useful information like forecasted usage, etc.

### To get the alerts about the upcoming cost
- Go to billing dashboard
- Go to `Budget` from sidebar
- Click `Create a budget` button
- New form will open to fill in the alert details
	- Budget setup
		- Use a template (Simplified)
		  Use the recommended configurations. We can change the configuration options after the budget has been created.
		- Customize (advanced)
		  Customize a budget to set parameters specific to our use case. You can customize the time period, the start month and specific amount.
	- Templates
		- Zero spend budget
		  Create a budget that notifies you once your spending exceeds AWS free tier limits.
		- Monthly cost budget
		  Create a monthly budget that notifies you if you exceed, or are forecasted to exceed, teh budget amount.
		- Daily savings plans coverage budget
		  Create a coverage budget for your savings plans that notifies you when you fall below the defined target.
	  - Budget name
	  - Email recipients
	  - Scope
	  - Click on create budget button to click budget.
  - It is better to create two budgets for starters
	  - Create a free tier budget by selecting following options
		  - Use a template
		  - under templates, select `Zero spend budget` and save with other necessary details
	  - Create a monthly limit budget if needed.
		  - Use simplified template
		  - Select `Monthly cost budget`
		  - Enter $10 for the maximum budget
		  - Enter other necessary details and save.
