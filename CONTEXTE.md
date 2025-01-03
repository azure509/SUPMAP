# SUPMAP 


# 1 - Contexte du projet
La société fictive Trafine souhaite développer une application de navigation en temps réel, similaire à Waze, qui permettrait aux utilisateurs de recevoir des informations sur la circulation, les accidents et les obstacles.
Cette application devra également permettre aux utilisateurs de signaler des incidents et de recevoir des itinéraires optimisés en fonction des conditions de circulation. 
Le projet devra inclure une application mobile pour les utilisateurs, une interface web pour la gestion et la visualisation des données en temps réel, ainsi qu'une architecture de micro-services pour gérer les différentes fonctionnalités. 
Pour la bêta, Trafine souhaite se concentrer sur les trajets en France métropolitaine. 
 
# 2 - Description du projet 
# 2.1 - Généralités 
L'objectif principal de l'application est de permettre aux utilisateurs de : 
•	Naviguer en temps réel avec des itinéraires optimisés. 
•	Recevoir des alertes sur les conditions de circulation (bouchons, accidents, routes fermées). 
•	Signaler des incidents (accidents, embouteillages, contrôles policiers). 
•	Contribuer à la communauté en validant ou en infirmant des signalements faits par d'autres utilisateurs. 
•	Choisir leur itinéraire en fonction de son coût (éviter les péages par exemple). 

# 2.2 - Fonctionnalités de l'application à implémenter 
# 2.2.1 - Connexion 
Les utilisateurs pourront se connecter via un compte spécifique ou en utilisant un service OAuth2 (Google, Facebook, etc.). 


# 2.2.2 - Navigation en temps réel 
L'application devra offrir : 
•	Calcul d'itinéraires : En fonction de la destination de l'utilisateur, l'application doit calculer le meilleur itinéraire en tenant compte du trafic en temps réel. 
•	Recalcul automatique des itinéraires : En cas de bouchons ou d'incidents, l'itinéraire devra être mis à jour automatiquement. 
•	Affichage de la carte : L'itinéraire et les informations doivent être affichés sur une carte en temps réel. 
 
# 2.2.3 - Signalement d'incidents 
Les utilisateurs pourront signaler les incidents suivants : 
•	Accidents 
•	Embouteillages 
•	Routes fermées 
•	Contrôles policiers 
•	Obstacles sur la route 
Les autres utilisateurs pourront confirmer ou infirmer ces signalements. 


# 2.2.4 - Gestion des alertes 
Les utilisateurs recevront des alertes basées sur leur itinéraire en temps réel (embouteillages, accidents, etc.). 


# 2.2.5 - Contributions communautaires 
Chaque utilisateur pourra valider ou infirmer des signalements d'incidents faits par d'autres utilisateurs, augmentant ainsi la fiabilité de l'application. 


# 2.2.6 - Partage d’itinéraires 
Les utilisateurs de la version web pourront envoyer directement leurs itinéraires sur leur application mobile. 
Ils pourront aussi générer un code QR qui chargera les itinéraires proposés dans l’application mobile. 
 
# 2.3 - Sécurité 
La sécurité est un aspect crucial de ce projet, en particulier pour garantir l'intégrité des données et la protection des utilisateurs. Les exigences suivantes doivent être respectées : 
2.3.1 - Authentification et gestion des identités 
•	Authentification : Utilisation d'un mécanisme d'authentification sécurisé avec prise en charge de OAuth2. 
•	Gestion des permissions : Les utilisateurs doivent avoir des permissions différenciées selon leur rôle (par exemple, contributeur, modérateur). 

# 2.3.2 - Chiffrement 
•	Chiffrement des données sensibles : Toutes les informations personnelles des utilisateurs doivent être chiffrées en base de données (par exemple, mots de passe et informations de connexion). 
•	Chiffrement des communications : Utilisation du protocole HTTPS pour toutes les communications entre le client et le serveur. 

# 2.3.3 - Protection contre les attaques 
•	Protection contre les injections SQL : Utilisation de requêtes préparées pour éviter toute injection SQL. 
•	Protection contre les attaques CSRF et XSS : Mise en place des protections nécessaires pour sécuriser l'interface web contre ces types d'attaques. 
 
# 2.4 - Data 
Le projet doit intégrer une gestion efficace des données, à la fois pour l'analyse des données de trafic et la persistance des informations. 


# 2.4.1 - Stockage des données 
•	Base de données : Les incidents signalés par les utilisateurs, les itinéraires et les informations de trafic en temps réel doivent être stockés de manière efficace et scalable. La base de données doit être capable de traiter un grand volume de données provenant de nombreuses sources. 

# 2.4.2 - Analyse et statistiques 
•	Analyse des données de trafic : Une section de l'interface web devra permettre d'analyser les données de trafic en temps réel. Ces statistiques incluront le nombre d’incidents signalés, les périodes de congestion, etc. 

# 2.4.3 - Prédiction des embouteillages 
•	Prédiction basée sur les données historiques : Utiliser les données accumulées pour prédire les conditions de circulation futures (par exemple, anticipation des heures de pointe ou des embouteillages réguliers). 
 
# 2.5 - Déploiement 
# 2.5.1 - Architecture 
Votre application doit comporter trois briques distinctes : 
•	Un serveur (API REST ou GraphQL) qui gère les itinéraires, les signalements d'incidents, et les données de trafic en temps réel. 
•	Un client web pour la gestion et visualisation des données. 
•	Un client mobile (Android/iOS) pour la navigation et les signalements. 
•	Une base de données (choix libre) pour stocker les itinéraires, les signalements et les informations sur le trafic. 

# 2.5.2 - Containérisation 
Le projet devra inclure un fichier docker-compose.yml permettant le déploiement des différents services (serveur, client, base de données). La containérisation devra fonctionner en l'état, sans qu'il soit nécessaire de lancer des programmes manuellement. Toute containérisation défaillante ou nécessitant une intervention manuelle entraînera un ajournement du projet. 

