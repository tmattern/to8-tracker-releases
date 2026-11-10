# TO8 Tracker

### La musique ProTracker sur Thomson TO8 — quatre voix, 8 kHz, 6 bits

**TO8 Tracker** est un lecteur de musiques Amiga au format **ProTracker `.MOD`**, conçu pour les **Thomson TO8 et TO8D équipés d'une extension mémoire de 256 Ko**. Il permet de retrouver les musiques des jeux et des démos Amiga sur un ordinateur équipé d'un **Motorola 6809 à 1 MHz**, sans carte son supplémentaire.

Le lecteur est distribué sous forme d'**images de disquettes `.sd` (SDDrive)** et de **disquettes `.fd` (format disquette Thomson)**. Ces dernières ont été fournies par le **premier utilisateur de TO8 Tracker**.

## À l'origine : le pari Lotus 2

TO8 Tracker est né d'un défi très simple : **réussir à jouer le module musical de Lotus 2 sur un Thomson TO8, et surtout faire en sorte que la musique reste reconnaissable**.

Sur Amiga, les quatre voix audio sont prises en charge par une puce spécialisée. Sur TO8, il fallait tout faire avec un processeur 6809 à seulement 1 MHz et un convertisseur audio très rudimentaire. L'objectif initial n'était donc pas de reproduire parfaitement le son de l'Amiga, mais de retrouver suffisamment de mélodie, de rythme et de timbres pour reconnaître le morceau.

Ce premier pari a progressivement donné naissance à un véritable lecteur ProTracker, capable de charger et de jouer d'autres modules, avec une interface et des commandes dédiées.

## Démonstrations en vidéo

Cliquez sur une miniature pour regarder la vidéo correspondante sur YouTube.

| Hero Quest | SuperCars 2 |
| :---: | :---: |
| <a href="https://youtu.be/0ES_-IJTEjo"><img src="images/hero-quest.jpg" alt="Hero Quest sur TO8 Tracker" width="320"></a> | <a href="https://youtu.be/Hrf9h6qHnBw"><img src="images/supercars-2.jpg" alt="SuperCars 2 sur TO8 Tracker" width="320"></a> |
| **Lotus 2** | **Démo Enigma** |
| <a href="https://youtu.be/qMdo7bRqxaQ"><img src="images/lotus-2.jpg" alt="Lotus 2 sur TO8 Tracker" width="320"></a> | <a href="https://youtu.be/ScSxWaBqgYs"><img src="images/enigma.jpg" alt="Démo Enigma sur TO8 Tracker" width="320"></a> |
| **Cannon Fodder** | **Red Sector** |
| <a href="https://youtu.be/MHNWjYhJxBw"><img src="images/cannon-fodder.jpg" alt="Cannon Fodder sur TO8 Tracker" width="320"></a> | <a href="https://youtu.be/teMMCTpapRQ"><img src="images/red-sector.jpg" alt="Red Sector sur TO8 Tracker" width="320"></a> |

## Ce que permet TO8 Tracker

- **Écouter des modules ProTracker à quatre voies**, avec leurs instruments échantillonnés et leurs boucles.
- **Profiter d'un son numérique à 8 kHz sur 6 bits**, directement sur le convertisseur audio du TO8.
- **Retrouver la hauteur des notes et la vitesse de lecture de l'Amiga PAL** grâce à l'adaptation des échantillons lors du chargement.
- **Lire un large éventail de notes**, y compris les notes étendues utilisées par certains modules.
- **Parcourir les fichiers sur plusieurs unités de disquette**, choisir un morceau et consulter ses instruments.
- **Passer au morceau suivant ou précédent**, mettre en pause et reprendre la lecture.
- **Enchaîner automatiquement les morceaux** avec *PLAY ALL*, ou répéter le morceau en cours.
- **Suivre la lecture à l'écran** : titre du module, instruments, position dans le morceau et progression du pattern.

