# Wiggles Mini

Inventario de equipos para visitas a clientes. Portable, sin instalación y sin dependencias:
un `.bat`, dos `.ps1`, catálogos JSON y una carpeta `External`. Guarda siempre en local (CSV)
y sincroniza con Google Sheets cuando hay internet. Incluye módulos portados de WinUtil
(aplicaciones, ajustes, reparaciones y apps preinstaladas) con interfaz propia en español.

![flujo](https://img.shields.io/badge/PowerShell-5.1%2B-blue) ![windows](https://img.shields.io/badge/Windows-10%20%7C%2011-0078D6)

---

## 1. Qué hace

| Módulo | Detalle |
|---|---|
| **Inventario** | 70 campos: equipo, CPU, RAM (módulos y slots), discos y SMART, batería (desgaste y ciclos), SO, licencias, red, TPM, Secure Boot, antivirus y software instalado. |
| **Google Sheets** | Una pestaña nueva por visita (`2026-10-22 · Cliente`) en el libro maestro. |
| **Office** | Instala Office 2024 con el ODT. Descarga el paquete de Microsoft durante la instalación. |
| **Aplicaciones** | 251 programas con búsqueda y 15 categorías; instala con winget o Microsoft Store (7-Zip, Chrome, PDF, utilidades, juegos, etc.). |
| **Ajustes** | 33 ajustes de privacidad, interfaz, sistema y mantenimiento. Guarda el valor original y permite deshacer. |
| **Sistema** | Estado del equipo escaneado: SO y build, instalación, último arranque, uptime, disco, batería, licencias, TPM, Secure Boot, antivirus y red. Diagnóstico: clave OEM, reporte de salud en PDF, reporte de batería y resumen completo. |
| **Software** | Office 2024, aplicaciones básicas, runtimes y lista del software instalado con búsqueda. |
| **Mantenimiento** | Punto de restauración, bloatware, rendimiento, red y administrador local; 6 reparaciones, respaldo del usuario, reparar tienda, CHKDSK rápido, 7 características de Windows y 15 paneles clásicos. |
| **AppX** | 34 apps preinstaladas de Windows: quitar, reinstalar y detectar instaladas. |
| **Herramientas** | Backup y restauración de drivers, limpieza de drivers fantasma, 3DP Chip, portables (CrystalDiskInfo/Mark, HWiNFO) y soporte del fabricante (Dell, HP, Lenovo, Intel, AMD, NVIDIA). Funciona sin pendrive: lo que falta se descarga del repositorio. |
| **Edición** | Cambio de Windows Home a Pro con la clave genérica de Microsoft. |

Todo se ejecuta desde el pendrive o, si lo olvidaste, desde el repositorio (`irm | iex`).
No escribe en el equipo del cliente salvo lo que apliques a propósito (Office, aplicaciones,
ajustes o reparaciones).

---

## 2. Contenido

```
2-Wiggles_Mini\
├─ INICIAR.bat            ← doble clic aquí
├─ WigglesMini.ps1        ← interfaz (WPF embebido)
├─ WigglesCore.ps1        ← escaneo, CSV, cola y Excel
├─ config.json            ← cliente, técnico, webhook y token
├─ apps.json              ← catálogo de 251 aplicaciones (winget / msstore)
├─ appx.json              ← catálogo de 34 apps preinstaladas
├─ tweaks.json            ← catálogo de 33 ajustes con deshacer
├─ tools.json             ← catálogo de herramientas (portables, winget, web)
├─ irm.ps1                ← arranque remoto (irm | iex)
├─ README.md
├─ TERCEROS.md            ← atribución de WinUtil y licencia MIT
├─ Publicar-GitHub.md     ← cómo publicarlo y usar dominio propio
├─ Tools\
│  ├─ Portables\          ← zips de CrystalDiskInfo, CrystalDiskMark, HWiNFO y 3DP Chip
│  └─ Instaladores\       ← opcional: tus instaladores locales (Nitro, Uninstall Tool)
├─ Sheets\
│  └─ Code.gs             ← Apps Script + instalador del libro
├─ External\
│  └─ Office\             ← Setup.exe (ODT) + Configuration.xml
└─ Data\                  ← se crea sola
   ├─ Inventario_<Cliente>_<fecha>.csv
   ├─ Inventario_<Cliente>_<fecha>.xlsx
   ├─ Ajustes\            ← estado.json de ajustes aplicados
   ├─ Pendientes\         ← registros sin enviar
   └─ Logs\
```

Tamaño: **~25 MB** (incluye el ODT de Office y los portables de 24 MB).
Office se descarga aparte durante la instalación. Para llevarlo en un pendrive basta
copiar la carpeta completa; los portables que no estén se bajan solos del repositorio.

---

## 3. Uso en 4 pasos

1. Copia la carpeta al pendrive.
2. Doble clic en `INICIAR.bat`. Pide permisos de administrador (necesario para TPM y Secure Boot).
3. Escribe **Cliente** y **Sede/Técnico**. El serial y el hardware se llenan solos al abrir.
4. **GUARDAR Y SIGUIENTE EQUIPO** graba el CSV, encola el envío y limpia el formulario para el próximo equipo.

Al terminar la visita pulsa **Sincronizar** (o deja que se sincronice al guardar si hay internet).
Si no hubo red, los registros quedan en `Data\Pendientes` y se envían solos en la siguiente
oportunidad, sin duplicarse.

> El botón **Exportar Excel** genera un `.xlsx` real (con filtro y encabezado) desde el CSV del día.
> **Abrir carpeta** lleva a `Data\`.
>
> ¿Sin pendrive? También puedes ejecutarlo con `irm | iex` (ver sección 11).

---

## 4. Configuración (`config.json`)

```json
{
  "Cliente": "",
  "Tecnico": "",
  "WebhookUrl": "https://script.google.com/macros/s/XXXX/exec",
  "Token": "WIGGLES-CAMBIAR-ESTE-TOKEN",
  "AppsBasicas": ["7zip.7zip", "Google.Chrome", "VideoLAN.VLC"],
  "SincronizarAlGuardar": true,
  "RepoBase": "https://raw.githubusercontent.com/WigglesVz/wiggles-mini/main"
}
```

| Campo | Uso |
|---|---|
| `Cliente` | Se recuerda entre sesiones; se puede cambiar en la interfaz. |
| `Tecnico` | Se carga en *Sede / Técnico*. |
| `WebhookUrl` | URL `/exec` de la implementación de Apps Script. |
| `Token` | Debe ser idéntico al de `Code.gs`. Si está vacío, no se envía nada. |
| `AppsBasicas` | IDs de winget para el botón **Instalar aplicaciones básicas**. |
| `SincronizarAlGuardar` | `true` intenta enviar al guardar; `false` solo con el botón. |
| `RepoBase` | Base del repositorio desde donde se bajan los portables que faltan. Si se omite, usa `https://raw.githubusercontent.com/WigglesVz/wiggles-mini/main`. |

Los cambios hechos en la interfaz se guardan solos al cerrar.

---

## 5. Google Sheets (el script crea todo)

`Sheets\Code.gs` incluye un instalador: prepara el libro maestro, la pestaña **INICIO** y
el **token**. No hay que crear pestañas a mano.

1. Entra a <https://script.google.com> → **Nuevo proyecto**.
2. Borra el contenido y pega `Sheets\Code.gs`.
3. Arriba elige la función **`instalar`** y pulsa **Ejecutar**. Autoriza los permisos.
   - Usa el libro indicado en `SPREADSHEET_ID` (ya viene configurado).
   - Genera el token y lo escribe en la pestaña **INICIO**.
   - El token también queda en el **registro de ejecución**.
4. **Implementar → Nueva implementación → Aplicación web**
   - *Ejecutar como:* Yo
   - *Quién tiene acceso:* Cualquier persona
5. Copia la URL que termina en `/exec` y el token en `WebhookUrl` y `Token` de `config.json`.
6. Prueba: abre la URL en el navegador. Debe responder `{"ok":true,...}`.

> Si prefieres usar un libro existente, pon su ID en `SPREADSHEET_ID` antes de ejecutar
> `instalar()`; en ese caso no crea uno nuevo.

Funciones disponibles en el editor:

| Función | Para qué |
|---|---|
| `instalar` | Crea libro + pestaña INICIO + token |
| `mostrarToken` | Imprime el token actual en el registro |
| `rotarToken` | Genera un token nuevo y actualiza INICIO |
| `estado` | Muestra libro, webhook y pestañas |
| `reordenarTodas` | Reordena todas las visitas al orden nuevo sin borrar datos |
| `reordenarHojaActiva` | Reordena solo la pestaña activa (menú **Wiggles** en la hoja) |

Cada visita crea su pestaña con encabezado verde, fila fija, filtro y formato.
Si un registro se reenvía, el `id` evita duplicarlo.

El orden de columnas se define una sola vez en `$script:Columnas` (`WigglesCore.ps1`):
primero **Orden, Hostname, Tipo_Equipo, Marca, Modelo, Serial, SO_Nombre, CPU_Modelo,
RAM_Total_GB, RAM_Tipo, Disco_Tipo, Disco_Capacidad_GB, Disco_Uso_Pct y Comentarios**,
y el resto del detalle a la derecha. Las pestañas nuevas ya nacen con ese orden; para
las antiguas ejecuta `reordenarTodas()` una vez desde el editor de Apps Script.

Las dos columnas técnicas (`ID` y `Recibido`) van siempre al final, después de
`Software_Lista`, para que la hoja empiece por **Orden**. Si algún día se agrega un campo
nuevo al inventario, se inserta justo antes de `ID` y no rompe los datos existentes.
`reordenarTodas()` no toca una pestaña cuyas cabeceras estén vacías o repetidas: en ese
caso avisa en el registro de ejecución para corregirlas a mano.

> Cada vez que edites `Code.gs` usa **Implementar → Gestionar implementaciones → Editar**
> para conservar la misma URL. Si creas una implementación nueva, cambia la URL en `config.json`.

---

## 6. Office 2024

- `External\Office\Setup.exe` es el **Office Deployment Tool** y `Configuration.xml` define
  Office Profesional Plus 2024 en español, sin interfaz.
- **Instalar Office 2024** descarga el paquete (~2 GB) y lo instala en la misma pasada.
  Requiere conexión a internet estable en el equipo del cliente.
- El pendrive no guarda el paquete: no crece y siempre se instala la versión vigente.

Para cambiar de edición o idioma edita `Configuration.xml` (producto, `Language`, `ExcludeApp`).

La activación se hace siempre con la licencia oficial del cliente desde
**Configuración → Sistema → Activación de Windows**. Wiggles solo lee y muestra el estado.

---

## 7. Aplicaciones (winget)

La pestaña **APLICACIONES** trae un catálogo de 251 programas agrupados en 15 categorías
(la base inicial de 75 se amplió con el catálogo de WinUtil):

- Busca por nombre, descripción o id de winget.
- Filtra por categoría (Navegadores, Oficina, Utilidades, Multimedia, Juegos, etc.).
- Las marcadas con **★** son recomendadas; **SELECCIONAR RECOMENDADAS** las marca todas.
- **INSTALAR SELECCIONADAS** instala en silencio, una por una, y muestra cuáles fallaron.
- Soporta ids de winget y de Microsoft Store (`msstore:...`); los de Store se instalan con
  `--source msstore`.

> Las 75 apps originales conservan sus descripciones en español; las nuevas usan una
> descripción breve por categoría. El **Id** es el dato que realmente usa winget.

No se descargan instaladores de portales como FilePuma: winget usa el manifiesto
oficial del fabricante y valida el hash. Si un programa no está en winget ni en Microsoft
Store, no aparece en el catálogo.

El catálogo está en `apps.json`. Para agregar uno, usa el **Id** de winget:

```json
{ "Id": "MiPrograma.Id", "Nombre": "Mi Programa", "Categoria": "Utilidades", "Descripcion": "Que hace", "Recomendada": false }
```

Busca el Id con:

```powershell
winget search "nombre del programa"
```

Requiere conexión a internet. Si winget falla, usa **Reparar winget** en MANTENIMIENTO.
El botón **APLICACIONES BASICAS** de la pestaña SOFTWARE sigue usando la lista corta de
`config.json` (`AppsBasicas`).

---

## 8. Ajustes, Mantenimiento y AppX

Estos módulos combinan funciones propias (**SISTEMA** y **SOFTWARE**) con las portadas desde
WinUtil (**AJUSTES**, **MANTENIMIENTO** y **APPX**), reescritas con una interfaz y un motor
propios en español.

### AJUSTES

- 33 tarjetas agrupadas en **Privacidad**, **Interfaz**, **Sistema** y **Mantenimiento**.
- Cada tarjeta indica **SEGURO** o **AVANZADO**. Los avanzados pueden afectar funciones
  del sistema; aplícalos solo si sabes lo que hacen.
- **APLICAR** guarda el valor actual (registro, servicios y tipo de inicio) en
  `Data\Ajustes\estado.json`; **DESHACER** lo restaura.
- Los ajustes con script (por ejemplo, reiniciar Explorer o cambiar la hibernación) también
  ejecutan su script de deshacer.
- **Widgets** se puede aplicar pero no deshacer desde la herramienta; se marca como
  `APLICADO (SIN DESHACER)`.
- **Limpieza de componentes** y **Archivos temporales** son acciones puntuales: se pueden
  ejecutar varias veces y no guardan estado.
- Antes de aplicar varios ajustes en un equipo de cliente, crea un **punto de restauración**
  desde MANTENIMIENTO. El estado de ajustes no sustituye un respaldo del sistema.

### SISTEMA

- **Estado del equipo**: SO y build, fecha de instalación, último arranque, uptime, disco
  (tipo, capacidad, uso y salud), batería (estado, desgaste y ciclos).
- **Seguridad y red**: estado de licencia de Windows y Office, TPM, Secure Boot, antivirus,
  dominio/grupo, IP y MAC.
- **Licenciamiento**: estado de las licencias y cambio de edición Home → Pro.
- **Diagnóstico**: clave OEM del BIOS, **reporte de salud en PDF** (todo el registro:
  hardware, disco, batería, red, seguridad y licencias; se genera desde el último escaneo),
  reporte de batería en HTML y resumen completo.
- El estado se actualiza al escanear y al abrir la pestaña. Las acciones de mantenimiento
  ya no viven aquí: están en MANTENIMIENTO.

### SOFTWARE

- **Office 2024**: instalar o desinstalar la versión existente.
- **Aplicaciones básicas**: lista corta de `config.json` y acceso al catálogo completo.
- **Complementos**: runtimes de Visual C++ y .NET.
- **Software instalado**: lista del equipo escaneado con búsqueda por nombre o versión.
  Sale del campo `Software_Lista` del inventario y no requiere conexión.

### MANTENIMIENTO

**Mantenimiento rápido**

| Botón | Qué hace |
|---|---|
| Crear punto de restauración | Punto de restauración del sistema. |
| Eliminar bloatware | Quita las apps preinstaladas más comunes (para control fino, usa APPX). |
| Plan de máximo rendimiento | Activa el plan de energía de alto rendimiento. |
| Reparar red | `winsock reset`, `int ip reset`, `winhttp reset proxy`, flush y register DNS. Requiere reinicio. |
| CompactOS | `compact.exe /CompactOS:always`: comprime los archivos del sistema y libera entre 1 y 4 GB. No borra nada; se revierte con `compact /CompactOS:never`. Pide confirmación y tarda unos minutos. |
| Crear administrador local | Crea un usuario local y lo agrega al grupo Administradores. |
| Gestionar apps preinstaladas | Lleva a la pestaña APPX. |

**Reparaciones**

| Botón | Qué hace |
|---|---|
| SFC + DISM | `sfc /scannow` y `DISM /RestoreHealth`. Puede tardar 20-40 minutos. |
| Resetear Windows Update | Detiene servicios, renombra `SoftwareDistribution` y `Catroot2`, reregistra DLLs y reinicia servicios. No borra políticas de grupo. |
| Hora NTP | Configura `pool.ntp.org` y resincroniza el reloj. |
| Limpiar temporales | Borra temporales del usuario y de Windows. |
| Limpiar componentes (DISM) | `DISM /StartComponentCleanup` (sin `ResetBase`). |
| Reparar winget | Reinstala/registra App Installer y winget. |

**Respaldo y chequeo**

| Botón | Qué hace |
|---|---|
| Mini-Backup del usuario | Copia con `robocopy` Escritorio, Documentos, Imágenes, Descargas, Favoritos, Música y Videos a la carpeta o USB que elijas, y deja un `LEEME.txt` con equipo, usuario y fecha. Es lo primero que hay que pulsar antes de formatear. |
| Reparar tienda | `wsreset.exe`: reinicia la caché de Microsoft Store. No borra aplicaciones. |
| CHKDSK rápido | `chkdsk C: /scan`: analiza el disco sin reiniciar (1-5 min) y deja el resultado en el log. |

**Características de Windows**: .NET Framework 2/3/4, Hyper-V, WSL, Sandbox, cliente NFS,
reproductor/DirectPlay y servidor OpenSSH. Se instalan con
`Enable-WindowsOptionalFeature`. Hyper-V y Sandbox no están disponibles en Windows Home.
Puede requerir reinicio y descarga desde Windows Update.

**Paneles clásicos**: 15 accesos directos (Administración de equipos, Administración de
discos, Panel de control, mouse, red, energía, impresoras, programas, región, seguridad,
sonido, sistema, fecha/hora, firewall y restauración).

### APPX

- 34 apps preinstaladas, agrupadas por categoría.
- **PREDETERMINADOS** marca 13 apps de bloatware común (Bing, Copilot, Noticias, Clima,
  Xbox, Dev Home, Power Automate, Solitaire, etc.). Calculadora, Fotos, Notepad, Paint,
  Cámara y Recortes no se marcan por defecto.
- **QUITAR SELECCIONADAS** elimina el paquete del usuario actual y, si existe, el paquete
  provisionado para nuevos usuarios.
- **INSTALAR SELECCIONADAS** intenta registrar el manifiesto local; si no está disponible,
  usa `winget --source msstore` con el `StoreId`.
- **DETECTAR INSTALADAS** marca con `●` las apps presentes. Al abrir la pestaña se ejecuta
  una detección automática.
- Quitar una app puede afectar dependencias (por ejemplo, Xbox o Store). Si algo falla,
  vuelve a instalar desde Microsoft Store.

> Los catálogos `apps.json`, `appx.json` y `tweaks.json` derivan de Chris Titus WinUtil
> (MIT). Consulta `TERCEROS.md` para la atribución y la licencia.

---

## 9. Herramientas (drivers y portables)

Pestaña **HERRAMIENTAS**. Está pensada para trabajar sin pendrive: en el paquete viajan
los portables livianos y el resto se descarga la primera vez que lo pulsas.

### DRIVERS

| Botón | Qué hace |
|---|---|
| Backup de drivers | `Export-WindowsDriver -Online` a `C:\Backups_Wiggles\Drivers_<equipo>_<fecha>`. No necesita internet ni USB: sirve para dejar los drivers listos antes de reinstalar Windows. |
| Restaurar drivers | Eliges la carpeta del backup (con los `.inf`) y aplica `pnputil /add-driver *.inf /subdirs /install`. Avisa cuántos `.inf` hay antes de tocar nada. |
| Limpieza de drivers fantasma | Lista los dispositivos que ya no están conectados (`Get-PnpDevice -Present:$false`) y los quita con `pnputil /remove-device`. No usa el WMI de VZ y no toca el hardware conectado. |
| 3DP Chip | Portable: lee el hardware y abre la página del driver que falta. |

### APLICACIONES PORTABLES

| Herramienta | Tamaño | Uso |
|---|---|---|
| CrystalDiskInfo | 6.2 MB | Salud SMART, temperatura y horas de encendido del disco. |
| CrystalDiskMark | 4.0 MB | Velocidad real de lectura y escritura. |
| HWiNFO | 8.8 MB | Sensores, temperaturas y detalle de todo el hardware. |
| Uninstall Tool | — | Desinstalador. Copia tu portable a `Tools\Instaladores\UninstallTool\`. |
| Nitro Pro 9 | — | PDF. Copia tu instalador a `Tools\Instaladores\NitroPro9.exe`. |

> **Uninstall Tool y Nitro Pro 9 no se publican** en el repositorio: son programas de pago
> (la copia de Nitro trae serial y web de cracks). Si los quieres a mano, cópialos tú en
> `Tools\Instaladores\`; el botón los detecta y los abre, y si no están avisa con la ruta
> exacta. El resto de la pestaña funciona igual.

Un botón con **●** significa que la herramienta ya está en disco; con **▸** indica que se
descargará al pulsarla y muestra su tamaño; con **○** es una herramienta local que aún no
copiaste.

### SOPORTE DEL FABRICANTE

Se instalan con winget/msstore y, si eso falla, abren la página oficial de descarga:

| Botón | Origen |
|---|---|
| Dell SupportAssist | `XPDCCXDN26VNBT` (msstore) |
| HP Support Assistant | `HP.SupportAssistant` (winget) |
| Lenovo Vantage | `9WZDNCRFJ4MV` (msstore) |
| Intel Driver & Support | `Intel.IntelDriverAndSupportAssistant` (winget) |
| AMD Drivers / NVIDIA Drivers | Página oficial (detectan el equipo) |

### DÓNDE SE GUARDAN

- Si existe `Tools\` junto al script (pendrive o copia local) se usa esa carpeta, y los
  zips que ya vienen en `Tools\Portables` se descomprimen sin internet.
- En modo `irm` se usa `%LOCALAPPDATA%\WigglesMini\Herramientas`, así no se vuelven a
  descargar en cada visita.
- **ABRIR CARPETA** lleva a esa ruta y **ACTUALIZAR** redibuja los botones.
- La base de descarga se toma de `RepoBase` en `config.json` (o el repositorio por defecto).

---

## 10. Problemas frecuentes

| Síntoma | Causa / solución |
|---|---|
| `... no se puede cargar porque la ejecución de scripts está deshabilitada` | Usa `INICIAR.bat` (aplica `-ExecutionPolicy Bypass`). |
| TPM y Secure Boot dicen `Requiere admin` | Abre con `INICIAR.bat` y acepta el UAC. |
| No sincroniza | Revisa `WebhookUrl`, `Token` y que la implementación sea *Cualquier persona*. Los registros quedan en `Data\Pendientes`. |
| Sheet responde `Token invalido` | Ejecuta `mostrarToken()` en Apps Script y copia ese valor en `config.json`. |
| No aparece la pestaña | Abre la pestaña **INICIO** del libro que creó `instalar()`; ahí está la URL real. |
| Office no instala | Verifica la conexión a internet y que `Setup.exe` y `Configuration.xml` estén en `External\Office`. |
| Un ajuste quedó a medias | Revisa `Data\Logs\Mini_<fecha>.log`. Pulsa **DESHACER** en la tarjeta; si no aparece, borra su entrada de `Data\Ajustes\estado.json` solo después de restaurar el valor a mano. |
| Una app AppX no se reinstala | Necesita internet y Microsoft Store. Si no tiene `StoreId`, solo se puede registrar el manifiesto local; revisa el log. |
| Windows Update sigue fallando | El reset moderado no borra políticas de grupo. Revisa `gpedit`/registro o usa el punto de restauración. |
| Las apps nuevas tienen descripción genérica | El catálogo se amplió desde WinUtil; el **Id** y el **Nombre** son los datos importantes. Puedes editar `Descripcion` en `apps.json`. |
| Antivirus bloquea el `.bat` | Es un lanzador de PowerShell sin firma; agrega la carpeta a exclusiones. |
| Acentos raros en los `.ps1` | Guárdalos siempre como **UTF-8 con BOM** si los editas. |

Los detalles de cada sesión quedan en `Data\Logs\Mini_<fecha>.log`.

---

## 11. Modo remoto (sin pendrive)

Pensado para cuando se olvida el pendrive. Jeric solo pega:

```powershell
irm "https://raw.githubusercontent.com/WigglesVz/wiggles-mini/main/irm.ps1" | iex
```

Enlaces cortos (más fáciles de recordar):

```powershell
irm "https://is.gd/wiggles" | iex
```

Respaldo: `https://tinyurl.com/wiggles-irm`. Ambos redirigen al `irm.ps1` del
repositorio; si algún día cambias de repositorio, crea un alias nuevo.

`irm.ps1` descarga la herramienta a `%TEMP%\WigglesMini`, pide administrador y la abre.
Siempre trae la última versión publicada. Trae también `tools.json`, así que la pestaña
HERRAMIENTAS funciona igual sin pendrive: cada portable se baja del repositorio la primera
vez y queda guardado en `%LOCALAPPDATA%\WigglesMini\Herramientas`.

> En modo remoto los CSV y la cola quedan en `%TEMP%\WigglesMini\Data`. Si el equipo se
> apaga o se limpia el temporal, se pierden; sincroniza antes de cerrar.

Requisitos y pasos completos (incluido dominio propio tipo `tudominio.com/irm`):
**`Publicar-GitHub.md`**.

> El modo remoto no guarda el paquete de Office; la instalación de Office sigue siendo
> por internet, igual que con el pendrive.

---

## 12. Notas

- Windows 10/11, PowerShell 5.1 (incluido). No requiere módulos ni instalar nada.
- No se envía información a ningún sitio salvo el webhook que configures.
- Los portables libres viajan en `Tools\Portables`; Nitro Pro 9 y Uninstall Tool **no** se
  publican (son de pago o con licencia dudosa) y se usan solo si los copias tú.
- El reporte de salud en PDF usa Edge o Chrome en modo `--headless`. Si no hay ninguno,
  abre el HTML y se puede imprimir a PDF a mano.
- Wiggles **no** activa licencias: solo informa si Windows y Office están activados.
