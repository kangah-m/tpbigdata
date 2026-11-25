tpbigdata

      TP : HADOOP SOUS UBUNTU

            REPONSES AUX QUESTIONS DE SYNTHESE

1-	Définissons un cluster et donnons ses trois avantages
Un cluster est un ensemble de machines indépendantes reliées en réseau et coordonnées comme un système unique. 
On peut également dire qu’on peut prendre une machine et la séparé en plusieurs machines.
Ses avantages sont : 
-Stockage massif distribué,
-Calcul parallèle rapide,
-Haute disponibilité à faible coût.
2-	 Quelle différence faisons-nous entre scalabilité verticale et horizontale ? Disons laquelle Hadoop exploite.
             La verticale (Scale-up) permet de renforcer une seule machine (CPU/RAM, plafond           matériel, coût élevé).
              L’horizontale (Scale-out) permet d’ajouter des machines standards au cluster.
              Hadoop exploite la scalabilité horizontale.
3-	 Le rôle du NameNode et sa sensibilité

-Il gère l’espace noms : Répertoire, Fichiers,
-Sait quels blocs composent chaque fichier et où ils se trouvent,
-Garde toutes les données en mémoire vive,
-Ne stocke aucune donnée,
-Sans lui le cluster est inutilisable.

4-	Secondary NameNode

Le secondary NameNode n’est pas une copie secours de NameNode. Le secondary NameNode fait fusionner fsimage et edit log pour éviter la saturation et accélérer le redémarrage de NameNode.



5-	Localité des données et son importance

Principe consistant à déplacer le programme vers le nœud stockant les données (volumineuses), évitant ainsi la saturation du réseau et maximisant la bande passante disque.

6-	Décrivons dans l’ordre les phases d’un job MapReduce et indiquons celle écrite par un développeur.

Entrée (Hadoop)
Map (Développeur)
Shuffle & sort (Hadoop)
Reduce (Développeur)
Sortie (Hadoop)

7-	Disons pourquoi le reducer Python peut-il supposer que les lignes d’une même clé sont consécutives.

Les lignes d’une même clé sont consécutives avant d’alimenter stdin du reducer, Hadoop streaming exécute obligatoirement la phase Shuffle & Sort qui regroupe toutes les paires clé, garantissant la contiguïté des clés identiques.

8-	Disons dans quels cas un combiner est-il utilisable et donnons un exemple où il ne l’est pas directement.

Un combiner est utilisable lorsque l’agrégation est associative et commutative avec même type entrée/sortie (somme, min, max, count). 
Non utilisable directement pour la moyenne arithmétique ou la médiane.

9-	Disons ce qui se passe si un nœud tombe en panne pendant l’exécution d’une tâche Map.

L’arrêt des heartbeats signale la panne au RessourceManager/ApplicationMaster, qui replanifie automatiquement la tâche Map sur autre nœud hébergeant une réplique du bloc HDFS.

10-	 Citons deux limites de MapReduce et un outil de l’écosystème qui y répond.

-Lenteur itérative et Latence élevée (écriture disque entre jobs) / Apache Spark (calcul en mémoire RAM)
-Verbosité bas niveau / Apache Hive (requêtes SQL)
 
                        COMPTE RENDU



Sortie de jps

 


<img width="945" height="311" alt="image" src="https://github.com/user-attachments/assets/afb1bb6c-6720-49fe-ace1-357304d67b0e" />




















Interface 9870
 





<img width="945" height="1032" alt="image" src="https://github.com/user-attachments/assets/9ec2df3e-d048-4897-b35d-2435f91a3efa" />






Interface 8088
 



<img width="724" height="768" alt="image" src="https://github.com/user-attachments/assets/4f1396e5-cdaa-41dc-a31a-c9aefb1f55a2" />



![Uploading image.png…]()








           Mappers et Reducers (exercices 2 à 6)
 
 







 

 








 
 


  








Résultat de chaque job

  
   




 



  

 








 






 
Réponses aux questions des encadrés « Question »


EXERCICE 1 : 

1)	Le nombre de blocs que poeme.txt occupe est un bloc. Parce que sa taille est très inférieure au bloc par défaut de 128 Mo. Sur disque physique, seul l’espace réel est consommé.

2)	Retrouvons le fichier dans l’interface du NameNode

Localisation Web UI (9870) : Via Utilities > Browse the file system > /user/hadoop/tp/input/poeme.txt, on visualise les permissions, la taille, le bloc (ID) et le DataNode hébergeur.

3)	Le nombre de blocs d’un fichier de 1 Go (1024 Mo) avec une réplication de 3 est :
Blocs : 1024 Mo ÷ 128 Mo = 8 blocs.
Espace disque : 8 blocs × 3 copies = 24 blocs physiques, soit 3 Go (3072 Mo) occupé sur le cluster.

             EXERCICE 5 :

Peut-on utiliser le reducer de moyenne comme combiner ? NON.
Parce qu’un combiner exige une fonction associative et commutative. Alors que la moyenne des moyennes partielles diffère de la moyenne globale dès que les effectifs diffèrent.
CONTRE-EXEMPLE : Split 1 = [10, 12] (moy. 11) et Split 2 = [20] (moy. 20). Moyenne des moyennes = 15,5 ≠ vraie moyenne (14,0).
SOLUTION : le mapper émet matiere<TAB>note<TAB>1. Le combiner somme séparément les notes et les effectifs : (matiere, somme_partielle, nb_partiel). Le reducer divise somme_totale / nb_total à la fin.
