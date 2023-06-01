# Base image is Microsoft's dotnet sdk image
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build-env
# Create a directory for the app and change to it
WORKDIR /usr/local/sample-app
# Copy csproj file and install dependencies
COPY *.csproj .
RUN dotnet restore
# Copy all files in the project folder
COPY . .
# Build the app as a release
RUN dotnet publish -c Release -o out

# Build runtime image
FROM mcr.microsoft.com/dotnet/aspnet:6.0
# Create a system user for the app to run as
RUN groupadd -r elta && useradd -r -g elta elta-user
# Create a directory for the app and change to it
WORKDIR /usr/local/sample-app
# Copy built app into the runtime image
COPY --chown=elta-user:elta --from=build-env /usr/local/sample-app/out .
ENTRYPOINT dotnet eltaMVC.dll
