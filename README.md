# Comedero inteligente para gatos — PCB custom ESP32

![PCB soldada del prototipo](docs/img/pcb-soldada.jpg)

> **Nota:** sube la imagen del PCB soldado en la ruta `docs/img/pcb-soldada.jpg`. Si usas otra carpeta o nombre de archivo, cambia la ruta de la imagen en esta línea.

## Descripción general

Este repositorio contiene el diseño de una **PCB custom basada en ESP32-S3** para un prototipo de comedero inteligente para gatos. El objetivo del proyecto no es construir una tarjeta de desarrollo genérica, sino una placa funcional orientada a controlar actuadores, leer sensores y permitir el monitoreo del comportamiento de alimentación de una mascota.

La PCB integra alimentación, programación, conexiones para sensores y actuadores, y módulos externos necesarios para el sistema. El diseño está pensado como una base de prototipado: permite validar el hardware, probar la lógica de control, registrar eventos y preparar una arquitectura donde el firmware pueda incorporar alertas o modelos simples de interpretación de comportamiento.

## Objetivo del proyecto

Diseñar y fabricar una PCB compacta, funcional y soldable que sirva como núcleo electrónico de un comedero para gatos con:

- Control de suministro sólido mediante servomotores.
- Control de motobomba para suministro líquido o sistema auxiliar.
- Medición de distancia mediante sensores ultrasónicos.
- Medición de peso mediante celda de carga y módulo HX711.
- Identificación mediante NFC.
- Alimentación regulada para el ESP32 y periféricos.
- Conexión de desarrollo separada de la conexión pensada para uso final.
- Posibilidad de integrar lógica de alertas e IA embebida en el firmware.


## Objetivo del proyecto


![PCB soldada del prototipo](docs/img/pcb-soldada.jpg)

## Estado actual

El proyecto se encuentra en fase de **prototipado físico**. La PCB ya fue diseñada, fabricada, soldada y puesta en pruebas iniciales. Aún se deben validar ajustes eléctricos, mecánicos y de distribución de pines, especialmente en los elementos que dependen de integración con sensores, actuadores y carcasa.

Este repositorio documenta el diseño actual, pero no debe asumirse como una versión final de producto.

## Arquitectura general del sistema

El sistema se organiza alrededor de un microcontrolador ESP32-S3, que actúa como núcleo de control y procesamiento. La placa conecta módulos externos y componentes SMD para manejar lectura sensórica, accionamiento y alimentación.

```text
                 ┌────────────────────────┐
                 │        ESP32-S3         │
                 │  Control + firmware     │
                 └───────────┬────────────┘
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
 ┌──────▼──────┐      ┌──────▼──────┐      ┌──────▼──────┐
 │ Ultrasonido │      │ HX711 +     │      │ PN532 NFC   │
 │ HC-SR04 x2  │      │ celda 1 kg  │      │ + tags NFC  │
 └─────────────┘      └─────────────┘      └─────────────┘
        │                    │                    │
        └────────────────────┼────────────────────┘
                             │
                 ┌───────────▼───────────┐
                 │ Actuadores             │
                 │ Servos + motobomba     │
                 └───────────────────────┘
```

## Funcionalidades previstas

### 1. Control de actuadores

La PCB contempla conexiones para servomotores y para una motobomba controlada mediante MOSFET NMOS. La intención es separar claramente las cargas de potencia del núcleo lógico del ESP32, evitando exigir corriente desde pines o líneas que no están diseñadas para eso.

Los servomotores se plantean para mecanismos de apertura, cierre o dosificación. La motobomba se contempla como actuador auxiliar para suministro líquido o pruebas de dispensación controlada.

### 2. Medición de distancia

Se decidió usar **dos sensores ultrasónicos HC-SR04** en lugar de sensores infrarrojos de bajo costo. La decisión se tomó porque los ultrasónicos entregan una lectura continua de distancia, mientras que varios sensores infrarrojos comerciales económicos solo entregan una señal binaria ajustada por umbral.

