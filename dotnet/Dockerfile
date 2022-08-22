FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS base
ARG PROJECT
ARG ENTRY_PREFIX
RUN --mount=type=secret,id=REGISTRY_AUTH_TOKEN 
RUN ls -at /run
RUN ls -at /run/secrets

RUN cat /run/secrets/REGISTRY_AUTH_TOKEN
RUN export REGISTRY_AUTH_TOKEN=$(cat /run/secrets/REGISTRY_AUTH_TOKEN) \

# ARG USER_NAME
# ARG REGISTRY_AUTH_TOKEN

# RUN echo PROJECT=$PROJECT
# RUN echo ENTRY_PREFIX=$ENTRY_PREFIX
# RUN echo USER_NAME=$USER_NAME
# RUN echo REGISTRY_AUTH_TOKEN=$REGISTRY_AUTH_TOKEN

WORKDIR /app
EXPOSE 80

# RUN apt update && \
#       apt install -y curl && \
#       curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl && \
#       chmod +x ./kubectl && \
#       mv ./kubectl /usr/local/bin/kubectl

FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
ARG PROJECT
ARG ENTRY_PREFIX
# ARG USER_NAME
# ARG REGISTRY_AUTH_TOKEN

WORKDIR /src
COPY ["./", "/src"]
     
# RUN echo $(ls -at ./docker-file/dotnet)
# RUN rm nuget.config
# COPY ./docker-file/dotnet/nuget.config.template.xml /src/nuget.config
# RUN sed -i -e "s/USER/$USER_NAME/g" -e "s/PW/$REGISTRY_AUTH_TOKEN/g" /src/nuget.config

# RUN cat /src/nuget.config

RUN dotnet nuget add source --username $USER_NAME --password $REGISTRY_AUTH_TOKEN --store-password-in-clear-text --name github "https://nuget.pkg.github.com/weknow-network/index.json"

# RUN dotnet restore "$PROJECT/$PROJECT.csproj" --configfile "/src/nuget.config"
RUN dotnet restore "$PROJECT/$PROJECT.csproj"
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
