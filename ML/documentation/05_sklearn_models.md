### Task 5 – Addestramento e selezione dei classificatori con Scikit-Learn

## 0. Obiettivo del Task
In questo task vengono addestrati, confrontati e ottimizzati diversi classificatori tramite la libreria Scikit-Learn, utilizzando il dataset `training.csv` (50.000 osservazioni). L'obiettivo è massimizzare le prestazioni su un test set separato e selezionare il classificatore più performante, che in sede d'esame verrà testato sul file `real_settings.csv`.

A differenza del Task 2, in cui i classificatori erano costruiti manualmente su poche feature discretizzate, qui vengono sfruttate tutte le 21 feature disponibili e algoritmi di apprendimento automatico in grado di ottimizzare da soli la struttura del modello.

## 1. Caricamento del dataset
Il dataset `training.csv` viene caricato in un DataFrame. Le variabili vengono separate in:
- X: le 21 feature esplicative
- y: la variabile target `Diabetes_binary`

## 2. Preparazione dei dati
Per il Task 5 vengono utilizzate tutte le feature disponibili nel dataset, senza la discretizzazione applicata nel Task 2. Gli algoritmi di Scikit-Learn gestiscono infatti direttamente le variabili numeriche, ordinali e binarie, quindi non è necessario trasformare BMI, Age o le altre feature in categorie.

## 3. Suddivisione in Training Set e Test Set
Il dataset viene suddiviso in due parti tramite `train_test_split`:
- Training set (80%): utilizzato per addestrare i modelli
- Test set (20%): utilizzato per valutare i modelli su dati mai visti in fase di addestramento

La suddivisione è effettuata con `stratify=y`, in modo da mantenere in entrambe le parti la stessa proporzione tra le classi (circa 86% non diabetici e 14% diabetici), e con `random_state=42` per garantire la riproducibilità. La separazione del test set è fondamentale: valutare un modello sugli stessi dati con cui è stato addestrato fornirebbe una stima falsata delle sue reali capacità di generalizzazione.

## 4. Baseline Model
Come riferimento iniziale viene considerato un classificatore banale che predice sempre la classe maggioritaria (non diabetico). A causa dello sbilanciamento del dataset, un modello di questo tipo otterrebbe già circa l'86% di accuracy, pur non individuando alcun diabetico.

Questa osservazione è cruciale e guida tutto il resto del task: in presenza di classi sbilanciate l'accuracy è una metrica ingannevole, perché può risultare elevata anche per un modello inutile. Per questo motivo la valutazione e la selezione dei modelli si basano principalmente sull'F1-score, che misura la qualità della classificazione sulla classe minoritaria (i diabetici).

## 5. Addestramento dei classificatori
Sono stati addestrati cinque classificatori, applicando a ciascuno la stessa procedura: creazione del modello, addestramento sul training set (`fit`), predizione sul test set (`predict`), calcolo delle metriche di valutazione (Accuracy, Precision, Recall, F1-score).

Per i modelli sensibili alla scala delle feature (Logistic Regression, k-NN, Perceptron) è stata applicata la standardizzazione tramite `StandardScaler`, calcolando media e deviazione standard sul solo training set (`fit_transform`) e applicando le stesse statistiche al test set (`transform`), in modo da evitare qualsiasi fuga di informazione dal test set verso l'addestramento.

I risultati ottenuti sul test set sono i seguenti:

| Modello | Accuracy | Precision | Recall | F1-score |
|---------------------|----------|-----------|--------|----------|
| Naive Bayes | 77.98% | 33.31% | 57.93% | 42.30% |
| Decision Tree | 79.76% | 29.47% | 32.52% | 30.92% |
| Logistic Regression | 86.35% | 53.50% | 15.36% | 23.87% |
| k-Nearest Neighbors | 85.19% | 43.37% | 20.89% | 28.20% |
| Perceptron | 78.98% | 29.73% | 37.33% | 33.10% |

## 6. Confronto dei risultati
Dal confronto emerge un comportamento molto significativo. I modelli con l'accuracy più elevata (Logistic Regression e k-NN) ottengono in realtà i risultati peggiori nell'individuazione dei diabetici: la loro recall è molto bassa (rispettivamente 15.36% e 20.89%), segno che tendono a classificare quasi tutte le osservazioni come non diabetiche, sfruttando lo sbilanciamento per massimizzare l'accuracy.

Al contrario, il Naive Bayes presenta l'accuracy più bassa tra i modelli "ad alta accuracy", ma ottiene la recall più alta (57.93%) e il miglior F1-score (42.30%). Questo lo rende il modello più efficace nell'individuare effettivamente i casi positivi, che è l'obiettivo realmente rilevante in un contesto di screening sanitario.

Questo confronto conferma concretamente perché l'accuracy non possa essere l'unico criterio di valutazione su un dataset sbilanciato.