En este prototipo, una lectura continua resulta más útil para interpretar niveles, presencia o variaciones en superficies irregulares. Además, el costo de los ultrasónicos era considerablemente menor frente a algunos sensores infrarrojos disponibles comercialmente.

### 3. Medición de peso

El sistema contempla una celda de carga de 1 kg conectada a un módulo HX711. Esta etapa permite medir cambios de peso asociados al alimento o al recipiente.

En este diseño, las conexiones entre la celda de carga y la tarjeta HX711 se consideran externas al esquemático principal de la PCB, por lo que deben revisarse directamente según el módulo y la celda utilizados.

### 4. Identificación NFC

El módulo PN532 y los tags NFC se contemplan para identificación. En el contexto del comedero, esto puede utilizarse para asociar una mascota, un recipiente, una tarjeta de configuración o un evento específico del sistema.

La implementación final depende del firmware y del flujo de uso definido para el prototipo.

### 5. Firmware e IA embebida

El hardware está pensado para recopilar datos de sensores y habilitar una capa de interpretación en firmware. La IA no toma decisiones críticas de control por sí sola; su función prevista es apoyar la clasificación de eventos y generar alertas.

Una posible clasificación de comportamiento puede incluir:

- Estado normal o sin alerta.
- Posible hambre posterior al suministro.
- Rechazo de comida.
- Consumo menor al esperado.
- Consumo mayor al esperado.
- Comportamiento atípico.

Las decisiones de seguridad, apertura, suministro adicional o activación de actuadores deben permanecer en lógica convencional validada.

## Diseño de PCB

### Enfoque

El diseño no busca replicar una DevKit comercial. La PCB fue concebida como una tarjeta específica para el comedero, con las conexiones necesarias para el proyecto y con separación entre uso final y desarrollo.

El diseño prioriza:

- Integración del ESP32-S3 como microcontrolador principal.
- Conexiones para módulos externos.
- Alimentación regulada a 3.3 V.
- Línea de 5 V para periféricos que lo requieran.
- Protección básica en líneas de alimentación y actuadores.
- Componentes SMD para reducir volumen y mejorar integración.
- Conexión de programación separada para facilitar pruebas.

### Conexión de programación

La placa incluye una conexión pensada para desarrollo/programación mediante módulo externo tipo Pololu USB-C. A diferencia de los headers de sensores y actuadores, este conector se mantiene como **macho**, porque es una conexión móvil de desarrollo.

Esta decisión permite conservar una separación entre la interfaz técnica de programación y las conexiones que usaría un usuario final.

### Headers y conectores

Para las footprints de headers se recomienda mantener la lógica definida en el prototipo:

- Headers de sensores y actuadores: **SMD y hembra**.
- Header de programación Pololu: **macho**, con footprint modificada y funcional.
- Verificar siempre el orden de pines de la huella contra el pinout real del módulo, sensor o actuador.

Este punto es crítico porque algunos módulos comerciales no respetan el mismo orden de pines entre fabricantes o versiones.

### Etiquetado de señales

Los labels del diseño se dejaron referenciando al **componente** y no necesariamente al GPIO final. Esta decisión facilita el ruteo y permite cambiar GPIOs durante la organización de pistas sin perder claridad funcional.

Por eso, antes de cerrar una nueva versión, debe revisarse la correspondencia final entre:

- Label del esquemático.
- GPIO asignado.
- Pin físico del ESP32.
- Conector o módulo externo.
- Definición usada en firmware.

## Fabricación de la PCB

La PCB fue fabricada mediante un proceso de prototipado con máquina de fibra. El flujo usado fue:

1. Definición de pistas sobre cobre mediante máquina de fibra.
2. Aplicación de máscara de soldadura sobre la placa.
3. Segunda pasada con la máquina de fibra para retirar la máscara únicamente sobre los pads.
4. Soldadura de componentes SMD y módulos de conexión.
5. Pruebas iniciales de alimentación y funcionamiento.

