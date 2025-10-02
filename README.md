# Proyecto 2 corte
# Sorting Color Machine
## Valentina Ruiz, Tomas Barrios, Darek Aljuri, Rafael Salcedo
## 1. Resumen General
El proyecto busca desarrollar un prototipo funcional de línea de clasificación de piezas por color, utilizando el kit Sorting Line with Color Detection 24V de Fischertechnik, con el fin de representar de forma práctica un proceso de control industrial real e integrar los principios de percepción (sensores), actuación (actuadores), computación (controlador) y conectividad, propios del IIoT.

En la vida real, este tipo de sistema es aplicable en múltiples sectores como la industria alimentaria con la clasificación de frutas, vegetales o granos según color, maduración o defectos, la industria farmacéutica para la separación de pastillas o cápsulas defectuosas, líneas de ensamblaje para control de calidad y separación de componentes en función de su acabado o características visuales.

Además, el proyecto busca desarrollar un gemelo digital de la máquina de clasificación por color, implementado en CODESYS. A través de la programación en Ladder, se diseñará una visualización funcional virtual del prototipo, permitiendo digitalizar el sistema, simular su comportamiento y facilitar tanto la validación como la optimización del proceso antes de su implementación física

### Descripción del proceso 

La línea de clasificación consiste en [1]:

- Alimentación de piezas: Las piezas, geométricamente idénticas pero de diferentes colores, ingresan al sistema mediante una cinta transportadora impulsada por un motor
  
- Percepción: Un sensor óptico de color detecta la tonalidad de cada pieza a partir de la reflexión de su superficie. Durante el paso de la pieza por debajo del sensor, se determina el valor mínimo medido y este se compara con valores límite para asignar la pieza a los colores blanco, rojo o azul.

- Computación y control: El controlador procesa la señal del sensor, compara el valor mínimo con los umbrales configurados y clasifica cada pieza según su color. El instante de expulsión se define a partir de la detección previa de la pieza por una barrera de luz y la posición medida por el interruptor de pulsos.

- Actuación: Dependiendo del color detectado, las válvulas solenoides activan cilindros neumáticos que desvían las piezas hacia la rampa o contenedor asignado.

- Salida: Las piezas expulsadas se dirigen a través de tres rampas hacia los compartimientos correspondientes. Barreras de luz supervisan el flujo de piezas y el estado de llenado de cada compartimiento.

## 2. Etapas de diseño

### Análisis del proceso
El sistema corresponde a una máquina clasificadora de objetos por color (sorting color machine). El flujo general del proceso es el siguiente:
- Alimentación y transporte:
  - Un motor acciona la banda transportadora.
  - En la zona inicial se ubica un sensor infrarrojo de presencia, que detecta si hay un objeto colocado sobre la banda.
    - Si no se detecta objeto, la banda permanece detenida.
    - Si se detecta objeto, la banda se activa y comienza el transporte.

- Detección de color:
  - Mientras el objeto avanza, ingresa a una cámara de detección (caja roja).
  - Allí, un sensor de color identifica el color del objeto.

- Salida de la cámara y temporización:

  - Al abandonar la caja roja, un segundo sensor infrarrojo confirma la salida del objeto.
  - En este punto, el sistema activa un contador/temporizador asociado al color detectado.
  - Este contador genera un retardo  que asegura que el objeto se encuentre correctamente alineado con el mecanismo de clasificación antes de actuar.

- Clasificación por color:

  - Una vez transcurrido el tiempo definido por el contador, se habilita la válvula solenoide correspondiente al color detectado.

  - Las válvulas son normalmente cerradas (NC) [2]: Con señal eléctrica, se abren y permiten el paso de aire comprimido, el aire acciona un cilindro neumático (pistón), que empuja la pieza hacia el compartimiento asignado según su color, cuando la válvula deja de recibir señal, se cierra y el pistón retorna a su posición inicial por medio de un resorte.

- Verificación de clasificación:
  - En cada compartimiento de descarga hay un sensor infrarrojo final, encargado de verificar si la pieza llegó correctamente.
  - Si la pieza fue clasificada, el sistema detiene la banda transportadora y reinicia el ciclo para el siguiente objeto.

  ![.](imagenesWiki/diagramabloques.png)


