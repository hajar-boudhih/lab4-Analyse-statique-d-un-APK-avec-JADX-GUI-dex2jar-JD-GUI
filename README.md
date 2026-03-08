# Lab4 - Analyse statique d'un APK avec JADX-GUI, dex2jar & JD-GUI

## Préparation du Workspace & Vérification de l'APK

### Objectif
L'objectif de cette étape est de préparer un environnement de travail organisé et de vérifier l'intégrité du fichier APK avant de commencer l'analyse.

## Mise en place de l'espace de travail
Un dossier dédié nommé APK-Analysis a été créé afin d'organiser tous les fichiers du laboratoire. Cela permet de travailler de manière structurée et d'éviter toute confusion entre les fichiers.
<img width="333" height="84" alt="Image" src="https://github.com/user-attachments/assets/a0a95a06-9983-4e61-8d9b-79c69f380d12" />

## Préparation de l'APK
Le fichier UnCrackable-Level1.apk a été copié dans le dossier de travail pour faciliter les opérations de vérification et d'analyse.



## Vérification du format de l'APK
Un fichier APK est une archive compressée basée sur le format ZIP. Les premiers octets du fichier ont été examinés et la signature hexadécimale suivante a été obtenue : 50 4B. Cette signature correspond aux lettres PK, ce qui confirme que le fichier est une archive ZIP valide, et donc un APK valide.
<img width="777" height="623" alt="Image" src="https://github.com/user-attachments/assets/683f408e-f91b-4562-949d-6fa5e5bbcfa5" />

## Inspection de la structure interne
Le contenu interne de l'APK a été listé afin d'identifier sa structure. Les éléments principaux suivants ont été retrouvés : META-INF/, AndroidManifest.xml, classes.dex, res/, lib/. Ces composants confirment qu'il s'agit bien d'un package d'application Android valide.
<img width="542" height="305" alt="Image" src="https://github.com/user-attachments/assets/f9061fa1-4792-423c-b543-499c3d18ce0d" />

## Calcul du hash SHA-256
Un hash cryptographique SHA-256 a été généré pour garantir l'intégrité et la traçabilité du fichier. Cette empreinte numérique permettra de vérifier que l'APK n'a subi aucune modification pendant l'analyse.
<img width="740" height="142" alt="Image" src="https://github.com/user-attachments/assets/b059ca42-544a-4cd0-82af-df2396164f5e" />



## Task 2 — Extraction / Obtention de l'APK

### Objectif
Obtenir un fichier APK valide pour l'analyse.

### Méthode utilisée
L'APK a été fourni dans le cadre du laboratoire (OWASP MSTG).

- Nom original : UnCrackable-Level1.apk
- Nom utilisé : app.apk (renommé pour simplifier)



<img width="1119" height="810" alt="Image" src="https://github.com/user-attachments/assets/daba9935-639a-45b9-96c9-8ff350853c53" />


## Task 3 — Analyse avec JADX GUI

### Objectif
Explorer la structure de l'APK et analyser le manifeste.

### Permissions
Aucune permission sensible détectée.

### Composants identifiés
- Activity principale : sg.vantagepoint.uncrackable1.MainActivity

### Composants exportés
- MainActivity exportée via intent-filter

### Configurations sensibles
- android:debuggable="true" → Non présent
- android:usesCleartextTraffic="true" → Non présent

### Ressources explorées
- Layouts XML
- Menus
- Ressources graphiques

<img width="1027" height="851" alt="Image" src="https://github.com/user-attachments/assets/1e297884-12c9-47ab-9a16-d5388b026880" />

## Task 4 — Recherche de chaînes sensibles

### Objectif
Identifier les informations sensibles codées en dur.

### Recherches effectuées
Mots-clés recherchés :
- URLs (http, https, api, server...)
- Authentification (token, api_key, password...)
- Debug/dev (debug, test, staging...)

### Résultat
Aucune donnée sensible trouvée en clair dans :
- Code source
- Ressources
- Chaînes de caractères
- Aucune URL sensible trouvée
- Aucune clé API trouvée
- Aucun mot de passe en clair
- Aucun token d'authentification
- Aucun endpoint de test/debug

## Task 5 — Conversion DEX → JAR

### Objectif
Convertir le bytecode Android pour une analyse alternative.

### Fichiers 
- classes.dex
- app.jar
<img width="737" height="440" alt="Image" src="https://github.com/user-attachments/assets/877ebc5e-892f-4fc2-a31d-a7fd67619c23" />

## Task 6 — Comparaison JADX vs JD-GUI

### Objectif
Comparer deux outils de décompilation.

### Classe comparée
MainActivity


### diference

**JADX**
-  Idéal pour APK Android
- Ressources visibles
-  Plus lourd

**JD-GUI**
-  Simple et rapide
-  Pas adapté aux APK

<img width="793" height="890" alt="Image" src="https://github.com/user-attachments/assets/e9c43161-258f-4dca-a9ee-34629337d886" />

# rapport
Le fichier a été placé dans le dossier de travail :
# Rapport d'analyse statique — UnCrackable Level 1

## Informations générales
Analyste :hajar boudhih
APK analysé : app.apk
Version : 1.0
Provenance : APK fourni dans le cadre du laboratoire (OWASP MSTG)
Outils utilisés : JADX GUI, dex2jar, JD-GUI

## Résumé exécutif
Cette analyse statique a permis d'examiner la structure interne et le code de l'application UnCrackable Level 1. Aucune information sensible (mots de passe, clés API, tokens, URLs privées) n'a été trouvée en clair dans le code ou les ressources.

Les principales observations concernent :
- L'exposition du composant principal de l'application
- La présence de logique de protection contre l'analyse
- L'absence de mécanismes visibles de dissimulation avancée des ressources

### Actions prioritaires recommandées
- Restreindre l'exportation des composants non nécessaires
- Éviter d'exposer la logique sensible côté client
- Implémenter des mécanismes anti-reverse engineering plus robustes

## Constats détaillés

###  Activité principale exportée
**Sévérité** : Moyenne
**Description** : L'activité principale possède un intent-filter, ce qui la rend accessible depuis d'autres applications.
**Localisation** : AndroidManifest.xml → MainActivity
**Impact potentiel** : Une application malveillante pourrait lancer ce composant et interagir avec l'application.
**Remédiation recommandée** : Définir android:exported="false" si l'exposition n'est pas nécessaire.

###  Absence de données sensibles en clair
**Sévérité** : Faible
**Description** : Aucune chaîne sensible (clé API, mot de passe, token, URL privée) n'a été trouvée lors de la recherche globale.
**Localisation** : Analyse globale du code et des ressources via JADX
**Impact potentiel** : Réduit le risque de fuite d'informations par reverse engineering.
**Remédiation recommandée** : Bonne pratique — maintenir le stockage sécurisé des secrets côté serveur.

###  Logique de protection côté client
**Sévérité** : Moyenne
**Description** : Présence de mécanismes de protection et de vérification intégrés dans le code Java.
**Localisation** : MainActivity.java
**Impact potentiel** : La logique peut être analysée et contournée via reverse engineering.
**Remédiation recommandée** : Déplacer la logique critique côté serveur et utiliser l'obfuscation du code.
