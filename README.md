
Objectif du Projet
Le but principal de ce projet est de développer un système capable de compter automatiquement des composants électroniques en se basant sur la reconnaissance d'images. Le processus de comptage repose sur un modèle de Deep Learning préalablement entraîné à l'aide de TensorFlow Lite ou Edge Impulse. 
Les données de classification sont ensuite transmises via Bluetooth Low Energy (BLE) vers Node-RED, qui effectue le comptage final et présente les résultats sur un tableau de bord interactif.

![](https://i.goopics.net/zpqyuw.png)

Ainsi pour commencer nous avons réaliser 100 photos pour chaque composants, ici nous avons une rapsberry pi1, un CAN et un capteur ultrason.
En tout on a 300 images à exploiter, pour entrainer notre modèle, alors pour l'entrainement on utilise Edge Impulse, et on partage les images
en un rations de 80% pour l'entrainement et 20% pour le test.

Raspberry Pi4:
![Schéma du Système](https://m.media-amazon.com/images/I/71A14Sz2bWL.jpg)
CAN ADS1115:
![](https://www.okdo.com/fr/wp-content/uploads/sites/4/2022/02/DFR0553-Image-2.jpg)
Capteur Ultrason:
![](https://boutique.semageek.com/953-thickbox_default/capteur-de-distance-ultrason-hc-sr04.jpg)

Ensuite l'impulsion a été créé tel que:
![](https://i.goopics.net/6y935p.png)

Qu'on puisse réaliser une classification des images, ainsi plussieurs modèles de classifications on
étés réalisés, cependant dans le cadre du tiny machine learning, nous devons prendre en compte
de la faible capacité en RAM de notre carte qui est une Arduino Nano 33 BLE, qui dipose de 256 KB
de ram. Alors les premiers modèles générés étaient aux alentours de 1 MB, il fallut alors une longue
phase de regénération de modèle afin d'arriver à un modèle optimiser en taille de RAM et sans qu'il 
y est trop de l'attence tout en conservant un score d'accuracy élevé et voici le modèle qu'on obtient:

![](https://i.goopics.net/fih9of.png)

Ainsi on obtient un modèle ayant des carractéristique adaptés à notre microcontrôler.

Ce modèle a donc été déployé sur notre carte et donc on obtient un maximum de probabilité que l'objet soit 
le bon de 40% en moyenne. Ce qui n'est pas énorme, une optimisation supplémentaire et plus approfondi du 
modème est nécessaire.

Voici qu'on obitent en sortie par exemple pour la Raspberry Pi 4:

21:01:45.512 -> Starting inferencing in 2 seconds...

21:01:47.536 -> Taking photo...

21:01:50.344 -> Predictions (DSP: 1 ms., Classification: 155 ms., Anomaly: 0 ms.): 

21:01:50.344 ->     CAN: 0.31250

21:01:50.344 ->     Raspberry: 0.39453

21:01:50.344 ->     capteur: 0.29688

21:01:50.344 ->     anomaly score: 0.000

Après avoir réalisé la classification d'images à l'aide du modèle optimisé sur la carte Arduino Nano 33 BLE, la prochaine étape cruciale est l'établissement de la connexion Bluetooth Low Energy (BLE) avec Node-RED. Cette connexion permettra la transmission des données de classification vers le tableau de bord interactif.
En utilisant la bibliothèque Arduino BLE, nous initialisons un service BLE et des caractéristiques associées sur la carte Arduino. Ces caractéristiques sont utilisées pour représenter les prédictions du modèle de classification. Une fois la connexion BLE établie, nous utilisons une boucle pour récupérer les prédictions du modèle et les transmettre à Node-RED à travers la caractéristique BLE correspondante. Cette approche permet une communication continue entre la carte Arduino et Node-RED,
facilitant la mise à jour en temps réel des résultats de la classification sur le tableau de bord interactif.

Après ça on peut voir que la carte Arduino est identifiable par le BLuetooh.

![](https://i.goopics.net/mtgrks.png)

Par la suite, la mise en place d'un tableau de bord sur Node-RED est nécessaire pour visualiser les résultats de la classification en temps réel. Cependant, Node-RED ne dispose pas nativement d'éléments permettant de capturer les données du BLE. Pour résoudre cette limitation, des éléments supplémentaires provenant de node-red-contrib-ble-sense ont été installés.
![](https://i.goopics.net/r354ye.png)

En ajoutant ces nouveaux nœuds BLE à Node-RED, nous pouvons créer des flux qui capturent les données transmises par la carte Arduino via BLE. Ici on aura un noeud pour scanner l'environnement afin de trouver des périphériques à lier et ensuite un autre noeud pour une connection direct au périphérique souhaité qui est celui de l'Arduino. 
Ce second noeaud permet aussi d'extraire sous la forme d'un objet JSON les carractéristiques qu'on veut. Malheuresement le node-red-contrib-ble-sense est adapté pour d'autre capteur, il est donc nécessaire d'approfondir ce dernier pour comprendre au mieux comment il fonctionne, donc l'objectif final du projet n'a pas plus être remplie.