### Restricciones de diseño

Las siguientes restricciones de diseño del prototipo se establecieron siguiendo la ISO/IEC/IEEE 29148:2018, estándar que define buenas prácticas para la especificación de requisitos de sistemas y software. Bajo esta guía, se formularon los requerimientos funcionales y no funcionales considerando aspectos técnicos, asi como de escalabilidad y conectividad, con el fin de asegurar un diseño claro, trazable y evaluable.

| Código | Tipo        | Nombre del Requerimiento | Descripción                                                                 | Prioridad | Viabilidad técnica                                                                 | Restricciones                                      | Recursos requeridos                        | Impacto                                               |
|--------|-------------|--------------------------|-----------------------------------------------------------------------------|-----------|--------------------------------------------------------------------------------------------------------|---------------------------------------------------|--------------------------------------------|-------------------------------------------------------|
| RF-01  | Funcional   | Detección de color       | El sistema debe identificar piezas de al menos 3 colores distintos mediante el sensor óptico. | Alta      | El kit Fischertechnik incluye un sensor de color analógico calibrable para distinguir múltiples tonos. | Limitado al espectro soportado por el sensor       | Sensor óptico de color                     | Permite clasificación automática.                     |
| RF-02  | Funcional   | Clasificación automática | El sistema debe desviar cada pieza hacia el contenedor correspondiente según su color. | Alta      | El kit dispone de compresor, válvulas y actuadores neumáticos que permiten desviar las piezas con precisión. Así como fototransistores para garantizar la presencia de los objetos y la sincronización del sistema. | Número de salidas limitado a 3 contenedores      | Motores, válvulas, compresor y fototransistores              | Representa el proceso industrial de sorting.          |
| RF-03  | Funcional   | Registro de datos        | El sistema debe enviar el resultado de clasificación (color detectado y cantidad de piezas) a un servidor IoT. | Media     | El controlador TXT/PLC puede comunicarse vía Ethernet/WiFi con un servidor externo, aunque requiere configuración adicional.  | Depende de conectividad disponible                 | Controlador con WiFi/Ethernet, servidor IoT| Integra IIoT y análisis remoto.                       |
| RF-04  | Funcional   | Interfaz de monitoreo    | El usuario debe visualizar en tiempo real la operación (colores detectados, conteo, estado de actuadores). | Media     |  Existen plataformas como Node-RED o Grafana que pueden integrarse con el controlador para mostrar datos en dashboards simples. Inicialmente, se muestran datos básicos en el display del controlador.| Requiere desarrollo de software adicional          | Node-RED, Grafana o app web                | Mejora la usabilidad y monitoreo remoto.              |
| RNF-01 | No funcional| Limitación de energía    | El prototipo debe funcionar con fuentes de 9v y los sensores adicionales deberian funcionar con 9 o 5 v   | Media      |  Los voltajes deben estar soportados oficialmente por Fischertechnik, y se dispone de fuentes de laboratorio. | Depende de disponibilidad de fuente y controlador  | Fuente de poder, adaptadores               | Asegura compatibilidad con componentes Fischertechnik. |
| RNF-02 | No Funcional | Mantenibilidad | El sistema debe estar diseñado de forma modular para facilitar el reemplazo de sensores, actuadores o controladores sin necesidad de rediseñar todo el prototipo. | Baja |  Los kits de Fischertechnik son modulares y permiten intercambiar componentes fácilmente. | Limitado a la compatibilidad de módulos disponibles en el kit. | Herramientas básicas, repuestos del kit. | Impacta en la sostenibilidad y reutilización del prototipo a largo plazo. |
| RNF-03 | No funcional| Escalabilidad            | El sistema debe permitir la ampliación hacia más colores o integración con otros módulos. | Baja     |  El controlador dispone de entradas/salidas adicionales que permiten integrar más sensores o módulos industriales. | Limitado por número de sensores/entradas del controlador | PLC o controlador con entradas libres     | Facilita futuras expansiones del proyecto.            |


