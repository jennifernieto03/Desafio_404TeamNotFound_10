# Solución de Almacenamiento

## 1. Definición del Problema
En InnovaCloud Solutions, los recientes fallos de disco en el servidor principal han ocasionado pérdida de datos crítica. Para una empresa cuyo núcleo de negocio son las soluciones tecnológicas y el almacenamiento, la integridad y disponibilidad de la información es innegociable. La pérdida de datos por falta de redundancia es crítica para la continuidad del negocio porque:
* **Interrupción Operativa:** Los servicios se detienen abruptamente, afectando la operatividad interna y los servicios brindados a los clientes.
* **Incumplimiento de SLAs (Acuerdos de Nivel de Servicio):** La caída de los servidores puede conllevar penalizaciones legales y financieras severas.
* **Daño a la Reputación:** El activo más importante de un proveedor tecnológico es la confianza. Una pérdida de datos daña irreparablemente la reputación de la empresa en el mercado.

## 2. Propuesta de Solución: Implementación de RAID 10
Para mitigar de forma definitiva este riesgo, nuestra firma propone la implementación de un arreglo **RAID 10** (conocido también como RAID 1+0).

### ¿Por qué elegimos RAID 10?
RAID 10 combina la creación de espejos (RAID 1 para redundancia) con la creación de bandas (RAID 0 para rendimiento). 
* **Alta Redundancia (Tolerancia a fallos):** Al contar con espejos, el arreglo puede soportar el fallo de hasta un disco en cada subgrupo sin perder datos. Esto soluciona directamente la vulnerabilidad del servidor principal.
* **Rendimiento Óptimo:** A diferencia de RAID 5, que sufre una penalización de rendimiento al calcular paridades durante las escrituras, RAID 10 ofrece velocidades de lectura y escritura significativamente superiores. Esto es ideal para bases de datos y servidores de alto tráfico que exigen tiempos de respuesta rápidos.

## 3. Guía de Implementación y Comandos (`mdadm`)
A continuación, se detalla la configuración técnica utilizando la herramienta de administración de software RAID `mdadm` en el entorno Linux de la empresa.

**Paso 1: Instalación de la utilidad mdadm**
\`\`\`bash
sudo apt update
sudo apt install mdadm -y
\`\`\`

**Paso 2: Creación del arreglo RAID 10**
*(Nota: Este comando asume el uso de 4 discos del mismo tamaño identificados en el sistema como /dev/sdb, /dev/sdc, /dev/sdd y /dev/sde)*
\`\`\`bash
sudo mdadm --create --verbose /dev/md0 --level=10 --raid-devices=4 /dev/sdb /dev/sdc /dev/sdd /dev/sde
\`\`\`

**Paso 3: Verificación de la creación y estado del RAID**
\`\`\`bash
cat /proc/mdstat
sudo mdadm --detail /dev/md0
\`\`\`

**Paso 4: Formateo con sistema de archivos ext4**
\`\`\`bash
sudo mkfs.ext4 /dev/md0
\`\`\`

**Paso 5: Montaje del volumen en el sistema**
\`\`\`bash
sudo mkdir -p /mnt/innovacloud_data
sudo mount /dev/md0 /mnt/innovacloud_data
\`\`\`

**Paso 6: Configuración de persistencia (Automontaje tras reinicio)**
Para asegurar que el sistema ensamble el RAID automáticamente cada vez que el servidor se reinicie:
\`\`\`bash
# Guardar la configuración en mdadm.conf
sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf

# Actualizar el initramfs
sudo update-initramfs -u

# Agregar la entrada al archivo fstab
echo '/dev/md0 /mnt/innovacloud_data ext4 defaults 0 0' | sudo tee -a /etc/fstab
\`\`\`
