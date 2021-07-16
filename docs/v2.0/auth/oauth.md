# OAuth authentication

Portainer allows that the authentication can be done with OAuth and with this help article, you will see a overview of what's the requirement to configure Portainer CE with OAuth. 

## Start to configure OAuth authentication in Portainer

Once you logged to Portainer, click in <b>Settings</b> and then in <b>authentication</b>. After that, select <b>OAuth</b> option.

![oauth](assets/oauth_1.png)

In this screen, you need to fill in the details using the details provided by your OAuth provider. The fields are:

* Use SSO: Using SSO, the OAuth provider is not forced to prompt for credentials when the user has a currently logged in session.
* Automatic User Provisioning: Toggle on the Automatic User Provisioning to see the options. You can use this option if you want a user added to Portainer for each OAuth useron first login. After enabling the toggle, you may choose a team for these Auto Populated Users. 

![oauth](assets/oauth_2.png)

* Client ID: This is the public identifier of the OAuth application.
* Client Secret: Here, you need to fill with the token access to the OAuth Application.
* Authorization URL: URL used to authenticate against the OAuth provider. Will redirect the user to the OAuth provider login view.
* Access Token URL: URL used to exchange a valid OAuth authentication code for an access token.
* Resource URL: URL used by Portainer to retrieve information about the authenticated user.
* Redirect URL: URL used by the OAuth provider to redirect the user after successful authentication. Should be set to your Portainer instance URL.
* Logout URL: URL used by the OAuth provider to logout the user. 
* User Identifier: Identifier that will be used by Portainer to create an account for the authenticated user. Retrieved from the resource server specified via the Resource URL field.
* Scopes: Required by the OAuth provider to retrieve information about the authenticated user. Refer to your OAuth provider documentation for more information about this.

Once that all fields are completed, do a click in <b>Save Settings</b>

## Manage access to OAuth Team and Users

To understand how to enable access to OAuth Teams and Users, please, refer to [this article](/v2.0/endpoints/access).

## Examples

Take a look at the following examples for configuration OAuth using Azure, Google and Github configuration.

* The client ID, called application ID in the MSFT world.
* The tenant ID (grayed information in the screenshot), is a GUID specific for your ID.
* This is just a custom-defined name for the URL of Portainer.

### Azure

![azure](assets/azure.jpg)

### Google

![google](assets/google.png)

### Github

![github](assets/github.jpg)

### Keycloak

![keycloack](assets/keycloak.png)

# Notes

[Contribute to these docs](https://github.com/portainer/portainer-docs/blob/master/contributing.md){target=_blank}