Este proceso permitió obtener una placa física soldable sin depender de un servicio industrial tradicional de fabricación de PCBs, aunque requiere especial cuidado en pads, continuidad, máscara y tolerancias de soldadura.

## Componentes principales

| Bloque | Componente | Función |
|---|---|---|
| Control | ESP32-S3-WROOM-1-N8R8 | Microcontrolador principal del sistema |
| Alimentación | AMS1117-3.3 | Regulación de 5 V a 3.3 V |
| Programación | Pololu USB-C | Conexión de alimentación/programación para desarrollo |
| Identificación | PN532 NFC | Lectura NFC/RFID |
| Identificación | Tags NTAG213 | Etiquetas NFC para identificación |
| Distancia | HC-SR04 x2 | Medición continua de distancia |
| Peso | HX711 | Conversión y amplificación para celda de carga |
| Peso | Celda de carga 1 kg | Medición de peso |
| Actuación | Servomotores | Apertura/cierre/dosificación |
| Actuación | Motobomba | Suministro líquido o función auxiliar |
| Potencia | AO3400 | Conmutación de motobomba |
| Protección | SS34 | Diodo Schottky para protección |
| Filtrado | Capacitores 100 nF, 10 uF, 470 uF | Estabilidad y desacople |
| Señalización | LEDs SMD | Indicadores de 5 V y 3.3 V |
| Control manual | Pulsadores SMD | Reset y modo programación |

## Lista de referencias de compra

Estos enlaces son referencias de los componentes utilizados o considerados durante el prototipo. Antes de comprar o fabricar una nueva versión, se debe verificar disponibilidad, encapsulado, dimensiones y compatibilidad con la footprint usada en KiCad.

| Componente | Referencia / enlace |
|---|---|
| PN532 NFC | https://www.mercadolibre.com.co/kit-modulo-leitor-rfid-nfc-pn532-com-tag-e-carto/p/MCO2039743778 |
| Tags NFC NTAG213 | https://www.sigmaelectronica.net/producto/ntag213-nfc/ |
| Condensador 470 uF 6.3 V SMD | https://www.sigmaelectronica.net/producto/ct2917-470uf6-3v/ |
| Sensor ultrasónico HC-SR04 | https://www.sigmaelectronica.net/producto/hc-sr04/ |
| Tarjeta HX711 | https://www.sigmaelectronica.net/producto/tarjeta-hx711/ |
| Celda de carga 1 kg | https://www.sigmaelectronica.net/producto/celda-1k/ |
| Diodo SS34 | https://www.sigmaelectronica.net/producto/ss34/ |
| MOSFET AO3400 | https://www.mercadolibre.com.co/mosfet-ao3400-a09t-sot23-smd-x-5-unidades/up/MCOU3179338761 |
| Resistencia SMD 220 Ω | https://www.sigmaelectronica.net/producto/r1206-220-ohm/ |
| Resistencia SMD 1 kΩ | https://www.sigmaelectronica.net/producto/r0805-1k/ |
| Resistencia SMD 1.96 kΩ | https://www.sigmaelectronica.net/producto/p1-96kect/ |

## Inventario resumido de soldables

La siguiente lista resume los elementos principales identificados para fabricación de varias unidades del prototipo. Las cantidades exactas deben revisarse contra el Excel de inventario y la versión actual del esquemático.

| Elemento | Valor / referencia | Encapsulado o formato | Cantidad por PCB |
|---|---:|---|---:|
| Capacitor | 0.1 uF | 0603 | 6 |
| Capacitor | 10 uF | 1206 | 3 |
| Capacitor | 470 uF | SMD | 3 |
| LED | Rojo | 0805 | 1 |
| LED | Verde | 0805 | 1 |
| Diodo | SS34 | SMC / DO-214AB | 1 |
| Header macho | Programación | SMD, mínimo 8 pines | 1 |
| Header hembra | 3 pines | SMD | 2 |
| Header hembra | 4 pines | SMD | 4 |
| Header hembra | 2 pines | SMD | 1 |
| Conector/fuente | JRC-B008 | SMD | 1 |
| MOSFET | AO3400A | SOT-23 | 1 |
| Resistencia | 10 kΩ | 0603 | 5 |
| Resistencia | 120 Ω | 1206 | 1 |
| Resistencia | 68 Ω | 1206 | 1 |
| Resistencia | 330 Ω | 0805 | 2 |
| Resistencia | 220 Ω | 1206 | 1 |
| Resistencia | 1.96 kΩ | 1206 | 2 |
| Resistencia | 1 kΩ | 0805 | 2 |
| Pulsador | Switch SMD | SMD | 2 |
| Regulador | AMS1117-3.3 | SOT-223-3 | 1 |
| MCU | ESP32-S3-WROOM-1-N8R8 | Módulo SMD | 1 |