## 7. Cross Validation
La valutazione basata su una singola suddivisione train/test dipende dalla specifica partizione casuale utilizzata. Per ottenere una stima più robusta e affidabile è stata applicata la Stratified 10-Fold Cross Validation: il dataset viene suddiviso in 10 blocchi che mantengono la proporzione tra le classi, e ogni modello viene addestrato e valutato 10 volte (9 blocchi per l'addestramento, 1 per la validazione), calcolando media e deviazione standard dell'F1-score.

Per i modelli che richiedono standardizzazione è stata utilizzata una Pipeline che incapsula lo scaler e il classificatore, garantendo che la standardizzazione venga ricalcolata all'interno di ogni fold solo sui dati di addestramento, senza alcuna fuga di informazione.

I risultati della cross validation (F1-score medio) confermano le conclusioni precedenti:

| Modello | F1 medio | Deviazione standard |
|---------------------|----------|---------------------|
| Naive Bayes | 0.4145 | 0.0096 |
| Perceptron | 0.3221 | 0.0544 |
| Decision Tree | 0.3069 | 0.0177 |
| k-Nearest Neighbors | 0.2671 | 0.0066 |
| Logistic Regression | 0.2328 | 0.0141 |

Il Naive Bayes risulta non solo il modello con l'F1-score medio più elevato, ma anche uno dei più stabili (deviazione standard molto bassa), a conferma della sua affidabilità.

## 8. Ottimizzazione degli iperparametri
I modelli sono stati inizialmente addestrati con i parametri di default. In questa sezione si è cercato di migliorarne le prestazioni ottimizzando i principali iperparametri tramite cross validation, in modo da non utilizzare il test set durante la fase di selezione.

- **Decision Tree (max_depth):** è stata analizzata la profondità massima dell'albero. La limitazione della profondità riduce l'overfitting ma non porta a un miglioramento significativo dell'F1-score rispetto alla configurazione di default.
- **k-NN (k):** è stato analizzato il numero di vicini. Il valore ottimale risulta k=3; all'aumentare di k le prestazioni sulla classe minoritaria peggiorano, perché la classe maggioritaria tende a dominare la decisione.
- **Logistic Regression (C):** è stato analizzato il livello di regolarizzazione. La variazione di C produce effetti molto limitati: l'F1-score rimane sostanzialmente stabile, segno che il problema principale non è la regolarizzazione ma lo sbilanciamento delle classi.

L'ottimizzazione degli iperparametri non modifica quindi la conclusione: il Naive Bayes rimane il modello più performante.

## 9. Analisi Bias-Variance
I risultati della cross validation sono stati riletti in chiave bias-variance, uno dei concetti fondamentali del Machine Learning:
- un F1-score medio basso indica un bias elevato (modello troppo semplice, underfitting);
- una deviazione standard elevata indica una varianza elevata (modello instabile, sensibile ai dati, tendente all'overfitting).

Il Naive Bayes presenta il miglior compromesso, con F1-score medio elevato e bassa varianza. Il Perceptron, pur avendo un F1-score discreto, mostra la varianza più alta, risultando quindi il modello meno stabile.

## 10. Selezione del modello finale
Al termine della fase di addestramento, valutazione, cross validation e ottimizzazione, il classificatore selezionato come modello finale è il **Naive Bayes**, in quanto presenta il miglior F1-score sia sul test set sia in cross validation, unito a un'elevata stabilità. Sarà questo il modello utilizzato per la predizione sul file `real_settings.csv` fornito in sede d'esame.

## 11. Conclusioni
L'obiettivo del progetto era costruire e valutare modelli di classificazione per il riconoscimento del diabete a partire da indicatori sanitari. Il percorso ha attraversato tutte le fasi previste: preparazione del dataset (Task 1), costruzione manuale di due classificatori di famiglie diverse — Naive Bayes e albero di decisione — (Task 2), analisi esplorativa (Task 3), valutazione dei classificatori manuali sul dataset esteso (Task 4) e, infine, addestramento di più classificatori tramite Scikit-Learn (Task 5).

L'analisi ha messo in evidenza alcuni principi metodologici fondamentali:
- su un dataset sbilanciato l'accuracy è una metrica fuorviante, e la valutazione deve basarsi su precision, recall e soprattutto F1-score;
- un modello più complesso non è necessariamente più performante: la Logistic Regression e il k-NN, pur raggiungendo l'accuracy più alta, si sono rivelati i meno efficaci nell'individuare i diabetici;
- la cross validation e l'analisi bias-variance permettono di valutare non solo le prestazioni medie ma anche la stabilità di un modello;
- la scelta del classificatore finale deve sempre basarsi su un confronto quantitativo e rigoroso.

Il Naive Bayes è risultato il miglior compromesso tra capacità di individuare i casi positivi e stabilità, ed è stato quindi selezionato come modello conclusivo del progetto.
