FROM mcr.microsoft.com/dotnet/aspnet:7.0 AS base
ARG PROJECT
ARG ENTRY_PREFIX

WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/sdk:7.0 AS build
ARG PROJECT
ARG ENTRY_PREFIX

WORKDIR /src
COPY ["./", "/src"]

RUN cat /src/nuget.config

RUN dotnet restore "$PROJECT/$PROJECT.csproj" --configfile "/src/nuget.config"
COPY . .

WORKDIR "/src/$PROJECT"
RUN dotnet build "$PROJECT.csproj" -c Release -o /app/build

FROM build AS publish
ARG PROJECT
ARG ENTRY_PREFIX
RUN dotnet publish "$PROJECT.csproj" -c Release -o /app/publish

FROM base AS final
ARG PROJECT
ARG ENTRY_PREFIX

WORKDIR /app
# RUN groupadd -r microuser && useradd -r -s /bin/false -g microuser microuser
COPY --from=publish /app/publish .
# RUN chown -R microuser:microuser /app
# USER microuser

ENV entry_point $ENTRY_PREFIX$PROJECT.dll
RUN echo $entry_point
ENTRYPOINT dotnet $entry_point
