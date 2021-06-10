#Depending on the operating system of the host machines(s) that will build or run the containers, the image specified in the FROM statement may need to be changed.
#For more information, please see https://aka.ms/containercompat 

FROM mcr.microsoft.com/dotnet/framework/sdk:4.8 as build
WORKDIR "/src"

# Copy packages to your image and restore them
COPY WCFTest/TestWcfService/TestWcfService.csproj WCFTest/TestWcfService/TestWcfService.csproj
COPY WCFTest/TestWcfService/packages.config WCFTest/TestWcfService/packages.config
RUN nuget restore WCFTest/TestWcfService/packages.config -PackagesDirectory WCFTest/packages

# Add files from source to the current directory and publish the deployment files to the folder profile
COPY . .
WORKDIR /src/WCFTest/TestWcfService
RUN msbuild TestWcfService.csproj /p:Configuration=Release /m /p:DeployOnBuild=true /p:PublishProfile=FolderProfile

# Layer the production runtime image
FROM mcr.microsoft.com/dotnet/framework/wcf:4.8-windowsservercore-ltsc2019 as deploy

# Add the publish files into the right directory
WORKDIR /inetpub/wwwroot
COPY --from=build /src/WCFTest/TestWcfService/bin/Release/Publish .

#FROM mcr.microsoft.com/dotnet/framework/wcf:4.8-windowsservercore-ltsc2019
#ARG source
#WORKDIR /inetpub/wwwroot
#COPY ${source:-obj/Docker/publish} .
