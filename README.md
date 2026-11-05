# Sistema de Adquisicion y Acondicionamiento de Senales EMG

## Descripcion del proyecto

Este repositorio contiene el diseno de hardware y el codigo desarrollado para el proyecto de adquisicion de senales electromiograficas (EMG) de superficie. El objetivo es tomar los potenciales electricos generados por la actividad muscular, amplificarlos, filtrarlos mediante un circuito analogico y acondicionarlos para que puedan ser leidos de forma segura por el convertidor analogo-digital (ADC) de un microcontrolador.

### Contexto y cambios respecto a la primera entrega

En la primera etapa del curso probamos el circuito en una protoboard. Aunque logramos captar senal, el montaje era muy inestable: los cables sueltos captaban demasiado ruido de 50 Hz de la red electrica y cualquier movimiento generaba falsos contactos o artefactos en la lectura.

Para esta segunda entrega trasladamos el circuito a una placa de circuito impreso (PCB) de una sola cara (capa inferior B.Cu), pensada para fabricarse mediante ruteo CNC en el laboratorio. Tambien anadimos zocalos y borneras para montar los componentes con firmeza y alimentar todo con dos baterias de 9V, lo que elimina el ruido de la red y previene riesgos electricos.

## Integrantes del grupo (Grupo 5)

* Marcelo de la Fuente (ma.delafuente@udd.cl)
* Benjamin Saldivar (b.saldivarb@udd.cl)
* Manuel Sepulveda (ma.sepulvedar@udd.cl)

Curso: Electronica Biomedica 
Carrera: Ingenieria Civil en Biomedicina  
Universidad del Desarrollo  

## Descripcion del circuito y componentes

El sistema cuenta con las siguientes etapas:

1. Captacion: Sensores de superficie conectados a un modulo Muscle Sensor V3 (o etapa equivalente de instrumentacion) que toma la diferencia de potencial muscular y atenua el ruido comun.
2. Acondicionamiento analogico: Dos amplificadores operacionales LM741 alimentados con fuente simetrica (+9V / -9V) encargados de ajustar la fase y la ganancia mediante resistencias de 15k, 8.2k y 10k ohmios.
3. Proteccion de entrada al microcontrolador: Un divisor de tension resistivo (18k y 10k ohmios) que reduce la salida maxima de 9V a menos de 3.3V, evitando danar el microcontrolador.
4. Procesamiento y envio de datos: Placa de desarrollo ESP32-S3 de 38 pines, la cual lee la senal acondicionada por sus canales ADC y la envia por puerto serie hacia la computadora para graficarla.
5. Alimentacion aislada: Dos baterias de 9V conectadas mediante borneras para generar +9V, -9V y tierra (GND).

### Lista de materiales

| Cantidad | Componente | Funcion | Encapsulado / Conexion |
| --- | --- | --- | --- |
| 1 | Modulo Advancer Muscle Sensor V3 | Captacion y pre-amplificacion EMG | Tira de pines hembra de 1x5 |
| 2 | Amplificadores operacionales LM741 | Inversion y ajuste de ganancia | DIP-8 con zocalo |
| 1 | ESP32-S3 (38 pines) | Lectura ADC y transmision serial | Montado en dos tiras de pines de 1x19 |
| 4 | Resistencias de ganancia | Ajuste de ganancia (15k, 8.2k y dos de 10k) | Resistencias axiales Through-Hole |
| 2 | Resistencias divisor | Escalado a rango de 3.3V (18k y 10k) | Resistencias axiales Through-Hole |
| 2 | Borneras de 2 pines (5.08 mm) | Entrada de alimentacion desde baterias de 9V | Montaje en placa Through-Hole |

## Reglas de diseno de la placa (KiCad / CNC)

El ruteo de la placa se hizo respetando las limitaciones de fabricacion de la fresadora CNC del laboratorio:

* Capa de cobre: Ruteo realizado en la cara inferior (B.Cu).
* Ancho de pista minimo: 0.8 mm.
* Espaciado entre pistas (Clearance): 0.8 mm.
* Borde de la placa: Delimitado en la capa Edge.Cuts.
* Montaje: 4 perforaciones de 3.2 mm de diametro en las esquinas para fijar la placa.
* Serigrafia: Indicaciones claras para la polaridad de las baterias (+9V, GND, -9V), pin 1 de los circuitos integrados y nombres de conectores.

## Estructura de carpetas

El repositorio esta ordenado de la siguiente manera:

```text
├── README.md                      # Descripcion general del proyecto
├── docs/                          # Hojas de datos e informes de entrega
│   ├── datasheets/
│   └── entregables/
├── electronica/                   # Diseno esquematico y PCB
│   ├── kicad/                     # Proyecto en KiCad (.kicad_pro, .kicad_sch, .kicad_pcb)
│   ├── libraries/                 # Simbolos, huellas y modelos 3D locales
│   └── outputs/                   # Archivos para fabricacion y revision
│       ├── gerbers/               # Archivos Gerber y de perforacion (.zip)
│       ├── schematic.pdf          # Esquema electronico en PDF
│       ├── pcb_views/             # Vistas de pistas, render 3D y reporte DRC
│       └── BOM.csv                # Lista de materiales exportada
└── software/                      # Codigo y programas de prueba
    ├── firmware_esp32/            # Codigo de lectura ADC para el ESP32-S3
    └── visualizacion/             # Scripts para graficar la senal
```

## Como abrir y revisar el proyecto

### Abrir los esquematicos y PCB en KiCad

1. Clona el repositorio o descarga el archivo ZIP:
   ```bash
   git clone <URL_DEL_REPOSITORIO>
   ```
2. Abre KiCad (version 7 o superior).
3. Ve a `Archivo > Abrir Proyecto` y selecciona el archivo `.kicad_pro` dentro de `electronica/kicad/`.
4. Las librerias de componentes y huellas estan guardadas de forma local en la carpeta `electronica/libraries/`, por lo que no deberian aparecer componentes rotos o desvinculados.

### Cargar el codigo al ESP32-S3

1. Abre la carpeta `software/firmware_esp32/` en Arduino IDE o PlatformIO.
2. Conecta la placa ESP32-S3 por USB. Recuerda desconectar el cargador del computador si estas haciendo pruebas con electrodos pegados al cuerpo para mantener el aislamiento electrico.
3. Sube el codigo y abre el Serial Plotter configurado a 115200 baudios para observar la senal.

## Referencias

* Informacion del sensor EMG: [Tutorial Sensor EMG - UNIT Electronics](https://blog.uelectronics.com/electronica/sensor-emg-tutorial-completo/)
* Circuito de ejemplo: [DIY Muscle Sensor / EMG Circuit - Instructables](https://www.instructables.com/Muscle-EMG-Sensor-for-a-Microcontroller/)
* Proyectos abiertos de referencia: [BioAmp EXG Pill](https://github.com/upsidedownlabs/BioAmp-EXG-Pill) y [Shield EKG-EMG de OLIMEX](https://www.olimex.com/Products/Duino/Shields/SHIELD-EKG-EMG/open-source-hardware)