#### Criterios y estandares de diseño establecidos
El diseño del prototipo de Sorting Line with Color Detection se fundamenta en los lineamientos de la norma ISO/IEC/IEEE 29148:2018 que se refiere a Systems and Software Engineering, Life Cycle Processes, Requirements Engineering [3], la cual establece directrices para la definición de requerimientos funcionales y no funcionales en proyectos de ingeniería. Adicionalmente, se adoptaron estándares aplicables en la industria de automatización y control, asegurando que el sistema sea seguro, escalable, reproducible y mantenible.

##### Principios generales de diseño (IEEE 29148:2018)

- Claridad y no ambigüedad: cada requerimiento debe estar expresado de forma precisa, sin interpretaciones múltiples.

- Corrección: los requerimientos deben reflejar exactamente las necesidades del proceso de clasificación automatizado.

- Consistencia: los requerimientos no deben entrar en conflicto entre sí.

- Rastreo (Traceability): los requerimientos se deben poder vinculae con un objetivo, módulo de diseño, implementación y prueba.

- Viabilidad técnica: los requerimientos deben ser alcanzables con los recursos de hardware/software disponibles.

- Verificabilidad: todo requerimiento debe poder validarse mediante pruebas medibles y repetibles.

- Priorización: los requerimientos deben clasificarse en críticos, deseables y opcionales según el impacto en la operación.

#### Estandar IEC 61131-3
- Estándar internacional para lenguajes de programación de  Controladores Lógicos Programables (PLC) industriales, proporcionando un conjunto de lenguajes y estructuras comunes para la automatización, asegurando la independencia del fabricante y permitiendo la portabilidad y reutilización de código (incluye Ladder). [4]
- 
##### Criterios específicos del proyecto

- El sistema debe clasificar piezas de acuerdo con colores rojo, azul y verde, con un nivel de precisión ≥ 95 %.

- El tiempo de respuesta entre la detección y la actuación debe ser ≤ 200 ms.

- El prototipo debe permitir escalabilidad para incluir nuevos sensores/actuadores.

- El código y los esquemáticos deben estar completamente documentados para garantizar mantenibilidad.


### Definición de variables


## 2.3 Definición de Variables  

| Nombre        | Tipo   | Atributo           | Descripción                                                                 |
|---------------|--------|--------------------|-----------------------------------------------------------------------------|
| `Stop`        | BOOL   | Entrada            | Pulsador de paro normal del sistema                                         |
| `Start`       | BOOL   | Entrada            | Pulsador de inicio del sistema                                              |
| `IRStart`     | BOOL   | Entrada            | Sensor IR inicial (detecta objeto en la entrada de la cinta)                |
| `F1`          | BOOL   | Entrada            | Final de carrera 1 / sensor de seguridad asociado                           |
| `M1`          | BOOL   | Salida             | Motor 1 (cinta transportadora principal)                                    |
| `M2`          | BOOL   | Salida             | Motor 2 (elemento auxiliar, p. ej. vibrador de piezas)                      |
| `C1`          | BOOL   | Interna / Marcador | Estado de clasificación para color 1                                        |
| `IRT1`        | BOOL   | Entrada            | Sensor IR asociado a la posición de expulsión 1                             |
| `TON1`        | TON    | Temporizador       | Temporizador para retardo de activación de V1                               |
| `V1`          | BOOL   | Salida             | Válvula 1 (desvía pieza color 1)                                            |
| `IRTV1`       | BOOL   | Entrada            | Sensor IR de verificación en compartimiento 1                               |
| `ET1`         | TIME   | Tiempo             | Tiempo acumulado de TON1                                                    |
| `ET`          | TIME   | Tiempo             | Variable auxiliar de tiempo general                                         |
| `TON2`        | TON    | Temporizador       | Temporizador para clasificación color 2                                     |
| `F3`          | BOOL   | Entrada            | Final de carrera 3 / sensor de seguridad asociado                           |
| `CVCOUNTERC1` | WORD   | Contador           | Contador de piezas clasificadas en compartimiento 1                         |
| `C2`          | BOOL   | Interna / Marcador | Estado de clasificación para color 2                                        |
| `IRT2`        | BOOL   | Entrada            | Sensor IR asociado a la posición de expulsión 2                             |
| `ET2`         | TIME   | Tiempo             | Tiempo acumulado de TON2                                                    |
| `TON3`        | TON    | Temporizador       | Temporizador auxiliar para expulsión en color 2                             |
| `TON4`        | TON    | Temporizador       | Temporizador auxiliar para control de M2 o sincronización                   |
| `V2`          | BOOL   | Salida             | Válvula 2 (desvía pieza color 2)                                            |
| `IRTV2`       | BOOL   | Entrada            | Sensor IR de verificación en compartimiento 2                               |
| `F4`          | BOOL   | Entrada            | Final de carrera 4 / sensor de seguridad asociado                           |
| `CVCOUNTERC2` | WORD   | Contador           | Contador de piezas clasificadas en compartimiento 2                         |
| `C3`          | BOOL   | Interna / Marcador | Estado de clasificación para color 3                                        |
| `IRT3`        | BOOL   | Entrada            | Sensor IR asociado a la posición de expulsión 3                             |
| `TON5`        | TON    | Temporizador       | Temporizador para expulsión en color 3                                      |
| `TON6`        | TON    | Temporizador       | Temporizador auxiliar en clasificación color 3                              |
| `ET3`         | TIME   | Tiempo             | Tiempo acumulado de TON5 / TON6                                             |
| `V3`          | BOOL   | Salida             | Válvula 3 (desvía pieza color 3)                                            |
| `IRTV3`       | BOOL   | Entrada            | Sensor IR de verificación en compartimiento 3                               |
| `F5`          | BOOL   | Entrada            | Final de carrera 5 / sensor de seguridad asociado                           |
| `CVCOUNTERC3` | WORD   | Contador           | Contador de piezas clasificadas en compartimiento 3                         |
| `F2`          | BOOL   | Entrada            | Final de carrera 2 / sensor de seguridad asociado                           |
| `IRF2`        | BOOL   | Entrada            | Sensor IR asociado al final de compartimiento 2                             |