Le chargement prépare les échantillons pour le TO8. Il peut donc prendre quelques instants avant le début de la musique.

## Un lecteur audio en temps réel, cadencé au cycle près

Le TO8 ne dispose pas d'une puce capable de mélanger directement quatre pistes audio comme l'Amiga. **TO8 Tracker réalise donc le mélange des quatre voix par logiciel**, avec le seul processeur 6809 à 1 MHz, puis envoie le résultat au convertisseur audio de la machine.

La particularité du lecteur est son **cadencement parfaitement régulier** : **un échantillon sonore est émis tous les 125 cycles processeur, exactement**. À 1 million de cycles par seconde, cela donne **8 000 échantillons par seconde (8 kHz)**.

Pour respecter cette cadence, le programme prépare à l'avance les échantillons et organise les calculs de chaque voix, les changements de notes, les volumes et les effets musicaux dans des séquences dont la durée est maîtrisée au cycle près. **Pendant la lecture audio, il n'utilise pas d'interruptions pour déclencher les échantillons** : c'est directement le code du lecteur qui maintient l'intervalle de 125 cycles, sans fluctuation de durée entre deux émissions.

### Pourquoi se passer du timer matériel ?

Le TO8 possède pourtant un **timer matériel très précis, capable de compter les cycles processeur**. Il pourrait déclencher une interruption tous les 125 cycles pour imposer la fréquence de sortie. Cette solution paraît naturelle, mais le temps nécessaire pour entrer dans l'interruption, sauvegarder le contexte, traiter l'événement et reprendre le programme consommerait une part trop importante des 125 cycles disponibles. Il n'en resterait pas assez pour mélanger quatre voies et gérer la musique.

Une autre approche a été expérimentée : **utiliser le timer sans interruption**, en consultant son compteur pour savoir combien de cycles il restait avant la prochaine émission. Le coût était inférieur à celui des interruptions, mais les lectures et les attentes prenaient encore trop de temps sur un processeur à 1 MHz.

Le choix final est donc un **lecteur à exécution déterministe** : le code est organisé et minuté pour que l'émission suivante arrive exactement 125 cycles après la précédente. Il n'y a ni interruption audio ni attente active du timer ; les cycles disponibles servent au travail utile.

### Des instruments plus grands que la mémoire directement accessible

Le 6809 possède un **bus d'adresses de 16 bits** : il ne peut adresser directement que 64 Kio à un instant donné. Le TO8 dispose de davantage de RAM grâce à un système de **banques mémoire**, mais celles-ci sont accessibles au travers de fenêtres de 16 Kio, qu'il faut sélectionner explicitement.

Or, certains instruments ProTracker occupent **plusieurs fois 16 Kio**. Leur lecture ne peut donc pas se résumer à avancer un pointeur dans une zone continue : il faut suivre la position dans l'échantillon, détecter les passages d'une banque à l'autre et modifier le mapping mémoire. Cela s'ajoute à la difficulté de lire simultanément quatre instruments qui peuvent se trouver dans des banques différentes, tout en conservant une cadence audio rigoureuse.

### Le double buffering : produire le son sans interrompre son émission

Ces contraintes ont conduit à une architecture à **deux tampons audio en RAM fixe**, de **160 échantillons chacun**. Pendant qu'un tampon fournit les valeurs envoyées au convertisseur audio, le processeur construit le mélange des quatre voies dans l'autre. À la fin du tick musical de 20 ms, les rôles des deux tampons sont échangés.

Le bénéfice majeur est de pouvoir **traiter les voies une par une**, plutôt que de passer d'un instrument à l'autre pour calculer chaque échantillon final. Le lecteur remplit d'abord le tampon avec la première voie, puis y additionne successivement les trois autres. Comme il travaille pendant un certain temps sur **un seul instrument**, il conserve autant que possible la même banque mémoire et limite fortement les changements de banque. Il peut aussi garder dans les **rares registres du 6809** les pointeurs, la position de lecture et les informations utiles à la voie en cours, sans les sauvegarder puis les recharger à chaque échantillon.