## Módulos externos considerados

| Módulo | Cantidad por PCB | Observación |
|---|---:|---|
| PN532 NFC | 1 | Módulo externo para lectura NFC |
| Motobomba | 1 | Controlada mediante MOSFET |
| HC-SR04 | 2 | Lecturas continuas de distancia |
| Celda de carga | 1 | Conectada al HX711 |
| HX711 | 1 | Módulo de lectura para celda de carga |
| Pololu programador | 1 | Uso de desarrollo/programación |
| Header hembra Pololu | 1 | Revisar orientación y pinout |
| Servomotores | 2 | Conectores dedicados |

## Recomendaciones de diseño y revisión

### Footprints

Antes de fabricar una nueva revisión de la PCB, revisar cuidadosamente:

- Que el encapsulado del componente comprado coincida con la footprint usada.
- Que las resistencias y capacitores SMD coincidan en tamaño real con el diseño.
- Que los headers SMD tengan la orientación correcta.
- Que el orden de pines de cada módulo coincida con la huella.
- Que los pads expuestos después de la máscara de soldadura sean suficientes para soldar cómodamente.
- Que el módulo ESP32 tenga área libre adecuada para antena.

### Inventario y Excel

Para actualizar el Excel del proyecto, conviene agrupar los componentes por valor y encapsulado. No es necesario repetir cada resistencia o capacitor en filas separadas si tienen el mismo valor y la misma footprint.

Una estructura útil sería:

| Componente | Valor | Encapsulado | Cantidad por PCB | Cantidad a fabricar | Total requerido | Disponible | Faltante | Tienda | Link |
|---|---|---|---:|---:|---:|---:|---:|---|---|

También es recomendable separar la lista por tienda:

- Sigma Electrónica: mayoría de resistencias, capacitores, sensores y módulos.
- Mercado Libre: PN532 y MOSFET AO3400, si no se consigue localmente en otra tienda.
- Electronilab: ESP32-S3-WROOM-1-N8R8, según disponibilidad.
- Otro proveedor: headers SMD hembra/macho si no están disponibles en Sigma.

### Alimentación

El diseño incluye regulación para 3.3 V y líneas de alimentación para periféricos. Como recomendación general:

- No alimentar servomotores ni motobomba desde el pin de 3.3 V del ESP32.
- Separar cargas de potencia y lógica siempre que sea posible.
- Usar capacitores de desacople cerca de alimentación del microcontrolador y módulos sensibles.
- Revisar caída de tensión cuando se activen actuadores.
- Validar temperatura del regulador durante pruebas.
- Confirmar continuidad y ausencia de cortos antes de montar el ESP32.

### Sensores ultrasónicos

Los sensores HC-SR04 operan típicamente a 5 V. Antes de conectar la señal `Echo` al ESP32, se debe revisar si se requiere adaptación de nivel hacia 3.3 V para proteger el pin del microcontrolador.

### Motobomba y MOSFET

Para la motobomba se contempla un MOSFET NMOS AO3400. La etapa debe revisarse con especial atención en:

- Corriente real de la motobomba.
- Diodo de protección.
- Tierra común entre lógica y potencia.
- Ancho de pista para corriente.
- Temperatura del MOSFET en funcionamiento.
- Separación entre señal de control y carga inductiva.

### Celda de carga y HX711

