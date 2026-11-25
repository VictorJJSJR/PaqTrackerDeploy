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

## Despliegue gratuito en Render

El Blueprint `render.yaml` crea el proyecto `DeployPrueba` y un servicio web Docker enlazado a la rama `main` de `VictorJJSJR/PaqTrackerDeploy`. Render debe usar la raíz del repositorio como contexto de Docker; el Dockerfile está en `PAQTERIA.Server/Dockerfile`.

La aplicación requiere SQL Server. Para una instancia gratuita remota compatible, se recomienda Azure SQL Database Free. Al crearla, selecciona la opción de pausar la base de datos hasta el siguiente mes al agotar la cuota gratuita para evitar cargos por excedentes. La oferta tiene límites mensuales de cómputo y almacenamiento; revisa el uso en Azure.

Antes del primer despliegue:

1. Crea una base `PAQTRACKER` en Azure SQL Database Free y carga el esquema requerido. La aplicación valida `dbo.PAQUETES` y `dbo.CENTROS_DISTRIBUCION` al arrancar, y el módulo de rutas necesita permisos para agregar columnas y crear `dbo.POSICIONES_REPARTIDOR`. El Blueprint no copia automáticamente tablas ni registros desde SQL Server local.
2. Configura en Azure SQL el acceso de red desde los rangos de salida del servicio Render. Consulta esos rangos en Render, en `Connect > Outbound`, y agrega únicamente los necesarios al firewall de Azure SQL.
3. En la creación inicial del Blueprint, proporciona `ConnectionStrings__DefaultConnection` como secreto de Render. Usa autenticación SQL con cifrado (`Encrypt=True;TrustServerCertificate=False`) y un usuario de aplicación con permisos necesarios de lectura, escritura y modificación del esquema. No copies la cadena a este repositorio ni al chat.
4. Usa el plan `Free` para el servicio web. Render puede detenerlo tras 15 minutos sin tráfico y tardar cerca de un minuto en reactivarlo. El servicio es adecuado para pruebas y proyectos personales, no para producción con requisitos de disponibilidad.

La cadena local guardada en User Secrets permanece independiente; la variable de entorno de Render reemplaza ese valor únicamente en el despliegue.
