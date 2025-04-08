# 🏗️ Kodeksa Infraestructura de Producción

Este módulo contiene la infraestructura necesaria para levantar la base de datos **PostgreSQL** usada en producción mediante **Docker Compose**. Utiliza una red Docker externa (`backnet`) para permitir la comunicación con otros contenedores, como el backend desplegado por separado.

---

## 📁 Estructura

```
infra/
├── docker-compose.yml    # Contenedor PostgreSQL de producción
├── .env                  # Variables de entorno sensibles
├── init.sql              # Script inicial (ej: extensiones, migraciones)
```

---

## 🚀 Instrucciones para levantar en local o servidor

1. **Crea la red externa `backnet` si no existe (una sola vez):**

```bash
docker network create backnet
```

2. **Levanta la base de datos:**

```bash
docker-compose -p kodeksa-infra -f infra/docker-compose.yml up -d
```

> Esto iniciará un contenedor llamado `kodeksa_db`, escuchando en el puerto `5433` y usará las variables de entorno de `.env`.

---

## ⚙️ Variables de entorno (.env)

```env
POSTGRES_USER=kodeksa
POSTGRES_PASSWORD=your_secure_password
POSTGRES_DB=kodeksa_db
TZ=America/Guayaquil
```

---

## 🧪 Verificar conexión

Puedes conectarte desde herramientas como **DBeaver**, **pgAdmin**, **psql**, o desde tu backend con los siguientes datos:

- **Host**: `localhost` (o el nombre del contenedor en la red Docker, `kodeksa_db`)
- **Puerto**: `5433`
- **Base de datos**: `kodeksa_db`
- **Usuario**: `kodeksa`
- **Contraseña**: la definida en `.env`

---

## 🩺 Healthcheck

El contenedor implementa un `healthcheck` que valida la conexión a la base de datos mediante:

```bash
pg_isready -U kodeksa -d kodeksa_db
```

Esto asegura que el servicio solo se considere saludable cuando está listo para aceptar conexiones.

---

## 📦 Volumen persistente

Los datos de la base de datos se almacenan en:

```
./vol-db/ → /var/lib/postgresql/data
```

Esto garantiza persistencia entre reinicios.

---

## 🧼 Resetear entorno (⚠️ elimina los datos)

Si necesitas reiniciar completamente:

```bash
docker-compose -p kodeksa-infra -f infra/docker-compose.yml down -v
```

Esto eliminará el contenedor **y su volumen de datos**.

---

## 🔗 Red compartida (`backnet`)

Este servicio se conecta a la red externa `backnet`, permitiendo que otros contenedores (como el backend) se comuniquen con la base de datos simplemente usando el nombre del contenedor `kodeksa_db` como host.

---

## 📄 Licencia

Este proyecto es parte de la infraestructura de despliegue de [Kodeksa](https://kodeksa.lat).
