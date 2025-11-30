# BenderXploit - Security Scanner Application


## Descripción

**BenderXploit** es una aplicación de escritorio desarrollada con Electron que integra dos poderosas herramientas de seguridad informática: **Nmap** y **OWASP ZAP**. La aplicación proporciona una interfaz gráfica intuitiva para realizar análisis de seguridad en redes, servidores y aplicaciones web.

### Características Principales

- Escaneo de puertos y servicios con Nmap
- Análisis de vulnerabilidades web con OWASP ZAP
- Modo de escaneo automático inteligente
- Escaneo masivo de múltiples IPs
- Generación automática de reportes en PDF
- Interfaz gráfica moderna y fácil de usar


Sitios en línea (no necesitas instalar nada)
 OWASP Juice Shop

Aplicación vulnerable gamificada.
 https://juice-shop.herokuapp.com

(En caso de que esté caída, puedes usar cualquier instancia desplegada en Docker o en Gitpod).

 OWASP Security Shepherd (versión pública)

Para practicar retos de seguridad.
 https://owasp.org/www-project-security-shepherd/

 Google Gruyere (by Google)

Aplicación vulnerable oficial de Google.
 https://google-gruyere.appspot.com/

 WebGoat (Hosted)

Versión online para pruebas.
 https://webgoat.cloud/

(Si falla, se instala local pero esta instancia suele funcionar).

---

## Índice

