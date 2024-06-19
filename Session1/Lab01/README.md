---
Order: #
Area: languages
TOCTitle: Markdown
ContentID: GUID
PageTitle: 
DateApproved: MM/DD/YYYY
MetaDescription: 
---

# Automatización de Versiones (SemVer / Conventional Commits / Git / GitVersion)

## Propósito

Este manual tiene como objetivo demostrar de forma práctica la relación entre los siguientes temas:

- Git - Fundamentos
- Confirmaciones Convencionales
- Versionamiento Semántico
- GitVersion

## Pre Requisitos

Antes de comenzar, asegúrese de tener estas instaladas en su entorno:

- Windows 10 21H2
- WSL
- Visual Studio Code
- Docker Desktop para Windows
- Kubectl
- Helm
- Git para Windows
- SDK de .NET 8.0
- Git Version
- PowerShell 7

## Actividades a Realizar

1. Abra una terminal en su equipo.
2. Navegue hasta el directorio C:\Users\[Su usuario de Windows]\source\repos\.

    ```powershell
    cd C:\Users\[Su usuario de Windows]\source\repos\
    ```

3. Cree el directorio lab01.

    ```powershell
    mkdir lab01
    ```

4. Navegue hasta el nuevo directorio lab01.

    ```powershell
    cd .\lab01\
    ```

5. Cree un nuevo repositorio Git.

    ```powershell
    git init
    ```

En este punto ya tiene un nuevo repositorio Git local.

Cargue el directorio en Visual Studio Code y cree la siguiente estructura:

```powershell
\\---src
    index.html
    styles.css
```

En el archivo `index.html`, copie y pegue el siguiente código:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Hola Mundo</title>
    <link rel="stylesheet" href="styles.css">
    <link href="<https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;700&display=swap>" rel="stylesheet">
</head>
<body>
    <div class="container">
        <div class="content">
            <h1>Hola Mundo</h1>
            <p>Bienvenido a mi página moderna y creativa</p>
            <a href="#" class="button">Descubre más</a>
        </div>
    </div>
</body>
</html>
```

En el archivo styles.css, copie y pegue el siguiente código:

```css
/* General Styles */
body, html {
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
    font-family: 'Montserrat', sans-serif;
    display: flex;
    justify-content: center;
    align-items: center;
    background: linear-gradient(135deg, #f06, #4a90e2);
    color: #ffffff;
}

/* Container */
.container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100%;
    text-align: center;
}

/* Content */
.content {
    background: rgba(255, 255, 255, 0.2);
    padding: 30px;
    border-radius: 15px;
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);
}

.content h1 {
    font-size: 4em;
    margin-bottom: 20px;
}

.content p {
    font-size: 1.2em;
    margin-bottom: 20px;
}

/* Button */
.button {
    background: #ff6347;
    color: #fff;
    padding: 10px 20px;
    text-decoration: none;
    border-radius: 5px;
    transition: background 0.3s;
}

