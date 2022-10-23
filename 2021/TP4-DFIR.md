# TP4 - Digital Forensics & Incident Response

_Sébastien Mériot ([@smeriot](https://twitter.com/smeriot))_

Durée: 2 heures

Préparation de l'environnement
==============================

Pour ce TP, contrairement aux précédents, nous n'utiliserons pas la plateforme [MI-LXC](https://github.com/flesueur/mi-lxc). Voici les outils dont vous aurez besoin:
- Un éditeur de texte
- Un tableau (Excel, Open Office, ou autre ...)
- GPG (vous pouvez utiliser une machine virtuelle sous Linux, la plateforme _MI-LXC_ ou autre);
- Ce que vous aurez appris pendant les cours et les TPs ;

Ce TP représente l'évaluation finale du module. Un compte-rendu vous est demandé expliquant votre raisonnement et comment vous êtes parvenus au résultat. Ne perdez pas de temps, vous devrez rendre le compte-rendu à la fin du TP.

**Vous devez impérativement travailler par groupe de 3 à 4. Les compte-rendus ne respectant pas cette contrainte ne seront pas notés.**

Bon courage.

Timing
======

Afin de vous aider à gérer votre temps, une estimation du temps à passer sur chaque question est indiquée.

Scénario
========

Vous êtes consultant. Votre société a été sollicitée par l'entreprise "target" à la suite d'une cyberattaque qui a complètement paralysé leur infrastructure survenue dimanche 24 octobre au soir. D'après les premières informations récoltées, un _ransomware_ aurait été déployé pendant le week-end et toutes les données ont été chiffrées.

Vous avez été choisi pour procéder à la réponse à incident de sécurité, notamment pour:
- Tenter de récupérer les données les plus critiques de l'entreprise "target" si cela est possible ;
- Récolter les éléments permettant d'identifier les attaquants ;
- Analyser la configuration qui était en place lors de la cyberattaque et proposer un vaste plan d'action permettant de rendre l'entreprise "target" plus résiliente à l'avenir face aux menaces cyber

Récupération des données critiques
==================================

_Durée estimée: 1 heure_

Par chance, un administrateur réseau avait lancé une capture réseau vendredi et ne l'avait pas coupé. Par conséquent, une capture réseau du week-end est disponible. L'équipe technique a pu isoler les flux de dimanche soir. Il y en a peu et la majorité sont chiffrés. Par conséquent, ils n'ont extrait que les flux sous forme d'un fichier CSV.

Cette même équipe technique a collecté tous les fichiers qu'ils ont pu pour que vous puissiez mener à bien votre mission : déchiffrer le fichier critique de l'entreprise "target". Vous trouverez l'archive contenant tous les éléments à [cet endroit](https://github.com/PandiPanda69/edu-hei-secu/raw/main/artefacts.zip). Tous les fichiers ne sont pas forcément utiles pour parvenir à notre fin.

Cette archive contient les fichiers suivants qui contiennent les données du dimanche soir :
| Nom du fichier    | Description |
| :---------------: | :---------- |
| auth_filer.log    | Fichier de logs système des authentifications pour la machine _target-filer_. |
| auth_intranet.log | Fichier de logs système des authentifications pour la machine _target-intranet_. |
| auth_router.log   | Fichier de logs système des authentifications pour la machine _target-router_. |
| bash_history_filer | Fichier bash_history contenant les dernières commandes saisies sur la machine _target-filer_. |
| bash_history_intranet | Fichier bash_history contenant les dernières commandes saisies sur la machine _target-intranet_. |
| bash_history_router | Fichier bash_history contenant les dernières commandes saisies sur la machine _target-router_. |
| database.CRYPTED  | Base de données chiffrée que l'entreprise "target" souhaite déchiffrer. |
| dns.log           | Fichier de logs du service DNS s'exécutant sur la machine _target-router_. |
| httpd.log         | Fichier de logs du service web s'exécutant sur la machine _target-intranet_. |
| netflows.csv      | Flux capturés au format CSV délimités par des ';'. La première ligne est le nom des différentes colonnes. |


Les équipes techniques de l'entreprise "target" nous ont transmis l'architecture de leur réseau afin de mieux interpréter les traces réseau.

| Machine           | Description                                         | IP     |
| :-------:         | -----------                                         | :-----: 
| target-router     | Routeur                                             | Pub: 100.64.0.10 / Priv: 100.80.0.1 |
| target-admin      | Ordinateur de l'administrateur système              | 100.80.0.4 |
| target-commercial | Ordinateur du commercial                            | 100.80.0.2 |
| target-dev        | Ordinateur du développeur                           | 100.80.0.3 |
| target-dmz        | Serveur dans la DMZ, exposant notamment un extranet | 100.80.1.2 |
| target-ldap       | LDAP de l'entreprise                                | 100.80.0.10 |
| target-filer      | Serveur de stockage de l'entreprise                 | 100.80.0.6 |
| target-intranet   | Serveur web de l'intranet                           | 100.80.0.5 |


> __Pour répondre aux questions, notez scrupuleusement toutes les IPs, nom de domaine, URL et mots de passe que vous identifiez. Ils pourraient être utiles.__

## Analyse du chiffrement (5min)

1. En analysant le fichier _database.CRYPTED_, déduisez la méthodologie utilisée par l'attaque pour chiffrer les données.
2. Savez-vous dire s'il s'agit d'un chiffrement symétrique ou à l'inverse, asymétrique ? Pourquoi ?
3. De quoi avons-vous besoin pour déchiffrer ce fichier ?

## Analyse des netflows (15min)

4. A partir du fichier _netflows.csv_, consolidez les informations présentes à l'aide des autres fichiers à votre disposition dans l'archive afin de faire figurer 4 informations supplémentaires :
- le protocole potentiellement utilisé (HTTP(S), SMTP, SSH, ...) ;
- le nom de la machine du réseau _target_ associée à l'IP **OU** le nom de domaine associé à l'IP (si récupérable) ;
- la date et l'heure (si récupérable)

6. En vous appuyant sur votre travail précédent, identifiez le ou les flux qui vous paraissent anormaux (vous pouvez par exemple les surligner dans votre tableau final).

Incluez le résultat final dans votre compte-rendu.

## Les actions de l'attaquant (25min)

> Il nous est indiqué que le fichier chiffré est présent sur la machine _target-intranet_ et qu'il aurait été chiffré sur cette même machine. Nous allons donc nous concentrer sur celle-ci.

7. En vous appuyant sur les investigations précédentes, identifiez la commande qui a déclenché le(s) flux réseau anormaux.
8. A quoi sert cette commande ?
9. Quelle est la commande qui suit ? A quoi cela correspond ?
10. Est-ce que la commande à la question 8. fonctionne toujours ? Pouvez-vous la rejouer ?
11. Analysez le fichier obtenu. De quel type de fichier s'agit-il ?
12. Ouvrez le et commentez la ligne 3. Le commentaire suivant vous aidera très certainement.

> Le [Base64](https://fr.wikipedia.org/wiki/Base64) est un mécanisme pour encoder des données. Il est souvent utilisé pour obfusquer du code et le rendre plus difficile à analyser. Pour décoder ce type de chaîne, vous pouvez utiliser la commande `base64 -d` sous Linux, `base64 -D` sous Mac ou bien un outil en ligne.

13. Décodez dans un fichier les données encodées. Quel type de fichier obtenez-vous ?
14. Détaillez chaque ligne du fichier obtenu.
15. Pourquoi la ligne 3 est-elle si intéressante ?
16. Est-ce que la ligne 3 fonctionne toujours ?

## Déchiffrer le fichier (ou pas) (5min)

17. En faisant preuve d'imagination et en utilisant la technique du [Directory Listing](https://tecadmin.net/disable-directory-listing-apache/), essayez de récupérer ce qui avait été identifié à la question 3. pour déchiffrer le fichier _database.CRYPTED_ ? Comment procédez-vous ?
18. Déchiffrez les données en expliquant la manipulation.

## Conclusion (10min)

19. Pour conclure vos observations, récapitulez les différents _indicateurs de compromission_ que vous avez pu identifier lors de vos investigations. Voici un exemple de tableau vous montrant ce qui est attendu :

| #      | Indicateurs                         | Type.      | Commentaire |
| :----: | -----------                         | :-----:    | :-----:
| 1      | 1.2.3.4                             | IP         | IP de l'attaquant |
| 2      | domaine.fr                          | Domaine    | Nom de domaine hébergeant  ... |
| 3      | https://domaine.fr/bad              | URL        | URL malveillante qui fait ... |
| 4      | 39e913e02b0940fea4359cc55d88d6ee    | Hash       | Hash du fichier malveillant qui fait ... |
| n      | ...                                 | ...        | ... |

20. Connaissez-vous l'identité précise de l'attaquant ?
21. (bonus) Comment l'attaquant avait accès au Système d'information de l'entreprise potentiellement ?

Plan d'action
==============

_Durée estimée: 1 heure_

Afin que l'entreprise "Target" ne revive pas cette situation, votre mission est également de leur suggérer des préconisations afin de renforcer la sécurité de leurs systèmes. Pour effectuer ces recommandations, vous allez devoir mobiliser vos connaissances, les analyses que vous avez pu mener, mais aussi faire un rapide audit de la configuration existante.

Une archive au format zip se trouve [ici](https://github.com/PandiPanda69/edu-hei-secu/raw/main/annexes.zip). Elle contient les différentes configurations `iptables` des machines du réseau, et un fichier retranscrivant un court interview de l'administrateur système de l'entreprise "Target" avec votre collègue architecte sécurité.

> Vous pouvez retrouver le plan d'adressage [plus haut](#r%C3%A9cup%C3%A9ration-des-donn%C3%A9es-critiques).

## Le premier bilan (10min)

22. Quel(s) commentaire(s) pouvez-vous faire en regardant la configuration `iptables` actuelle ?
23. A la lecture de l'interview, faites un tableau récapitulatif des es éléments qui vont semblent positifs, et les éléments négatifs. Faites une appréciation globale du niveau de maturité de l'entreprise sur l'aspect sécurité de l'information.

## Le plan d'action (50min)

Un plan d'action a pour vocation d'être présenté aux décisionnaires. Vous allez donc devoir préparer un plan d'action que vous présenterez aux responsables de l'entreprise "Target" afin qu'ils valident, ou non, les mesures que vous recommandez.

24. En vous inspirant du tableau présenté ci-dessous (vous pouvez l'adaptez à vos besoins), listez les actions que vous recommanderiez de mettre en place, évaluez (à la louche) le temps d'implémentation, ainsi que le gain en sécurité. L'objectif est de (1) retrouver confiance dans le système actuel en nettoyant ce qui doit être nettoyé et (2) donner la capacité à l'entreprise "Target" de détecter plus rapidement et/ou d'empêcher ce type d'attaque à l'avenir.

| Réf.  | Titre                         | Objectifs                                                | Priorité | Complexité d'implém. | Gain en sécurité | Jrs/H estimés | Profil        |
| :--:  | ----------------------------- | -------------------------------------------------------- | :-------: | :------------------: | :--------------: | :-----------: | :-----------: |
| SEC_R | Revoir l'architecture réseau  | Mettre en place une architecture réseau plus sécurisée.  | CRITIQUE | ELEVEE               | IMPORTANT        |    N jours    | Expert réseau |
| SEC_X | Déployer un outil de sécurité | L'outil X est vraiment génial pour faire de la sécurité. | MOYEN | FAIBLE               | MOYEN            |    K jours    | Admin. Sys.   |

25. En prenant un compte les éléments financiers présentés dans le tableau suivant, déterminez le coût financier approximatif de l'attaque pour l'entreprise "Target". Puis déterminez le coup de la mise en place des recommandations que vous préconisez (en détaillant vos calculs).

| Type | € |
| --------- | :---: |
| Durée pendant laquelle "Target" n'a pas pu fonctionner jusqu'à maintenant | 4 jours |
| Durée restante avant que "Target" ne retrouve un fonctionnement normal | à vous de l'estimer |
| Perte de chiffre d'affaire estimée par jour | 20k€ |
| Estimation coût masse salariale de "Target" par jour | 4k€ |
| Tarif Journalier Moyen Dev. | 450€ |
| Tarif Journalier Moyen Admin. Sys. | 600€ |
| Tarif Journalier Moyen Commercial | 1000€ |
| Tarif Journalier Moyen Expert Réseau | 600€ |
| Tarif Journalier Moyen Chef de Projet | 600€ |
| Tarif Journalier Moyen Architecte | 1000€ |
| Tarif Journalier Moyen Pentesteur | 1250€ |
| Prix de licence Antivirus EDR | 30€ / utilisateur / mois |
| Formation à l'administration d'une infrastructure sécurisée | 5000€ |
| Boîter Cisco Firepower (NIDS Hardware) | 300000€ |
| Abonnement NordVPN | 1€ avec le code promo `LES_VPN_C_EST_TROP_SUPAYR.` |


26. Proposez une implémentation des recommandations de votre tableau en _lot_ en développant une ébauche de calendrier que vous pourriez communiquer à l'entreprise "Target" avec, le coût associé à chaque lot. Exemple: _Lot 1 - Sécurisation du système, en commençant maintenant, fin estimée du chantier en juin 2024_, etc...
