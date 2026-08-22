# Configuración de Red

## 1. Definición del Problema
En el entorno de desarrollo actual de InnovaCloud Solutions, las máquinas virtuales están configuradas utilizando el modo de red por defecto (NAT). En un ecosistema de desarrollo colaborativo, esta configuración presenta severas limitaciones:
* **Aislamiento de la red:** En modo NAT, la máquina virtual queda oculta detrás de la dirección IP del host físico. Aunque la VM tiene salida a Internet, los demás equipos y recursos corporativos de la red local no pueden iniciar conexiones directas hacia ella.
* **Bloqueo en la colaboración:** Los desarrolladores no pueden acceder fácilmente a los servicios web, bases de datos o APIs alojadas en las máquinas virtuales de sus compañeros.
* **Complejidad en el enrutamiento:** Se requeriría configurar el reenvío de puertos (Port Forwarding) para cada servicio individual, lo cual no es escalable ni eficiente para una infraestructura empresarial ágil.

## 2. Análisis de Modos de Red en VirtualBox
Para reestructurar la arquitectura de red de los entornos virtualizados, es fundamental comprender las opciones disponibles en la plataforma de virtualización:

* **NAT (Traducción de Direcciones de Red):** La VM usa una IP privada interna asignada por el hipervisor y comparte la IP del host para salir a la red externa. Excelente para navegación básica, pero aisla a la VM del exterior.
* **Red Interna (Internal Network):** Crea una red aislada por software donde solo las máquinas virtuales configuradas en la misma red interna pueden comunicarse entre sí. No tienen acceso al host físico ni a Internet. Útil para laboratorios cerrados de seguridad.
* **Adaptador Puente (Bridged Adapter):** Conecta la interfaz de red de la máquina virtual directamente a la red física del host. La VM recibe su propia dirección IP en la misma subred que los demás equipos físicos de la empresa.

## 3. Propuesta de Solución: Adaptador Puente y Direccionamiento Estático
Para garantizar una comunicación fluida, proponemos migrar todas las máquinas virtuales del entorno de desarrollo a **Adaptador Puente (Bridged Adapter)**. De esta manera, cada instancia actuará como un nodo independiente dentro de la red corporativa, permitiendo conectividad bidireccional total.

Adicionalmente, para asegurar que los servicios alojados en los entornos Linux basados en Ubuntu siempre sean localizables por el equipo, estandarizaremos la asignación de direcciones IP estáticas utilizando **Netplan**, el gestor de red predeterminado en estas distribuciones.

## 4. Guía de Configuración con Netplan
A continuación, se detalla el procedimiento técnico para establecer una IP estática en los servidores virtualizados.

**Paso 1: Identificar la interfaz de red**
Primero, debemos identificar el nombre lógico de la interfaz de red puenteada (comúnmente `enp0s3`, `eth0`, etc.).
\`\`\`bash
ip link show
\`\`\`

**Paso 2: Crear / Editar el archivo de configuración YAML**
Netplan utiliza archivos YAML ubicados en `/etc/netplan/`. Crearemos o editaremos el archivo de configuración principal (ej. `01-netcfg.yaml` o `50-cloud-init.yaml`).
\`\`\`bash
sudo nano /etc/netplan/01-netcfg.yaml
\`\`\`

**Paso 3: Estructura del archivo YAML**
Aplicaremos la siguiente configuración, reemplazando la interfaz y la subred según la topología de la empresa:
\`\`\`yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s3:
      dhcp4: no
      addresses:
        - 192.168.10.50/24
      routes:
        - to: default
          via: 192.168.10.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
\`\`\`
*(Atención: Es vital respetar la indentación estricta de YAML, utilizando espacios y no tabulaciones).*

**Paso 4: Probar la configuración**
Antes de aplicar los cambios permanentemente, verificaremos que no haya errores de sintaxis y que la configuración no desconecte la sesión actual:
\`\`\`bash
sudo netplan try
\`\`\`

**Paso 5: Aplicar los cambios**
Si la prueba es exitosa, aplicamos la configuración para que la IP estática se asigne a la interfaz:
\`\`\`bash
sudo netplan apply
\`\`\`

**Paso 6: Validar la nueva IP**
Finalmente, confirmamos que la dirección IP estática se ha configurado correctamente y comprobamos la salida a la red:
\`\`\`bash
ip a
ping -c 4 8.8.8.8
\`\`\`
