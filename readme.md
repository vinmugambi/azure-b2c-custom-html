# Passwordless signin/signup with facebook login

This policy authenticates users without need of a password by verifying a code sent to their email address or delegating authentication to Facebook.

## Overview

We combine the sign in and sign up journeys into one. If the user is new, we generate the account information to record in Azure AD B2C. We ask them to verify their email each time they sign in as opposed to asking for a password.

Users can also login using their facebook accounts. Just like in the email flows, we don't ask for additional information, and only save the information we acquire from Facebook.

We can later collect additional information after they are signed in and use Microsoft Graph APIs to update their Azure AD B2C account. This allows us more freedom to customize the interactions precisely, as we'll be using a general purpose programming language we're already familiar with.

To handle both local sign in and sign up scenarios using the same journey, we:

1. Display a page where the user can enter their email, and a button to sign in.
2. If the user submits their credentials (signs in), we display to them a page that ask them to verify their email with a Timed One Time Passcode sent to their email address.
3. When they verify their email, we check whether their email exists in Azure AD B2C, if it does not we create new AD user with that email, a randomly generated password, and set all the other required fields, like displayName, to unknown.
4. If they exist, fetch their profile from AD.
5. Issue an id token.

To handle facebook login, we:

1. Display a page where the user can select to use their Facebook account.
2. When the user clicks to "Login with Facebook", the user will be redirected to Facebook.
3. When the user returns from Facebook, read the information Facebook provided.
4. Lookup the account in the Azure AD B2C directory to determine if this user has already signed in with Facebook previously.
5. Write the account information to Azure AD B2C if the account was not already present in the directory.
6. Issue an id token.

## How to set up

You'll need to first prepare your tenant for custom policies. If you have not done so follow [this tutorial by Microsoft](https://learn.microsoft.com/en-us/azure/active-directory-b2c/tutorial-create-user-flows?pivots=b2c-custom-policy).
1. Clone this repository and open it in Visual Studio Code.
2. Install the [Azure AD B2C extension for VS code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)
3. Rename the `appsettings.sample.json` to `appsettings.json`. Edit the file to match your Azure AD B2C tenant properties from the [azure portal](portal.azure.com)
4. Generate policy based on `appsettings.json`. To do this, click `ctrl+shift+p` to open VS code command pallet. Search for "B2C build all policies" and click it.
5. Upload the generated files, located in `dist/Development` , to AD B2C tenant using the azure portal.

This policy is built upon the SocialAndLocalAccount sample from [the azure policy starter pack](https://github.dev/Azure-Samples/active-directory-b2c-custom-policy-starterpack). If you are already using those files, you only need to upload the `NoPassword.xml` file.


## How it looks like

![](https://storage.googleapis.com/azureb2c/blue-step-1.png)
![](https://storage.googleapis.com/azureb2c/blue-step-2.png)
![](https://storage.googleapis.com/azureb2c/blue-step-3.png)

## How we want it to look like

![](https://storage.googleapis.com/azureb2c/custom-verify.png)
![](https://storage.googleapis.com/azureb2c/custom-login.png)
