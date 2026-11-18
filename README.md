# Ansible playbooks para mantenimiento de Apple Mac Os

### Instalar Ansible para Mac Os: 

Para instalar Ansible en macOS, existen varias opciones dependiendo de tus necesidades de desarrollo, 
control de versiones y aislamiento del sistema.

Las principales opciones de instalación son:

Homebrew (Recomendado): Es la forma más sencilla y rápida para la mayoría de los usuarios. Instala Ansible y gestiona sus dependencias automáticamente.

Pip (Gestor de paquetes de Python): Es el método recomendado oficialmente por el equipo de Ansible. Permite instalar la versión más reciente directamente desde los repositorios de Python.

Pipx: Ideal para instalar herramientas de línea de comandos de Python de forma aislada. Evita conflictos con las librerías del sistema operativo.

Entornos Virtuales de Python (venv): La mejor opción si trabajas en múltiples proyectos que requieren diferentes versiones de Ansible o colecciones específicas.

MacPorts: Una alternativa a Homebrew para usuarios que prefieren este gestor de paquetes clásico en macOS.

COMANDOS DE INSTALACIÓN PARA CADA MÉTODO

1. HOMEBREW

   Es la opción más rápida si ya usas Homebrew en tu Mac.

```bash
       brew install ansible
```

   2. PIP (PYTHON)
Instala Ansible globalmente en tu entorno de usuario de Python.

```bash
python3 -m pip install --user ansible
```
3. PIPX (AISLADO)
Recomendado si quieres evitar que Ansible interfiera con otras herramientas de Python en tu sistema.

```bash
brew install pipx
pipx ensurepath
pipx install ansible
```
4. ENTORNO VIRTUAL (PARA DESARROLLADORES)

    Perfecto si necesitas probar diferentes versiones de Ansible sin que afecten al resto del sistema.

```bash
python3 -m venv ansible-env
source ansible-env/bin/activate
pip install ansible
```
5. MACPORTS

   Si prefieres MacPorts sobre Homebrew, puedes usar:
```bash
sudo port install ansible
```

> Nota: Independientemente del método que elijas, puedes verificar que la instalación ha sido correcta ejecutando `ansible --version` en tu terminal.

🍎 macOS Intel (x86_64) App & Daemon Detector
Este playbook de Ansible está diseñado para usuarios de macOS con Apple Silicon (M1/M2/M3/M4). Su objetivo es detectar qué aplicaciones y procesos en segundo plano (daemons) aún dependen exclusivamente de la arquitectura Intel (x86_64) y requieren Rosetta 2 para funcionar.

🚀 ¿Por qué es útil?
Con el tiempo, Apple podría eliminar el soporte para Rosetta 2 en futuras versiones de macOS. Este playbook te ayuda a identificar qué software necesitas actualizar a versiones nativas (Universal o ARM64) antes de que dejen de funcionar.

📋 ¿Qué hace exactamente?
Busca aplicaciones: Escanea los directorios /Applications y ~/Applications.
Verifica la arquitectura: Extrae el binario principal de cada app y comprueba si es x86_64 puro.
Filtra binarios universales: Ignora las aplicaciones que ya soportan Apple Silicon (arm64).
Detecta procesos activos: Utiliza un script de Python integrado para analizar los programas que se están ejecutando en ese momento y que solo son para Intel.

🛠️ Requisitos
macOS (Apple Silicon)
Ansible instalado
Python 3
Uso
Ejecuta el playbook localmente con el siguiente comando:

```bash
ansible-playbook intelapps.yaml
```

⚙️ DETALLES TÉCNICOS DEL PLAYBOOK
El playbook utiliza una combinación de módulos nativos de Ansible y scripts en shell/python para lograr su objetivo sin necesidad de instalar dependencias externas complejas.

TAREA

DESCRIPCIÓN

Pre-tasks

Obtiene dinámicamente el usuario actual y su directorio $HOME para asegurar que las rutas sean correctas.

Búsqueda de Apps

Utiliza ansible.builtin.find para listar los paquetes .app sin entrar en recursividad profunda para mayor velocidad.

Filtrado

Usa filtros Jinja2 (selectattr, rejectattr) para limpiar la salida y mostrar solo las apps que son estrictamente Intel.

Detección de Daemons

Ejecuta un script de Python que analiza la salida de ps -A, resuelve las rutas reales de los binarios dentro de los paquetes .app y verifica su arquitectura.

📌 NOTAS
Se requiere become: yes (privilegios de administrador) para la última tarea, ya que necesita inspeccionar los procesos de todo el sistema.
Es normal que aparezcan advertencias de Ansible sobre el uso de shell en lugar de módulos nativos, pero es necesario para interactuar con herramientas específicas de macOS como defaults y file.

