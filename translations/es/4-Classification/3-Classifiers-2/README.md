<!--
CO_OP_TRANSLATOR_METADATA:
{
  "original_hash": "808a71076f76ae8f5458862a8edd9215",
  "translation_date": "2025-09-03T23:58:47+00:00",
  "source_file": "4-Classification/3-Classifiers-2/README.md",
  "language_code": "es"
}
-->
# Clasificadores de cocina 2

En esta segunda lección sobre clasificación, explorarás más formas de clasificar datos numéricos. También aprenderás sobre las implicaciones de elegir un clasificador sobre otro.

## [Cuestionario previo a la lección](https://gray-sand-07a10f403.1.azurestaticapps.net/quiz/23/)

### Prerrequisitos

Asumimos que has completado las lecciones anteriores y tienes un conjunto de datos limpio en tu carpeta `data` llamado _cleaned_cuisines.csv_ en la raíz de esta carpeta de 4 lecciones.

### Preparación

Hemos cargado tu archivo _notebook.ipynb_ con el conjunto de datos limpio y lo hemos dividido en los dataframes X e y, listos para el proceso de construcción del modelo.

## Un mapa de clasificación

Anteriormente, aprendiste sobre las diversas opciones que tienes al clasificar datos utilizando la hoja de referencia de Microsoft. Scikit-learn ofrece una hoja de referencia similar, pero más detallada, que puede ayudarte aún más a reducir tus opciones de estimadores (otro término para clasificadores):