La celda de carga se conecta a la tarjeta HX711. En este repositorio, las conexiones entre celda y HX711 se consideran parte del cableado externo del módulo, no del esquemático principal de la PCB.

Antes de calibrar el sistema se debe:

- Verificar cableado de la celda.
- Confirmar orientación de alimentación.
- Probar lectura cruda del HX711.
- Calibrar con pesos conocidos.
- Registrar factor de calibración en firmware.

## Estructura sugerida del repositorio

```text
.
├── README.md
├── hardware/
│   ├── kicad/
│   │   ├── esquematico/
│   │   ├── pcb/
│   │   └── gerbers/
│   ├── renders/
│   └── fabricacion/
├── firmware/
│   ├── src/
│   ├── include/
│   └── README.md
├── docs/
│   ├── img/
│   │   └── pcb-soldada.jpg
│   ├── inventario/
│   └── pruebas/
└── tools/
    └── scripts/
```

Esta estructura es solo una recomendación para mantener separados el diseño electrónico, el firmware, las imágenes, la documentación y los archivos auxiliares.

## Pruebas recomendadas

Antes de conectar todos los módulos al mismo tiempo, se recomienda validar por etapas:

### 1. Prueba de continuidad

- Verificar continuidad de GND.
- Verificar ausencia de corto entre 5 V y GND.
- Verificar ausencia de corto entre 3.3 V y GND.
- Revisar continuidad de EN, IO0 y líneas de programación.
- Revisar pads expuestos y soldaduras frías.

### 2. Prueba de alimentación

- Energizar sin ESP32 si el diseño lo permite.
- Medir 5 V.
- Medir 3.3 V.
- Revisar temperatura del regulador.
- Confirmar comportamiento de LEDs de alimentación.
- Medir consumo en reposo.

### 3. Prueba de programación

- Conectar programador.
- Verificar enumeración USB/serial.
- Probar modo boot.
- Cargar firmware mínimo.
- Confirmar salida serial.

### 4. Prueba de sensores

- Leer HC-SR04 individualmente.
- Leer HX711 sin carga.
- Calibrar celda de carga.
- Probar PN532 con tag NFC.
- Registrar estabilidad de lecturas.

### 5. Prueba de actuadores

- Probar servos por separado.
- Probar motobomba con fuente adecuada.
- Confirmar que la activación de actuadores no reinicie el ESP32.
- Medir caída de tensión durante activación.
- Revisar ruido eléctrico o reinicios.

### 6. Prueba integrada

- Ejecutar un ciclo de alimentación simulado.
- Registrar distancia, peso y eventos.
- Probar identificación NFC.
- Validar que las alertas del firmware no activen cargas sin pasar por lógica convencional.
- Documentar errores encontrados para la siguiente revisión.

## Pendientes

- Validar pinout final de todos los headers.
- Confirmar si se requiere adaptación de nivel para señales de sensores a 5 V.
- Ajustar GPIOs según facilidad de ruteo y estabilidad del firmware.
- Documentar esquema final de alimentación.
- Subir fotografías del PCB soldado.
- Agregar capturas del esquemático y layout.
- Publicar archivos KiCad organizados.
- Documentar pruebas eléctricas realizadas.
- Documentar calibración de celda de carga.
- Definir estructura final del firmware.
- Separar con claridad la lógica convencional de la capa de IA.

## Advertencia

Este proyecto está en fase de prototipado. No debe usarse como circuito final sin validar alimentación, consumo, temperatura, aislamiento, protección, corriente de actuadores y seguridad mecánica. Cualquier modificación debe revisarse en esquemático, PCB y firmware antes de energizar la placa.

## Créditos

Proyecto desarrollado como ejercicio de diseño de PCB custom, sistemas embebidos e integración sensórica aplicada a un comedero inteligente para gatos.

El diseño toma como punto de partida la idea de construir una placa propia con ESP32, pero adapta el hardware a una aplicación específica: monitoreo y control de alimentación animal mediante sensores, actuadores y procesamiento embebido.
