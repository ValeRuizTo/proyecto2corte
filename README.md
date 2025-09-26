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



## 6. Referencias

[1] fischertechnik GmbH, "Sorting Line with Color Detection 24 V", fischertechnik, Art.-No. 536633. Disponible en: https://www.fischertechnik.de/en/products/industry-and-universities/training-models/536633-sorting-line-with-color-detection-24v 

[2] Emerson, “Válvulas solenoide normalmente cerradas,” Emerson. [En línea]. Disponible: https://www.https://www.emerson.com/es-py/catalog/solenoid-valves/normally-closed-solenoid-valves?fetchFacets=true#facet:&partsFacet:&modelsFacet:&facetLimit:&searchTerm:&partsSearchTerm:&modelsSearchTerm:&productBeginIndex:0&partsBeginIndex:0&modelsBeginIndex:0&orderBy:&partsOrderBy:&modelsOrderBy:&pageView:grid&minPrice:&maxPrice:&pageSize:&facetRange
