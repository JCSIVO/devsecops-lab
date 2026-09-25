# ---------- ETAPA 1: construir ----------
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS builder
WORKDIR /source

COPY *.csproj .
RUN dotnet restore

COPY . .
RUN dotnet publish -c Release -o /app

# ---------- ETAPA 2: ejecutar ----------
FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS runner
WORKDIR /app

COPY --from=builder --chown=app:app /app .

USER app
EXPOSE 8080
ENTRYPOINT ["dotnet", "mi-api.dll"]