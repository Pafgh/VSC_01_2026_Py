# Convertir un Repo GitHub Existente en Multi-Remoto (GitHub + GitLab)

Guía rápida para añadir GitLab como espejo de un repositorio que ya tienes funcionando en GitHub.

## 1. En la web de GitLab
1. Crea un **Proyecto Nuevo** en blanco.
2. **IMPORTANTE:** Déjalo completamente vacío (sin README, sin License).
3. Copia la URL del nuevo repositorio (ej. `https://gitlab.com/Usuario/NuevoRepo.git`).

## 2. En tu Terminal Local
Ejecuta estos 4 pasos en la carpeta de tu proyecto (sustituye las URLs por las tuyas):

```bash
# 1. Conectar el nuevo remoto de GitLab
git remote add gitlab https://gitlab.com/Usuario/NuevoRepo.git

# 2. Crear el grupo de sincronización 'all' (Base: tu GitHub actual)
git remote add all https://github.com/Usuario/RepoOriginal.git

# 3. Configurar 'all' para que apunte a los dos sitios
git remote set-url --add --push all https://gitlab.com/Usuario/NuevoRepo.git
git remote set-url --add --push all https://github.com/Usuario/RepoOriginal.git

# 4. Enviar todo el historial y vincular el botón Sync de VS Code
git push -u all master
```
*(Nota: Si tu rama principal se llama `main`, cambia `master` por `main` en el último comando)*

## Resultado
*   Todo tu historial de GitHub se ha copiado a GitLab.
*   A partir de ahora, al hacer **Push** o **Sync** en VS Code, los cambios se subirán a ambos servidores automáticamente.
