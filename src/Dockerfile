FROM mcr.microsoft.com/dotnet/core/sdk:3.1 AS build-env
WORKDIR /app

# Copy csproj and restore as distinct layers
COPY *.csproj ./
RUN dotnet restore

# Copy everything else and build
COPY . ./
RUN dotnet publish -c Release -o out

# Build runtime image
FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
WORKDIR /app
COPY --from=build-env /app/out .

# Expose 8080 as we are going to be a non-root user
EXPOSE 8080
ENV ASPNETCORE_URLS=http://*:8080

# Adds fruits group and user.
# useradd -r will add a system account
# A system account is an account that a program/deamon starts and/or uses to run
RUN groupadd -g 1100 fruits && \
    useradd -r -u 1100 -g fruits fruits
# chown -R recursively changes the owner of all files and directories under /app
RUN chown -R fruits:fruits /app

USER fruits

ENTRYPOINT ["dotnet", "fruit-preferences-api.dll"]

# USAGE:
# docker build -t [name] .
# docker run -p 8080:80 --ti [name]