Le double buffering **sépare donc la régularité de l'émission de l'organisation du mixage**. Le travail reste réparti sur les 160 intervalles de 125 cycles du tick : chaque intervalle comporte une émission à date fixe et une part des opérations nécessaires à la suite du morceau. Cette organisation n'augmente pas la puissance du 6809, mais évite d'en perdre une part précieuse en changements de contexte et de banque.

### Du fichier MOD au son : le pipeline audio

Le traitement se déroule en deux phases : une **préparation au chargement**, puis une **lecture en temps réel**.

1. **Lecture et analyse du module.** Le programme lit les instruments, les patterns, les notes et les effets ProTracker. Il repère les notes réellement utilisées et les boucles des instruments.
2. **Adaptation des échantillons.** Les sons d'origine sont rééchantillonnés pour retrouver la hauteur des notes de l'Amiga PAL. Une interpolation et des filtres adaptés à la restitution à 8 kHz limitent les défauts liés à la réduction de fréquence. Selon les notes utilisées, le lecteur prépare différentes versions d'un instrument pour privilégier la qualité sonore.
3. **Préparation en mémoire.** Les échantillons adaptés et les informations nécessaires à la lecture sont répartis dans les banques de RAM du TO8. Le travail coûteux est ainsi réalisé **avant** de démarrer la musique.
4. **Lecture et mélange des quatre voies.** Pendant le morceau, le processeur suit les patterns, fait avancer les quatre instruments à leur hauteur respective, applique les volumes et les effets pris en charge, puis additionne leurs contributions pour obtenir un seul signal mono. Les opérations sont réparties sur les intervalles disponibles, sans interrompre la cadence audio.
5. **Compression du signal de sortie.** L'addition des quatre voies peut produire des valeurs trop fortes pour la sortie audio du TO8. Avant la conversion sur 6 bits, le lecteur applique une **compression de la dynamique** à l'aide d'une table optimisée. L'objectif est de **préserver autant d'informations et de nuances sonores que possible**, notamment dans les passages plus faibles, tout en évitant d'écrêter les pics et de rendre le son excessivement saturé. C'est un compromis entre le niveau sonore, la fidélité et les limites du convertisseur.
6. **Émission vers le DAC.** Le signal compressé est converti sur **6 bits** et envoyé au convertisseur numérique-analogique du TO8, **une fois tous les 125 cycles**, soit **8 000 fois par seconde**.

À **125 BPM**, le lecteur dispose de **160 émissions audio par tick musical de 20 ms**. Même lorsque la musique change de note, de volume ou de pattern, chaque émission conserve sa place dans cette séquence minutée.

Cette régularité est essentielle : sur une machine aussi lente, le moindre décalage entre les échantillons pourrait créer des parasites audibles. Elle permet d'obtenir une restitution stable tout en conservant les quatre voies du morceau original.

### Quelques astuces de programmation pour économiser les cycles

**Traiter deux échantillons à la fois avec le registre D.** Le 6809 possède deux accumulateurs de 8 bits, **A et B**, qui forment ensemble le registre **D de 16 bits**. TO8 Tracker exploite cette particularité à presque toutes les étapes du traitement audio : une instruction peut charger ou enregistrer **deux échantillons de 8 bits d'un coup**, tandis que A et B permettent de les transformer séparément. Cette organisation par paires accélère notamment les parcours de tampons, les traitements par tables et la conversion finale du son sur 6 bits.

**Remplacer les calculs de position par des kernels et des anneaux.** Dans un lecteur classique, chaque voie avance dans son instrument à l'aide d'une position à virgule fixe **8.8** : 8 bits pour la partie entière et 8 pour la fraction. À chaque échantillon, il faut additionner l'avance, gérer le report de la fraction et déterminer le prochain octet à lire. Répéter ces calculs pour quatre voies coûterait trop de cycles.