![Mapa de ML de Scikit-learn](../../../../translated_images/map.e963a6a51349425ab107b38f6c7307eb4c0d0c7ccdd2e81a5e1919292bab9ac7.es.png)
> Consejo: [visita este mapa en línea](https://scikit-learn.org/stable/tutorial/machine_learning_map/) y haz clic en el camino para leer la documentación.

### El plan

Este mapa es muy útil una vez que tienes un entendimiento claro de tus datos, ya que puedes "caminar" por sus caminos hacia una decisión:

- Tenemos >50 muestras
- Queremos predecir una categoría
- Tenemos datos etiquetados
- Tenemos menos de 100K muestras
- ✨ Podemos elegir un Linear SVC
- Si eso no funciona, dado que tenemos datos numéricos:
    - Podemos intentar un ✨ KNeighbors Classifier 
      - Si eso tampoco funciona, probar con ✨ SVC y ✨ Ensemble Classifiers

Este es un camino muy útil a seguir.

## Ejercicio - dividir los datos

Siguiendo este camino, deberíamos comenzar importando algunas bibliotecas para usar.

1. Importa las bibliotecas necesarias:

    ```python
    from sklearn.neighbors import KNeighborsClassifier
    from sklearn.linear_model import LogisticRegression
    from sklearn.svm import SVC
    from sklearn.ensemble import RandomForestClassifier, AdaBoostClassifier
    from sklearn.model_selection import train_test_split, cross_val_score
    from sklearn.metrics import accuracy_score,precision_score,confusion_matrix,classification_report, precision_recall_curve
    import numpy as np
    ```

1. Divide tus datos de entrenamiento y prueba:

    ```python
    X_train, X_test, y_train, y_test = train_test_split(cuisines_feature_df, cuisines_label_df, test_size=0.3)
    ```

## Clasificador Linear SVC

El clustering de vectores de soporte (SVC) es un miembro de la familia de técnicas de aprendizaje automático de Máquinas de Vectores de Soporte (SVM, por sus siglas en inglés) (aprende más sobre estas a continuación). En este método, puedes elegir un 'kernel' para decidir cómo agrupar las etiquetas. El parámetro 'C' se refiere a la 'regularización', que regula la influencia de los parámetros. El kernel puede ser uno de [varios](https://scikit-learn.org/stable/modules/generated/sklearn.svm.SVC.html#sklearn.svm.SVC); aquí lo configuramos como 'linear' para asegurarnos de aprovechar el Linear SVC. La probabilidad por defecto es 'false'; aquí la configuramos como 'true' para obtener estimaciones de probabilidad. Establecemos el estado aleatorio en '0' para mezclar los datos y obtener probabilidades.

### Ejercicio - aplicar un Linear SVC

Comienza creando un array de clasificadores. Irás agregando progresivamente a este array a medida que probemos.

1. Comienza con un Linear SVC:

    ```python
    C = 10
    # Create different classifiers.
    classifiers = {
        'Linear SVC': SVC(kernel='linear', C=C, probability=True,random_state=0)
    }
    ```

2. Entrena tu modelo usando el Linear SVC e imprime un informe:

    ```python
    n_classifiers = len(classifiers)
    
    for index, (name, classifier) in enumerate(classifiers.items()):
        classifier.fit(X_train, np.ravel(y_train))
    
        y_pred = classifier.predict(X_test)
        accuracy = accuracy_score(y_test, y_pred)
        print("Accuracy (train) for %s: %0.1f%% " % (name, accuracy * 100))
        print(classification_report(y_test,y_pred))
    ```

    El resultado es bastante bueno:

    ```output
    Accuracy (train) for Linear SVC: 78.6% 
                  precision    recall  f1-score   support
    
         chinese       0.71      0.67      0.69       242
          indian       0.88      0.86      0.87       234
        japanese       0.79      0.74      0.76       254
          korean       0.85      0.81      0.83       242
            thai       0.71      0.86      0.78       227
    
        accuracy                           0.79      1199
       macro avg       0.79      0.79      0.79      1199
    weighted avg       0.79      0.79      0.79      1199
    ```

## Clasificador K-Neighbors

K-Neighbors es parte de la familia de métodos de ML "neighbors", que pueden usarse tanto para aprendizaje supervisado como no supervisado. En este método, se crea un número predefinido de puntos y los datos se agrupan alrededor de estos puntos para que se puedan predecir etiquetas generalizadas para los datos.

### Ejercicio - aplicar el clasificador K-Neighbors

El clasificador anterior fue bueno y funcionó bien con los datos, pero tal vez podamos obtener una mejor precisión. Prueba un clasificador K-Neighbors.

1. Agrega una línea a tu array de clasificadores (agrega una coma después del elemento Linear SVC):

    ```python
    'KNN classifier': KNeighborsClassifier(C),
    ```

    El resultado es un poco peor:

    ```output
    Accuracy (train) for KNN classifier: 73.8% 
                  precision    recall  f1-score   support
    
         chinese       0.64      0.67      0.66       242
          indian       0.86      0.78      0.82       234
        japanese       0.66      0.83      0.74       254
          korean       0.94      0.58      0.72       242
            thai       0.71      0.82      0.76       227
    
        accuracy                           0.74      1199
       macro avg       0.76      0.74      0.74      1199
    weighted avg       0.76      0.74      0.74      1199
    ```

    ✅ Aprende sobre [K-Neighbors](https://scikit-learn.org/stable/modules/neighbors.html#neighbors)

## Clasificador de Vectores de Soporte

Los clasificadores de Vectores de Soporte son parte de la familia de métodos de ML [Máquinas de Vectores de Soporte](https://wikipedia.org/wiki/Support-vector_machine) que se utilizan para tareas de clasificación y regresión. Las SVM "mapean ejemplos de entrenamiento a puntos en el espacio" para maximizar la distancia entre dos categorías. Los datos posteriores se mapean en este espacio para que se pueda predecir su categoría.

### Ejercicio - aplicar un clasificador de Vectores de Soporte

Intentemos obtener una mejor precisión con un clasificador de Vectores de Soporte.

1. Agrega una coma después del elemento K-Neighbors y luego agrega esta línea:

    ```python
    'SVC': SVC(),
    ```

    ¡El resultado es bastante bueno!

    ```output
    Accuracy (train) for SVC: 83.2% 
                  precision    recall  f1-score   support
    
         chinese       0.79      0.74      0.76       242
          indian       0.88      0.90      0.89       234
        japanese       0.87      0.81      0.84       254
          korean       0.91      0.82      0.86       242
            thai       0.74      0.90      0.81       227
    
        accuracy                           0.83      1199
       macro avg       0.84      0.83      0.83      1199
    weighted avg       0.84      0.83      0.83      1199
    ```

    ✅ Aprende sobre [Vectores de Soporte](https://scikit-learn.org/stable/modules/svm.html#svm)

## Clasificadores Ensemble

Sigamos el camino hasta el final, aunque la prueba anterior fue bastante buena. Probemos algunos 'Clasificadores Ensemble', específicamente Random Forest y AdaBoost:

```python
  'RFST': RandomForestClassifier(n_estimators=100),
  'ADA': AdaBoostClassifier(n_estimators=100)
```

El resultado es muy bueno, especialmente para Random Forest:

```output
Accuracy (train) for RFST: 84.5% 
              precision    recall  f1-score   support

     chinese       0.80      0.77      0.78       242
      indian       0.89      0.92      0.90       234
    japanese       0.86      0.84      0.85       254
      korean       0.88      0.83      0.85       242
        thai       0.80      0.87      0.83       227

    accuracy                           0.84      1199
   macro avg       0.85      0.85      0.84      1199
weighted avg       0.85      0.84      0.84      1199

Accuracy (train) for ADA: 72.4% 
              precision    recall  f1-score   support

     chinese       0.64      0.49      0.56       242
      indian       0.91      0.83      0.87       234
    japanese       0.68      0.69      0.69       254
      korean       0.73      0.79      0.76       242
        thai       0.67      0.83      0.74       227

    accuracy                           0.72      1199
   macro avg       0.73      0.73      0.72      1199
weighted avg       0.73      0.72      0.72      1199
```

✅ Aprende sobre [Clasificadores Ensemble](https://scikit-learn.org/stable/modules/ensemble.html)

Este método de aprendizaje automático "combina las predicciones de varios estimadores base" para mejorar la calidad del modelo. En nuestro ejemplo, usamos Random Trees y AdaBoost. 

- [Random Forest](https://scikit-learn.org/stable/modules/ensemble.html#forest), un método de promediado, construye un 'bosque' de 'árboles de decisión' infundidos con aleatoriedad para evitar el sobreajuste. El parámetro n_estimators se establece en el número de árboles.

- [AdaBoost](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.AdaBoostClassifier.html) ajusta un clasificador a un conjunto de datos y luego ajusta copias de ese clasificador al mismo conjunto de datos. Se enfoca en los pesos de los elementos clasificados incorrectamente y ajusta el ajuste para el siguiente clasificador para corregir.

---

## 🚀Desafío

Cada una de estas técnicas tiene una gran cantidad de parámetros que puedes ajustar. Investiga los parámetros predeterminados de cada una y piensa en lo que significaría ajustar estos parámetros para la calidad del modelo.

## [Cuestionario posterior a la lección](https://gray-sand-07a10f403.1.azurestaticapps.net/quiz/24/)

## Repaso y autoestudio

Hay mucho vocabulario técnico en estas lecciones, así que tómate un momento para revisar [esta lista](https://docs.microsoft.com/dotnet/machine-learning/resources/glossary?WT.mc_id=academic-77952-leestott) de terminología útil.

## Tarea 

[Experimenta con parámetros](assignment.md)

---

**Descargo de responsabilidad**:  
Este documento ha sido traducido utilizando el servicio de traducción automática [Co-op Translator](https://github.com/Azure/co-op-translator). Si bien nos esforzamos por lograr precisión, tenga en cuenta que las traducciones automáticas pueden contener errores o imprecisiones. El documento original en su idioma nativo debe considerarse como la fuente autorizada. Para información crítica, se recomienda una traducción profesional realizada por humanos. No nos hacemos responsables de malentendidos o interpretaciones erróneas que puedan surgir del uso de esta traducción.