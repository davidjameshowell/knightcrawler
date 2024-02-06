FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build-env

ARG TARGETARCH
ARG TARGETOS
ARG BUILDPLATFORM

RUN arch=$TARGETARCH \
    && if [ "$arch" = "amd64" ]; then arch="x64"; fi \
    && echo $TARGETOS-$arch > /tmp/rid

WORKDIR /App
COPY . ./

RUN dotnet restore -r $(cat /tmp/rid)
RUN dotnet publish -c Release -o out -r $(cat /tmp/rid)

FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine3.18
WORKDIR /App
COPY --from=build-env /App/out .
RUN addgroup -S producer && adduser -S -G producer producer
USER producer
HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 \
  CMD pgrep -f dotnet || exit 1
ENTRYPOINT ["dotnet", "Producer.dll"]
