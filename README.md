# GitHub Actions — Ejemplos y notas

Repositorio de ejemplo con flujos de trabajo y notas sobre GitHub Actions.

## Descripción

Colección de archivos YAML y documentación que muestran ejemplos prácticos de workflows para distintos escenarios (Laravel, SSH, matrices, dispatch manual, pruebas, etc.). Útil como referencia o plantilla para integrar en tus propios proyectos.

## Contenido

- `laravel.yml` : Ejemplo de flujo para aplicaciones Laravel (tests, composer, despliegue básico).
- `ssh_use.yml` : Ejemplo de uso de SSH en workflows (conexión a servidores remotos, despliegue).
- `test_1.yml` / `test_2.yml` : Flujos de prueba de ejemplo para distintos escenarios.
- `uso de Matrix.yml` : Ejemplo demostrando la estrategia de `matrix` para ejecutar combinaciones.
- `workflow dispatch.yml` : Flujo que usa `workflow_dispatch` para ejecución manual.
- `workflow.md` : Documentación interna sobre las secciones y su uso en uin workflow.
- `conceptos.md` : Notas conceptuales y apuntes sobre GitHub Actions.

> Nota: En repositorios reales, los workflows deben vivir normalmente en `.github/workflows/`.

## Cómo usar

1. Copia los archivos YAML que quieras a la carpeta `.github/workflows/` (créala si no existe).
2. Ajusta secretos y variables en `Settings > Secrets and variables > Actions` del repositorio en GitHub.
3. Usa `workflow_dispatch` para ejecuciones manuales o configura los triggers (`push`, `pull_request`, `schedule`, etc.).

Ejemplo de comandos en PowerShell para mover los YAML a la ubicación recomendada:

```powershell
New-Item -ItemType Directory -Force -Path .github\workflows; Move-Item -Path *.yml -Destination .github\workflows\
```

Y para commitear el `README.md`:

```powershell
git add README.md; git commit -m "Add README"; git push
```

## Contribuir

- Abre un issue para proponer mejoras o nuevos ejemplos.
- Crea un fork y un pull request con tus cambios.

## Licencia

Este repositorio está publicado bajo la licencia **MIT**.

Resumen: permiso libre para usar, copiar, modificar y distribuir el código, con atribución.

El texto completo de la licencia está en el archivo `LICENSE` en la raíz del repositorio.

## Contacto

Si necesitas ayuda o quieres que adapte ejemplos a un caso concreto, abre un issue o coméntalo en el PR.
