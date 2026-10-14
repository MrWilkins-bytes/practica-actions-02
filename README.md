# Práctica 2: Pipeline de CI con una App Real

## 1. Objetivo

Crear un pipeline de integración continua con GitHub Actions para un proyecto Node.js. El workflow debe ejecutarse al subir cambios a `main` y permitir una ejecución manual.

Se configurarán dos trabajos: uno para ejecutar las pruebas mediante `npm test` y otro para comprobar la versión de Node.js. Además, se provocará un fallo intencionado para comprobar que GitHub Actions detecta los errores y, posteriormente, se corregirá.

## 2. Crear y preparar el repositorio

Crear el repositorio remoto con GitHub CLI:

```bash
gh repo create practica-actions-02 --public --description "Práctica 2 de GitHub Actions con Node.js"
```

Ir a la carpeta de trabajo:

```bash
cd ~/Documents/1ºDAM/EntornoDesarrollo/practica-actions-02
```

Comprobar la ubicación, la rama actual, el estado del repositorio y el remoto:

```bash
pwd
git branch --show-current
git status
git remote -v
```

## 3. Crear la aplicación Node.js

Crear el archivo `package.json` con la información del proyecto y un comando de prueba:

```json
{
  "name": "practica-actions-02",
  "version": "1.0.0",
  "description": "Práctica 2 de GitHub Actions con Node.js",
  "main": "index.js",
  "scripts": {
    "test": "node -e \"console.log('Tests OK'); process.exit(0)\""
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

El comando `npm test` ejecuta una instrucción de Node.js que muestra `Tests OK` y termina con el código `0`, indicando que la prueba ha finalizado correctamente.

Consultar el archivo y comprobar los cambios:

```bash
cat package.json
git status
```

## 4. Crear el workflow de integración continua

Crear las carpetas donde GitHub Actions busca los workflows:

```bash
mkdir -p .github/workflows
```

Crear el archivo `.github/workflows/ci.yml`:

```yaml
name: CI Node.js

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm install
      - run: npm test

  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: node --version
```

El workflow utiliza un entorno Ubuntu y configura Node.js 20. El trabajo `test` instala las dependencias y ejecuta las pruebas, mientras que `lint` comprueba que Node.js está disponible mostrando su versión.

Consultar el archivo:

```bash
cat .github/workflows/ci.yml
git status
```

## 5. Ejecutar el workflow y comprobar el resultado inicial

Añadir los archivos, crear un commit y subir los cambios:

```bash
git add package.json .github/workflows/ci.yml
git commit -m "ci: crear pipeline Node.js"
git push
```

En GitHub, abrir la pestaña **Actions**, seleccionar `CI Node.js` y comprobar que los trabajos `test` y `lint` finalizan correctamente.

También se puede consultar la ejecución desde Git Bash:

```bash
gh run list
```

## 6. Provocar un fallo intencionado

Modificar el comando de prueba para que termine con el código `1`, que indica un error:

```bash
sed -i 's/process\.exit(0)/process.exit(1)/' package.json
```

Comprobar el cambio y subirlo:

```bash
cat package.json
git diff
git add package.json
git commit -m "test: provocar fallo intencionado"
git push
```

En GitHub Actions, abrir la nueva ejecución y comprobar que el trabajo `test` falla al ejecutar `npm test`. El trabajo `lint` puede terminar correctamente porque es independiente.

Consultar las ejecuciones:

```bash
gh run list
```

Esta prueba demuestra que el pipeline detecta un error en el comando de pruebas.

## 7. Corregir el fallo y comprobar el pipeline

Restaurar el código de salida `0` en `package.json`:

```bash
sed -i 's/process\.exit(1)/process.exit(0)/' package.json
```

Comprobar el archivo, guardar la corrección y subirla:

```bash
cat package.json
git diff
git add package.json
git commit -m "fix: restaurar tests correctos"
git push
```

Abrir **Actions** y comprobar que la nueva ejecución termina correctamente, con los trabajos `test` y `lint` en verde.

Consultar las últimas ejecuciones:

```bash
gh run list
```

## 8. Comandos de consulta utilizados

Consultar la versión y la autenticación de GitHub CLI:

```bash
gh --version
gh auth status
```

Consultar los archivos del proyecto:

```bash
ls -la
ls -la .github/workflows
ls -la img
```

Consultar el estado del repositorio y las últimas ejecuciones:

```bash
git status
git log --oneline -5
gh run list
```

## 9. Evidencias

### Evidencia 01: pipeline inicial en verde

![Evidencia 01](img/Evidencia01.png)

### Evidencia 02: pipeline con el fallo intencionado

![Evidencia 02](img/Evidencia02.png)

### Evidencia 03: registro del error en `npm test`

![Evidencia 03](img/Evidencia03.png)

### Evidencia 04: pipeline corregido en verde

![Evidencia 04](img/Evidencia04.png)

## 10. Resultado

Se ha configurado un pipeline de integración continua con GitHub Actions para un proyecto Node.js. El workflow se ejecuta al subir cambios a `main` y también puede lanzarse manualmente mediante `workflow_dispatch`.

Se han creado dos trabajos independientes: `test`, que instala las dependencias y ejecuta `npm test`, y `lint`, que comprueba la disponibilidad de Node.js.

Por último, se ha provocado un fallo intencionado para comprobar que el pipeline lo detecta y se ha corregido el código para recuperar una ejecución satisfactoria.

Repositorio: [practica-actions-02](https://github.com/MrWilkins-bytes/practica-actions-02)
