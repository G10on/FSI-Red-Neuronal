# Memoria - Red Neuronal

El dataset sobre el que se ha trabajado en este proyecto tiene como tópico principal las zonas geográficas habitadas. Está constituido por imágenes obtenidas por satélites, y que se pueden clasificar en 10 categorías diferentes: AnnualCrop, Forest, HerbaceousVegetation, Highway, Industrial, Pasture, PermanentCrop, Residential, River y SeaLake. Esta clasificación puede ser empleada en la exploración geográfica para determinar características importantes de ciertas zonas (ya sean pobladas o no), así como realizar seguimientos de su evolución a través de imágenes satelitales.

En primer lugar, importamos las utilidades de Keras del framework de TensorFlow, que nos permitirán preparar los datos y entrenar el modelo. Establecemos también la ruta al dataset, así como fijar el tamaño del batch y establecer el tamaño de la imagen.

Seguidamente, aplicamos la técnica de Data Augmentation sobre la parte del dataset de entrenamiento. Para ello, especificamos en los objetos de ImageDataGenerator que el conjunto de validación sea de un 20% del dataset (el otro 80% para el entrenamiento), que las imágenes se roten ciertos grados y que se reescalen, de forma que se encuentren entre 0 y 1 para facilitar el entrenamiento.

Tras realizar el aumento de nuestra parte del dataset de entrenamiento, cargamos la parte del dataset de validación con la clase ImageDataGenerator y reescalamos los valores de los píxeles de la imagen de forma que se encuentren entre 0 y 1.

Para crear el modelo, inicializamos un objeto de la clase Sequential (pues las capas se añadirán y conectarán una tras otra) y añadimos las capas ocultas. Compilamos el modelo especificando que use la función de pérdida de Categorical Cross Entropy y que el optimizador a usar sea el Adadelta, así como indicarle que se muestren las métricas de accuracy. 

Finalmente, entrenamos el modelo con el método fit, al que le pasamos los objetos con el dataset de entrenamiento y de validación, y le indicamos el número de épocas que iterará y las condiciones de parada. El resultado que devolverá este método lo podemos guardar para posteriormente visualizar la evolución del entrenamiento, así como los resultados de los tests de validación.


## Categorical Crossentropy:

La función de pérdida Categorical Cross Entropy nos permite calcular el error de una predicción focalizándose siempre en la categoría que debe salir como resultado. Lo hace de tal forma que nos indica un valor elevado únicamente cuando el error es grande en la predicción de la categoría correspondiente a la salida, independientemente del error en el resto de predicciones. Para ello, hace uso de la función Softmax, donde la salida es un vector del tamaño del número de categorías, en el que cada posición representa una probabilidad para cada categoría. Esto lo hace mediante el logaritmo neperiano de la probabilidad predicha, de tal forma que devolverá un número cercano a 0 si la predicción coincide con la categoría esperada, y tiende a menos infinito si la probabilidad predicha es baja (por eso se le cambia el signo al final).

Además, para aislar y quedarse solo con el error en la categoría esperada, se multiplica este vector elemento a elemento con la codificación One-Hot. Este es un vector de ceros del tamaño del número de categorías, pero con un 1 en la posición que represente la categoría esperada. De esta forma, el sumatorio de la multiplicación elemento a elemento nos dará un error que estará centrado únicamente en la predicción de la categoría esperada.


## Configuraciones de Hiperparámetros

Los commits del código son algunas de las configuraciones de hiperparámetros que probamos para el modelo de la red neuronal. La más reciente ("epoch24_92") es la que mejor precisión ha desarrollado.