1. [Requisitos Previos](#requisitos-previos)
2. [Instalación](#instalación)
3. [Estructura del Proyecto](#estructura-del-proyecto)
4. [Uso de la Aplicación](#uso-de-la-aplicación)
5. [Tipos de Escaneo](#tipos-de-escaneo)
6. [Componentes Técnicos](#componentes-técnicos)
7. [Solución de Problemas](#solución-de-problemas)

---

## Requisitos Previos

Antes de ejecutar la aplicación, asegúrate de tener instalado:

### Software Requerido

1. **Node.js** (versión 14 o superior)
   - Descarga: https://nodejs.org/

2. **Nmap** (herramienta de escaneo de red)
   - Windows: https://nmap.org/download.html
   - Linux: `sudo apt-get install nmap`
   - macOS: `brew install nmap`

3. **OWASP ZAP** (Zed Attack Proxy)
   - Descarga: https://www.zaproxy.org/download/
   - Debe estar ejecutándose en `localhost:8080` con la API habilitada
   - API Key configurada: `hedvrb1lvqidkvpfaevqlla03c`

### Configuración de OWASP ZAP

Para que ZAP funcione correctamente con la aplicación:

1. Inicia OWASP ZAP
2. Ve a `Tools > Options > API`
3. Habilita la API
4. Configura la API Key: `hedvrb1lvqidkvpfaevqlla03c`
5. Asegúrate de que ZAP esté escuchando en `localhost:8080`

---

## Instalación

### Paso 1: Clonar o Descargar el Proyecto

```bash
cd "C:\Users\Anderson Guerrero\Documents\BRM-DESARROLLO SEGURIDAD\Bender"
```

### Paso 2: Instalar Dependencias

```bash
npm install
```

### Paso 3: Ejecutar la Aplicación

```bash
npm start
```

---

## Estructura del Proyecto

```
Bender/
│
├── main.js              # Proceso principal de Electron (backend)
├── preload.js           # Script de preload (puente seguro IPC)
├── renderer.js          # Lógica de la interfaz (frontend)
├── index.html           # Interfaz gráfica principal
├── style.css            # Estilos de la aplicación
├── bender.png           # Icono de la aplicación
├── package.json         # Configuración del proyecto
└── README.md            # Este archivo
```

### Descripción de Archivos Clave

#### **main.js**
Archivo principal que contiene toda la lógica del backend:

- **Funciones de Nmap**: Ejecuta escaneos de red con diferentes perfiles
- **Funciones de ZAP**: Realiza análisis de vulnerabilidades web mediante la API REST
- **Generación de PDFs**: Crea reportes detallados con PDFKit
- **Gestión IPC**: Maneja la comunicación entre frontend y backend
- **Escaneo por lotes**: Procesa múltiples IPs secuencialmente

#### **preload.js**
Script de seguridad que expone APIs controladas al renderer:

```javascript
contextBridge.exposeInMainWorld('electronAPI', {
  startNmap: (target, scanType) => ipcRenderer.invoke('start-nmap', target, scanType),
  startZap: (target, scanType) => ipcRenderer.invoke('start-zap', target, scanType),
  generatePdf: (result) => ipcRenderer.invoke('generate-pdf', result),
  // ... más funciones
});
```

#### **renderer.js**
Lógica de la interfaz gráfica:

- Manejo de pestañas (Nmap, ZAP, Auto Scan, Multiple Scanning)
- Formateo de resultados
- Animaciones de progreso
- Integración con la API de Electron

#### **index.html**
Interfaz de usuario con 4 modos principales:

1. **Nmap Scanner**: Escaneo de puertos y servicios
2. **ZAP Proxy**: Análisis de vulnerabilidades web
3. **Auto Scan**: Detección automática y escaneo combinado
4. **Multiple Scanning**: Escaneo masivo de IPs

---

## Uso de la Aplicación

### 1. Modo Nmap Scanner

**Propósito**: Escanear puertos, servicios y sistema operativo de un objetivo.

**Pasos**:
1. Haz clic en la pestaña "Nmap Scanner"
2. Ingresa la IP o URL del objetivo (ejemplo: `192.168.1.1` o `example.com`)
3. Selecciona el tipo de escaneo:
   - **Normal**: Escaneo estándar con detección de versiones y OS
   - **Quick**: Escaneo rápido de puertos comunes
   - **Full**: Escaneo completo con scripts de vulnerabilidades
   - **Stealth**: Escaneo sigiloso (SYN Scan)
   - **UDP**: Escaneo de puertos UDP
4. Haz clic en "Escanear"
5. Una vez completado, puedes generar un reporte PDF

**Información que obtendrás**:
- Puertos abiertos, cerrados y filtrados
- Servicios en ejecución y sus versiones
- Sistema operativo detectado
- Banners de servicios
- Potenciales vulnerabilidades

---

### 2. Modo ZAP Proxy

**Propósito**: Analizar vulnerabilidades de seguridad en aplicaciones web.

**Pasos**:
1. **IMPORTANTE**: Asegúrate de que OWASP ZAP esté ejecutándose
2. Haz clic en la pestaña "ZAP Proxy"
3. Ingresa la URL completa del sitio web (ejemplo: `https://example.com`)
4. Selecciona el tipo de escaneo:
   - **Básico**: Solo Spider tradicional (rastreo de URLs)
   - **Rápido**: Spider + Active Scan rápido
   - **Completo**: Spider + AJAX Spider + Active Scan completo
   - **AJAX**: Solo AJAX Spider (para aplicaciones JavaScript)
5. Haz clic en "Escanear con ZAP"
6. **Nota**: Los escaneos web pueden tardar varios minutos

**Información que obtendrás**:
- Alertas de seguridad clasificadas por riesgo (Alto, Medio, Bajo, Info)
- URLs vulnerables detectadas
- Descripción de vulnerabilidades
- Recomendaciones de solución
- Nivel de confianza de cada alerta

**Tiempos de Escaneo Aproximados**:
- Básico: 5-15 minutos (dependiendo del tamaño del sitio)
- Rápido: 10-30 minutos
- Completo: 30+ minutos
- AJAX: Variable (optimizado para SPAs)

---

### 3. Modo Auto Scan

**Propósito**: Ejecutar automáticamente el escaneo apropiado según el tipo de objetivo.

**Funcionamiento Inteligente**:
- Si ingresas una **IP** (ejemplo: `192.168.1.1`): Solo ejecuta Nmap
- Si ingresas una **URL** (ejemplo: `https://example.com`): Ejecuta Nmap + ZAP

**Pasos**:
1. Haz clic en la pestaña "Auto Scan"
2. Ingresa la IP o URL del objetivo
3. Configura los tipos de escaneo deseados:
   - **Nmap**: Selecciona el perfil (Normal, Quick, Full, Stealth, UDP)
   - **ZAP**: Selecciona el perfil (Básico, Rápido, Completo, AJAX)
4. Haz clic en "Escaneo Automático"
5. La aplicación detectará automáticamente si es IP o URL
6. Puedes generar PDFs separados para Nmap y ZAP

**Ventajas**:
- No necesitas preocuparte por elegir la herramienta correcta
- Análisis completo en una sola acción
- Resultados consolidados en la misma terminal

---

### 4. Modo Multiple Scanning

**Propósito**: Escanear múltiples IPs de forma masiva y automatizada.

**Pasos**:
1. Haz clic en la pestaña "Multiple Scanning"
2. Tienes dos opciones para ingresar las IPs:

   **Opción A - Archivo de texto**:
   - Haz clic en "Seleccionar Archivo"
   - Elige un archivo `.txt` con una IP por línea

   **Opción B - Manual**:
   - Escribe las IPs directamente en el área de texto, una por línea

   Ejemplo:
   ```
   192.168.1.1
   192.168.1.2
   192.168.1.3
   10.0.0.1
   example.com
   ```

3. Selecciona el tipo de escaneo Nmap para todas las IPs
4. Haz clic en "Iniciar Escaneo Múltiple"
5. El progreso se mostrará en tiempo real
6. Una vez completado, genera un reporte PDF consolidado

**Información en el Reporte**:
- Resumen general (total escaneadas, exitosas, fallidas)
- Resultados detallados por cada IP:
  - Estado del host
  - Puertos abiertos y servicios
  - Sistema operativo detectado
  - Direcciones MAC y fabricantes
  - Tiempos de respuesta

**Nota**: Los escaneos se ejecutan **secuencialmente** para evitar sobrecarga de la red.

---

## Tipos de Escaneo

### Nmap - Tipos de Escaneo y Parámetros

#### 1. Escaneo Quick (Rápido)
**Uso**: Escaneo rápido para obtener información básica.

**Parámetros**:
```
-Pn                    # No hacer ping (asumir que el host está activo)
-F                     # Fast mode (escanear menos puertos)
-T4                    # Timing template 4 (agresivo)
--open                 # Mostrar solo puertos abiertos
--script-timeout 15s   # Timeout de scripts: 15 segundos
-oX -                  # Salida en formato XML
```

**Tiempo estimado**: 30 segundos - 2 minutos

---

#### 2. Escaneo Normal
**Uso**: Escaneo estándar equilibrado entre velocidad y detalle.

**Parámetros**:
```
-Pn                              # No hacer ping
-sV                              # Detección de versión de servicios
-O                               # Detección de sistema operativo
--script=default,banner,version  # Scripts de detección estándar
--version-intensity 5            # Intensidad de detección (nivel medio)
-oX -                            # Salida XML
```

**Tiempo estimado**: 2-5 minutos

---

#### 3. Escaneo Full (Completo)
**Uso**: Análisis completo y profundo del objetivo.

**Parámetros**:
```
-Pn                          # No hacer ping
-sV                          # Detección de versión
-sS                          # SYN Stealth Scan
-O                           # Detección de OS
--version-all                # Probar todas las versiones
-A                           # Enable OS detection, version detection, script scanning, and traceroute
--osscan-limit               # Limitar OS scan a objetivos prometedores
--script=banner,version,discovery,vuln-info,default  # Scripts extensivos
-T4                          # Timing agresivo
-oX -                        # Salida XML
```

**Tiempo estimado**: 10-30 minutos

**Información adicional obtenida**:
- Detección avanzada de OS
- Traceroute
- Scripts de vulnerabilidades
- Información detallada de servicios

---

#### 4. Escaneo Stealth (Sigiloso)
**Uso**: Escaneo furtivo para evitar detección por IDS/IPS.

**Parámetros**:
```
-Pn                    # No hacer ping
-sS                    # SYN Stealth Scan
-T2                    # Timing polite (más lento pero más sigiloso)
--max-retries 1        # Solo 1 reintento
--host-timeout 30m     # Timeout máximo por host: 30 minutos
-oX -                  # Salida XML
```

**Tiempo estimado**: Variable (muy lento, puede tardar horas)

**Características**:
- No completa la conexión TCP (SYN Scan)
- Velocidad reducida para evitar alarmas
- Menos ruidoso en los logs del objetivo

---

#### 5. Escaneo UDP
**Uso**: Detectar servicios en puertos UDP.

**Parámetros**:
```
-Pn                      # No hacer ping
-sU                      # UDP Scan
-sV                      # Detección de versión
--version-intensity 0    # Intensidad mínima (más rápido)
-T4                      # Timing agresivo
--top-ports 100          # Solo los 100 puertos UDP más comunes
-oX -                    # Salida XML
```

**Tiempo estimado**: 5-15 minutos

**Puertos UDP comunes detectados**:
- 53 (DNS)
- 67/68 (DHCP)
- 69 (TFTP)
- 123 (NTP)
- 161/162 (SNMP)

---

### ZAP - Tipos de Escaneo

#### 1. Escaneo Básico
**Uso**: Rastreo simple del sitio web.

**Proceso**:
1. Spider tradicional: Rastrea todas las URLs del sitio
2. Recopila estructura del sitio
3. No ejecuta pruebas activas de vulnerabilidades

**Tiempo estimado**: 5-15 minutos

**Timeout máximo**: 15 minutos

---

#### 2. Escaneo Rápido
**Uso**: Detección rápida de vulnerabilidades comunes.

**Proceso**:
1. Spider tradicional (10 min máx)
2. Active Scan con perfil rápido (20 min máx)
3. Pruebas de vulnerabilidades básicas

**Tiempo estimado**: 10-30 minutos

**Vulnerabilidades detectadas**:
- SQL Injection básica
- XSS (Cross-Site Scripting)
- Configuraciones inseguras
- Divulgación de información

---

#### 3. Escaneo Completo
**Uso**: Análisis exhaustivo de seguridad.

**Proceso**:
1. Spider tradicional (10 min máx)
2. AJAX Spider para contenido dinámico (variable)
3. Active Scan completo (30 min máx)
4. Pruebas extensivas de vulnerabilidades

**Tiempo estimado**: 30+ minutos

**Vulnerabilidades detectadas**:
- Todas las del escaneo rápido
- CSRF (Cross-Site Request Forgery)
- Inyección de comandos
- Path Traversal
- Vulnerabilidades de autenticación
- Configuraciones SSL/TLS
- Headers de seguridad faltantes
- Y muchas más según OWASP Top 10

---

#### 4. Escaneo AJAX
**Uso**: Específico para aplicaciones de una sola página (SPA) con JavaScript.

**Proceso**:
1. Utiliza un navegador headless
2. Ejecuta JavaScript para descubrir contenido dinámico
3. Rastrea aplicaciones React, Angular, Vue, etc.

**Ideal para**:
- Single Page Applications (SPA)
- Sitios web con mucho contenido JavaScript
- APIs REST consumidas desde el frontend

---

## Componentes Técnicos

### Dependencias Principales

```json
{
  "electron": "^35.0.3",      // Framework de aplicación de escritorio
  "node-nmap": "^4.0.0",      // Wrapper de Nmap para Node.js
  "pdfkit": "^0.16.0",        // Generación de documentos PDF
  "xml2js": "^0.6.2"          // Parseo de resultados XML de Nmap
}
```

### Arquitectura de la Aplicación

```
┌─────────────────────────────────────────┐
│         index.html (UI)                 │
│  ┌──────────────────────────────────┐   │
│  │   Tabs: Nmap | ZAP | Auto | Batch│   │
│  │   Terminal Output                 │   │
│  │   Progress Bars                   │   │
│  └──────────────────────────────────┘   │
└─────────────────────────────────────────┘
              ↕ (IPC Communication)
┌─────────────────────────────────────────┐
│         preload.js (Security Bridge)    │
│  - contextBridge para APIs seguras      │
└─────────────────────────────────────────┘
              ↕
┌─────────────────────────────────────────┐
│         main.js (Backend)               │
│  ┌──────────────────────────────────┐   │
│  │  Nmap Engine                      │   │
│  │  - runNmapScan()                  │   │
│  │  - batchScanIps()                 │   │
│  ├──────────────────────────────────┤   │
│  │  ZAP Engine                       │   │
│  │  - runZapScan()                   │   │
│  │  - zapRequest() (HTTP API)        │   │
│  ├──────────────────────────────────┤   │
│  │  PDF Generator                    │   │
│  │  - generatePDF()                  │   │
│  │  - generateZAPPDF()               │   │
│  │  - generateBatchPdf()             │   │
│  └──────────────────────────────────┘   │
└─────────────────────────────────────────┘
       ↕                    ↕
┌────────────┐      ┌──────────────┐
│   Nmap     │      │  OWASP ZAP   │
│ (External) │      │ REST API     │
│            │      │ :8080        │
└────────────┘      └──────────────┘
```

### Mejoras de Rendimiento Implementadas

#### Sistema de Reintentos HTTP para ZAP

La aplicación incluye un sistema robusto de manejo de errores:

```javascript
function zapRequest(endpoint, params = {}, retries = 3, timeout = 30000) {
  // - Timeout de 30 segundos por petición
  // - 3 reintentos automáticos
  // - Manejo de errores ECONNRESET y ETIMEDOUT
  // - Espera de 2 segundos entre reintentos
}
```

#### Polling Inteligente

- **Nmap**: Resultados inmediatos (ejecución síncrona)
- **ZAP Spider**: Polling cada 5 segundos (mejorado desde 2 segundos)
- **ZAP Active Scan**: Polling cada 5 segundos

#### Timeouts Máximos

| Tipo de Escaneo | Timeout Máximo |
|-----------------|----------------|
| ZAP Basic Spider | 15 minutos |
| ZAP Quick Spider | 10 minutos |
| ZAP Quick Active Scan | 20 minutos |
| ZAP Full Spider | 10 minutos |
| ZAP Full Active Scan | 30 minutos |
| Nmap (todos) | Sin límite (depende del tipo) |

---

## Solución de Problemas

### Error: "ZAP no está en ejecución"

**Síntoma**: Al hacer clic en "Escanear con ZAP" aparece un error.

**Solución**:
1. Asegúrate de que OWASP ZAP esté ejecutándose
2. Verifica que esté escuchando en `localhost:8080`
3. Habilita la API en ZAP (Tools > Options > API)
4. Configura la API Key: `hedvrb1lvqidkvpfaevqlla03c`

---

### Error: "socket hang up" o "ECONNRESET"

**Síntoma**: El escaneo ZAP se interrumpe con error de conexión.

**Causas**:
- ZAP cerró la conexión por sobrecarga
- El sitio objetivo bloqueó las peticiones

**Solución**:
- El sistema ahora reintenta automáticamente 3 veces
- Si persiste, usa un tipo de escaneo más ligero (Básico en vez de Completo)
- Verifica que el sitio objetivo no tenga WAF/protección anti-scraping

---

### Escaneo ZAP Muy Lento

**Síntoma**: El escaneo tarda mucho más de lo esperado.

**Causas**:
- Sitio web muy grande con muchas URLs
- AJAX Spider en aplicaciones complejas
- Servidor lento o con limitación de tasa

**Soluciones**:
1. **Usa escaneo Básico** primero para evaluar el tamaño del sitio
2. **Configura contextos en ZAP** para limitar el alcance
3. **Aumenta los recursos de ZAP**:
   ```bash
   # En Windows, edita zap.bat y ajusta la memoria:
   -Xmx4g  # Para 4GB de RAM
   ```
4. **Establece límites** en ZAP (Tools > Options > Spider)

---

### Nmap No Detecta Sistema Operativo

**Síntoma**: El campo OS muestra "N/A" o no aparece.

**Causas**:
- Requiere privilegios de administrador para detección de OS
- El host tiene firewall que bloquea las pruebas

**Solución**:
1. **Windows**: Ejecuta la aplicación como Administrador
2. **Linux/Mac**: Ejecuta con `sudo`
3. Usa el escaneo "Full" que tiene mejores algoritmos de detección

---

### Error: "Nmap not found"

**Síntoma**: Error al ejecutar escaneo Nmap.

**Solución**:
1. Verifica que Nmap esté instalado: `nmap --version`
2. Asegúrate de que esté en el PATH del sistema
3. **Windows**: Reinicia la terminal después de instalar Nmap

---

### PDF No Se Genera

**Síntoma**: Botón "Generar PDF" no hace nada o da error.

**Solución**:
1. Asegúrate de haber completado un escaneo primero
2. Verifica que tengas permisos de escritura en la carpeta del proyecto
3. Cierra cualquier PDF anterior que esté abierto (puede bloquear el archivo)

---

### Aplicación No Inicia

**Síntoma**: `npm start` falla o la ventana no aparece.

**Solución**:
1. Elimina `node_modules` y reinstala:
   ```bash
   rm -rf node_modules
   npm install
   ```
2. Verifica la versión de Node.js: `node --version` (debe ser 14+)
3. Revisa los logs en la terminal para errores específicos

---

## Notas de Seguridad

### Uso Ético

Esta herramienta está diseñada para **pruebas de seguridad autorizadas**.

**IMPORTANTE**:
- Solo escanea sistemas que tengas **permiso explícito** para probar
- Los escaneos no autorizados son **ilegales** y están penados por ley
- Usa la herramienta en entornos de laboratorio o con autorización escrita

### Privacidad

- La aplicación **NO** envía datos a servidores externos
- Todos los escaneos se ejecutan localmente
- Los reportes PDF se guardan en tu máquina local

---

## Mejoras Futuras

Funcionalidades planificadas:

- [ ] Integración con Metasploit
- [ ] Programación de escaneos automáticos
- [ ] Base de datos de resultados históricos
- [ ] Exportación a JSON/CSV
- [ ] Modo oscuro
- [ ] Multi-idioma
- [ ] Gráficos de tendencias de vulnerabilidades

---

## Licencia

ISC License

---

## Créditos

Desarrollado para pruebas de seguridad educativas y profesionales.

**Herramientas Integradas**:
- [Nmap](https://nmap.org/) - Gordon Lyon (Fyodor)
- [OWASP ZAP](https://www.zaproxy.org/) - OWASP Foundation
- [Electron](https://www.electronjs.org/) - OpenJS Foundation

---

**Autor**: Jefferson Guerrero - BRM Desarrollo Seguridad
**Versión**: 1.0.0
**Última actualización**: 2025
**Estado**: Producción
