# Patrones de diseño - El clima

Este ejercicio lo guiara en el desarrollo de un servicio Web RestFul, que retorna las condiciones actuales del clima y brinda recomendaciones de protección.  El Web API ejemplifica de forma practica los cuatro patrones de diseño mas usados en nuestro desarrollo diario:

- IOptions
- Singleton
- Repository
- Dependency Injection

> El mundo de los patrones de diseño es muy extenso y no es posible cubrirlo en su totalidad en una capacitación. Como siempre, apelamos a su curiosidad y lo invitamos a investigar más acerca del tema. Le aseguramos que será de mucho provecho para su desarrollo profesional.

## Paso 1: Crear el Proyecto Web API

1. **Abrir la Terminal.**
   Cree el directorio de trabajo `mkdir ~\source\repos\hands-onlabs\session2\02`.  Y navegue hasta el `cd ~\source\repos\hands-onlabs\session2\02`.
   >Importante que tenga en cuenta que los ejercicios están diseñados para ambientes Windows y el uso de PowerShell como terminal de sistema.  Si usted usa algún otro OS, deberá adaptar los comandos.

2. **Crear la solución y el proyecto Web API:**
   Desde la terminal ejecute cada uno de estos comando por separado.

   ```powershell
    dotnet new sln -n WeatherApi
    
    dotnet dev-certs https --trust
    
    dotnet new webapi -n WeatherApiExample --framework net8.0 --use-controllers --use-program-main
    
    dotnet sln add ./WeatherApiExample/WeatherApiExample.csproj
    
   ```

## Paso 2: Configurar el Proyecto para Usar IOptions

1. **VSCode**
   Desde la terminal, asegurándose que se encuentra en el directorio de la solución `WeatherApi`, ejecute el comando `code .`, para iniciar VS Code con el Web API Cargado.

2. **Ajuste de configuración:**
   Apoyado en el explorador de soluciones, localice el directorio `WeatherApiExample`, expandalo y abra en el editor de código el archivo `appsettings.json`.  Copie y peque al final el siguiente fragmento:

   ```json
       ,
       "WeatherSettings": {
           "DefaultTemperature": 25,
           "DefaultUvIndex": 5
       }
   ```

3. **Agregar paquetes necesarios para usar `IOptions`:**
   **VSCode**, es un editor de código muy robusto y completo. Ofreciendo una terminal integrada, lo que permitirá ejecutar todos los comando necesarios sin cambiar de aplicación.  Para activar la terminal integrada utiliza combinación de teclas **ctrl+ñ / cmd+`**

   ```powershell
   cd WeatherApiExample
   dotnet add package Microsoft.Extensions.Options.ConfigurationExtensions
   dotnet add package Microsoft.Extensions.Configuration.Json
   ```

4. **Crear la clase de configuración `WeatherSettings`:**
   En el directorio `WeatherApiExample`, cree el directorio `Models` y dentro de el la clase `WeatherSettings.cs`.  Copie y pegue el siguiente fragmento de código:

   ```csharp
   public class WeatherSettings
   {
       public int DefaultTemperature { get; set; }
       public int DefaultUvIndex { get; set; }
   }
   ```

5. **Modificar `Program.cs` para configurar IOptions:**
   Localice el archivo `Program.cs` y apoyándose en el editor de código agregue las siguientes lineas al archivo.

    ```csharp
    using Microsoft.Extensions.Options;
    using WeatherApiExample;
    .
    .
    .
    // Add services to the container.
    builder.Services.Configure<WeatherSettings>(builder.Configuration.GetSection("WeatherSettings"));
    .
    .
    .
    ```

## Paso 3: Implementar el Patrón Singleton

1. **Crear el servicio singleton `WeatherService`:**
   Cree el directorio `Services`, dentro del directorio raíz del proyecto.  Y cree los siguientes archivos:
   - IWeatherService.cs
   - WeatherService.cs

    Abra cada uno de los archivos creados, y copie estos fragmentos de código

    ```csharp
    using WeatherApiExample;

    public interface IWeatherService
    {
        WeatherForecast GetForecast();
    }
    ```

    ```csharp
    using Microsoft.Extensions.Options;
    using WeatherApiExample;

    public class WeatherService : IWeatherService
    {
        private readonly WeatherSettings _settings;
        private readonly Random _random = new Random();

        public WeatherService(IOptions<WeatherSettings> settings)
        {
            _settings = settings.Value;
        }

        public WeatherForecast GetForecast()
        {
            var temperature = _random.Next(-10, 40);
            var uvIndex = _random.Next(0, 11);
            var advice = GetAdvice(temperature, uvIndex);

            return new WeatherForecast
            {
                TemperatureC = temperature,
                UvIndex = uvIndex,
                Advice = advice
            };
        }

        private string GetAdvice(int temperature, int uvIndex)
        {
            if (temperature > 30)
            {
                return "Wear light clothing and stay hydrated.";
            }
            else if (uvIndex > 7)
            {
                return "Wear sunscreen and protective clothing.";
            }
            else if (temperature < 0)
            {
                return "Wear warm clothing.";
            }
            else
            {
                return "Weather is moderate. Dress comfortably.";
            }
        }
    }
    ```

## Paso 4: Implementar el Patrón Repository

1. **Crear el modelo y el repositorio:**
En la raíz del proyecto, cree el directorio `Repository`.  Y dentro de el cree los siguientes archivos:

- IRepository.cs
- InMemoryRepository.cs

Copie y pegue los siguientes fragmentos de código:

```csharp
public interface IRepository<T>
{
    IEnumerable<T> GetAll();
    T GetById(int id);
    void Add(T entity);
}
```

```csharp
using WeatherApiExample;

