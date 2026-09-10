# See https://aka.ms/customizecontainer to learn how to customize your debug container and how Visual Studio uses this Dockerfile to build your images for faster debugging.

# This stage is used when running from VS in fast mode (Default for Debug configuration)
FROM mcr.microsoft.com/dotnet/aspnet:8.0@sha256:9a464e9a7e8c6144631020975f703c89034fe386417cb740620df69c2c6cfe24 AS base
USER $APP_UID
WORKDIR /app
EXPOSE 5001

# This stage is used to build the service project
FROM mcr.microsoft.com/dotnet/sdk:8.0@sha256:5ef85cc12cb25be6ec319a7392d1e9efd53c3bc8abb971c53d8058a473f09053 AS build
ARG BUILD_CONFIGURATION=Release
WORKDIR /src
COPY ["dotnet/Directory.Packages.props", "dotnet/"]
COPY ["dotnet/Directory.Build.props", "dotnet/"]
COPY ["dotnet/Directory.Build.targets", "dotnet/"]
COPY ["dotnet/NuGet.config", "dotnet/"]
COPY ["dotnet/src/Microsoft.Autogen.AgentHost/Microsoft.Autogen.AgentHost.csproj", "dotnet/src/Microsoft.Autogen.AgentHost/"]
COPY ["dotnet/src/Microsoft.AutoGen.Runtime.Grpc/Microsoft.AutoGen.RuntimeGateway.Grpc.csproj", "dotnet/src/Microsoft.AutoGen.RuntimeGateway.Grpc/"]
COPY ["dotnet/src/Microsoft.AutoGen.Contracts/Microsoft.AutoGen.Contracts.csproj", "dotnet/src/Microsoft.AutoGen.Contracts/"]
RUN dotnet restore "./dotnet/src/Microsoft.Autogen.AgentHost/Microsoft.Autogen.AgentHost.csproj"
COPY . .
WORKDIR "/src/dotnet/src/Microsoft.Autogen.AgentHost"
RUN dotnet build "./Microsoft.Autogen.AgentHost.csproj" -c $BUILD_CONFIGURATION -o /app/build

# This stage is used to publish the service project to be copied to the final stage
FROM build AS publish
ARG BUILD_CONFIGURATION=Release
RUN dotnet publish "./Microsoft.Autogen.AgentHost.csproj" -c $BUILD_CONFIGURATION -o /app/publish /p:UseAppHost=false

# This stage is used in production or when running from VS in regular mode (Default when not using the Debug configuration)
FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "Microsoft.Autogen.AgentHost.dll"]