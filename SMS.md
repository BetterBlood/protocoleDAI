# SMS (Send Message Simply)

## 1. Table des matières
1. [Table des matières](#1-table-des-matières)
2. [Introduction](#2-introduction)
3. [But du protocole](#3-but-du-protocole)
4. [Ports](#4-ports)
5. [Protocoles nécessaires](#5-protocoles-nécessaires)
6. [Initialisation de la connection](#6-initialisation-de-la-connection)
7. [Messages et Actions autorisés](#7-messages-et-actions-autorisés)
8. [Formats des Messages et Actions](#8-formats-des-messages-et-actions)
9. [Cas d'erreur et message non conforme](#9-cas-derreur-et-message-non-conforme)
10. [Diagramme](#10-diagramme)

## 2. [Introduction](#1-table-des-matières)
Ce protocole est purement théorique, il a été conçu dans le cadre du cours DAI dans le chapitre concernant les protocoles.

## 3. [But du protocole](#1-table-des-matières)
Le but de ce protocole est de pouvoir envoyer de simple message text entre utilisateur connecté via un server, nous avons donc une architecture client-serveur.
L'encodage des charactères se fait en UTF-8.

## 4. [Ports](#1-table-des-matières)
Le transfert des messages ce fait par le port 1234.

## 5. [Protocoles nécessaires](#1-table-des-matières)
SMS se base sur les protocoles suivants :
- Transmission Control Protocol (TCP)

## 6. [Initialisation de la connection](#1-table-des-matières)
La connection est initialisée pas le client.

## 7. [Messages et Actions autorisés](#1-table-des-matières)

### 7.1. Côté client

#### 7.1.1. Connection
Un Client utilise le message de connection pour initialiser une connection avec le serveur, le client fourni son nom d'utilisateur, la connection est validée par le serveur avec un message de validation de connection. Dans ce cas le client peut commencer à envoyer des messages à des destinataires. La connection peut être refusée par le serveur, dans ce cas un message de refus est envoyé en retour.

#### 7.1.2. Envoyer un message
Un client préalablement connecté peut envoyer des messages à d'autre utilisateurs connectés, le destinataire doit être mentionné. Le serveur envoi une confirmation, ou un message d'erreur le cas échéant.

#### 7.1.3. Deconnection
Un client peut choisir de se déconnecter à tout moment, il n'est, dès la reception par le serveur, plus considéré comme connecté. Le serveur confirme la déconnection

#### 7.1.4. Confirmation de Reçu
Le client envoye une confirmation de reçu quand il reçoit un message.

### 7.2. Côté serveur

#### 7.2.1. Confirmation de connection
Le serveur envoye une confirmation de connection quand un client c'est correctement authentifié

#### 7.2.2. Infirmation de connection
Le serveur envoye un refus de connection dans les cas suivants :
- nom d'utilisateur non fournit
- nom d'utilisateur déjà utilisé
- nombre d'utilisateur trop élevé
L'information concernant le refus est fournit au client

#### 7.2.3. Confirmation de transmission
Le serveur envoie une confirmation de transmission si le message du client peut être délivré.

#### 7.2.4. Infirmation de transmission
Le serveur envoie un refus de transmission dans les cas suivants :
- le nom du client n'est pas dans la liste des Clients connectés
- le nom de destination n'est pas dans la liste des Clients connectés
- message vide
- le message dépasse la limite max de charactère

#### 7.2.5. Transmition d'un message
Le serveur envoye le message au destinataire en notifiant le nom de l'envoyeur, une confirmation de reçu est de mandée

#### 7.2.6. Confimation de déconnection
##### 7.2.6.1. Client connecté
Le serveur envoie une confirmation lorsq'un Client connecté souhaite se déconnecter

##### 7.2.6.2. Client non connecté :
si l'utilisateur n'est pas connecté, le serveur le lui fait savoir 


## 8. [Formats des Messages et Actions](#1-table-des-matières)

### 8.1. Connection
#### 8.1.1 Format
```CONNECT <username>``` 

exemples : 
- ```CONNECT user1```
- ```CONNECT user2```

#### 8.1.2 Confirmation de connection
```CONFIRME <demande>``` 

exemple : 
- ```CONFIRME CONNECTION```

#### 8.1.3 Infirmation de connection
```REFUSE <demande> <reason>``` 

exemples : 
- ```REFUSE CONNECTION MISSING_USERNAME```
- ```REFUSE CONNECTION ALREADY_USED_USERNAME```
- ```REFUSE CONNECTION TOO_MANY_USERS```

### 8.2 Envoie de message
#### 8.2.1 Format
```SEND <username> <message>``` 

exemple : 
- ```SEND user1 salut```

#### 8.2.2 Confirmation de message
```CONFIRME <demande>``` 

exemple : 
- ```CONFIRME MESSAGE```

#### 8.2.3 Infirmation de message
```REFUSE <demande> <reason>``` 

exemples : 
- ```REFUSE MESSAGE NOT_KNOWN_SENDER```
- ```REFUSE MESSAGE NOT_KNOWN_DESTINATION```
- ```REFUSE MESSAGE EMPTY```
- ```REFUSE MESSAGE TOO_MANY_CHARS```

### 8.3 Deconnection
#### 8.3.1 Format
```DISCONNECT``` 

exemple : 
- ```DISCONNECT```

#### 8.3.2 Confirmation de déconnection
```CONFIRME <demande>``` 

exemple : 
- ```CONFIRME DISCONNECTION```


#### 8.3.3 Infirmation de déconnection
```REFUSE <demande>``` 

exemple : 
- ```REFUSE DISCONNECTION```

En réalité rien ne se passe ici, puisque le user n'est pas connecté on lui indique simplement qu'il ne peut pas se déconnecter puisqu'il ne l'est pas


### 8.4 Transmition de message 
#### 8.4.1 Format
```TRANSMISSION <from_user> <message>``` 

exemple : 
- ```TRANSMISSION user1 salut```

#### 8.4.2 Reçu du Client
```CONFIRME <demande>``` 

exemple : 
- ```CONFIRME TRANSMISSION```

Pour l'instant pas utilisé, il serait possible de proposé un accusé de reception à l'envoyeur du message initial dans une prochaine version.


## 9. [Cas d'erreur et message non conforme](#1-table-des-matières)

### 9.1 Erreur de commande 
les messages non reconnu ont pour réponse un refus

```REFUSE <demande>``` 

exemples :

```REFUSE UNKNOWN_COMMANDE```

### 9.2 Erreur d'utilisation de commande 
Voir chapitre [7](#7-messages-et-actions-autorisés) et plus particulièrement [8](#8-formats-des-messages-et-actions)

#### 9.2.1 Erreurs prévues
##### 9.2.1.1 Connection
voir section [8.1. Connection](#81-connection)

##### 9.2.1.2 Envoie de message
voir section [8.2. Envoie de message](#82-envoie-de-message)

##### 9.2.1.3 Envoie de message
voir section [8.3. Deconnection](#83-deconnection)

##### 9.2.1.4 Transmition de message
voir section [8.4 Transmition de message](#84-transmition-de-message)

## 10. [Diagramme](#1-table-des-matières)

```
@startuml
Client1 -> Server: CONNECT user1
Server --> Client1: CONFIRME CONNECTION

Client2 -> Server: CONNECT user2
Server --> Client2: CONFIRME CONNECTION

Client1 -> Server: SEND user2 salut
Server --> Client1: CONFIRME MESSAGE

Server -> Client2: TRANSMISSION user1 salut
Client2 --> Server: CONFIRME TRANSMISSION

Client1 -> Server: CONNECT user1
Server --> Client1: REFUSE CONNECTION ALREADY_USED_USERNAME

Client1 -> Server: DISCONNECT
Server --> Client1: CONFIRME DISCONNECTION

Client1 -> Server: SEND user2 salut
Server --> Client1: REFUSE MESSAGE NOT_KNOWN_SENDER

Client2 -> Server: SEND user1 salut
Server --> Client2: REFUSE MESSAGE NOT_KNOWN_DESTINATION

Client1 -> Server: DISCONNECT
Server --> Client1: REFUSE DISCONNECTION

Client2 -> Server: DISCONNECT
Server --> Client2: CONFIRME DISCONNECTION
@enduml
```
![diagramme](https://github.com/BetterBlood/protocoleDAI/blob/main/diagramme.PNG)
