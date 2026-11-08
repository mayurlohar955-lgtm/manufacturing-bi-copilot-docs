User & Team Management
Overview
The Team Management system allows Company Owners and Managers to invite new users to their workspace. Instead of creating passwords for them, the system generates a secure, single-use invitation link.

Crucially, because this is a multi-plant SaaS, users are not just invited to the "Company"—they are invited to a Specific Plant.

1. The Invitation Workflow (Admin Side)
Navigate to Settings → Team Members.

Enter the email address of the new user.

Select the Plant they will be assigned to (e.g., "Chennai Plant").

Click Invite.

The system checks the limit: Base 2 Users + Extra Seats (Purchased).

If the limit is reached, the form is disabled, and the Admin is prompted to upgrade their billing plan.

A unique token is generated and saved to the Invitation database table with a 7-day expiration.

2. The Signup Workflow (User Side)
The invited user clicks the link: https://app.com/signup?invite=TOKEN.

Smart UI: The page recognizes the token. It shows a blue banner: "You've been invited to join [Company Name] at [Plant Name]". The email field is locked to prevent tampering.

The user enters their name, password, and verifies their email via a 6-digit OTP.

Database Action: The system creates the User, deletes the Invitation token, and creates a PlantMember record linking the user to the specific plant.

3. Role-Based Access Control (RBAC)
When inviting a user, you assign them a role:

OWNER: Full access to billing, plants, users, and data.

MANAGER: Can upload data, manage plants, and invite users. Cannot access billing.

VIEWER: Read-only access to dashboards.

