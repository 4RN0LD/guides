# Guía para crear y publicar una imagen Docker en un Container Registry Privado

En esta guía, aprenderás cómo construir una imagen Docker para una aplicación y subirla a un container registry privado. Usaremos Digital Ocean como ejemplo de container registry, aunque los pasos son aplicables a otros registros privados con ajustes mínimos.

## Prerrequisitos

- Tener instalado Docker en tu sistema.
- Tener una cuenta y un container registry configurado en Digital Ocean.
- Instalar la CLI de Digital Ocean (`doctl`) para poder interactuar con el registry. [Instrucciones de instalación aquí](https://docs.digitalocean.com/reference/doctl/how-to/install/).

## Pasos

### 1. Construir la imagen Docker desde un Dockerfile

Primero, crea la imagen Docker a partir de tu `Dockerfile`. Asegúrate de estar en el directorio donde se encuentra el archivo.

```bash
docker build -t <tu-app-spring-boot>:<version> .
```

- `<tu-app-spring-boot>`: Nombre de la imagen que le quieres asignar. Puedes poner el nombre de tu aplicación.
- `<version>`: Versión de la imagen, como `1.0.0` o `latest`, para identificar la versión de la imagen.

**Ejemplo:**

```bash
docker build -t myapp:1.0.0 .
```

### 2. Verificar que la imagen funcione correctamente

Antes de subir la imagen, es importante asegurarse de que funcione correctamente al ejecutarla localmente. Usa el siguiente comando para iniciar un contenedor a partir de la imagen:

```bash
docker run -p 8080:8080 <tu-app-spring-boot>:<version>
```

- `-p 8080:8080`: Mapea el puerto 8080 del contenedor al puerto 8080 de tu máquina. Ajusta el puerto si tu aplicación usa otro puerto.
- `<tu-app-spring-boot>:<version>`: Reemplaza con el nombre y la versión de tu imagen.

Si la aplicación inicia correctamente y puedes acceder a ella, por ejemplo, en `http://localhost:8080`, entonces la imagen está lista para ser publicada.

### 3. Loguearse en el container registry de Digital Ocean

Para subir la imagen a un container registry privado, primero debes autenticarte. En el caso de Digital Ocean:

1. Inicia la autenticación con `doctl`:

    ```bash
    doctl auth init
    ```

   - **Nota**: Necesitarás un token de acceso, que puedes generar en tu cuenta de Digital Ocean en la sección de API.

2. Loguea `doctl` en el container registry de Digital Ocean:

    ```bash
    doctl registry login
    ```

   Si la autenticación es exitosa, verás un mensaje confirmando el inicio de sesión.

### 4. Etiquetar la imagen para el registry

Para subir la imagen al registry, es necesario etiquetarla con el nombre del registry. Usando Digital Ocean como ejemplo, sigue este formato:

```bash
docker tag <tu-app-spring-boot>:<version> registry.digitalocean.com/<nombre-del-registro>/<tu-app-spring-boot>
```

- `<nombre-del-registro>`: Nombre del registry privado que creaste en Digital Ocean.
- `<tu-app-spring-boot>` y `<version>`: Reemplaza con el nombre y la versión de tu imagen.

**Ejemplo:**

```bash
docker tag myapp:1.0.0 registry.digitalocean.com/my-registry/myapp
```

### 5. Subir la imagen al container registry

Finalmente, sube la imagen al registry utilizando el siguiente comando:

```bash
docker push registry.digitalocean.com/<nombre-del-registro>/<tu-app-spring-boot>
```

**Ejemplo:**

```bash
docker push registry.digitalocean.com/my-registry/myapp
```

La imagen comenzará a subirse al registry. Esto puede tardar algunos minutos dependiendo del tamaño de la imagen y de tu conexión.

## Conclusión

Listo! Has construido, verificado y publicado una imagen Docker en un container registry privado. Ahora puedes usar esta imagen en otros entornos, como producción o pruebas, accediendo a ella desde tu container registry.

### Comandos resumen

```bash
# Construir la imagen
docker build -t <tu-app-spring-boot>:<version> .

# Verificar la imagen
docker run -p 8080:8080 <tu-app-spring-boot>:<version>

# Iniciar sesión en Digital Ocean
doctl auth init
doctl registry login

# Etiquetar la imagen
docker tag <tu-app-spring-boot>:<version> registry.digitalocean.com/<nombre-del-registro>/<tu-app-spring-boot>

# Subir la imagen
docker push registry.digitalocean.com/<nombre-del-registro>/<tu-app-spring-boot>
```

Espero que esta guía te haya servido.
