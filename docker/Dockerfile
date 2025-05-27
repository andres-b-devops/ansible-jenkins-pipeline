# Usar la imagen base de Jenkins
FROM jenkins/inbound-agent:3309.v27b_9314fd1a_4-3

# Cambiar a usuario root para instalar paquetes
USER root

# Actualizar paquetes e instalar dependencias
RUN apt-get update && \
    apt-get install -y \
    python3 \
    python3-pip \
    python3-venv \
    python3-full \
    openssh-client \
    git \
    curl \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Opción 1: Instalar Ansible usando --break-system-packages (recomendado para contenedores)
RUN pip3 install --no-cache-dir --break-system-packages \
    ansible \
    ansible-core \
    paramiko

# Opción 2 (alternativa): Instalar desde apt si prefieres
# RUN apt-get update && \
#     apt-get install -y ansible && \
#     apt-get clean && \
#     rm -rf /var/lib/apt/lists/*

# Verificar instalación de Ansible
RUN ansible --version && ansible-playbook --version

# Volver al usuario jenkins
USER jenkins

# Verificar que el usuario jenkins puede usar ansible
RUN ansible --version