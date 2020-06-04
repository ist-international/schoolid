---
layout: post
title: G Suite SchoolID Integration Guide
---

This document describes how to configure Google G Suite for user authentication via SchoolID.

## Prerequisites

- G Suite administration access
- Enrolled chrome devices

## Provide SchoolID with G Suite information

Send the following information to the contact person for SchoolID at IST:

| Setting                               | Description                                                                                                                                                                                                                                                       |
| ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Domain name                           | The G Suite domain name is need to for SchoolID to recognize the source of the SAML Requests.                                                                                                                                                                       |
| Email domains                         | This enables only users with a matching email domain for login to the G Suite domain. This is not a security setting but enables better error message and lets SchoolID know which of potentially several email addresses that is expected by the G Suite instance. |

## Setting up SSO with SchoolID as a third party IdP

Related G Suite documentation: https://support.google.com/a/answer/60224

Configure the following in the G Suite administration console under `Security -> Settings -> Set up single sign-on (SSO) with a third party IdP`:

| Setting                                       | Description                                                                                                                                                                                                                                                                                             |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Set up SSO with third-party identity provider | Check                                                                                                                                                                                                                                                                                                   |
| Sign-in page URL                              | https://skolid.se/saml                                                                                                                                                                                                                                                                                  |
| Sign-out page URL                             | https://skolid.se/logout                                                                                                                                                                                                                                                                                |
| Verification certificate                      | Download from https://skolid.se/saml/metadata/certificate                                                                                                                                                                                                                                               |
| Use a domain-specific issuer                  | Check                                                                                                                                                                                                                                                                                                   |
| Network masks                                 | **NOTE** that during setup and testing it is recommended to use this setting to only enable SchoolID for some users. Also note that chrome devices are not affected by this setting and must be enabled to use SSO separately (see more information in the section `Setting up SchoolID for chrome devices` |
| Change password URL                           | https://skolid.se/account                                                                                                                                                                                                                                                                               |

### Enabling SchoolID SSO for chrome devices

Related G Suite documentation: https://support.google.com/chrome/a/answer/6060880

**NOTE** that the following settings can be applied for a part of the organization using the organizational units tree-view in the administration console. It is recommended to use this feature to limit the effects of the configuration while setting up and testing SchoolID.

Configure the following in the G Suite administration `Devices > Chrome management > (Settings) > User & Browser Settings`:

| Setting                               | Description                                                                                                                                                                     |
| ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Single sign-on                        | Required: `Enable SAML-based single sign-on for Chrome devices`                                                                                                                 |
| Single sign-on Online Login Frequency | Optional                                                                                                                                                                        |
| Client certificate                    | Required for chrome device two factor authentication with SchoolID: `{"pattern":"https://skolid-mtls.azurewebsites.net","filter":{"SUBJECT":{"CN":"SchoolID Client Certificate"}}}` |

Configure the following in the G Suite administration under `Devices > Chrome management > (Settings) > Device Settings`:

| Setting                            | Description                                                                                                                                                                     |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Single sign-on Cookie Behavior     | Required: `Enable transfer of SAML SSO Cookies into user session during sign-in`                                                                                                |
| Single sign-on IdP redirection     | Recommended: `Allow users to go directly to the SAML SSO IdP page`                                                                                                              |
| Single Sign-On client certificates | Required for chrome device two factor authentication with SchoolID: `{"pattern":"https://skolid-mtls.azurewebsites.net","filter":{"SUBJECT":{"CN":"SchoolID Client Certificate"}}}` |

## Enable chrome device two factor authentication with SchoolID

The following section describes how to configure G Suite to enable chrome devices for two factor authentication with SchoolID. This allows the chrome device to be used as the second factor when authenticating the user with SchoolID on the device. This is achieved by utilizing hardware-backed private keys for mutual TLS authentication with SchoolID. The client certificates corresponding to the private keys are associated with the SchoolID user account after activating the account with SchoolID using the device.

In the G Suite administration console under `Devices > Chrome management > (Settings) > Apps & Extensions` click `Add the chrome app or extension id` and use the following configuration:

| Setting           | Description                                                           |
| ----------------- | --------------------------------------------------------------------- |
| Extension ID      | `oaepmjmcmnmckchebkdomiamemdkiokc`                                    |
| From a custom URL | `https://skolidblob.blob.core.windows.net/public/chromeos/update.xml` |

Then choose to `Force install` the extension. Depending on the default permissions for the organization the extension may need to be `allowed all permissions`.
