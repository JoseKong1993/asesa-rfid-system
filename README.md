# Sistema de Control RFID para Gestión de Llaves - ASESA

[![Build Status](https://travis-ci.com/josekong1993/asesa-rfid-system.svg?branch=main)](https://travis-ci.com/josekong1993/asesa-rfid-system)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Java Version](https://img.shields.io/badge/Java-11-orange.svg)](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-2.7.0-brightgreen.svg)](https://spring.io/projects/spring-boot)

## 📋 Descripción

Sistema integral de gestión de llaves mediante tecnología RFID desarrollado para **Aeroservicios Especializados S.A. de C.V. (ASESA)**, empresa de servicios aeronáuticos ubicada en Ciudad del Carmen, Campeche. El sistema automatiza el control de 40 llaves de habitaciones para 80 empleados rotativos (40 pilotos y 40 técnicos) que cambian de guardia cada 15 días.

## 🔴 Problema Identificado

ASESA enfrenta pérdidas críticas en el control de llaves:
- **8-10 llaves extraviadas mensualmente** generando costos de $4,500 MXN
- **Sin trazabilidad** del proceso de entrega/devolución
- **2-3 horas diarias** perdidas en búsqueda de llaves no devueltas
- **Conflictos operativos** durante cambios de guardia
- **Habitaciones inutilizables** por 2-3 días mientras se reponen llaves

## ✅ Solución Propuesta

Implementación de un sistema automatizado que combina:
- **Tecnología RFID** para identificación única de llaves
- **Llaveros personalizados** mediante impresión 3D
- **Dashboard web** en tiempo real
- **Sistema de alertas** automáticas por email
- **Base de datos** con histórico completo de movimientos
- **Reportes gerenciales** para toma de decisiones

### Beneficios Esperados:
- Reducción del 95% en pérdidas de llaves
- Ahorro mensual de $4,500 MXN
- ROI en menos de 2 meses
- Trazabilidad 100% para auditorías

## 🏗️ Arquitectura
```
┌─────────────────────────────────────────────────────┐
│                  CAPA PRESENTACIÓN                  │
│         [Web Browser] ←→ [Dashboard RH]            │
├─────────────────────────────────────────────────────┤
│                  CAPA APLICACIÓN                    │
│     [Apache Tomcat] ←→ [Spring Boot REST API]      │
├─────────────────────────────────────────────────────┤
│                   CAPA NEGOCIO                      │
│  [RFID Controller] ←→ [Employee Service] ←→ [Keys] │
├─────────────────────────────────────────────────────┤
│                    CAPA DATOS                       │
│         [MySQL 8.0] ←→ [Backup System]             │
├─────────────────────────────────────────────────────┤
│                   CAPA HARDWARE                     │
│      [RFID Reader RC522] ←→ [3D Printed Tags]      │
└─────────────────────────────────────────────────────┘
```

## 📑 Tabla de Contenidos

- [Requerimientos](#-requerimientos)
- [Instalación](#-instalación)
- [Configuración](#️-configuración)
- [Uso](#-uso)
- [API Documentation](#-api-documentation)
- [Contribución](#-contribución)
- [Testing](#-testing)
- [Deployment](#-deployment)
- [Roadmap](#-roadmap)
- [Wiki Externa](https://github.com/josekong1993/asesa-rfid-system/wiki)

## 💻 Requerimientos

### Servidores y Servicios
| Componente | Versión Mínima | Recomendada | Propósito |
|------------|---------------|-------------|-----------|
| Java JDK | 11 | 11.0.12 | Runtime principal |
| Apache Tomcat | 9.0 | 9.0.65 | Servidor de aplicaciones |
| MySQL | 8.0 | 8.0.30 | Base de datos |
| Redis (Opcional) | 6.0 | 7.0 | Cache de sesiones |
| Nginx (Producción) | 1.20 | 1.22 | Reverse proxy |

### Dependencias del Proyecto
```xml


    
        org.springframework.boot
        spring-boot-starter-web
        2.7.0
    
    
        org.springframework.boot
        spring-boot-starter-data-jpa
        2.7.0
    
    
        mysql
        mysql-connector-java
        8.0.30
    
    
        com.fazecast
        jSerialComm
        2.9.2
    

```

### Hardware Requerido
- **Lector RFID:** RC522 o RDM6300 con conexión USB
- **Chips RFID:** 125kHz o 13.56MHz (45 unidades)
- **Impresora 3D:** Para producción de llaveros (PLA+ o PETG)

### Requisitos del Sistema
- **Sistema Operativo:** Windows 10/11, Ubuntu 20.04+, macOS 11+
- **RAM:** Mínimo 4GB, Recomendado 8GB
- **Espacio en Disco:** 10GB libres
- **Puertos:** 8080 (App), 3306 (MySQL), 80/443 (Web)

## 🚀 Instalación

### Ambiente de Desarrollo

#### 1. Clonar el Repositorio
```bash
git clone https://github.com/josekong1993/asesa-rfid-system.git
cd asesa-rfid-system
```

#### 2. Configurar Base de Datos
```bash
# Instalar MySQL si no lo tienes
# Windows: Descargar desde https://dev.mysql.com/downloads/installer/
# Linux:
sudo apt-get update
sudo apt-get install mysql-server

# Crear base de datos
mysql -u root -p
CREATE DATABASE asesa_rfid;
CREATE USER 'asesa_user'@'localhost' IDENTIFIED BY 'AsEsA2024!';
GRANT ALL PRIVILEGES ON asesa_rfid.* TO 'asesa_user'@'localhost';
FLUSH PRIVILEGES;
exit;

# Ejecutar script de inicialización
mysql -u asesa_user -p asesa_rfid < src/backend/database/schema.sql
mysql -u asesa_user -p asesa_rfid < src/backend/database/sample_data.sql
```

#### 3. Instalar Dependencias
```bash
# Backend (Java/Maven)
cd src/backend
mvn clean install

# Frontend (si usa Node.js)
cd ../frontend
npm install
```

#### 4. Configurar Variables de Entorno
```bash
# Crear archivo .env en la raíz
cp .env.example .env

# Editar con tus valores
nano .env
```

### Ejecutar Pruebas Manualmente
```bash
# Pruebas unitarias
mvn test

# Pruebas de integración
mvn verify

# Pruebas con cobertura
mvn clean test jacoco:report

# Ver reporte de cobertura
open target/site/jacoco/index.html
```

### Ejecutar en Modo Desarrollo
```bash
# Opción 1: Maven
mvn spring-boot:run

# Opción 2: Java directamente
java -jar target/asesa-rfid-system-1.0.0.jar

# La aplicación estará disponible en:
# http://localhost:8080
```

## ⚙️ Configuración

### application.properties
```properties
# src/main/resources/application.properties

# Configuración de Base de Datos
spring.datasource.url=jdbc:mysql://localhost:3306/asesa_rfid
spring.datasource.username=asesa_user
spring.datasource.password=AsEsA2024!
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA/Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect

# Configuración del Servidor
server.port=8080
server.servlet.context-path=/api

# Configuración RFID
rfid.port=COM3
rfid.baudrate=9600
rfid.timeout=1000

# Configuración de Email
spring.mail.host=smtp.gmail.com
spring.mail.port=587
spring.mail.username=${EMAIL_USERNAME}
spring.mail.password=${EMAIL_PASSWORD}
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.starttls.enable=true

# Configuración de Alertas
alert.days.threshold=15
alert.email.recipients=rh@asesa.mx,admin@asesa.mx
alert.check.schedule=0 0 8 * * *

# Configuración de Logs
logging.level.root=INFO
logging.level.com.asesa=DEBUG
logging.file.name=logs/asesa-rfid.log
logging.pattern.file=%d{yyyy-MM-dd HH:mm:ss} - %msg%n
```

### Configuración de Hardware RFID
```python
# config/rfid_config.py

RFID_CONFIG = {
    'port': '/dev/ttyUSB0',  # Linux
    # 'port': 'COM3',         # Windows
    'baudrate': 9600,
    'timeout': 1,
    'retry_attempts': 3,
    'debounce_time': 2  # segundos entre lecturas
}
```

## 📖 Uso

### Para Usuario Final (Recepcionista)

#### Proceso de Entrega de Llave
1. Ingresar al sistema: `http://servidor-asesa/`
2. El empleado solicita una llave
3. Buscar empleado por número o nombre
4. Acercar llavero al lector RFID
5. Click en "Asignar Llave"
6. Confirmación visual en pantalla

#### Proceso de Devolución
1. Empleado acerca llavero al lector
2. Sistema detecta automáticamente la llave
3. Click en "Devolver Llave"
4. Llave queda disponible inmediatamente

#### Dashboard de Estado
- **Verde:** Llaves disponibles
- **Rojo:** Llaves asignadas  
- **Amarillo:** Llaves en mantenimiento
- **Gris:** Llaves extraviadas

### Para Usuario Administrador

#### Acceso al Panel de Administración
```
URL: http://servidor-asesa/admin
Usuario: admin
Password: [Configurado en instalación]
```

#### Funciones Administrativas

##### Gestión de Empleados
- Agregar nuevo empleado
- Editar información
- Activar/Desactivar
- Ver histórico de llaves

##### Gestión de Llaves
- Registrar nueva llave
- Asociar chip RFID
- Cambiar estado
- Reportar extravío

##### Reportes Disponibles
- Reporte mensual de movimientos
- Estadísticas de pérdidas
- Tiempo promedio de asignación
- Empleados con llaves pendientes

##### Configuración del Sistema
- Cambiar tiempo de alerta (días)
- Configurar destinatarios de email
- Programar backups
- Gestionar usuarios del sistema

## 🤝 Contribución

### Guía para Contribuidores

¡Agradecemos las contribuciones de la comunidad! Por favor sigue estos pasos:

#### 1. Fork del Repositorio
```bash
# En GitHub, click en "Fork"
# Luego clona tu fork
git clone https://github.com/TU_USUARIO/asesa-rfid-system.git
cd asesa-rfid-system
```

#### 2. Configurar Upstream
```bash
git remote add upstream https://github.com/josekong1993/asesa-rfid-system.git
git fetch upstream
```

#### 3. Crear Branch para tu Feature
```bash
git checkout -b feature/nombre-descriptivo
# Ejemplo: git checkout -b feature/agregar-reporte-excel
```

#### 4. Realizar Cambios
```bash
# Hacer cambios necesarios
# Agregar pruebas si es código nuevo
# Actualizar documentación si es necesario
```

#### 5. Commit con Mensaje Descriptivo
```bash
git add .
git commit -m "feat: Agregar exportación de reportes a Excel

- Implementado generación de Excel con Apache POI
- Agregadas pruebas unitarias
- Actualizada documentación de API"
```

#### 6. Push a tu Fork
```bash
git push origin feature/nombre-descriptivo
```

#### 7. Crear Pull Request
1. Ve a tu fork en GitHub
2. Click en "Pull Request"
3. Base: `josekong1993/asesa-rfid-system` <- Head: `tu-fork/feature`
4. Describe los cambios detalladamente
5. Esperar revisión y merge

### Estándares de Código
- Seguir convenciones de Java/Spring Boot
- Documentar métodos públicos con Javadoc
- Mantener cobertura de pruebas > 70%
- Usar meaningful variable names

## 🧪 Testing

### Ejecutar Suite Completa
```bash
# Todos los tests
mvn clean test

# Solo tests de integración
mvn test -Dtest=**/*IntegrationTest

# Solo tests unitarios
mvn test -Dtest=**/*Test

# Test específico
mvn test -Dtest=RFIDServiceTest#testValidarTag
```

### Estructura de Tests
```
tests/
├── unit/
│   ├── RFIDServiceTest.java
│   ├── EmpleadoServiceTest.java
│   └── LlaveServiceTest.java
└── integration/
    ├── ApiIntegrationTest.java
    └── DatabaseIntegrationTest.java
```

## 🚢 Deployment

### Opción 1: Deployment Local (Producción)
```bash
# Compilar proyecto
mvn clean package

# Crear estructura de directorios
sudo mkdir -p /opt/asesa-rfid
sudo cp target/asesa-rfid-system-1.0.0.jar /opt/asesa-rfid/

# Crear servicio systemd
sudo nano /etc/systemd/system/asesa-rfid.service
```
```ini
[Unit]
Description=ASESA RFID System
After=syslog.target mysql.service

[Service]
User=asesa
ExecStart=/usr/bin/java -jar /opt/asesa-rfid/asesa-rfid-system-1.0.0.jar
SuccessExitStatus=143
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```
```bash
# Iniciar servicio
sudo systemctl enable asesa-rfid
sudo systemctl start asesa-rfid
sudo systemctl status asesa-rfid
```

### Opción 2: Deployment en Heroku
```bash
# Instalar Heroku CLI
# https://devcenter.heroku.com/articles/heroku-cli

# Login
heroku login

# Crear aplicación
heroku create asesa-rfid-system

# Agregar addon MySQL
heroku addons:create cleardb:ignite

# Configurar variables
heroku config:set SPRING_PROFILES_ACTIVE=production
heroku config:set EMAIL_USERNAME=tu-email@gmail.com
heroku config:set EMAIL_PASSWORD=tu-password-app

# Deploy
git push heroku main

# Ver logs
heroku logs --tail

# Abrir aplicación
heroku open
```

### Opción 3: Docker
```dockerfile
# Dockerfile
FROM openjdk:11-jdk-slim
VOLUME /tmp
COPY target/*.jar app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```
```bash
# Construir imagen
docker build -t asesa-rfid:latest .

# Ejecutar contenedor
docker run -d \
  --name asesa-rfid \
  -p 8080:8080 \
  -e DB_HOST=mysql \
  -e DB_PORT=3306 \
  asesa-rfid:latest
```

### Docker Compose (Aplicación + MySQL)
```yaml
# docker-compose.yml
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: asesa_rfid
      MYSQL_USER: asesa_user
      MYSQL_PASSWORD: AsEsA2024!
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql

  app:
    build: .
    ports:
      - "8080:8080"
    depends_on:
      - mysql
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/asesa_rfid
      SPRING_DATASOURCE_USERNAME: asesa_user
      SPRING_DATASOURCE_PASSWORD: AsEsA2024!

volumes:
  mysql_data:
```
```bash
# Iniciar todo
docker-compose up -d

# Ver logs
docker-compose logs -f

# Detener
docker-compose down
```

## 📈 Roadmap

### Versión 1.0 (Actual - Enero 2024)
- ✅ CRUD de empleados
- ✅ Control RFID básico
- ✅ Dashboard web
- ✅ Sistema de alertas email
- ✅ Reportes básicos

### Versión 1.5 (Q2 2024)
- ⏳ App móvil para consultas
- ⏳ Reportes avanzados en PDF
- ⏳ Integración con sistema de nómina
- ⏳ API REST documentada con Swagger

### Versión 2.0 (Q3 2024)  
- 📅 Notificaciones WhatsApp Business
- 📅 Machine Learning para predicción de pérdidas
- 📅 Modo offline con sincronización
- 📅 Multi-sede (expansión a otras ubicaciones)

### Versión 3.0 (2025)
- 💡 Integración con control de acceso físico
- 💡 Reconocimiento facial para entrega
- 💡 Blockchain para auditoría inmutable
- 💡 Panel analytics con BI

## 📺 Demo

### Video de Demostración
🎥 [Ver Demo Completa en YouTube](https://www.youtube.com/watch?v=-XfBKtqmFhY)

### Capturas de Pantalla

#### Dashboard Principal
![Dashboard](docs/images/dashboard.png)

#### Proceso de Entrega
![Entrega](docs/images/entrega.png)

#### Panel de Administración
![Admin](docs/images/admin.png)

## 🔗 Enlaces Útiles

- [Wiki del Proyecto](https://github.com/josekong1993/asesa-rfid-system/wiki)
- [Documentación API](https://documenter.getpostman.com/view/asesa-api)
- [Board de Tareas](https://github.com/josekong1993/asesa-rfid-system/projects/1)
- [Reportar Bug](https://github.com/josekong1993/asesa-rfid-system/issues/new?template=bug_report.md)
- [Solicitar Feature](https://github.com/josekong1993/asesa-rfid-system/issues/new?template=feature_request.md)

## 📄 Licencia

Este proyecto está licenciado bajo la Licencia MIT - ver el archivo [LICENSE](LICENSE) para más detalles.

## 👥 Equipo

- **José Manuel Ceballos Lazaro** - *Desarrollador Principal* - [@josekong1993](https://github.com/josekong1993)
- **ASESA** - *Cliente* - [Aeroservicios Especializados](http://www.asesa.mx)

## 🙏 Agradecimientos

- Universidad Tecmilenio por el apoyo académico
- Personal de ASESA por su colaboración
- Comunidad Open Source por las librerías utilizadas

---

**© 2024 ASESA RFID System - Desarrollado con ❤️ en Ciudad del Carmen, Campeche**
**Jose Manuel Ceballos Lazaro**
```

---
