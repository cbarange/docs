# Password Policy and Session Changes

Go to [https://iam.console.greennode.ai/account-settings](https://iam.console.greennode.ai/account-settings) or navigate to Portal → IAM → Account Settings.

Adjust the password policy and login session settings to suit your needs.

The **Account settings** page in **IAM** lets you set two security policies for user accounts in IAM:

* **Password policy** — password requirements: length, complexity, expiration, and reuse of old passwords.
* **Session timeout policy** — how long a sign-in session lasts before users have to sign in again.

Each policy has two options:

* **IAM default** — use GreenNode's default settings.
* **Custom** — configure your own settings to meet your organization's security requirements.

### Password policy

#### IAM default

This option is selected by default. Passwords must meet the following requirements:

| Requirement                 | Default value                                                                                                  |
| --------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Password minimum length** | 8 characters                                                                                                   |
| **Password strength**       | At least 1 character of each type: uppercase letter, lowercase letter, number, and non-alphanumeric character. |
| **Password expiration**     | Never expires (_Never expire password_)                                                                        |
| **Password reuse**          | Allowed (_Do not prevent password reuse_)                                                                      |

#### Custom

Select **Custom** to set your own password requirements.

| Setting                                                                 | Description                                                                                       | Allowed range  |
| ----------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- | -------------- |
| **Password minimum length**                                             | The minimum number of characters in a password.                                                   | 8 to 50        |
| **Require at least one uppercase letter from the Latin alphabet (A-Z)** | Passwords must contain at least 1 uppercase letter.                                               | —              |
| **Require at least one lowercase letter from the Latin alphabet (a-z)** | Passwords must contain at least 1 lowercase letter.                                               | —              |
| **Require at least one number**                                         | Passwords must contain at least 1 number.                                                         | —              |
| **Require at least one non-alphanumeric character**                     | Passwords must contain at least 1 special character (not a letter or number).                     | —              |
| **Turn on password expiration** → **Expire password in**                | Passwords expire after the set number of days, and users must create a new one. Default: 90 days. | 1 to 1095 days |
| **Prevent password reuse** → **Remember**                               | Users can't reuse their most recent passwords. Default: remembers 4 passwords.                    | 1 to 24        |

### Session timeout policy

Set how long a sign-in session lasts. When the session expires, users have to sign in again.

| Option          | Session length                                                                   |
| --------------- | -------------------------------------------------------------------------------- |
| **IAM default** | 2 hours                                                                          |
| **Custom**      | Choose from the **Session expires in** list: 15 minutes, 30 minutes, or an hour. |
