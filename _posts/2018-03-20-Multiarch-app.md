---
layout: post
title: Dockerfile targeting Windows & Linux OS using .NET Core
---

Create a new folder in file system. Open the folder in [Visual Studio Code](https://code.visualstudio.com/). Launch the command palette by ``` ctrl + ~ ```

#### Steps:

* On the command terminal type ``` dotnet new mvc ```. This step will create .Net Core MVC project in the current folder and restores the nuget packages.

* You can try running this code by ``` dotnet run ```. This step starts the application. You can browse exposed URL in browser and see your app running.

### Add this code in github. 
* ``` git remote add origin https://github.com/<username>/<reponame>.git ```
* ``` git add . ```
* ``` git commit -m "Initial commit" ```
* ``` git push -u origin master ```

### Multi-arch based .Net core docker images
* Docker has a [multi-arch](https://github.com/moby/moby/issues/15866) feature that [microsoft/dotnet-nightly](https://hub.docker.com/r/microsoft/dotnet-nightly/) recently started utilizing. The plan is to port this to the [official microsoft/dotnet repo](https://hub.docker.com/r/microsoft/dotnet/) shortly. The multi-arch feature allows a single tag to be used across multiple machine configurations. Without this feature each architecture/OS/platform requires a unique tag. 

* Create a new file in the folder. Rename it as ``` Dockerfile ```. Place the content in the Dockerfile and replacing ```[your project name] ``` with your current app name.

 ``` dockerfile
      FROM microsoft/aspnetcore-build-nightly AS builder
      WORKDIR /source
      COPY *.csproj .
      RUN dotnet restore
      COPY . .
      RUN dotnet publish --output /app/ --configuration Release
      FROM microsoft/aspnetcore-nightly
      WORKDIR /app
      COPY --from=builder /app .
      ENTRYPOINT ["dotnet", "[your project name].dll"]
  ```
* Before running docker commands please make sure you install Docker on your system. To download Docker for windows please refer:[https://docs.docker.com/docker-for-windows/install/](https://docs.docker.com/docker-for-windows/install/) 

* Launch the Terminal windows and build the docker image by typing the command ```docker build -t multiarch . ```. You can specify any other tag name for image,  i have used mutiarch. This step will read the docker file and build the image as specified in docker file. 

* You can check the image created by using command ``` docker images ```

* Run the docker image by ``` docker run -d -p 8080:80 [image name] ```. ``` -d ``` option to run this container in background as daemon process.  ` -p ` option to specify the port of `host:container` format. Once the command is executed you can browse the container on browser by http://localhost:8080. However if you are testing this on Windows Containers, follow below steps to get ip of container:

* Run ``` docker ps ``` to get the container id.

* Run ``` docker inspect -f "{{.NetworkSettings.Networks.nat.IPAddress}}" <container id> ``` to get the ip address of the container.
 
* Browse to ip to test your application on windows container.

   Happy Coding !

