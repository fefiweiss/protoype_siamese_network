# Prototypes' Siamese Neural Network (P-SN)

Proyecto enmarcado en la Tesis de Magíster en Ciencias de la Ingeniería Informática. Este proyecto está enfocado en la detección de noticias falsas en redes sociales, para lo cual se propone un clasificador de noticias falsas que utiliza características agnósticas al lenguaje, llamado Prototypes' Siamese Neural Network o P-SN.

## Datos

Los datos experimentales provienen de dos datasets: TWitter 15 y Twitter 16, cuyos datos originales se pueden [descargar desde su fuente original.](https://www.dropbox.com/s/7ewzdrbelpmrnxu/rumdetect2017.zip "Fuente de datos") Estos datasets contienen la información completa de la propagación de una noticia: 
- Árbol de propagación, donde cada nodo representa una interacción y contiene el id del usuario, id del post y tiempo de emisión en minutos. Notar que para obtener la inforación de usuarios y posts se debe utilizar API de Twitter para recuperar dicha información.
- Label de la noticia. Se trabaja con cuatro clases: non-rumour, true rumour, false rumour y unverifies rumour.

## Modelado de datos

En este proyecto se pretende trabajar con características de contexto y agnósticas al lenguaje, las cuales son representadas mediante series de tiempo. Las series de tiempo, denominadas huellas temporales, se crean sobre una base de tiempo de largo fijo (por ejemplo las primeras 12 horas de propagación) en donde se define una secuencia de intervalos de largo *L*. En cada intervalo se cuenta la cantidad de características en estudio de la huella temporal, pudiendo ser uno de los siguientes indicadores: \#posts, \#retweets, \#replies, \#total number of tweets (volume), \#distinct users que participan en la propagación, \#followers y \#friends de los usuarios que participan en la propagación. Cada huella temporal de una noticia está condicionada a un indicador. De esta forma, la representación final de una noticia es un stack de huellas temporales.

Para generar las huellas temporales es necesario incluir el archivo ```get_time_series.py``` como libreria. Luego, con la función ```get_interactions``` se obtienen las series de tiempo de los indicadores \#posts, \#retweets, \#replies, \#total number of tweets (volume) y con la función ```get_fingerprints``` se obtienen las series de tiempo del resto de los indicadores. <!--- especificando el parámetro '''count_type''' --> Para obtener la representación final de una noticia utilizar la función ```get_multivariate_series``` que incluye opciones de proprocesamiento para las series de tiempo mediante el parámetro ```scale```.

## Red Neuronal Siamesa de Prototipos

Una red neuronal siamesa tiene dos entradas y tiene como objetivo indicar cuán similares son. Así, una entrada es de referencia y la otra es un ejemplo en evaluación. Para este proyecto la entrada de referencia corresponde a un prototipo de noticia (stack de huellas temporales) perteneciente a una clase de veracidad.

### Aprendizaje de prototipos

Para generar los prototipos se proponen dos enfoques:
- Utilizar algoritmos de clustering: Para obtener los prototipos mediante este enfoque se utiliza el archivo ```Prototypes(kmeans+khmetis).ipynb```.
- Utilizar algoritmo de deep learning: Para obtener los prototipos mediante este enfoque se utiliza el archivo ```SOM_VAE_Time_Series.ipynb``` y es necesario incluir el archivo ```utils.py``` como libreria.

### Entrenamiento

El entrenamiento de la red neuronal se encuentra en el archivo ```P-SN.ipynb```. Primero, se debe crear un dataset por pares, por lo cual una noticia se parea con *x* cantidad de prototipos de su misma clase, lo cual se traduce en *y=0* para la red siamesa, y también se parea con *x* prototipos de otra clase, lo cual se traduce en *y=1* para la red siamesa. La red siamesa utiliza como red complementaria o codificadora *G* una red feed-forward de dos capas ocultas, la cual codifica las entradas a un mismo espacio latente, en donde se mide la distancia euclideana para medir la similitud. Se utiliza la función de pérdida *contrastive loss*.

## Evaluación 

La clasificación final de una noticia también se encuentra en el archivo ```P-SN.ipynb```. Al momento de evaluar una noticia para obtener su clasificación, primero se parea con todos los prototipos obtenidos. Luego, cada par se evalúa en la Red Neuronal Siamesa de Prototipos y se obtiene la similititud de cada par. Esta información se ingesta en una red multi-layer perceptron, la cual alinea la información de similitud con las cuatro clases de credibilidad con que se está trabajando.

## Método híbrido

También se propone un método híbrido que por un lado utiliza el modelo expuesto anteriormente, que usa como entrada series de tiempo. Y, por otro lado utiliza características del contenido como el texto, el cual es analizado como lo hace el modelo recursivo TD-RvNN.

## Comparación de métodos

El método propusto se compara con varios estudios del trabajo relacionado. Algunos de ellos tienen su implementación disponible en github ([TD-RvNN](https://github.com/majingCUHK/Rumor_RvNN), [CSI](https://github.com/sungyongs/CSI-Code))


*Una explicación más detallada del proyecto la puede encontrar en el archivo ```Tesis.pdf``` :wink:*