## Desarrollo del Sistema

![.](imagenesWiki/diagrama.jpg)


### Diagrama de función secuencial
El sistema se diseñó bajo un enfoque de control secuencial por etapas (Step Sequence Control). Cada etapa (S) representa un estado del proceso, mientras que las transiciones se activan cuando se cumplen condiciones de sensores (entradas).

El siguiente diagrama muestra la secuencia de operación implementada:

![.](imagenesWiki/secuencia.png)

#### Descripción de la secuencia:


1. Inicio (S000)

 - Estado inicial del sistema.
 - Se asegura de reiniciar todas las salidas (Output_0, Output_1, Output_2, Output_3).

2. Ejecución inicial (S001)

  - Condición de inicio: Input_0 o Input_8.
  - Acciones: activa las salidas principales (Output_0 y Output_1) que corresponden al motor de la cinta transportadora.

3. Detección de pieza (S002.1)

  - Condición: sensor de entrada (Input0_1) detecta una pieza.
  - Acción: habilita Output0_2 (p. ej., registro del sensor y arranque de temporización).

4. Clasificación (S002.2 y S002.3)

  - Dependiendo de la detección de color:
    - Input0_2 activa Output0_3 (válvula de clasificación 1).
    - Input0_3 activa Output0_4 (válvula de clasificación 2).

5. Fin de ciclo y reset (S002.2.2 y S002.3.2)

  - Una vez completada la expulsión, las salidas se resetean (R).
  - El sistema queda listo para el siguiente objeto.

### Programación Ladder

#### ¿Qué es un TON?
Los TON permiten crear retardos controlados en la secuencia, garantizando que los actuadores no se activen todos al mismo tiempo.
  - Cuando la entrada IN del TON pasa a TRUE (1):

  - El temporizador comienza a contar el tiempo programado (PT = tiempo de preset).
  - Si la entrada permanece en 1 hasta que el tiempo llega a PT:
    - La salida Q pasa a TRUE (1).

  - Mientras tanto, la variable ET (Elapsed Time) guarda el tiempo transcurrido.

  - Si la entrada IN vuelve a FALSE (0) antes de alcanzar el tiempo PT:

    - El temporizador se resetea, ET vuelve a 0 y Q se queda en 0.
      
