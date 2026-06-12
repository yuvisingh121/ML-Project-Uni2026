# **Task 3 — Exploratory Data Analysis (EDA)**

## 0. Obiettivi dell’EDA

L’Exploratory Data Analysis ha lo scopo di:
- comprendere la struttura del dataset `training.csv`;
- verificare la qualità dei dati (valori mancanti, anomalie, incoerenze);
- analizzare la distribuzione delle variabili;
- individuare pattern utili per la modellazione;
- valutare lo sbilanciamento della variabile target;
- supportare le scelte di preprocessing e selezione delle feature.


## 1. Caricamento e struttura del dataset

Il dataset `training.csv` contiene:
- 50.000 osservazioni,
- 21 feature,
- 1 variabile target (`Diabetes_binary`).

Sono stati verificati:
- numero di righe e colonne,
- tipi di dato,
- assenza di valori mancanti,
- prime osservazioni per controllare la coerenza dei valori.

Il dataset risulta completo e pronto per l’analisi.


## 2. Distribuzione della variabile target

La variabile `Diabetes_binary` presenta un forte sbilanciamento:

- Classe 0 (non diabetici): ~86%
- Classe 1 (diabetici): ~14%

Questo sbilanciamento influisce sulle metriche dei modelli, rendendo necessaria un’attenzione particolare a precision, recall e F1-score.


## 3. Analisi delle variabili numeriche

### 3.1 BMI
- Distribuzione concentrata tra 20 e 40.
- Valori elevati più frequenti tra i soggetti diabetici.
- Per stabilizzare le stime probabilistiche è stata introdotta la variabile derivata `BMI_Category`:
  - Normal (≤ 24)
  - Overweight (25–29)
  - Obese (≥ 30)

### 3.2 Age
- Variabile già discretizzata in 13 fasce.
- Le fasce più alte mostrano una maggiore incidenza di diabete.
- È stata mantenuta la codifica originale e aggiunta la variabile `Age_Category` per semplificare l’analisi.


## 4. Analisi delle variabili binarie

Per ciascuna variabile binaria sono state analizzate:
- frequenze assolute,
- frequenze condizionate rispetto alla classe target,
- eventuali pattern rilevanti.

Osservazioni principali:
- HighBP e HighChol sono più frequenti nei soggetti diabetici.
- PhysActivity è più comune nei non diabetici.
- DiffWalk mostra una forte associazione con la classe diabetica.
- Le variabili legate alla salute percepita (GenHlth, PhysHlth, MentHlth) mostrano peggioramenti nella classe 1.


## 5. Correlazioni

È stata calcolata la matrice di correlazione tra le variabili numeriche e binarie.

Le correlazioni risultano generalmente deboli, ma alcune relazioni sono rilevanti:
- HighBP ↔ HighChol  
- GenHlth ↔ PhysHlth  
- Age ↔ Diabetes_binary (correlazione positiva moderata)


## 6. Analisi grafica

Sono stati utilizzati:
- istogrammi per BMI e Age,
- countplot per le variabili binarie,
- boxplot per individuare valori anomali,
- heatmap per la matrice di correlazione.

I grafici confermano:
- lo sbilanciamento della variabile target,
- la relazione tra BMI elevato e diabete,
- la maggiore incidenza di HighBP e HighChol nei soggetti diabetici,
- la presenza di pattern coerenti con il dominio sanitario.


## 7. Conclusioni dell’EDA

L’EDA ha evidenziato:
- dataset completo e privo di valori mancanti;
- forte sbilanciamento della variabile target;
- alcune feature con buona capacità discriminativa (HighBP, HighChol, BMI_Category, Age_Category, PhysActivity);
- correlazioni deboli ma coerenti con il contesto clinico;
- necessità di discretizzare alcune variabili per stabilizzare le stime probabilistiche.

L’analisi esplorativa fornisce le basi per:
- la costruzione del classificatore manuale,
- la preparazione dei dati per i modelli successivi,
- la valutazione critica delle prestazioni dei classificatori nello Step 4.
