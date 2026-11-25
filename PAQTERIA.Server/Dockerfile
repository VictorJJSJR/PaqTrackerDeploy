FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src

RUN apt-get update \
	&& apt-get install -y --no-install-recommends ca-certificates curl xz-utils \
	&& curl -fsSL https://nodejs.org/dist/v22.14.0/node-v22.14.0-linux-x64.tar.xz \
		| tar -xJ --strip-components=1 -C /usr/local \
	&& rm -rf /var/lib/apt/lists/*

COPY PAQTERIA.Server/PAQTERIA.Server.csproj PAQTERIA.Server/
COPY paqteria.client/paqteria.client.esproj paqteria.client/
RUN dotnet restore PAQTERIA.Server/PAQTERIA.Server.csproj

COPY PAQTERIA.Server/ PAQTERIA.Server/
COPY paqteria.client/ paqteria.client/
RUN cd paqteria.client \
	&& npm ci --ignore-scripts \
	&& npm run build
RUN mkdir -p PAQTERIA.Server/wwwroot \
	&& cp -a paqteria.client/dist/. PAQTERIA.Server/wwwroot/
RUN dotnet publish PAQTERIA.Server/PAQTERIA.Server.csproj \
	--configuration Release \
	--output /app/publish \
	--no-restore \
	-p:BuildProjectReferences=false \
	-p:UseAppHost=false

FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS runtime
WORKDIR /app
ENV ASPNETCORE_URLS=http://+:10000
EXPOSE 10000

COPY --from=build /app/publish .
ENTRYPOINT ["sh", "-c", "ASPNETCORE_URLS=http://+:${PORT:-10000} exec dotnet PAQTERIA.Server.dll"]
