FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build-env

ARG TARGETARCH
WORKDIR /App
COPY . ./

RUN echo "Target: $TARGETARCH"

RUN dotnet restore -a $TARGETARCH
RUN dotnet publish -c Release -o out -a $TARGETARCH

FROM mcr.microsoft.com/dotnet/aspnet:8.0.1-bookworm-slim
WORKDIR /App
COPY --from=build-env /App/out .
#RUN addgroup -S producer && adduser -S -G producer producer
#USER producer
HEALTHCHECK --interval=30s --timeout=30s --start-period=5s --retries=3 \
  CMD pgrep -f dotnet || exit 1
ENTRYPOINT ["dotnet", "Producer.dll"]
