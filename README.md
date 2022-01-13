# Memoria - Red Neuronal - EuroSAT

El dataset sobre el que se ha trabajado en este proyecto tiene como tópico principal las zonas geográficas habitadas. Está constituido por imágenes obtenidas por satélites, y que se pueden clasificar en 10 categorías diferentes: AnnualCrop, Forest, HerbaceousVegetation, Highway, Industrial, Pasture, PermanentCrop, Residential, River y SeaLake. Esta clasificación puede ser empleada en la exploración geográfica para determinar características importantes de ciertas zonas (ya sean pobladas o no), así como realizar seguimientos de su evolución a través de imágenes satelitales. El dataset se puede encontrar en https://www.kaggle.com/apollo2506/eurosat-dataset.

En primer lugar, importamos las utilidades de Keras del framework de TensorFlow, que nos permitirán preparar los datos y entrenar el modelo. Establecemos también la ruta al dataset, así como fijar el tamaño del batch y establecer el tamaño de la imagen.

Seguidamente, aplicamos la técnica de Data Augmentation sobre la parte del dataset de entrenamiento. Para ello, especificamos en los objetos de ImageDataGenerator que el conjunto de validación sea de un 20% del dataset (el otro 80% para el entrenamiento), aplicamos un zoom a la imagen, volteamos la imagen horizontalmente, hacemos que las imágenes se roten ciertos grados y que se reescalen, de forma que se encuentren entre 0 y 1 para facilitar el entrenamiento.

Tras realizar el aumento de nuestra parte del dataset de entrenamiento, cargamos la parte del dataset de validación con la clase ImageDataGenerator y reescalamos los valores de los píxeles de la imagen de forma que se encuentren entre 0 y 1.

Para crear el modelo, inicializamos un objeto de la clase Sequential (pues las capas se añadirán y conectarán una tras otra) y añadimos las capas ocultas. Compilamos el modelo especificando que use la función de pérdida de Categorical Cross Entropy y que el optimizador a usar sea el Adadelta, así como indicarle que se muestren las métricas de accuracy. 

Finalmente, entrenamos el modelo con el método fit, al que le pasamos los objetos con el dataset de entrenamiento y de validación, y le indicamos el número de épocas que iterará y las condiciones de parada. El resultado que devolverá este método lo podemos guardar para posteriormente visualizar la evolución del entrenamiento, así como los resultados de los tests de validación.


## Categorical Cross Entropy

La función de pérdida Categorical Cross Entropy nos permite calcular el error de una predicción focalizándose siempre en la categoría que debe salir como resultado. Lo hace de tal forma que nos indica un valor elevado únicamente cuando el error es grande en la predicción de la categoría correspondiente a la salida, independientemente del error en el resto de predicciones. Para ello, hace uso de la función Softmax, donde la salida es un vector del tamaño del número de categorías, en el que cada posición representa una probabilidad para cada categoría. Esto lo hace mediante el logaritmo neperiano de la probabilidad predicha, de tal forma que devolverá un número cercano a 0 si la predicción coincide con la categoría esperada, y tiende a menos infinito si la probabilidad predicha es baja (por eso se le cambia el signo al final).

Además, para aislar y quedarse solo con el error en la categoría esperada, se multiplica este vector elemento a elemento con la codificación One-Hot. Este es un vector de ceros del tamaño del número de categorías, pero con un 1 en la posición que represente la categoría esperada. De esta forma, el sumatorio de la multiplicación elemento a elemento nos dará un error que estará centrado únicamente en la predicción de la categoría esperada.


## Configuraciones de Hiperparámetros

Los commits del código son algunas de las configuraciones de hiperparámetros que probamos para el modelo de la red neuronal. La más reciente ("v4") es la que mejor precisión ha desarrollado. A continuación una tabla comparativa que muestra algunas configuraciones de hiperparámetros que hemos utilizado (la que está en rojo es la que mejor resultado ha proporcionado).

![TABLA](https://user-images.githubusercontent.com/90768791/148784907-8fc71320-7859-4955-a182-fca382ac65b5.png)


## Comparaciones y análisis

Observando la gráfica del modelo que mejores resultados ha proporcionado, nos podemos fijar en que la métrica del loss y el accuracy, así como las de validación, evolucionan rápidamente en las primeras epochs. Esto es, que las predicciones son cada vez más certeras, y el error que se calcula en la función de coste decrece. Además, en una primera vista notamos que las formas de las evoluciones entre las métricas de accuracy y loss aparentan seguir trayectorias a lo largo de cada epoch, como era de esperar. 


A partir de la 4º epoch, podemos apreciar cómo los cambios en los pesos de la red neuronal generan cada vez mejoras más pequeñas. Se podría pensar que, tras alcanzar el 70% en accuracy (o 0.5 de loss) los pesos de nuestro modelo empiezan a encontrarse cerca de un mínimo en la función de pérdida, y por tanto, los cambios deben ser cada vez más pequeños (evolución más lenta) para alcanzarlo.


Cuando nos fijamos en las métricas entre el entrenamiento y la validación, podíamos afirmar que en nuestro modelo no se había producido overfitting. Es decir, el modelo no ha memorizado el dataset para devolver predicciones correctas, sino que ha sido capaz de generalizar. Este sobreajuste se hubiese apreciado si los resultados del entrenamiento (en nuestro caso, con accuracy y loss) hubiesen presentado mejores rendimientos que en los resultados de validación. Como vemos, no ha sido el caso.


En cuanto a otras configuraciones de hiperparámetros que empleamos, nos hemos percatado de determinados patrones. La primera es que el hiperparámetro de learning rate lo podemos incrementar para que al principio de las epochs se realizen cambios en los pesos de tal forma que se acerque rápidamente al mínimo, y por tanto, requerirá de menos números de epochs. Sin embargo, tras las primeras epochs, cada vez le resulta más difícil realizar mejoras, pues los cambios son muy grandes. Por otro lado, al contrario que en el anterior caso, establecer valores muy bajos para este hiperparámetro sí permitiría acercarse mejor al mínimo. No obstante, provoca una evolución muy lenta durante las epochs, por lo que requerirá de un mayor número de epochs.


Otro de los aspectos destacados es la carga computacional. Durante las pruebas de las distintas configuraciones de hiperparámetros, el tiempo de entrenamiento en cada epoch se incrementaba considerablemente cuanto más grande y mayores capas densas poseía el modelo. Este aspecto era especialmente notorio a la entrada de las capas densas. Es decir, en la capa donde se serializan las matrices, flatten Por otro lado, las convoluciones eran más ligeras de llevarse a cabo. Por ello, equilibramos el número de cálculos entre las capas convolucionales y las densas, de tal forma que las convoluciones reducían la complejidad de las imágenes de entrada y extraían las características más relevantes, para que las capas densas detectaran patrones más sencillos.


En algunas configuraciones de hiperparámetros se produjeron overfitting en determinados momentos durante el entrenamiento, pues se podía apreciar en las gráficas que la precisión en el entrenamiento era mayor que en la validación (como se mencionó anteriormente). Por ello, añadimos capas de Dropout y redujimos la cantidad de parámetros para evitar estos sobreajustes que se producían durante el entrenamiento.
