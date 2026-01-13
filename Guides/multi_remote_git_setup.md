# Configuración de Git Multi-Remoto (GitHub + GitLab)

Esta guía explica cómo configurar un repositorio local para que sincronice los cambios con ambos repositorios remotos (GitHub y GitLab) simultáneamente.

## 1. Configuración Inicial

Asumiendo que ya tienes inicializado tu repositorio local.

### Paso 1: Añadir los Remotos Individuales
Primero, definimos cada servidor por separado.

```bash
# Añadir GitHub
git remote add github https://github.com/Usuario/Repositorio.git

# Añadir GitLab
git remote add gitlab https://gitlab.com/Usuario/Repositorio.git
```

### Paso 2: Crear el Remoto "All" (Todo)
Creamos un grupo lógico llamado `all` que agrupe ambos. La técnica consiste en crear un remoto y añadirle múltiples URLs de "push".

1. Crear el remoto apuntando al primero (ej. GitHub):
   ```bash
   git remote add all https://github.com/Usuario/Repositorio.git
   ```

2. Añadir la segunda URL (GitLab) al mismo remoto para PUSH:
   ```bash
   git remote set-url --add --push all https://gitlab.com/Usuario/Repositorio.git
   ```

3. Asegurarse que la primera URL también está explícita para PUSH:
   ```bash
   git remote set-url --add --push all https://github.com/Usuario/Repositorio.git
   ```

## 2. Comandos de Uso Diario

### Crear Versiones (Commits)
Esto no cambia, es igual que siempre. Trabajas en local.
```bash
git add .
git commit -m "Descripción de los cambios"
```

### Subir Cambios (Push)
Aquí está la magia. En lugar de hacer push a `origin`, haces push a `all`.

```bash
# Sube a ambos sitios a la vez
git push all master
```
*(Nota: Usa `main` en lugar de `master` si esa es tu rama principal)*

### Descargar Cambios (Pull)
**Importante:** No puedes hacer "pull" de dos sitios a la vez "mezclados". Debes decidir cuál es tu fuente de verdad principal para descargar, o bajarlos individualmente.

Lo normal es bajar de uno (por ejemplo github):
```bash
git pull github master
```

O si quieres sincronizar ambos manualmente:
```bash
git fetch --all
git merge github/master
```

### Ver Estado
Para ver a dónde apunta cada remoto:
```bash
git remote -v
```
Deberías ver que `all` tiene dos líneas `(push)`.

## 3. Configuración para VS Code (Botón Sync)

Para poder usar el botón de "Sync/Push" (la flecha circular) en la interfaz gráfica de VS Code y que envíe los cambios a **ambos sitios automáticamente**, debes configurar la rama para que `all` sea su destino predeterminado.

Ejecuta este comando **una sola vez**:

```bash
git push -u all master
```

**¿Qué hace esto?**
*   `-u` (upstream): Le dice a git que a partir de ahora, cuando estés en la rama `master` (o `main`), su "pareja" en la nube es el remoto `all`.
*   Esto habilita el botón de la interfaz de VS Code. Ahora, al hacer clic en **"Sync Changes"** o **"Push"**, Git enviará tu código a GitHub y GitLab simultáneamente sin que tengas que escribir comandos.
