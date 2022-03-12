# Sectigo


### Select Enrollment

After logging into the Sectigo Certificate Management portal, select **Enrollment** -> **ACME** from the left panel.

![SCM Enrollment ACME](../../docs/1-scm-enrollment_ACME.png)


### Select ACME Endpoint

Select an ACME endpoint that corresponds to the type of certificate you wish to generate.  Generally this will be either the **InCommonRSAOV** or **InCommonECCOV**.  Later you will supply this url for certificate registration.

![SCM Enrollment ACME](../../docs/2-scm-select-acme-endpoint.png)


### Select Accounts Button

After selecting the desired endpoint, click the **Accounts** button.

![SCM Enrollment ACME](../../docs/3-scm-select-accounts-button.png)


### Create ACME Account

Create a new ACME account.  Click SAVE.

![SCM Enrollment ACME](../../docs/4-scm-create-acme-account.png)


### ACME Account Creation Results

Here you are presented with a new **Key ID** and **HMAC Key**.  These are important, and should probably be stored safely away in your password manager.  

![SCM Enrollment ACME](../../docs/5-scm-acme-account-creation-results.png)


### Account Pending

After closing the Account Creation Results, you will notice that your new account is listed as _Pending_.

![SCM Enrollment ACME](../../docs/6-scm-account-pending.png)
