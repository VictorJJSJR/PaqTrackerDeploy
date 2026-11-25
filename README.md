# PAQTRACKER

Aplicación web de gestión de paquetes y seguimiento de rutas. El proyecto incluye una API ASP.NET Core y una interfaz React/Vite.

## Requisitos

- .NET 10 SDK
- Node.js 22
- SQL Server local con la base `PAQTRACKER` y las tablas requeridas por la aplicación
- Permisos de lectura y escritura para la identidad de Windows que ejecuta la API. En el arranque, el módulo de rutas puede agregar columnas y crear `POSICIONES_REPARTIDOR`, lo que requiere permisos DDL o que un administrador haya preparado el esquema.

## Configurar la conexión local

La cadena de conexión se guarda en User Secrets, fuera del repositorio. Desde la raíz del proyecto, configura tu instancia local:

```powershell
dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Server=<INSTANCIA_SQL_LOCAL>;Database=PAQTRACKER;Integrated Security=True;TrustServerCertificate=True;" --project PAQTERIA.Server/PAQTERIA.Server.csproj
```

Reemplaza `<INSTANCIA_SQL_LOCAL>` con el nombre de tu instancia. La aplicación se conecta usando la cuenta de Windows que ejecuta el proceso.

## Instalar y ejecutar

```powershell
cd paqteria.client
npm ci
cd ..
dotnet run --project PAQTERIA.Server/PAQTERIA.Server.csproj --launch-profile https
```

El perfil `https` inicia la API en entorno `Development` y el proxy SPA de Vite. Si el navegador no se abre, visita `https://localhost:57290`.

La conexión mediante SQL Server en Docker fue retirada. La ejecución local requiere iniciar la API en Windows para usar autenticación integrada.
