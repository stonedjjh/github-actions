# Workflow

- name: nombre que le asignamos a nuestro workflow.

- on: evento que disparara la ejecucion del workflow (ejemplo: push, pull_request, schedule).

- jobs: conjunto de trabajos que se ejecutaran como parte del workflow, puede ser uno o varios.

  - job_id: identificador unico para el trabajo.

    - runs-on: especifica el entorno donde se ejecutara el trabajo (ejemplo: ubuntu-latest, windows-latest).

    - steps: lista de pasos que componen el trabajo.

      - name: nombre descriptivo del paso.

      - uses: accion reutilizable que se utilizara en el paso (ejemplo: actions/checkout@v2).

      - run: comando o script que se ejecutara en el paso.

    needs: especifica los jobs de los que depende este job; se indica una lista de identificadores para forzar orden y reutilizar outputs (ejemplo: needs: [build, test]). Permite acceder a outputs de otro job con needs.<job_id>.outputs.<output_name>.

## On

El campo `on` define los eventos que disparan la ejecución del workflow. Puede ser un solo evento o una lista de eventos. Algunos ejemplos comunes incluyen:

- push: se ejecuta cuando se hace un push a una rama específica.
- pull_request: se ejecuta cuando se crea o actualiza una pull request.
- schedule: se ejecuta en horarios específicos definidos por una expresión cron.
- workflow_dispatch: permite ejecutar el workflow manualmente desde la interfaz de usuario de GitHub.
- repository_dispatch: permite disparar el workflow mediante una llamada API externa.

También se pueden combinar múltiples eventos y configurar opciones adicionales para cada evento. Por ejemplo:

Aqui el disparador es cuando se hace push en ramas especificas como lo son main y develop.

```yaml
on:
  push:
    branches:
      - main
      - develop
  pull_request:
    types: [opened, synchronize, reopened]
  schedule:
    - cron: '0 0 * * 0' # Ejecuta cada domingo a medianoche
    workflow_dispatch:
    repository_dispatch:
      types: [custom-event]
```

## Actions

es un fragmento de código reutilizable que realiza una tarea específica dentro de un flujo de trabajo de automatización. Estos "actions" son los componentes fundamentales que se utilizan en la plataforma de integración y despliegue continuos (CI/CD) de GitHub para automatizar tareas como compilar, probar y desplegar código. Se pueden crear acciones personalizadas, utilizar acciones oficiales o tomar acciones del GitHub Marketplace.

Por ejemplo, una acción común es `actions/checkout`, que permite clonar el repositorio en el entorno de ejecución del flujo de trabajo. Otro ejemplo es `actions/setup-node`, que configura un entorno de Node.js para ejecutar scripts relacionados con JavaScript.

```yaml
steps:
  - name: Checkout repository
    uses: actions/checkout@v2

  - name: Set up Node.js
    uses: actions/setup-node@v2
    with:
      node-version: "14"

  - name: Install dependencies
    run: npm install

  - name: Run tests
    run: npm test
```

### Directiva with

with es una palabra clave utilizada dentro de un paso para pasar argumentos y parámetros a una acción. Esencialmente, es una forma de configurar la acción con la información que necesita para ejecutarse correctamente, como variables de entorno, valores de entrada o configuraciones específicas.

```yaml
steps:
  - name: Set up Node.js
    uses: actions/setup-node@v2
    with:
      node-version: "14"
      cache: "npm"
```

## run

Es la ejecución de un comando o script de línea de comandos como un paso dentro de un flujo de trabajo de GitHub Actions. Se utiliza para tareas como instalar dependencias, compilar código o ejecutar pruebas. La palabra clave run en los archivos de flujo de trabajo YAML especifica qué comandos se deben ejecutar en un contenedor de un ejecutor.

```yaml
steps:
  - name: Install dependencies
    run: |
      npm install
      npm run build

  - name: Run tests
    run: npm test
```

> [!NOTE]
> Puedes usar múltiples líneas en un comando run utilizando el símbolo de barra vertical (|) para indicar que el comando continúa en la siguiente línea.

## inputs

Los inputs en GitHub Actions son parámetros que puedes pasar a un flujo de trabajo o una acción para hacerlos más dinámicos y configurables. Permiten que los usuarios proporcionen información, como un nombre de rama o una versión, al ejecutar una tarea, lo que permite que el mismo flujo de trabajo se adapte a diferentes necesidades. Estos inputs se definen en el archivo YAML del flujo de trabajo.

```yaml
on:
  workflow_dispatch:
    inputs:
      branch-name:
        description: 'Nombre de la rama'
        required: true
        default: 'main'

jobs:
    deploy:
        runs-on: ubuntu-latest
        steps:
        - name: Checkout code
            uses: actions/checkout@v2

        - name: Deploy to branch
            run: |
            echo "Deploying to branch: ${{ github.event.inputs.branch-name }}"
            # Comandos de despliegue aquí
```

## Services

Un "service" en GitHub Actions es un servicio (como una base de datos o un servidor) que se ejecuta como un contenedor dentro de un trabajo, accesible a través de localhost o una red de contenedores. Permite que las acciones dentro del mismo trabajo se comuniquen con ese servicio, ya que se inicia antes de que los pasos comiencen y se detiene cuando finalizan. Por ejemplo, se puede configurar un servicio de base de datos para ejecutar pruebas de una aplicación.

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:latest
        ports:
          - 5432:5432
        env:
          POSTGRES_USER: user
          POSTGRES_PASSWORD: password
          POSTGRES_DB: testdb
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Run tests
        run: |
          # Comandos para ejecutar pruebas que utilizan la base de datos Postgres
```

## Variables de entorno

Las variables de entorno en GitHub Actions son pares clave-valor que almacenan información accesible durante la ejecución de un flujo de trabajo. Se pueden definir a nivel global, por trabajo o por paso, y se utilizan para configurar comportamientos, almacenar secretos o compartir datos entre diferentes partes del flujo de trabajo.

```yaml
#variable global level
env:
  GLOBAL_VAR: 'valor_global'
jobs:
    build:
        runs-on: ubuntu-latest
        #variable jobs level
        env:
        JOB_VAR: 'valor_del_trabajo'
        steps:
        - name: Checkout code
            uses: actions/checkout@v2
            env:
            #variable steps level
            STEP_VAR: 'valor_del_paso'
        - name: Print variables
            run: |
            echo "Global Variable: $GLOBAL_VAR"
            echo "Job Variable: $JOB_VAR"
            echo "Step Variable: $STEP_VAR"
```

## Matrix

La estrategia matrix en GitHub Actions permite ejecutar un mismo trabajo múltiples veces con diferentes configuraciones, como distintos sistemas operativos, versiones de lenguajes o entornos. Esto se logra definiendo una matriz de variables en el flujo de trabajo, lo que genera automáticamente un trabajo para cada combinación posible de estas variables.

```yaml
name: CI - Matrix example

on:
  push:
    branches: [ main ]
  pull_request:

jobs:
  build:
    runs-on: ${{ matrix.os }}
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, windows-latest]
        node: [14, 16]
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up Node.js ${{ matrix.node }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}

      - name: Install dependencies
        run: npm ci 

      - name: Run tests
        run: npm test
```