TO8 Tracker utilise donc des **kernels de lecture et de rééchantillonnage spécialisés** selon les avances nécessaires aux notes. Des **anneaux de valeurs pré-calculées** représentent la succession périodique des positions fractionnaires : au lieu de refaire l'arithmétique 8.8 pendant la lecture, le processeur suit ces séquences et exécute directement les accès prévus. Le gain est considérable sur le chemin critique : les opérations de calcul d'avance sont largement remplacées par des accès et des instructions beaucoup plus simples, dont le temps d'exécution est connu.

Cette vitesse se paie par un compromis assumé : **de nombreuses variantes de kernels, des tables et des anneaux occupent beaucoup de mémoire**, auxquels s'ajoutent les différentes représentations préparées des instruments. Le programme à charger est ainsi **volumineux pour un TO8**, mais ce surcoût en RAM et en taille de binaire permet de préserver les cycles indispensables au mélange audio en temps réel.

## Démarrer le lecteur

### Sur DCMoto

Pour une meilleure restitution sonore, privilégier la **version de DCMoto adaptée à la sortie 8 kHz**, présentée ci-dessous.

Dans le menu **Options** de DCMoto, sélectionner également la **fréquence audio de 8 kHz**. Ce réglage est nécessaire pour profiter de la sortie synchronisée sur les 125 cycles du lecteur.

1. Choisir un **Thomson TO8 ou TO8D**.
2. Activer l'extension **RAM 256 Ko**.
3. Configurer le lecteur correspondant au format de l'image (`.sd` ou `.fd`).
4. Insérer l'image de disquette de TO8 Tracker et démarrer la machine.

**Sur TO8 avec une image `.sd`**, activer l'extension **SDDRIVE** et insérer l'image dans SDDrive.

#### Particularité du TO8D : choisir le bon lecteur

Le TO8D dispose de son propre lecteur de disquette. Dans DCMoto, il faut sélectionner le périphérique correspondant au type d'image utilisé.

**Pour une image `.sd` (SDDrive) :**

1. Dans **Options**, sous **Contrôleur externe**, sélectionner **SDDrive**.
2. Dans **Supports amovibles**, charger l'image dans **Fichier sur carte SD**.
3. Dans **Périphériques connectés**, **décocher Lecteur de disquette** et **cocher Lecteur SDDrive**.

**Pour une image `.fd` (disquette) :**

1. Dans **Supports amovibles**, charger l'image dans **Disquette**.
2. Dans **Périphériques connectés**, **cocher Lecteur de disquette** et **décocher Lecteur SDDrive**.

### Sur une vraie machine

Utiliser un **TO8 ou TO8D avec extension mémoire de 256 Ko**. Les images `.sd` sont destinées à une interface **SDDrive** ; sur TO8D, les images `.fd` correspondent au format du lecteur de disquette intégré.

**À noter :** les fichiers fournis sont des images de disquettes, et non des programmes à lancer directement sur un PC.

## DCMoto : une sortie audio adaptée au lecteur

Le lecteur de TO8 Tracker a une particularité : il envoie un échantillon sonore **tous les 125 cycles du 6809**, soit exactement **8 000 échantillons par seconde**. Pour entendre le résultat le plus fidèlement possible dans un émulateur, il est important que la capture du son respecte cette cadence.

La sortie audio de DCMoto utilisait auparavant un échantillonnage à **25 kHz**. Cette fréquence ne correspond pas à celle du lecteur : elle impose un rééchantillonnage susceptible d'ajouter des **artéfacts dans les hautes fréquences**, qui ne proviennent pas du signal émis par le TO8 Tracker lui-même.

**L'auteur de DCMoto a spontanément proposé d'adapter l'émulateur**, puis a réalisé très rapidement cette modification pour proposer une sortie à **8 kHz, synchronisée sur les 125 cycles** du lecteur. Cette adaptation évite autant que possible les artéfacts dans les aigus liés à la conversion vers 25 kHz et permet de mieux apprécier le rendu audio réel du programme.

