Task 1 — Preparazione del Dataset e Generazione di manuale.csv e training.csv


1. Introduzione
Il dataset utilizzato in questo progetto è il CDC Diabetes Health Indicators Dataset (UCI Repository, ID 891), composto da 253.680 osservazioni e 22 variabili, di cui 21 feature e 1 variabile target (Diabetes_binary).
L’obiettivo del Task 1 è:
- preparare il dataset in un formato adatto ai classificatori;
- analizzare la struttura e la qualità dei dati;
- generare due file fondamentali per il progetto:
    - manuale.csv 
    - training.csv 


2. Caricamento del Dataset
Il dataset è stato importato tramite la libreria ucimlrepo, unificato in un unico DataFrame e verificato nella sua struttura:
- 253.680 righe
- 22 colonne
- nessun valore mancante


3. Analisi dei Tipi di Attributi
Ogni variabile è stata classificata secondo le categorie viste a lezione:

| Attributo                                    | Tipo                     | Motivazione                         |
| -------------------------------------------- | ------------------------ | ----------------------------------- |
| HighBP, HighChol, CholCheck, Smoker, Stroke, |                          |                                     |
| HeartDiseaseorAttack, PhysActivity, Fruits,  |  Nominale binario        | Variabili 0/1                       |
| Veggies, HvyAlcoholConsump, AnyHealthcare,   |                          |                                     |
| NoDocbcCost, DiffWalk, Sex                   |                          |                                     |
| -------------------------------------------- | ------------------------ | ----------------------------------- |
| GenHlth, Age, Education, Income              | Ordinale                 | Valori discreti con ordine naturale |
| -------------------------------------------- | ------------------------ | ----------------------------------- |
| BMI, MentHlth, PhysHlth                      | Numerico                 | Quantità misurabili                 |
| -------------------------------------------- | ------------------------ | ----------------------------------- |
| Diabetes_binary                              | Target binario           | Classe da predire                   |
| -------------------------------------------- | ------------------------ | ----------------------------------- |


4. Analisi della Variabile Target
La distribuzione delle classi è fortemente sbilanciata:
- 0 (non diabetici): 86.07%
- 1 (diabetici): 13.93%


5. Creazione del File manuale.csv
Il file manuale.csv deve contenere 10–15 campioni, scelti manualmente, e deve essere bilanciato tra le due classi.
Criteri adottati:
A. Selezione casuale ma stratificata  
Sono stati estratti:
- 7 campioni con Diabetes_binary = 0
- 7 campioni con Diabetes_binary = 1
B. Motivazione  
Un dataset piccolo e bilanciato è necessario per:
- calcolare a mano Naive Bayes e Decision Tree;
- evitare che una classe domini l’altra;
- rendere i calcoli manuali gestibili.


6. Creazione del File training.csv
Il file training.csv rappresenta un sottoinsieme del dataset originale.
Dimensione scelta:  
È stato selezionato un campione del 20% del dataset originale (≈ 50.000 righe).
Questa dimensione è sufficiente per:
- ottenere risultati statisticamente significativi;
- ridurre i tempi di addestramento;
- mantenere la rappresentatività del dataset.