# Inventario y clasificación de vulnerabilidades del contenedor · CenfoPay (semana 2)

## 1. Inventario (SBOM)
- Archivo: `reportes/sbom-cyclonedx.json`   · Componentes totales: 512
- Imagen base: ________________  · Digest: `sha256:________________`
- Sistema operativo y versión: __________  · Paquetes del SO: ____  · Librerías Python: ____

## 2. Clasificación (antes / después del endurecimiento)
| Origen | Severidad | Antes | Después | Con corrección | Sin corrección | En CISA KEV |
|--------|-----------|------:|--------:|---------------:|---------------:|------------:|
| SO (trivy-so) | CRITICAL | 235 | | | | |
| SO (trivy-so) | HIGH | 2599 | | | | |
| Dependencias (trivy-dependencias) | CRITICAL | 1 | | | | |
| Dependencias (trivy-dependencias) | HIGH | 16 | | | | |
| Configuración (trivy-config / hadolint) | ALTA | | | | | |

## 3. Top 5 para priorizar
| CVE | Paquete | CVSS | EPSS | ¿KEV? | ¿Se usa la función vulnerable? | Prioridad |
|-----|---------|------|------|-------|--------------------------------|-----------|
| CVE-2026-109518 | apt 3.0.4 | N/A | 1.15% | No | Si | Alta |
| CVE-2026-53612 | bsdutils 2.41.5-0+deb13u1 | 7.0 | 0.15% | No | Si | Alta |
| CVE-2026-12064 | curl | 7.5 | 0.41% | Sí | No | Urgente |
| CVE-2025-68973 | dirmngr 2.4.7-21+deb13u1 | 7.8 | 0.15% | Sí | Si | Urgente |
| CVE-2026-5201 | gir1.2-gdkpixbuf-2.0 2.42.12+dfsg-4+deb13u1 | 7.5 | 1.17% | Sí | Si | Urgente |

## 4. Selección de la imagen base
| Criterio | Opción A | Opción B | Opción C |
|----------|----------|----------|----------|
| Imagen | python:3.9 (actual) | python:3.9-slim-bookworm | python:3.9-alpine |
| Mantenida / con soporte | No, Python 3.9 sin soporte | No, Python 3.9 sin soporte | No, Python 3.9 sin soporte |
| Tamaño (MB) | Aproximadamente 1000 MB | Aproximadamente 150 MB | Pendiente de medir |
| Vulnerabilidades C/H | Pendiente: reporte Trivy antes | Pendiente: reporte Trivy después | Pendiente de escanear |
| Tiene shell / gestor de paquetes | Sí, shell y apt | Sí, shell y apt | Sí, shell y apk |
| Origen verificado (oficial, firmado) | Imagen oficial de Python; firma no verificada | Imagen oficial de Python; digest fijado en el proyecto | Imagen oficial de Python; firma no verificada |
| Compatibilidad con la app | Sí, utilizada originalmente | Sí, contemplada en la guía | No comprobada; posible incompatibilidad de librerías |
| **Decisión y justificación** | No se recomienda mantenerla por su tamaño y componentes adicionales. | **Seleccionada:** reduce el tamaño y la superficie de ataque, manteniendo compatibilidad. | No seleccionada porque requiere validar las dependencias. |

## 5. Verificación post-endurecimiento
Pega aquí la salida de `bash scripts/verificar-endurecimiento.sh cenfopay:local cenfopay-app-1`.

[i] Imagen: cenfopay:local
✅ END-01 La imagen define un usuario no root (USER)       USER=10001:10001
✅ END-02 Sin secretos en variables ENV de la imagen       ninguna variable sensible
✅ END-03 HEALTHCHECK definido                             presente
❌ END-04 Tamaño de la imagen <= 250 MB                   312 MB (superficie de ataque mayor)
❌ END-05 Sin herramientas innecesarias en runtime         encontradas: apt-get __sin_shell__
[i] Contenedor en ejecución: cenfopay-app-1
✅ END-06 Sistema de archivos raíz de solo lectura        read_only: true
✅ END-07 Capacidades Linux eliminadas (cap_drop ALL)      [ALL]
✅ END-08 no-new-privileges activo                         activo
✅ END-09 Contenedor no privilegiado                       privileged=false
✅ END-10 Puertos publicados solo en 127.0.0.1             127.0.0.1:8000 
✅ END-11 El proceso corre con UID distinto de 0           uid=10001
✅ END-12 Límite de memoria definido                      256 MB
[✓] Resultados en reportes/endurecimiento.json