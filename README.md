# helm

Helm is a package manager for Kubernetes, which helps you manage, deploy, and maintain applications on Kubernetes clusters. Helm Charts are a collection of files that describe a related set of Kubernetes resources. They are essentially templates that are parameterized using a set of values to be customized during deployment. Helmfiles are an additional layer on top of Helm, which helps manage multiple Helm Charts and their configurations in a more declarative way.

Let's walk through a simple "Hello World" example of deploying a .NET C# WebAPI app using Helm and Helmfile.

1. **Create a .NET WebAPI application:**

First, create a simple .NET WebAPI application. You can use the following command to create a new WebAPI project:

```
dotnet new webapi -n HelloWorldApp
```

This will create a new WebAPI project called "HelloWorldApp" in a folder with the same name.

2. **Dockerize the application:**

Create a Dockerfile in the root of your project with the following content:

```Dockerfile
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /app
COPY *.csproj ./
RUN dotnet restore
COPY . ./
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app/out ./
ENTRYPOINT ["dotnet", "HelloWorldApp.dll"]
```

Build the Docker image:

```
docker build -t your-dockerhub-username/helloworldapp:latest .
```

Push the Docker image to your container registry (e.g., Docker Hub):

```
docker push your-dockerhub-username/helloworldapp:latest
```

3. **Create a Helm Chart for the application:**

Initialize a new Helm Chart for your application:

```
helm create helloworldapp-chart
```

This will create a new folder called "helloworldapp-chart" containing the template files and a default `values.yaml` file.

Edit the `helloworldapp-chart/templates/deployment.yaml` file to reference your Docker image:

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag | default .Chart.AppVersion }}"
...
```

Edit the `helloworldapp-chart/values.yaml` file to specify the image repository and tag:

```yaml
image:
  repository: your-dockerhub-username/helloworldapp
  tag: latest
...
```

4. **Create a Helmfile to manage the Helm Chart:**

Install Helmfile:

```sh
curl -L https://github.com/roboll/helmfile/releases/download/v0.139.9/helmfile_linux_amd64 -o /usr/local/bin/helmfile
chmod +x /usr/local/bin/helmfile
```

Create a `helmfile.yaml` in the root directory of your project:

```yaml
repositories:
  - name: stable
    url: https://charts.helm.sh/stable

releases:
  - name: helloworldapp
    namespace: default
    chart: ./helloworldapp-chart
    values:
      - helloworldapp-chart/values.yaml
```

This Helmfile specifies the Helm Chart for your "Hello World" application and the values file to be used during deployment.

5. **Deploy the application to your Kubernetes cluster:**

Ensure your `kubectl` is connected to your Kubernetes cluster, and then use Helmfile to deploy your application:

```sh
helmfile sync
```

This command will deploy the "Hello World" application to your Kubernetes cluster using the Helm Chart and values specified in the `helmfile.yaml`. The `helmfile sync` command ensures that the desired state described in the Helmfile is reflected in your Kubernetes cluster. If the application isn't already deployed, it will be created. If it is already deployed, it will be updated to match the Helmfile's specifications.

After running `helmfile sync`, you can verify that your application is running by executing the following command:

```sh
kubectl get pods
```

This command will list all the pods in the default namespace. You should see a pod with a name starting with "helloworldapp" in a Running state.

To access your .NET C# WebAPI application, you can either expose the service using a LoadBalancer or use port forwarding. For this example, we'll use port forwarding:

```sh
kubectl port-forward svc/helloworldapp 8080:80
```

Now, you can access your application by navigating to `http://localhost:8080` in your browser or using a tool like `curl`. You should see the "Hello World" response from your .NET C# WebAPI application.

Remember that this is a simple example and doesn't cover more advanced topics like adding persistent storage, configuring ingress, or setting up monitoring and logging. However, it should give you a basic understanding of how to deploy a .NET C# WebAPI application using Helm Charts and Helmfile in the context of Kubernetes.
