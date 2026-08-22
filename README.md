# Desafio_404TeamNotFound_10


> **Firma Consultora:** 404TeamNotFound  
> **Cliente Contratante:** InnovaCloud Solutions.  
> **Materia:** Admin. e Impl. Serv. de Red con Sist. Operativos Libres (ASI104) - G01T  
> **Institución:** Universidad Don Bosco — Facultad de Ingeniería  
> **Docente:** Mtra. Ingrid Rubenia Vela Recinos  

---

## 1. Sobre Nuestra Firma Consultora

Somos **404TeamNotFound**, una firma de consultoría en ingeniería especializada en el diseño, optimización y administración de infraestructuras basadas en **Sistemas Operativos Libres (GNU/Linux)**.

En este repositorio presentamos la propuesta técnica integral para diagnosticar y solucionar las fallas críticas de almacenamiento, gestión de software, conectividad de red y auditoría de servicios en **InnovaCloud Solutions**

---

## 2. Integrantes del Equipo y Roles

| Integrante | Rol en la Consultoría | Sección / Archivo Asignado |
| :--- | :--- | :--- |
| **a** | Líder de Proyecto / Arq. de Redes | `networking.md` |
| **[Nombre Integrante 2]** | Especialista en Almacenamiento | `storage.md` |
| **Valeria Melissa Hernández Duarte** | Analista de Gestión de Software | `packages.md`|
| **[Nombre Integrante 4]** | Auditor/a de Diagnóstico y Sistemas | `diagnostics.md` |

---

## 3. Arquitectura General de la Solución

```text
               ┌──────────────────────────────────────────────┐
               │         InnovaCloud Solutions Infra          │
               └──────────────────────┬───────────────────────┘
                                      │
     ┌──────────────────┬─────────────┴────────────┬──────────────────┐
     ▼                  ▼                          ▼                  ▼
 [ Storage ]       [ Packages ]               [ Networking ]       [ Diagnostics ]
 RAID 5 (mdadm)   Mirror Local (apt)         Netplan + Bridged     ss / ip / ping
 Redundancia      Homologación LAN           IP Estática / LAN    Auditoría Activa
```

1. **Almacenamiento (`storage.md`):** Implementación de **RAID 5** con `mdadm` para garantizar redundancia de datos ante fallos de disco en los servidores principales[cite: 1].
2. **Gestión de Software (`packages.md`):** Configuración de un **Repositorio Espejo (Mirror) Local** con `apt` para erradicar la instalación manual y optimizar el ancho de banda WAN[cite: 1].
3. **Configuración de Red (`networking.md`):** Migración del modo NAT a **Adaptador Puente (Bridged)** en VirtualBox y asignación de IP estática con **Netplan** para la red de desarrollo[cite: 1].
4. **Diagnóstico y Control (`diagnostics.md`):** Protocolo estandarizado de auditoría con herramientas nativas de consola (`ip`, `ss`, `ping`, `systemctl`) para monitoreo continuo[cite: 1].

---

## 4. Módulos de Documentación Técnica

Haz clic en cada enlace para revisar la solución detallada redactada por cada especialista[cite: 1]:

* [**Solución de Almacenamiento y Redundancia** (`storage.md`)](./storage.md)[cite: 1]
* [**Gestión de Software y Repositorio Espejo** (`packages.md`)](./packages.md)[cite: 1]
* [**Configuración de Red y Netplan** (`networking.md`)](./networking.md)[cite: 1]
* [**Verificación y Diagnóstico de Servicios** (`diagnostics.md`)](./diagnostics.md)[cite: 1]

---

## 5. Enlaces de Entrega y Evidencias

> **Defensa Grabada:** Demostración en video mostrando la estructura del repositorio en GitHub y la explicación de las soluciones técnicas acordadas[cite: 1].

* **Repositorio Oficial en GitHub:** `https://github.com/tu-usuario/Desafio_NombreEquipo_01`[cite: 1]
* **Video de Defensa (Loom / Screenity):** `[Inserta aquí el enlace de tu video]`[cite: 1]