**Important :** après avoir installé cette version adaptée de DCMoto, il faut choisir **8 kHz dans le menu Options** : la fréquence ne doit pas rester à 25 kHz. **Une prochaine version officielle de DCMoto devrait également proposer le choix de 8 kHz**, sans nécessiter cette version spécifique.

- **[Télécharger la version de DCMoto avec sortie audio à 8 kHz](https://forum.system-cfg.com/download/file.php?id=49340)**
- [Lire la discussion complète sur le forum system-cfg](https://forum.system-cfg.com/viewtopic.php?f=25&t=7847)

## Les commandes

| Depuis | Touche | Action |
|---|---|---|
| Navigateur | **Flèches** | Sélectionner un module ; haut/bas aux extrémités pour changer de page |
| Navigateur | **ENTRÉE** | Charger le module sélectionné |
| Navigateur | **P** | **PLAY ALL** : lancer la lecture en série des modules |
| Navigateur | **R** | Actualiser la liste des fichiers |
| Navigateur | **H** | Afficher l'aide |
| Aide | **E**, **M** ou **ENTRÉE** | Revenir au navigateur |
| Lecture | **ENTRÉE** ou **ESPACE** | Lecture, pause ou reprise |
| Lecture | **N** / **P** | Module suivant / précédent |
| Lecture | **M** | Retour au navigateur |
| Lecture ou pause | **Flèches gauche/droite** | Reculer ou avancer dans les positions du morceau |
| Lecture ou pause | **4** | Revenir au début des positions |
| Navigateur ou lecture | **L** | Changer le mode de répétition |
| Navigateur ou lecture | **A** | Activer ou désactiver la lecture automatique |

### Répétition et lecture automatique

La touche **L** fait défiler trois modes :

- **OFF** : lecture simple, sans répétition.
- **ALL** : enchaînement et répétition de la liste des modules.
- **ONE** : répétition du module courant.

La touche **A** détermine si la lecture démarre automatiquement après le chargement d'un module. Elle est **activée par défaut**.

## Compatibilité des effets ProTracker

TO8 Tracker reconnaît les notes et les instruments des modules ProTracker classiques à **quatre canaux**, mais il ne reproduit pas encore tous les effets du logiciel Amiga. Voici ce qui est effectivement pris en charge dans cette version.

| Effet MOD | Fonction | Prise en charge |
|---|---|---|
| `0xy` | Arpège | ❌ Non |
| `1xx` | Glissement de hauteur vers l'aigu (*slide up*) | ✅ Oui |
| `2xx` | Glissement de hauteur vers le grave (*slide down*) | ✅ Oui |
| `3xx` | Portamento vers une note cible | ✅ Oui |
| `4xy` | Vibrato | ⚠️ Partiel : un vibrato actif à la fois, selon la vitesse de lecture |
| `5xy` | Portamento + glissement de volume | ⚠️ Partiel : **portamento uniquement** |
| `6xy` | Vibrato + glissement de volume | ⚠️ Partiel : **glissement de volume uniquement** |
| `7xy` | Trémolo | ❌ Non |
| `8xx` | Position stéréo (*panning*) | ❌ Non : sortie audio mono |
| `9xx` | Décalage de départ d'un échantillon | ❌ Non |
| `Axy` | Glissement progressif du volume | ✅ Oui |
| `Bxx` | Saut vers une position du morceau | ❌ Non |
| `Cxx` | Réglage du volume d'une voie | ✅ Oui |
| `Dxx` | Passage à une autre ligne du pattern suivant | ⚠️ **`D00` uniquement** |
| `Exy` | Famille des effets étendus ProTracker | ❌ Non |
| `Fxx` | Vitesse de lecture / tempo | ⚠️ Vitesses **3 à 31** ; changement de BPM non pris en charge |

**Précisions sur les limites :**

- Le tempo reste fixé à **125 BPM**. Les commandes `F20` et supérieures, qui demandent un autre BPM, sont ignorées.
- Les commandes `F00`, `F01` et `F02` sont ramenées à la vitesse minimale `F03`.
- La vitesse **3** utilise un mode dégradé : notamment, le vibrato est désactivé et l'indication de progression du pattern est limitée.
- Pour `Axy`, lorsqu'une montée et une descente de volume sont demandées simultanément, aucun glissement n'est appliqué.
- Certaines combinaisons de changements de hauteur et de vibrato restent limitées par les capacités de lecture simultanée du TO8.
- Les effets non pris en charge sont ignorés : les notes et les instruments restent joués.

## Ajouter ses propres musiques

Les modules livrés sur les disquettes sont fournis **tels quels**, sans modification des fichiers d'origine. Ils servent d'exemples : **vous êtes encouragé à les remplacer ou à en ajouter d'autres**.

On trouve de nombreux modules sur les sites suivants :

- [The Mod Archive](https://modarchive.org/) — grande bibliothèque de musiques au format tracker.
- [Modland — répertoire ProTracker](https://modland.com/pub/modules/Protracker/) — classement par compositeur.
- [Amiga Music Preservation](https://amp.dascene.net/home.php) — archives de musiques Amiga.

Privilégiez les fichiers **ProTracker `.MOD` à quatre voies**. Les formats `.XM`, `.S3M`, `.IT` ou les MOD utilisant davantage de voies ne sont pas pris en charge.

Pour modifier le contenu d'une image de disquette, vous pouvez utiliser **DCFDUTIL** (également appelé DCFutil), disponible sur le [site de DCMoto](https://dcmoto.pages-perso.free.fr/emulateur/index.html). Cet outil permet de copier des fichiers sur les disquettes. **Il ne permet pas de les supprimer** : pour effacer un fichier, utilisez le **moniteur du TO8**.

L'espace libre d'une disquette et la mémoire de la machine restent des limites : tous les modules, notamment les plus volumineux, ne pourront pas être chargés.

## Quelques informations techniques

| Caractéristique | TO8 Tracker |
|---|---|
| Ordinateurs | Thomson TO8 / TO8D |
| Processeur | Motorola 6809 à 1 MHz |
| Format musical | ProTracker `.MOD`, quatre canaux |
| Restitution sonore | PCM mono, **8 000 Hz**, **6 bits** |
| Adaptation des échantillons | Rééchantillonnage et filtrage lors du chargement |
| Tempo | 125 BPM fixes ; vitesse par ligne ajustable |
| Distribution | Images de disquettes **SDDrive `.sd`** et **disquettes `.fd`** |
| Mémoire | Extension **256 Ko** requise, soit **512 Ko de RAM au total** |

La restitution est adaptée aux possibilités du TO8 : elle ne peut donc pas être parfaitement identique à celle d'un Amiga, notamment pour les fréquences aiguës et les effets non gérés.

## Les principales difficultés rencontrées

**Faire tenir quatre voix dans 125 cycles.** Sur Amiga, le matériel lit et mélange les échantillons. Sur TO8, chaque valeur sonore doit être calculée par le 6809. Il faut donc partager le temps processeur entre les quatre voies, les changements de notes et les effets, tout en conservant exactement le même intervalle entre deux sorties audio. À 1 MHz, chaque cycle compte.

**Garder un son reconnaissable à 8 kHz et sur 6 bits.** La réduction de la fréquence d'échantillonnage peut rendre certains instruments métalliques ou produire des grésillements, surtout dans les aigus. Différentes méthodes de rééchantillonnage, d'interpolation et de filtrage ont été étudiées pour limiter ces défauts. Il faut trouver un compromis : préserver les détails audibles sans dépasser les capacités de la machine.

**Retrouver la hauteur des notes de l'Amiga.** Le lecteur du TO8 repose sur une cadence fixe de **8 kHz, soit 160 échantillons par tick de 20 ms**. Or cette cadence ne correspond pas exactement à la vitesse de lecture des échantillons par la puce Paula de l'Amiga PAL. Sans correction, les notes seraient légèrement décalées, ce qui devient particulièrement gênant dans les morceaux qui enchaînent plusieurs instruments pour former une même phrase musicale. Deux approches étaient possibles : corriger cette différence directement pendant la lecture en modifiant l'avance des échantillons, avec une méthode **nearest neighbor** qui saute certains échantillons et peut accentuer les défauts de décimation, ou **rééchantillonner légèrement les instruments dès le chargement**, avec une interpolation de meilleure qualité. C'est cette seconde solution qui a été retenue pour l'adaptation à la cadence Amiga : elle permet de retrouver la bonne hauteur des notes sans ajouter de calculs au lecteur temps réel, qui continue à utiliser le **nearest neighbor, sans interpolation**.

**Organiser la mémoire malgré les banques de 16 Kio.** Les modules contiennent des échantillons parfois volumineux, des boucles et des patterns, alors que le 6809 ne peut accéder qu'à une partie de la RAM à la fois. Le lecteur doit répartir ces données sans empiéter sur le programme, l'affichage ou les zones du système.

La solution retenue exploite **deux fenêtres de mémoire bancarisée accessibles simultanément** : l'une accueille les **anneaux et les kernels de rééchantillonnage**, l'autre les **échantillons des instruments**. Chaque fenêtre peut sélectionner la banque nécessaire à la voie en cours. **Le reste du moteur audio réside en RAM fixe**, toujours accessible, même lorsque les banques changent. Cette séparation évite que la lecture d'un instrument fasse disparaître le code en cours d'exécution.

L'**interface graphique réside elle aussi dans une banque mémoire distincte**. Pour y revenir après le chargement, la préparation ou la lecture, le programme passe par de petits **trampolines en RAM fixe** : ces routines rétablissent le bon mapping mémoire, puis transfèrent l'exécution vers l'IHM. Sans ce détour, le processeur risquerait de poursuivre son exécution à une adresse dont le contenu aurait changé de banque.

**Reproduire les effets sans casser le rythme.** Le portamento, le vibrato ou les glissements de volume modifient le son pendant qu'il est joué. Leur prise en charge doit rester compatible avec le calendrier extrêmement strict des émissions audio. Certains effets ProTracker ont donc été adaptés ou simplifiés, tandis que d'autres restent à implémenter.

**Conserver une interface utilisable pendant la lecture.** Naviguer entre les morceaux, afficher la progression ou réagir au clavier ne doit pas perturber le son. Le lecteur sépare ainsi les opérations de chargement et d'affichage des traitements audio qui exigent une précision au cycle près.

Face à l'**Amiga 500**, le TO8 n'a pourtant pas grand-chose pour lui sur le plan sonore :

- **Aucune puce audio dédiée** pour lire et mélanger les quatre voies à la place du processeur.
- **Un processeur 8 bits à seulement 1 MHz**, chargé de tous les calculs.
- **Un bus d'adresses de 16 bits**, qui impose de jongler avec les banques mémoire.
- **Aucun canal DMA** pour transférer les échantillons sans mobiliser le processeur.
- **Un convertisseur audio mono sur 6 bits**, loin des capacités sonores de l'Amiga.

Mais, comparé à beaucoup d'autres micro-ordinateurs 8 bits, le TO8 dispose d'un atout décisif : **512 Ko de RAM avec son extension mémoire**. TO8 Tracker exploite cette capacité pour **préparer les échantillons, précompiler les kernels de rééchantillonnage et stocker leurs anneaux**. En acceptant d'utiliser beaucoup de mémoire et de charger un programme volumineux, il économise les cycles qui manqueraient autrement pendant la lecture.

Le défi n'est donc pas seulement de produire du son sur un TO8 : **c'est de rendre reconnaissable un morceau Amiga à quatre voix sur une machine qui n'a presque aucun des moyens matériels prévus pour cela**.
