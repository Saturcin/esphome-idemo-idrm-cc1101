# Gateway ESPHome para Idemo IDRM (ESP32 + CC1101)

Proyecto comunitario para controlar **motores de persiana Idemo IDRM de 433,92 MHz** desde **Home Assistant** usando **ESPHome, un ESP32 y un CC1101**.

> **Estado:** experimental / probado por la comunidad.  
> **Equipo usado durante el desarrollo:** motores tubulares Idemo BLU45 IDRM, mando IDRM de 6 canales, ESP32 y CC1101.  
> Proyecto **no oficial** y sin afiliación con Idemo Motors, Home Assistant ni ESPHome.

## Qué hace

El ESP32 + CC1101 actúa como emisor RF IDRM y publica seis entidades `cover` en Home Assistant.

Incluye:

- 6 canales independientes.
- Subir, bajar, parar y posicionamiento porcentual.
- API nativa ESPHome/Home Assistant.
- CC1101 a 433,92 MHz ASK/OOK.
- Tiempos de subida y bajada editables desde Home Assistant.
- Curvas de calibración por sentido editables desde Home Assistant.
- Posicionamiento matemático directo, sin ir primero a un final de carrera.
- Tratamiento especial del STOP/RELEASE del canal 6 descubierto experimentalmente.
- Sin sniffer RF en la configuración normal.

## Hardware

- ESP32.
- CC1101 para 433 MHz.
- Antena adecuada.
- Motor(es) Idemo IDRM.

### Cableado

| CC1101 | ESP32 |
|---|---|
| VCC | 3V3 |
| GND | GND |
| SCK | GPIO18 |
| MOSI | GPIO23 |
| MISO | GPIO19 |
| CSN / CS | GPIO5 |
| GDO0 | GPIO32 |
| GDO2 | Sin uso |

**No alimentar nunca el CC1101 con 5 V.**

Más información: [docs/WIRING.md](docs/WIRING.md).

## Instalación rápida

1. Copia `esphome/idemo-idrm-gateway.yaml` a ESPHome.
2. Configura en `secrets.yaml`:
   ```yaml
   wifi_ssid: "TU_WIFI"
   wifi_password: "TU_CLAVE"
   ```
3. Revisa el ID del emisor, canales, tiempos y curvas.
4. Ejecuta **Validate** en ESPHome.
5. Instala el firmware.
6. Añade el dispositivo ESPHome a Home Assistant.
7. Empareja el emisor con los motores si fuese necesario.
8. Lleva cada persiana una vez completamente arriba o abajo.
9. Ajusta tiempos y curvas desde Home Assistant.

## ID de emisor

El YAML actual utiliza durante el desarrollo:

```text
46 84 5D 9C
```

No debe suponerse que ese ID sea universal. Está documentado en [docs/PAIRING_AND_ID.md](docs/PAIRING_AND_ID.md).

## Canales

| Canal | Bytes IDRM |
|---:|---|
| 1 | `02 00` |
| 2 | `04 00` |
| 3 | `08 00` |
| 4 | `10 00` |
| 5 | `20 00` |
| 6 | `40 00` |

## Tiempos por defecto

| Canal | Subida | Bajada |
|---:|---:|---:|
| 1 | 17 s | 17 s |
| 2 | 19,5 s | 19,5 s |
| 3 | 16 s | 16 s |
| 4 | 25 s | 25 s |
| 5 | 14 s | 14 s |
| 6 | 26 s | 26 s |

Son valores de esta instalación y pueden modificarse desde Home Assistant.

## Curvas por defecto

Subida:

```text
25:25|50:70|70:90|80:100
```

Bajada:

```text
25:3|50:7|75:45|85:60|90:75
```

Formato:

```text
conceptual:físico|conceptual:físico|...
```

La conversión se hace mediante interpolación lineal por tramos y su función inversa.

Consulta [docs/CALIBRATION.md](docs/CALIBRATION.md).

## Limitación principal

El sistema no recibe la posición física real del motor. La posición es una estimación basada en tiempo y curvas. Si utilizas el mando RF original, Home Assistant puede perder la referencia. Un recorrido completo hasta 0 % o 100 % permite volver a establecer un extremo conocido.

Consulta [docs/LIMITATIONS.md](docs/LIMITATIONS.md).

## Ingeniería inversa del protocolo

Los detalles RF incluidos en este repositorio proceden de capturas y pruebas físicas. Se documentan por separado los hechos observados y las inferencias.

Consulta [docs/PROTOCOL.md](docs/PROTOCOL.md).

## Contribuciones

Son especialmente útiles:

- capturas RF de otros mandos IDRM,
- pruebas con otros motores Idemo,
- mejoras en la calibración,
- validación de canales/comandos,
- correcciones del YAML,
- mejoras de documentación.

Consulta [CONTRIBUTING.md](CONTRIBUTING.md).

## Licencia

MIT. Consulta [LICENSE](LICENSE).

## Aviso

Proyecto independiente y no oficial. Úsalo bajo tu responsabilidad. El ESP32/CC1101 trabaja a baja tensión, pero la instalación del motor de persiana puede trabajar a tensión de red y debe manipularse únicamente con los conocimientos y medidas de seguridad adecuados.
