You can use your Single Sign-On (SSO) credentials to log in to your Azure Container Registry (ACR) via the Azure CLI.

To do this, first, make sure you have the Azure CLI installed. If you don't have it installed, you can download and install it from the [official Azure CLI website](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli).

1. **Log in to your Azure account using SSO:**

Use the `az login` command to log in to your Azure account using your SSO credentials:

```sh
az login
```

This command will open a web page in your default web browser prompting you to enter your SSO credentials. Once you've successfully logged in, the command will return a JSON object containing details about your subscriptions and tenant ID.

2. **Get an ACR access token:**

To interact with your ACR using Docker, you need to obtain an access token. You can use the `az acr login` command to log in to your ACR and get an access token:

```sh
az acr login --name <acr-name>
```

Replace `<acr-name>` with the name of your ACR. This command will return a message indicating that the login succeeded, and it will configure Docker to use the access token.

3. **Push and pull Docker images:**

Now that you're logged in with your SSO credentials and have an access token, you can push and pull Docker images from your ACR using the standard Docker commands. Just make sure to use the correct ACR repository in your image tags:

```sh
docker tag helloworldapp:latest myacr.azurecr.io/helloworldapp:latest
docker push myacr.azurecr.io/helloworldapp:latest
```

By following these steps, you can log in to your Azure Container Registry using your SSO credentials and interact with it using Docker commands.