.button:hover {
    background: #e5533d;
}
```

Revise el estado de su repositorio:

 ```powershell
 git status
 ```

Agregue los nuevos archivos al seguimiento en Git:

 ```powershell
 git add .
 ```

Valide que los archivos HTML y CSS se hayan agregado con éxito al seguimiento del repositorio. Su salida debe verse así:

 ```powershell
 On branch main
 No commits yet

 Changes to be committed:
   (use "git rm --cached <file>..." to unstage)
  new file:   src/hola_mundo.html
  new file:   src/styles.css
 ```

Cree una nueva confirmación en el repositorio:

```powershell
git commit -m "feat: Initial Commit."
```

Luego de ejecutar el comando, deberá tener la siguiente salida:

 ```powershell
 [main (root-commit) b125a25] feat: Initial Commit.
  2 files changed, 73 insertions(+)
  create mode 100644 src/index.html
  create mode 100644 src/styles.css
 ```

Con el nuevo commit creado, podemos validar la versión de nuestra aplicación web. Para esto, ejecute el siguiente comando:

 ```powershell
 dotnet-gitversion
 ```

Deberá tener la siguiente salida en su terminal:

 ```json
 {
   "Major": 0,
   "Minor": 1,
   "Patch": 0,
   "PreReleaseTag": "",
   "PreReleaseTagWithDash": "",
   "PreReleaseLabel": "",
   "PreReleaseLabelWithDash": "",
   "PreReleaseNumber": null,
   "WeightedPreReleaseNumber": 60000,
   "BuildMetaData": 0,
   "BuildMetaDataPadded": "0000",
   "FullBuildMetaData": "0.Branch.main.Sha.b125a253090a3fea886795f9fa8329799311e40a",
   "MajorMinorPatch": "0.1.0",
   "SemVer": "0.1.0",
   "LegacySemVer": "0.1.0",
   "LegacySemVerPadded": "0.1.0",
   "AssemblySemVer": "0.1.0.0",
   "AssemblySemFileVer": "0.1.0.0",
   "FullSemVer": "0.1.0+0",
   "InformationalVersion": "0.1.0+0.Branch.main.Sha.b125a253090a3fea886795f9fa8329799311e40a",
   "BranchName": "main",
   "EscapedBranchName": "main",
   "Sha": "b125a253090a3fea886795f9fa8329799311e40a",
   "ShortSha": "b125a25",
   "NuGetVersionV2": "0.1.0",
   "NuGetVersion": "0.1.0",
   "NuGetPreReleaseTagV2": "",
   "NuGetPreReleaseTag": "",
   "VersionSourceSha": "b125a253090a3fea886795f9fa8329799311e40a",
   "CommitsSinceVersionSource": 0,
   "CommitsSinceVersionSourcePadded": "0000",
   "UncommittedChanges": 0,
   "CommitDate": "2024-05-16"
 }
 ```

Si analiza la salida del comando, encontrará la variable SemVer, que contiene la versión de nuestra aplicación web en el formato que revisamos en la capacitación (X.Y.Z - Major.Minor.Patch).

Continuemos con el desarrollo de nuestra aplicación. Para este ejercicio, corregiremos un error en nuestra aplicación de forma que aumente la sección PATCH (Z) de nuestra versión.

En el archivo `index.html`, modifique el texto de la línea 14 por:

```html
<p>Bienvenido a la Capacitación: Mejores prácticas de desarrollo (PRESENCIAL)</p>
```

Guarde la página.

Valide el estado de su repositorio, agregue los cambios al seguimiento, y cree una nueva confirmación con el mensaje:

Copie cada comando por separado

```powershell
git add .
git commit -m "fix: Improvement of the message for the end user (GD-666)"
```

Valide nuevamente la versión de su aplicación, ¿nota algún cambio? El fix que creó no modificó la versión de su aplicación, esto se debe a que aún falta enseñarle a GitVersion cómo interpretar nuestros mensajes de confirmación y cómo debe crecer la versión.

Cree el archivo gitversion.yml y copie el siguiente texto:

```yaml
mode: ContinuousDelivery
major-version-bump-message: "^(build|chore|ci|docs|feat|fix|perf|refactor|revert|style|test)(\\\\([\\\\w\\\\s-]*\\\\))?(!:|:.*\\\\n\\\\n((.+\\\\n)+\\\\n)?BREAKING CHANGE:\\\\s.+)"
minor-version-bump-message: "^(feat)(\\\\([\\\\w\\\\s-]*\\\\))?:"
patch-version-bump-message: "^(build|chore|ci|docs|fix|perf|refactor|revert|style|test)(\\\\([\\\\w\\\\s-]*\\\\))?:"
tag-prefix: ""
assembly-versioning-scheme: MajorMinorPatch
assembly-file-versioning-scheme: MajorMinorPatch
commit-message-incrementing: Enabled
branches:
  Dev:
    mode: ContinuousDelivery
    regex: "^(?i)(dev|Dev|DEV)$"
    source-branches: []
    tag: ""
```

Valide nuevamente la versión de su aplicación. Deberá tener una salida como la siguiente:

```json
{
  "Major": 0,
  "Minor": 1,
  "Patch": 1,
  "PreReleaseTag": "",
  "PreReleaseTagWithDash": "",
  "PreReleaseLabel": "",
  "PreReleaseLabelWithDash": "",
  "PreReleaseNumber": null,
  "WeightedPreReleaseNumber": 60000,
  "BuildMetaData": 1,
  "BuildMetaDataPadded": "0001",
  "FullBuildMetaData": "1.Branch.main.Sha.06c402ece7535e012820abc16f197545fba75936",
  "MajorMinorPatch": "0.1.1",
  "SemVer": "0.1.1",
  "LegacySemVer": "0.1.1",
  "LegacySemVerPadded": "0.1.1",
  "AssemblySemVer": "0.1.1.0",
  "AssemblySemFileVer": "0.1.1.0",
  "FullSemVer": "0.1.1+1",
  "InformationalVersion": "0.1.1+1.Branch.main.Sha.06c402ece7535e012820abc16f197545fba75936",
  "BranchName": "main",
  "EscapedBranchName": "main",
  "Sha": "06c402ece7535e012820abc16f197545fba75936",
  "ShortSha": "06c402e",
  "NuGetVersionV2": "0.1.1",
  "NuGetVersion": "0.1.1",
  "NuGetPreReleaseTagV2": "",
  "NuGetPreReleaseTag": "",
  "VersionSourceSha": "b125a253090a3fea886795f9fa8329799311e40a",
  "CommitsSinceVersionSource": 1,
  "CommitsSinceVersionSourcePadded": "0001",
  "UncommittedChanges": 1,
  "CommitDate": "2024-05-16"
}
```

De nuevo, valide la variable SemVer y notará el incremento del PATCH (Z) de 0 → 1.

Le recomiendo realizar algunas otras pruebas agregando nuevos mensajes de confirmación que modifiquen otras partes de la aplicación.

---
**Nota**
Si desea ver la "aplicación" web ejecutándose, le recomiendo instalar la extensión Live Server.

Con estos pasos, hemos demostrado cómo utilizar Git y GitVersion para gestionar el versionamiento de su aplicación de manera efectiva. Continúe explorando y aplicando estos conceptos para mejorar sus prácticas de desarrollo.