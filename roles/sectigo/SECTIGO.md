# Sectigo


Select Enrollment
=======================
![SCM Enrollment ACME](../../docs/1-scm-enrollment_ACME.png)

After logging into the Sectigo Certificate Management portal, select **Enrollment** -> **ACME** from the left panel.



Select ACME Endpoint
=======================
![SCM Enrollment ACME](../../docs/2-scm-select-acme-endpoint.png)

Select an ACME endpoint that corresponds to the type of certificate you wish to generate.  Generally this will be either the **InCommonRSAOV** or **InCommonECCOV**.  Later you will supply this url for certificate registration.



Select Accounts Button
=======================

![SCM Enrollment ACME](../../docs/3-scm-select-accounts-button.png)

After selecting the desired endpoint, click the **Accounts** button.



Create ACME Account
=======================
![SCM Enrollment ACME](../../docs/4-scm-create-acme-account.png)

Create a new ACME account.  Click SAVE.


ACME Account Creation Results
=======================

![SCM Enrollment ACME](../../docs/5-scm-acme-account-creation-results.png)

Here you are presented with a new **Key ID** and **HMAC Key**.  These are important, and should probably be stored safely away in your password manager.  


Account Pending
=======================

![SCM Enrollment ACME](../../docs/6-scm-account-pending.png)

After closing the Account Creation Results, you will notice that your new account is listed as _Pending_.
