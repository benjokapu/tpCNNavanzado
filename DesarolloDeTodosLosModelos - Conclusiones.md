














## modelo 4 - AleNet
 La idea fue implementar varias capas convolucionales con activación ReLU. Queriamos algo robusto que pueda lididar con nuestro dataset.

Las primeras pruebas fueron con capas convolucionales de distintos tamaños de filtro, empezando con 11x11 en la primera capa. Luego nos dimos cuenta de que una de las cosas que más nos costó fue lidiar con los shapes de las entradas y las salidas de las capas. A veces los tamaños no coincidían y había que hacer ajustes, como el padding o los strides, para poder mantener las dimensiones adecuadas entre capa y capa.

La parte de normalización fue otro tema importante. Para ayudar con la estabilidad del entrenamiento, incluímos capas de BatchNormalization después de cada capa convolucional. Esto nos ayudó a evitar problemas con los gradientes y aceleró el proceso de entrenamiento, pero nos tomó algunas pruebas para ver el efecto real que tenía en el modelo.

En cuanto a la parte de pooling, porbamos con MaxPooling de 3x3(es bastante común) pero lo importante era el stride, que decidimos ajustar para reducir las dimensiones de la imagen de manera efectiva sin perder demasiada información. Los resultados en este aspecto fueron muy buenos, porque las imágenes se redujeron a las dimensiones correctas para pasar a las capas densas al final.

Añadimos Dropout para evitar el sobreajuste. A la hora de elegir el optimizador, usamos el optimizador SGD (Stochastic Gradient Descent) con una tasa de aprendizaje de 0.01 y momentum de 0.9. Para la parte de las etiquetas, trabajamos con el one-hot encoding, porque las clases eran mutuamente excluyentes, y convertimos tanto el conjunto de entrenamiento como el de prueba con la función to_categorical de Keras. 





## modelo 6 - ResNet50
Planteamos un modelo de trasnferencia de aprendizaje, es decir, aprovechamos mucho la arquitectura preentrenada de esta red con pesos de ImageNet, ya que se adapta bien a problemas de clasificación multiclase. Agregamos una capa de GlobalAveragePooling2D para reducir dimensionalidad y capas densas para realizar la clasificación final.

Primero, preparamos las imágenes con un generador que las escalaba para normalizarlas entre 0 y 1. Luego dividimos los datos en 60,20,20 (train,val y est). Utilizamos el optimizador  Adam.

En cuanto al modelo, congelamos las capas originales de ResNet50 (no las entrenamos) y añadimos nuevas capas al final. Una para resumir la información de las imágenes (GlobalAveragePooling2D) y otras para realizar la clasificación. Esto nos permitió entrenar únicamente las capas nuevas mientras aprovechábamos las características generales que ResNet50 ya había aprendido. Tamvine, luego liberamos las últimas capas de ResNet50 para que también se entrenaran (fine-tuning) y ajustamos la velocidad de aprendizaje para que los cambios fueran más sutiles. Aquí, disminuimos la tasa de aprendizaje (learning_rate=1e-4) para evitar grandes oscilaciones en el ajuste de los pesos.

Sin embargo, los primeros resultados no fueron buenos, aunque esto es normal ya que el modelo base está congelado (trainable=False) y aparte solo realziamos 10 epochs (no ibamos a hacer mas porque ya correr eso nos llevó mucho tiempo y necesitabamos avanzar)

- Añadimos augmentación de datos, que aplica transformaciones como rotaciones, zooms y volteos a las imágenes, haciéndolas más variadas.

Problemas:
1. Algunas clases tenían más imágenes que otras, lo que hacía que el modelo favoreciera ciertas categorías.
2. Creemos que algunas imagenes son muy parecidas, lo que puede provocar que el modelo no haga una buena prediccion.Nos dimos cuenta de esto mirando la CONFUSION MATRIX.
3. El modelo predecia muy mal (mas abajo se especifica el porque)

- Incorporamos un ajuste llamado class_weight, que asigna más importancia a las clases con menos imágenes durante el entrenamiento.

Finalmente, realizamos un segundo entrenamiento con más epochs y ajustes en los datos. El bajo rendimiento se debió en parte a la cantidad limitada de datos y su calidad, lo que nos mostró que no solo es importante el modelo que se utiliza, sino también cómo se preparan los datos. 


