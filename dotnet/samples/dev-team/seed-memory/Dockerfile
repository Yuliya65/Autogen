FROM mcr.microsoft.com/dotnet/runtime:7.0@sha256:8cd26108e7a329458e5b84342816ae39b2b38bd041cfdc0b79b8cf1c0e0d376f AS base
WORKDIR /app

FROM mcr.microsoft.com/dotnet/sdk:7.0@sha256:d32bd65cf5843f413e81f5d917057c82da99737cb1637e905a1a4bc2e7ec6c8d AS build
WORKDIR /src
COPY ["util/seed-memory/seed-memory.csproj", "util/seed-memory/"]
RUN dotnet restore "util/seed-memory/seed-memory.csproj"
COPY . .
WORKDIR "/src/util/seed-memory"
RUN dotnet build "seed-memory.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "seed-memory.csproj" -c Release -o /app/publish /p:UseAppHost=false

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "seed-memory.dll"]
