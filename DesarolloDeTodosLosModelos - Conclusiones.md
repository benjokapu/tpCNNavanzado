














## modelo 4 - AleNet
 La idea fue implementar varias capas convolucionales con activación ReLU. Queriamos algo robusto que pueda lididar con nuestro dataset.

Las primeras pruebas fueron con capas convolucionales de distintos tamaños de filtro, empezando con 11x11 en la primera capa. Luego nos dimos cuenta de que una de las cosas que más nos costó fue lidiar con los shapes de las entradas y las salidas de las capas. A veces los tamaños no coincidían y había que hacer ajustes, como el padding o los strides, para poder mantener las dimensiones adecuadas entre capa y capa.

La parte de normalización fue otro tema importante. Para ayudar con la estabilidad del entrenamiento, incluímos capas de BatchNormalization después de cada capa convolucional. Esto nos ayudó a evitar problemas con los gradientes y aceleró el proceso de entrenamiento, pero nos tomó algunas pruebas para ver el efecto real que tenía en el modelo.

En cuanto a la parte de pooling, porbamos con MaxPooling de 3x3(es bastante común) pero lo importante era el stride, que decidimos ajustar para reducir las dimensiones de la imagen de manera efectiva sin perder demasiada información. Los resultados en este aspecto fueron muy buenos, porque las imágenes se redujeron a las dimensiones correctas para pasar a las capas densas al final.

Añadimos Dropout para evitar el sobreajuste. A la hora de elegir el optimizador, usamos el optimizador SGD (Stochastic Gradient Descent) con una tasa de aprendizaje de 0.01 y momentum de 0.9. Para la parte de las etiquetas, trabajamos con el one-hot encoding, porque las clases eran mutuamente excluyentes, y convertimos tanto el conjunto de entrenamiento como el de prueba con la función to_categorical de Keras. 
