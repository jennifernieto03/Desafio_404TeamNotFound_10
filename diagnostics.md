# Auditoría, Verificación y Diagnóstico de Red y Servicios
# Verificación y Diagnóstico de Red: InnovaCloud Solutions

### 1. Objetivo

Establecer un procedimiento estandarizado para la verificación, auditoría y diagnóstico de la infraestructura de red de **InnovaCloud Solutions**, permitiendo identificar de manera rápida y ordenada problemas relacionados con interfaces de red, direccionamiento IP, conectividad, rutas, resolución DNS, puertos abiertos y servicios activos.

La estandarización del diagnóstico permite que el personal técnico pueda seguir una metodología uniforme, reducir el tiempo de resolución de incidentes y generar evidencia técnica de las condiciones encontradas en los servidores Linux.

---

## 2. Problema identificado

Actualmente, InnovaCloud Solutions no cuenta con un procedimiento estandarizado para verificar los servicios activos ni diagnosticar la conectividad de sus servidores.

Esta situación dificulta la identificación de fallos porque cada administrador podría utilizar diferentes herramientas o realizar comprobaciones en distinto orden. Como consecuencia, un problema aparentemente sencillo, como una interfaz deshabilitada, una ruta incorrecta o un servicio detenido, puede requerir más tiempo para ser localizado.

Además, la ausencia de un procedimiento formal dificulta la documentación de los incidentes y la comparación del estado de los servidores antes y después de aplicar una solución.

---

# 3. Propuesta de solución

Se propone utilizar un procedimiento de diagnóstico dividido en diferentes niveles, comenzando por las comprobaciones más básicas y avanzando hacia verificaciones más específicas.

El orden recomendado es:

```text
1. Verificar interfaces
          ↓
2. Verificar direccionamiento IP
          ↓
3. Verificar tabla de rutas
          ↓
4. Comprobar conectividad local
          ↓
5. Comprobar conectividad extremo a extremo
          ↓
6. Verificar resolución DNS
          ↓
7. Identificar puertos abiertos
          ↓
8. Auditar servicios activos
          ↓
9. Analizar resultados
          ↓
10. Documentar el diagnóstico
```

Este orden permite determinar primero si existe conectividad básica antes de investigar problemas relacionados con servicios específicos.


---

# 4. Herramientas de diagnóstico

| Herramienta | Propósito |
|---|---|
| `ip` | Verificar interfaces, direcciones IP y rutas |
| `ping` | Comprobar conectividad mediante ICMP |
| `ss` | Identificar puertos y conexiones activas |
| `systemctl` | Auditar servicios administrados por systemd |
| `nmap` | Identificar puertos y servicios accesibles |
| `dig` / `nslookup` | Verificar resolución DNS |
| `traceroute` | Analizar el recorrido hacia un destino |

---


# 5. Procedimiento estandarizado

## 5.1 Identificación de las interfaces de red

El primer paso consiste en comprobar qué interfaces existen en el servidor y cuál se encuentra activa.

```bash
ip link
```

Para consultar las interfaces junto con sus direcciones IP:

```bash
ip addr
```

También puede utilizarse:

```bash
ip -br addr
```

La última opción presenta la información de forma resumida, facilitando la identificación de la interfaz y su dirección IP.

### ¿Qué se debe verificar?

Se debe comprobar:

- Que la interfaz esperada exista.
- Que se encuentre en estado `UP`.
- Que tenga una dirección IP asignada.
- Que la dirección corresponda a la red configurada para el servidor.

---

# 6. Verificación del direccionamiento IP

Una vez identificada la interfaz, se debe comprobar que la dirección IP configurada sea correcta.

```bash
ip addr show
```

Para consultar específicamente una interfaz:

```bash
ip addr show enp0s3
```

> **Nota:** `enp0s3` es un ejemplo. Debe sustituirse por el nombre real de la interfaz del servidor.

La información obtenida permite verificar la dirección IP, máscara de red y estado de la interfaz.

---

# 7. Verificación de la tabla de rutas

Después de comprobar la dirección IP, se debe verificar que el servidor conozca la ruta hacia otras redes.

```bash
ip route
```

Si no existe una ruta predeterminada cuando debería existir, el servidor podría tener conectividad con su red local pero no con redes externas.


---

# 8. Comprobación de conectividad

## 8.1 Prueba de conectividad con la puerta de enlace

El primer nivel de prueba consiste en comprobar comunicación con la puerta de enlace.

```bash
ping -c 4 ip (gateway)
```

Una respuesta satisfactoria indica que existe comunicación entre el servidor y la puerta de enlace.

---

## 8.2 Prueba de conectividad con otro servidor

Posteriormente se debe comprobar la comunicación con otro equipo de la infraestructura.

```bash
ping -c 4 <IP_SERVIDOR_DESTINO>
```

Esta prueba permite determinar si los servidores pueden comunicarse entre sí.

---

## 8.3 Prueba de conectividad externa

Si el servidor debe tener acceso a Internet, puede realizarse una prueba utilizando una dirección IP externa.
Si esta prueba funciona pero una prueba utilizando un nombre de dominio falla, puede existir un problema de resolución DNS.

---

# 9. Verificación de resolución DNS

La resolución DNS permite convertir nombres de dominio en direcciones IP.
Puede utilizarse:

```bash
nslookup example.com
```

o:

```bash
dig example.com
```

Si `dig` no está instalado, puede instalarse mediante:

```bash
sudo apt install dnsutils
```

Una resolución exitosa debe devolver información que permita identificar la dirección IP asociada al dominio consultado.

También puede comprobarse utilizando:

```bash
ping -c 4 example.com
```
Si no funciona, es posible que exista un problema de resolución DNS y no necesariamente un problema de conectividad IP.

---

# 10. Identificación de puertos abiertos

Los puertos permiten determinar qué servicios de red están disponibles en un servidor.

Una herramienta adecuada para realizar esta comprobación es `ss`.

```bash
sudo ss -tuln
```

Las opciones utilizadas significan:

- `-t`: conexiones TCP.
- `-u`: conexiones UDP.
- `-l`: sockets en estado de escucha.
- `-n`: muestra las direcciones y puertos numéricamente.

---

# 11. Auditoría mediante Nmap

Para realizar una comprobación desde otro equipo autorizado de la red, puede utilizarse `nmap`.

```bash
nmap <IP_SERVIDOR>
```

# 12. Auditoría de servicios activos

En sistemas Linux que utilizan `systemd`, `systemctl` permite consultar y administrar los servicios.

Para comprobar el estado de un servicio:

```bash
systemctl status ssh
```

Para comprobar si está habilitado para iniciar automáticamente:

```bash
systemctl is-enabled ssh
```

Para consultar los servicios que se encuentran activos:

```bash
systemctl list-units --type=service --state=running
```

Esto permite identificar qué servicios se están ejecutando actualmente en el servidor.

---

# 13. Relación entre servicios y puertos

Para realizar un diagnóstico correcto no basta con conocer los puertos abiertos.

Debe comprobarse la relación:

```text
Puerto abierto
      ↓
Servicio escuchando
      ↓
Servicio esperado
      ↓
Configuración correcta
```

---

# 14. Analizar resultados y documentar diágnostico



