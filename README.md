# Instrucciones para instalar

## Crear ambiente virtual

```bash
python3 -m venv venv

./venv/Scripts/activate

# instalar librerias dependiendo del sistema operativo
pip install -r requirements.linux.txt
pip install -r requirements.windows.txt
``` 

# Descripción
Se realizó un modelo para hacer un análisis de sentimiento de reviews en inglés de tripadvisor. Se entrenaron 30 modelos modificando hiperparámetros y se seleccionó el mejor modelo según la métrica F1. Sin embargo, se notó que el mejor modelo tiene muy mal tiempo de entrenamiento e inferencia, por lo que se recomendó una alternativa de modelo si la aplicación requiriera mejores tiempos de inferencia o entrenamiento.

## Data Split
```bash
jupyter notebook data_split.ipynb
```
Para iniciar con la preparación se decidió hacer un notebook únicamente para hacer el split de los dataset de train, test y validación. Este notebook toma el archivo `data/reviews.csv` y lo separa aleatoriamente de la siguiente forma:
- `data/train.csv` con el 70% de los casos
- `data/test.csv` con el 15% de los casos
- `data/validation.csv` con el 15% de los casos

## Model train
```bash
jupyter notebook model.ipynb
```
Se definió una clase Model para entrenar el modelo de análisis de sentimeiento. La clase posee un método fit para entrenar el modelo y un método predict para inferir un review. Se parametrizaron los siguientes hiperparámetros para buscar el mejor modelo:

- `steps`: un listado de pasos a ejecutar en el pipeline de entrenamiento e inferencia. Se puede colocar los siguientes valores:
    - `remove_stopwords`: elimina stopwords
    - `lemmatization`: lematiza utilizando un NLP model de spacy
    - `ngram`: calcula n-gramas utilizando gensim
- `nlp_model`: modelo de spacy a utilizar. Los valores válidos son:
    - `en_core_web_trf`
    - `en_core_web_lg`
    - `en_core_web_md`
    - `en_core_web_sm`
- `allowed_postags`: listado de postags disponibles para spacy. Tiene los tipos de palabras que utiliza el modelo de spacy para filtrar palabras. En la tabla abajo se muestran los valores posibles
- `ngrams`: valor de cantidad de n-gramas que se desea hacer. 2 calcularía bigramas, 3 trigramas, etc.
- `min_count` y `threshold`: parámetros para calcular ngrams 
    
Postags disponibles en spacy:

POS|DESCRIPTION|EXAMPLES
---|---|---
ADJ|adjective|*big, old, green, incomprehensible, first*
ADP|adposition|*in, to, during*
ADV|adverb|*very, tomorrow, down, where, there*
AUX|auxiliary|*is, has (done), will (do), should (do)*
CONJ|conjunction|*and, or, but*
CCONJ|coordinating conjunction|*and, or, but*
DET|determiner|*a, an, the*
INTJ|interjection|*psst, ouch, bravo, hello*
NOUN|noun|*girl, cat, tree, air, beauty*
NUM|numeral|*1, 2017, one, seventy-seven, IV, MMXIV*
PART|particle|*’s, not,*
PRON|pronoun|*I, you, he, she, myself, themselves, somebody*
PROPN|proper noun|*Mary, John, London, NATO, HBO*
PUNCT|punctuation|*., (, ), ?*
SCONJ|subordinating conjunction|*if, while, that*
SYM|symbol|*$, %, §, ©, +, −, ×, ÷, =, :), *
VERB|verb|*run, runs, running, eat, ate, eating*
X|other|*sfpksdpsxmsa*
SPACE|space

# Resultados

Se entrenó 30 modelos buscando el que mejor resultados diera de F1. Se obtuvo el mejor modelo al lematizar y utilizar el modelo de spacy `en_ccore_web_trf` y se obtuvo un F1 superior a 95.4% para la data de prueba. Se determinó que el modelo no está haciendo overfitting debido a que los resultados son bastante similares entre train y validation.


## Análisis de Resultados

De la tabla de resumen que se encuentra en `model.ipynb` se pueden obtener las siguientes conclusiones:
- Los modelos que lematizan (con cualquier modelo de spacy) son los que entregan mejor performance
- Como se puede observar, los resultados de validation y test son bastante similares, lo que nos indica que el modelo generalizó bastante bien (no hizo overfitting)
- El remover stopwords antes de lematizar se vio que hace que el entrenamiento y la inferencia sean más rápidos aunque disminuyen ligeramente el performance a pesar que técnicamente en el paso de lematizar también se eliminan stopwords al utilizar los allowed postags
- El utilizar spacy y lematizar hace que el entrenamiento e inferencia sean lentos, por lo que si se requiere un modelo con el mejor tiempo de entrenamiento e inferencia se puede utilizar únicamente el remover stopwords (el 8vo mejor modelo), el cual obtuvo únicamente 0.0049 menos performance en F1 respecto al mejor modelo (F1=94.93%), pero con un tiempo de entrenamiento de 0.09min vs 4.64min y un tiempo de inferencia de 2.9s vs 118s, por lo que el performance no se degradaría tanto y el modelo sería mucho más rápido
- El aplicar cualquier nivel de ngrams parecía disminuir performance de los modelos en general
- Se realizaron pruebas con diferentes modelos de spacy y, como era de esperar, los modelos más pesados fueron los que mejor performance dieron (encore_web_trf > en_core_web_lg > en_core_web_md > en_core_web_sm) aunque el tiempo de entrenamiento e inferencia del trf fue bastante más lento que los otros


# Contribuciones
Grupo 3 en canvas
- Kevin García: repositorio, jupyter de data_split, instalación y configuración de ambiente, modelo base con método de fit y parametrización de hiperparámetros
- Martín Guzmán: método predict, función para calcular métricas
- Entre los 2: definición de pruebas a realizar y ejecución
