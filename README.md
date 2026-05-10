# FLARE-VM [ES]

<p align="center">
  <img src="https://raw.githubusercontent.com/mandiant/flare-vm/main/Images/flarevm-logo.png" alt="FLARE-VM Logo" width="600">
</p>

## 👋 Bienvenido / Welcome

**Español:** Bienvenido a FLARE-VM - una colección de scripts de instalación de software para sistemas Windows que te permite configurar y mantener fácilmente un entorno de ingeniería inversa en una máquina virtual (VM). FLARE-VM fue diseñado para resolver el problema de la curación de herramientas de ingeniería inversa y se basa en dos tecnologías principales: [Chocolatey](https://chocolatey.org) y [Boxstarter](https://boxstarter.org). Chocolatey es un sistema de gestión de paquetes basado en Nuget para Windows, donde un "paquete" es esencialmente un archivo ZIP que contiene scripts de instalación PowerShell que descargan y configuran una herramienta específica. Boxstarter aprovecha los paquetes de Chocolatey para automatizar la instalación de software y crear entornos de Windows repetibles y scriptados.

**English:** Welcome to FLARE-VM - a collection of software installation scripts for Windows systems that allows you to easily setup and maintain a reverse engineering environment on a virtual machine (VM). FLARE-VM was designed to solve the problem of reverse engineering tool curation and relies on two main technologies: [Chocolatey](https://chocolatey.org) and [Boxstarter](https://boxstarter.org). Chocolatey is a Windows-based Nuget package management system, where a "package" is essentially a ZIP file containing PowerShell installation scripts that download and configure a specific tool. Boxstarter leverages Chocolatey packages to automate the installation of software and create repeatable, scripted Windows environments.

## ⚙️ Requisitos / Requirements

> [!CAUTION]
> **FLARE-VM SOLO debe instalarse en una máquina virtual / FLARE-VM should ONLY be installed on a virtual machine**.

La VM debe cumplir los siguientes requisitos / The VM should satisfy the following requirements:

- Windows ≥ 10
- PowerShell ≥ 5
- Capacidad de disco de al menos 60 GB y memoria de al menos 2GB / Disk capacity of at least 60 GB and memory of at least 2GB
- Nombres de usuario sin espacios u otros caracteres especiales / Usernames without spaces or other special characters
- Conexión a Internet / Internet connection
- Protección contra manipulación y cualquier solución Anti-Malware (ej. Windows Defender) deshabilitados, preferiblemente vía Política de Grupo / Tamper Protection and any Anti-Malware solution (e.g., Windows Defender) disabled, preferably via Group Policy
- Actualizaciones de Windows deshabilitadas / Windows Updates Disabled

## 📚 Instrucciones de Instalación / Installation Instructions

Esta sección documenta los pasos para instalar FLARE-VM. También puede ser útil el [video de instalación de FLARE-VM](https://www.youtube.com/watch?v=i8dCyy8WMKY).

This section documents the steps to install FLARE-VM. You may also find useful the [FLARE-VM installation video](https://www.youtube.com/watch?v=i8dCyy8WMKY).

### Pre-instalación / Pre-installation

**Preparar una máquina virtual Windows 10+ / Prepare a Windows 10+ virtual machine:**

1. Instalar Windows en la máquina virtual, por ejemplo usando el ISO de Windows 10 desde [Microsoft](https://www.microsoft.com/en-us/software-download/windows10ISO)
2. Asegurarse de que se cumplan los [requisitos anteriores](#-requisitos--requirements), incluyendo:
   - **Deshabilitar Actualizaciones de Windows** (al menos hasta que finalice la instalación)
     - [Cómo deshabilitar actualizaciones automáticas](https://www.windowscentral.com/how-stop-updates-installing-automatically-windows-10)
   - **Deshabilitar Protección contra manipulación y cualquier solución Anti-Malware** (ej. Windows Defender), preferiblemente vía Política de Grupo:
     - GPO: [Deshabilitar Defender por GPO](https://superuser.com/a/1757341)
     - No-GPO - Manual: [Deshabilitar permanentemente Defender](https://www.maketecheasier.com/permanently-disable-windows-defender-windows-10/)
     - No-GPO - Automatizado: [windows-defender-remover](https://github.com/ionuttbara/windows-defender-remover)
     - No-GPO - Semi-Automatizado: [ToggleDefender.ps1](https://github.com/AveYo/LeanAndMean/blob/main/ToggleDefender.ps1)
3. **Tomar una instantánea de la VM** para poder revertir siempre a un estado anterior a la instalación de FLARE-VM
4. **NOTA para IDA Pro:** Si estás instalando IDA Pro vía `idapro.vm`, debes colocar tu instalador de IDA Pro (y opcionalmente, tu archivo de licencia) en el Escritorio antes de ejecutar el instalador de FLARE-VM

### Instalación de FLARE-VM / FLARE-VM Installation

1. Abrir un prompt de `PowerShell` como administrador / Open a `PowerShell` prompt as administrator
2. Descargar el script de instalación [`install.ps1`](https://raw.githubusercontent.com/mandiant/flare-vm/main/install.ps1) a tu Escritorio:
   ```powershell
   (New-Object net.webclient).DownloadFile('https://raw.githubusercontent.com/mandiant/flare-vm/main/install.ps1',"$([Environment]::GetFolderPath("Desktop"))\install.ps1")
   ```
3. Desbloquear el script de instalación / Unblock the installation script:
   ```powershell
   Unblock-File .\install.ps1
   ```
4. Habilitar la ejecución de scripts / Enable script execution:
   ```powershell
   Set-ExecutionPolicy Unrestricted -Force
   ```
   - Si recibes un error diciendo que la política de ejecución está sobrescrita por una política definida en un ámbito más específico, puedes necesitar pasar un ámbito vía: 
     ```powershell
     Set-ExecutionPolicy Unrestricted -Scope CurrentUser -Force
     ```
   - Para ver las políticas de ejecución para todos los ámbitos: 
     ```powershell
     Get-ExecutionPolicy -List
     ```
5. Finalmente, ejecutar el script del instalador / Finally, execute the installer script:
   ```powershell
   .\install.ps1
   ```
   - Para pasar tu contraseña como argumento: `.\install.ps1 -password <password>`
   - Para usar el modo CLI con mínima interacción: `.\install.ps1 -password <password> -noWait -noGui`
   - Para usar el modo CLI con archivo de configuración personalizado: `.\install.ps1 -customConfig <config.xml> -password <password> -noWait -noGui`
6. Después de la instalación se recomienda cambiar al modo de red `host-only` y tomar una instantánea de la VM

### Parámetros del Instalador / Installer Parameters

A continuación se describen los parámetros CLI / Below are the CLI parameter descriptions:

```
PARÁMETROS / PARAMETERS
    -password <String>
        Contraseña del usuario actual para permitir resiliencia de reinicio vía Boxstarter. El script solicita la contraseña si no se proporciona.
        Current user password to allow reboot resiliency via Boxstarter. The script prompts for the password if not provided.

    -noPassword [<SwitchParameter>]
        Parámetro de interruptor que indica que no se necesita contraseña para reinicios.
        Switch parameter indicating a password is not needed for reboots.

    -customConfig <String>
        Ruta a un archivo XML de configuración. Puede ser una ruta de archivo o URL.
        Path to a configuration XML file. May be a file path or URL.

    -customLayout <String>
        Ruta a un archivo XML de diseño de barra de tareas. Puede ser una ruta de archivo o URL.
        Path to a taskbar layout XML file. May be a file path or URL.

    -noWait [<SwitchParameter>]
        Parámetro de interruptor para omitir el mensaje de instalación antes de que comience la instalación.
        Switch parameter to skip installation message before installation begins.

    -noGui [<SwitchParameter>]
        Parámetro de interruptor para omitir la GUI de personalización.
        Switch parameter to skip customization GUI.

    -noReboots [<SwitchParameter>]
        Parámetro de interruptor para prevenir reinicios (no recomendado).
        Switch parameter to prevent reboots (not recommended).

    -noChecks [<SwitchParameter>]
        Parámetro de interruptor para omitir verificaciones de validación (no recomendado).
        Switch parameter to skip validation checks (not recommended).
```

Obtener información completa de uso ejecutando / Get full usage information by running:
```powershell
Get-Help .\install.ps1 -Detailed
```

### GUI del Instalador / Installer GUI

La GUI del instalador se muestra después de ejecutar las verificaciones de validación e instalar Boxstarter y Chocolatey (si no están instalados ya).

Usando la GUI del instalador puedes personalizar:
- Selección de paquetes de FLARE-VM y la comunidad de Chocolatey
- Rutas de variables de entorno

![Installer GUI](https://raw.githubusercontent.com/mandiant/flare-vm/main/Images/installer-gui.png)

### Configuración / Configuration

El instalador descargará [`config.xml`](https://raw.githubusercontent.com/mandiant/flare-vm/main/config.xml) del repositorio de FLARE-VM. Este archivo contiene la configuración predeterminada, incluida la lista de paquetes a instalar y las rutas de variables de entorno. Puedes usar tu propia configuración especificando el argumento CLI `-customConfig` y proporcionando una ruta de archivo local o URL a tu archivo `config.xml`.

### Pasos Post-instalación / Post Installation Steps

Puedes incluir cualquier paso de post-instalación que desees en la configuración dentro de las etiquetas `apps`, `services`, `path-items`, `registry-items` y `custom-items`.

Por ejemplo, para mostrar extensiones de archivo conocidas:
```xml
<registry-items>
    <registry-item name="Show known file extensions" path="HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced" value="HideFileExt" type="DWord" data="0"/>
</registry-items>
```

Para más ejemplos, consulta el archivo de configuración predeterminado: [`config.xml`](https://raw.githubusercontent.com/mandiant/flare-vm/main/config.xml).

## 🐞 Solución de Problemas / Troubleshooting

Si tu instalación falla, intenta identificar la razón del error leyendo los archivos de registro listados a continuación:

- `%VM_COMMON_DIR%\log.txt`
- `%PROGRAMDATA%\chocolatey\logs\chocolatey.log`
- `%LOCALAPPDATA%\Boxstarter\boxstarter.log`

Asegúrate de estar ejecutando la última versión del instalador de FLARE-VM y que tu VM cumpla con los [requisitos](#-requisitos--requirements).

### Error del Instalador / Installer Error

Si la instalación falló debido a un problema en el script de instalación (ej. `install.ps1`), [reporta el bug en FLARE-VM](https://github.com/mandiant/flare-vm/issues/new?labels=%3Abug%3A+bug&template=bug.yml).

> **Nota:** Rara vez `install.ps1` es la razón de un fallo de instalación. Lo más probable es que sea un paquete específico o conjunto de paquetes que están fallando.

### Error de Paquete / Package Error

Los paquetes fallan al instalarse de vez en cuando -- esto es normal. Las razones más comunes son:

1. Fallo o tiempo de espera de Chocolatey o MyGet para descargar un archivo `.nupkg`
2. Fallo o tiempo de espera debido al host remoto al descargar una herramienta
3. Sistema de Detección de Intrusiones (IDS) o producto AV (ej. Windows Defender) previene una descarga de herramienta o elimina la herramienta del sistema
4. Problema específico del host, por ejemplo al usar una versión no probada
5. La herramienta falla al construirse debido a dependencias
6. URL de herramienta antigua (ej. `HTTP STATUS 404`)
7. El hash SHA256 de la herramienta ha cambiado de lo que está codificado en el script de instalación del paquete

Razones **1-4** son difíciles de arreglar ya que no las controlamos. Si se presenta un problema relacionado con las razones **1-4**, es poco probable que podamos ayudar.

¡Podemos ayudar con las razones **5-7** y damos la bienvenida a la comunidad para contribuir con correcciones también!

Por favor [reporta el bug en VM-Packages](https://github.com/mandiant/VM-Packages/issues/new?labels=%3Abug%3A+bug&template=bug.yml) proporcionando toda la información solicitada.

### Actualizaciones / Updates

Ten en cuenta que las actualizaciones de paquetes se realizan con el mejor esfuerzo y que las actualizaciones no se están probando. Si encuentras errores, realiza una instalación nueva de FLARE-VM.

Note that package updates are best effort and that updates are not being tested. If you encounter errors, perform a fresh FLARE-VM install.

---

## 💰 Donaciones / Donations

Si este proyecto te ha sido útil y quieres apoyar su desarrollo y mantenimiento, puedes hacer una donación en Bitcoin:

**If this project has been useful and you want to support its development and maintenance, you can make a Bitcoin donation:**

### Bitcoin (BTC)
```
bc1qxy2kgdygjrsqtzq2n0yrf2493p83kkfjhx0wlh
```

¡Cualquier contribución es muy apreciada y ayuda a mantener este proyecto actualizado! 🙏

**Any contribution is greatly appreciated and helps keep this project updated!** 🙏

---

## 👥 Autoría y Créditos / Authorship and Credits

### 🏆 Autores Originales / Original Authors

**Mandiant FLARE Team**
- Repositorio original / Original repository: [mandiant/flare-vm](https://github.com/mandiant/flare-vm)
- Creadores del proyecto FLARE-VM / Creators of the FLARE-VM project
- Licencia / License: Apache 2.0

### ✨ Versión Mejorada y Documentación en Español / Improved Version and Spanish Documentation

**Gustavo Lobato Clara (Murdok)** - [murdok1982](https://github.com/murdok1982)

**Mejoras realizadas en esta versión / Improvements made in this version:**
- ✅ Traducción completa de la documentación al español
- ✅ Mejora en la estructura y formato del README
- ✅ Añadida sección de donaciones Bitcoin
- ✅ Documentación bilingüe (Español/Inglés) mejorada
- ✅ Mejores advertencias de seguridad y notas importantes
- ✅ Mejor organización de las secciones
- ✅ Ejemplos de comandos más claros

**Contacto / Contact:**
- 📧 Email: gustavolobatoclara@gmail.com
- 💼 LinkedIn: [Gustavo Lobato Clara](https://www.linkedin.com/in/gustavo-lobato-clara1982/)
- 🌍 Ubicación / Location: Valencia, España
- 💻 *Apasionado por la ciber-seguridad y PYTHON!!!*

---

## 🤝 Contribuir / Contributing

Consulta la [guía CONTRIBUTING](https://github.com/mandiant/flare-vm/blob/main/CONTRIBUTING.md) del proyecto original para aprender cómo contribuir.

Check the [CONTRIBUTING guide](https://github.com/mandiant/flare-vm/blob/main/CONTRIBUTING.md) from the original project to learn how to contribute.

---

## 📧 Lista de Correo / Mailing List

¡Suscríbete a la lista de correo de FLARE para anuncios de la comunidad! Envía un email con "subscribe" a [flare-external@google.com](mailto:flare-external@google.com?subject=subscribe).

Subscribe to the FLARE mailing list for community announcements! Email "subscribe" to [flare-external@google.com](mailto:flare-external@google.com?subject=subscribe).

---

## ⚠️ Aviso Legal / Legal Notice

**ES:** Este script de configuración de descarga se proporciona para ayudar a los analistas de ciberseguridad a crear cajas de herramientas prácticas y versátiles para entornos de análisis de malware. Proporciona una interfaz conveniente para que obtengan un conjunto útil de herramientas de análisis directamente de sus fuentes originales. La instalación y el uso de este script están sujetos a la Licencia Apache 2.0. Como usuario de este script, debes revisar, aceptar y cumplir con los términos de licencia de cada paquete descargado/instalado. Al proceder con la instalación, estás aceptando los términos de licencia de cada paquete y reconociendo que tu uso de cada paquete estará sujeto a sus respectivos términos de licencia.

**EN:** This download configuration script is provided to assist cyber security analysts in creating handy and versatile toolboxes for malware analysis environments. It provides a convenient interface for them to obtain a useful set of analysis tools directly from their original sources. Installation and use of this script is subject to the Apache 2.0 License. You as a user of this script must review, accept and comply with the license terms of each downloaded/installed package. By proceeding with the installation, you are accepting the license terms of each package, and acknowledging that your use of each package will be subject to its respective license terms.

---

## 📝 Licencia / License

Este proyecto está licenciado bajo la Licencia Apache 2.0 - consulta el archivo [LICENSE](https://github.com/mandiant/flare-vm/blob/main/LICENSE.txt) para más detalles.

This project is licensed under the Apache 2.0 License - see the [LICENSE](https://github.com/mandiant/flare-vm/blob/main/LICENSE.txt) file for details.

---

**🚀 ¡Feliz Ingeniería Inversa! / Happy Reverse Engineering!**
---

## 🎖️ CENTRO DE COMUNICACIONES Y REPORTES OFICIALES
**NIVEL DE ACCESO:** AUTORIZADO | **DESTINATARIO:** COMANDANCIA DE DESARROLLO (gustavolobatoclara@gmail.com)

A través del siguiente portal de comunicaciones, el personal autorizado puede emitir reportes de incidencias, fallas críticas en despliegue (compilación) o solicitudes de mejoras estratégicas. Seleccione la directiva correspondiente para visualizar los protocolos de envío:

<details>
<summary><b>🚨 REPORTAR QUEJA O INCIDENCIA DISCIPLINARIA / OPERATIVA</b></summary>
<br>
Para tramitar una queja sobre el funcionamiento, estructura o contenido del sistema, envíe un mensaje a <b>gustavolobatoclara@gmail.com</b> siguiendo este protocolo:
<ol>
  <li><b>Asunto:</b> [QUEJA] - Nombre del Sistema - Breve descripción.</li>
  <li><b>Cuerpo del mensaje:</b> Detallar claramente la incidencia, impacto operativo y, si es posible, la evidencia (capturas o logs).</li>
  <li><b>Prioridad:</b> Indicar si es de atención inmediata o diferida.</li>
</ol>
</details>

<details>
<summary><b>🛠️ REPORTE DE PROBLEMAS DE COMPILACIÓN O DESPLIEGUE</b></summary>
<br>
Si experimenta fallos durante la fase de compilación o instalación del sistema, reporte a <b>gustavolobatoclara@gmail.com</b> con la siguiente estructura técnica:
<ol>
  <li><b>Asunto:</b> [COMPILACIÓN] - Falla en entorno &lt;Entorno/OS&gt;.</li>
  <li><b>Especificaciones:</b> Sistema Operativo, versión de dependencias y herramientas de compilación utilizadas.</li>
  <li><b>Traza de Error (Logs):</b> Adjunte el log completo de errores proporcionado por la terminal (en formato texto o captura legible).</li>
  <li><b>Pasos de Reproducción:</b> Secuencia exacta de comandos ejecutados antes del fallo crítico.</li>
</ol>
</details>

<details>
<summary><b>💡 SUGERENCIAS O SOLICITUDES DE DESARROLLO</b></summary>
<br>
Para proponer nuevas capacidades tácticas, módulos de inteligencia o mejoras de arquitectura, envíe su solicitud a <b>gustavolobatoclara@gmail.com</b>:
<ol>
  <li><b>Asunto:</b> [PROPUESTA] - Mejora o Nuevo Módulo.</li>
  <li><b>Objetivo Táctico:</b> ¿Qué problema resuelve o qué ventaja proporciona esta nueva característica?</li>
  <li><b>Viabilidad:</b> (Opcional) Posible enfoque técnico o herramientas recomendadas para su implementación.</li>
</ol>
</details>

---