![.](imagenesWiki/codesys1.png)

![.](imagenesWiki/codesys2.png)

![.](imagenesWiki/codesys3.png)

![.](imagenesWiki/codesys4.png)


#### Descripción del Programa Ladder

- Red 1: Arranque y paro
  - Botón Start enciende IRStart si no está activo Stop.
  - Se establece la condición inicial del sistema.

- Red 2: Activación de motores

  - Con IRStart y el sensor F1 activo, se activan M1 y M2.

- Red 3: Secuencia de validación

  - Si se detecta IRT1, IRT2 o IRT3 junto con F2, se activa la señal IRF2.

  - Esto sincroniza los pasos siguientes.

- Red 4–5: Control de temporización de V1

  - Si IRF2 y los motores están activos, se inicia el TON1 (1s).

  - Al finalizar, se activa la válvula V1 y se resetea IRT1.

- Red 6–10: Control de V2

  - Uso de TON2 y TON3 con IRTV1 y IRF2.

  - Activan válvula V2 después de un retardo de 2s.

  - Luego V2 se apaga tras TON4 (0.5s) y habilita F4.

- Red 11–15: Control de V3

  - Similar al anterior, con TON5 (3s) y TON6 (0.5s).

  - Maneja la válvula V3, activando F5 y reseteando las condiciones previas.

- Red 16: Apagado de motores

  - Cuando cualquiera de los sensores finales (F3, F4, F5) está activo, se apagan M1 y M2.

- Red 17: Reset general

  - El botón Stop resetea:

  - M1, M2, IRStart, IRF2, IRT1, IRT2, IRT3, etc.

  - Esto asegura que el sistema vuelva a estado inicial.

#### Contadores (CTU)

- CTU_0 → Conteo asociado a F3 → almacena en CVCOUNTERC1.

- CTU_1 → Conteo asociado a F4 → almacena en CVCOUNTERC2.

- CTU_2 → Conteo asociado a F5 → almacena en CVCOUNTERC3.

#### Secuencia de Operación

- Arranque: Se presiona Start, se activa IRStart, luego M1 y M2.

- Sensor F1 confirma inicio → motores siguen activos.

- Secuencia de válvulas:

- V1 tras 1s

- V2 tras 2s

- V3 tras 3s

- Sensores F3, F4, F5 apagan motores en condiciones finales.

- Contadores  registran ciclos de cada válvula.

- Stop reinicia todo el sistema.


## Implementación digital (codesys)
El sistema fue implementado en CODESYS como un gemelo digital, permitiendo la simulación y validación del proceso. La interfaz HMI se diseñó con una vista superior del modelo físico, lo que facilita la representación visual de cada elemento y el control intuitivo del sistema.

Imagen de Base:

![.](imagenesWiki/imagenes.jpg)

Interfaz HMI



## 6. Referencias

[1] fischertechnik GmbH, "Sorting Line with Color Detection 24 V", fischertechnik, Art.-No. 536633. Disponible en: https://www.fischertechnik.de/en/products/industry-and-universities/training-models/536633-sorting-line-with-color-detection-24v 

[2] Emerson, “Válvulas solenoide normalmente cerradas,” Emerson. [En línea]. Disponible: https://www.https://www.emerson.com/es-py/catalog/solenoid-valves/normally-closed-solenoid-valves?fetchFacets=true#facet:&partsFacet:&modelsFacet:&facetLimit:&searchTerm:&partsSearchTerm:&modelsSearchTerm:&productBeginIndex:0&partsBeginIndex:0&modelsBeginIndex:0&orderBy:&partsOrderBy:&modelsOrderBy:&pageView:grid&minPrice:&maxPrice:&pageSize:&facetRange

[3] ISO/IEC/IEEE, ISO/IEC/IEEE 29148:2018 Systems and software engineering — Life cycle processes — Requirements engineering, 2nd ed. Geneva, Switzerland: International Organization for Standardization, Nov. 2018. [Online]. Available: https://www.iso.org/standard/72089.html

[4] “IEC 61131-3 Protocol Overview,” Real Time Automation, Inc., [En línea]. Disponible: https://www.rtautomation.com/technologies/control-iec-61131-3/