public class InMemoryRepository : IRepository<WeatherForecast>
{
    private readonly List<WeatherForecast> _forecasts = new List<WeatherForecast>();

    public IEnumerable<WeatherForecast> GetAll()
    {
        return _forecasts;
    }

    public WeatherForecast GetById(int id)
    {
        return _forecasts.ElementAtOrDefault(id);
    }

    public void Add(WeatherForecast entity)
    {
        _forecasts.Add(entity);
    }
}
```

## Paso 5: Modificar el Controlador

Como estamos iniciando con una Web Api de ejemplo de .Net, ya contamos con un controlador, vamos a aprovecharlo y modifiquelo de la siguiente manera.

1. **Abra `WeatherForecastController`:**
   Seleccione todo el contenido y bórrelo.
   Copie y pegue el siguiente fragmento de código.

    ```csharp
    using Microsoft.AspNetCore.Mvc;

    namespace WeatherApiExample.Controllers
    {
        [ApiController]
        [Route("[controller]")]
        public class WeatherController : ControllerBase
        {
            private readonly IWeatherService _weatherService;
            private readonly IRepository<WeatherForecast> _repository;

            public WeatherController(IWeatherService weatherService, IRepository<WeatherForecast> repository)
            {
                _weatherService = weatherService;
                _repository = repository;
            }

            [HttpGet]
            public ActionResult<WeatherForecast> Get()
            {
                var forecast = _weatherService.GetForecast();
                _repository.Add(forecast);
                return Ok(forecast);
            }
        }
    }
    ```

## Paso 6: Ajustemos la inyección de dependencias

Localice el archivo `Program.cs` y apoyándose en el editor de código agregue las siguientes lineas al archivo.

    ```csharp
    using Microsoft.Extensions.Options;
    using WeatherApiExample;

    // Agregue estas lineas debajo de esta sección
    // Add services to the container.
    builder.Services.Configure<WeatherSettings>(builder.Configuration.GetSection("WeatherSettings"));
    builder.Services.AddSingleton<IWeatherService, WeatherService>();
    builder.Services.AddSingleton<IRepository<WeatherForecast>, InMemoryRepository>();
    ```

## Paso 7: Explicación de Patrones

1. **IOptions:**
   - **Uso:** La clase `WeatherSettings` y su configuración a través de `appsettings.json`.
   - **Propósito:** Permite gestionar y acceder a configuraciones de forma centralizada, fácil de modificar y mantener.

2. **Singleton:**
   - **Uso:** `WeatherService` se registra como singleton.
   - **Propósito:** Asegura que solo una instancia de `WeatherService` exista y se comparta en toda la aplicación, reduciendo la sobrecarga de creación de instancias y manteniendo un estado único.

3. **Repository:**
   - **Uso:** `IRepository<T>` y su implementación `InMemoryRepository`.
   - **Propósito:** Abstrae la lógica de acceso a datos, facilitando la prueba y mantenimiento del código, y permite cambiar la fuente de datos sin modificar la lógica de negocio.

4. **Inyección de dependencias:**
   - **Uso:** Configuración en `Program.cs` y constructor de `WeatherController`.
   - **Propósito:** Proporciona una forma flexible y modular de gestionar dependencias, mejorando la modularidad, escalabilidad y facilidad de prueba de la aplicación.

## Paso 8: Ejecutar y Probar la API

1. **Ejecutar la aplicación:**

   ```powershell
   dotnet run .
   ```

2. **Probar la API:**
   - Abra Postman, y aprovechando la colección `Hands-On Labs`, que se creo en el LAB anterior, cargue la definición del servicio, que se encuentra en la URL `http://localhost:[Puerto]/swagger/v1/swagger.json`.

    Deberá obtener una salida como esta.

    ```json
        {
            "date": "0001-01-01",
            "temperatureC": 23,
            "temperatureF": 73,
            "summary": null,
            "uvIndex": 2,
            "advice": "Weather is moderate. Dress comfortably."
        }
    ```

    >**Importante:** Si al ejecutar el Get, valide los valores de las variables de la colección, y ajuste el valor de la variable `baseUrl`, por la URL base de su servicio.
