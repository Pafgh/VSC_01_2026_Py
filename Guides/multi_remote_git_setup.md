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

1. Crear el remoto apuntando al repositorio principal (ej. GitLab):
   *Este será el repositorio desde el que se descargarán los cambios por defecto.*
   ```bash
   git remote add all https://gitlab.com/Usuario/Repositorio.git
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
**Importante:** Cuando ejecutas `git pull all`, Git descargará los cambios del repositorio que configuraste al crear el remoto `all` (en este caso, GitLab).

**Recomendación sobre Colaboradores:**
Es crucial que el `fetch` (descarga) apunte al repositorio donde trabajan los **colaboradores** activos. Si otros usuarios suben código a GitLab, tú debes descargar de GitLab; de lo contrario, no recibirás sus cambios y podrías generar **inconsistencias** o conflictos graves en el historial al intentar subir tu código.

Para descargar:
```bash
git pull all master
# O explícitamente del remoto principal
git pull gitlab master
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

## 4. Clonado en un Ordenador Nuevo (Importante)

**LA CONFIGURACIÓN GIT ES LOCAL.**
La configuración de los remotos (el grupo 'all', etc.) se guarda en el archivo oculto `.git/config` de tu máquina. Este archivo **NO** viaja con tu código a la nube.

Si clonas este repositorio en un ordenador nuevo, al principio solo tendrás el remoto `origin` (del que clonaste).

**Pasos para restaurar la configuración multi-remoto en un ordenador nuevo:**

1.  **Clona el repositorio** (usando cualquiera de los dos, ej. GitHub):
    ```bash
    git clone https://github.com/Usuario/Repositorio.git
    cd Repositorio
    ```
    *(En este momento, si haces `git remote -v`, solo verás 'origin')*

2.  **Ejecuta el script de configuración**:
    Debes volver a ejecutar manualmente los comandos del "Paso 2" de esta guía para recrear el remoto `all`.

    *Resumen rápido para copiar-pegar (reemplaza las URLs por las tuyas):*
    ```bash
    # 1. Añadir el otro remoto (el que te falte, ej. gitlab)
    git remote add gitlab https://gitlab.com/Usuario/Repositorio.git

    # 2. Recrear el grupo 'all' (Apuntando el fetch a GitLab)
    git remote add all https://gitlab.com/Usuario/Repositorio.git
    git remote set-url --add --push all https://gitlab.com/Usuario/Repositorio.git
    git remote set-url --add --push all https://github.com/Usuario/Repositorio.git

    # 3. Reconectar la rama master (para que el botón Sync funcione)
    git push -u all master
    ```
