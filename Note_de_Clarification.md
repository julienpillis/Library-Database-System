# Note de Clarification
## Système de gestion d'une bibliothèque
![example workflow](https://img.shields.io/badge/build-in%20progress-blue)
---
**Etudiants :** 
 - Pillis Julien
 - Galleze Rayane
 - Labouré Alexandre
 - Vivat Benjamin
    
### Enoncé
Vous êtes chargés de concevoir un système de gestion pour une bibliothèque municipale qui souhaite informatiser ses activités : catalogage, consultations, gestion des utilisateurs, prêts, etc.

La bibliothèque offre un accès à un large choix de ressources de différents types (livres, films, et enregistrement musicaux). Une ressource, quelque soit son type, a un code unique, un titre, une liste de contributeurs, une date d'apparition, un éditeur, un genre et un code de classification qui permet de la localiser dans la bibliothèque. Un contributeur est caractérisé par son nom, son prénom, sa date de naissance et sa nationalité. Dans le cas d'un livre, les contributeurs sont les auteurs du document. Dans le cas d'une œuvre musicale, on distinguera compositeurs et interprètes. De même, on distinguera les réalisateurs et les acteurs pour les films. On souhaite également conserver des informations spécifiques suivant le type du document, par exemple : l'ISBN d'un livre et son résumé, la langue des documents écrits et des films, la longueur d'un film ou d'une œuvre musicale, le synopsis d'un film, etc. Enfin, les ressources dont dispose la bibliothèque peuvent être disponibles en plusieurs exemplaires, chacun dans un état différent : neuf, bon, abîmé ou perdu.

Chaque membre du personnel de la bibliothèque dispose d'un compte utilisateur (login et mot de passe) qui lui permet d'accéder aux fonctions d'administration du système. Chaque membre est caractérisé par son nom, son prénom, son adresse et son adresse e-mail.

Les adhérents de la bibliothèque disposent, eux aussi, d'un compte utilisateur (login et mot de passe) ainsi que d'une carte d'adhérent qui leur permettent d'emprunter des documents. Un adhérent est caractérisé par son nom, prénom, date de naissance, adresse, adresse e-mail et numéro de téléphone. La bibliothèque souhaite garder trace de toutes les adhésions, actuelles et passées.

Pour pouvoir emprunter un document, un adhérent à besoin de s'authentifier. Chaque prêt est caractérisé par une date de prêt et une durée de prêt. Un document ne peut être emprunté que s'il est disponible et en bon état. Un adhèrent ne peut emprunter simultanément qu'un nombre limité d'œuvres, chacune pour une durée limitée. Un adhérent sera sanctionné pour les retards dans le retour d'un ouvrage, ainsi que s'il dégrade l'état de celui-ci. Tout retard dans la restitution des documents empruntés entraîne une suspension du droit de prêt d'une durée égale au nombre de jours de retard. En cas de perte ou détérioration grave d'un document, la suspension du droit de prêt est maintenue jusqu'à ce que l'adhérent rembourse le document. Enfin, la bibliothèque peut choisir de blacklister un adhérent en cas de sanctions répétées.



---

### Besoins 
 - Faciliter aux adhérents la recherche de documents et la gestion de leurs emprunts.

 - Faciliter la gestion des ressources documentaires : ajouter des documents, modifier leur description, ajouter des exemplaires d'un document, etc.

 - Faciliter au personnel la gestion des prêts, des retards et des réservation.

 - Faciliter la gestion des utilisateurs et de leurs données.

 - Établir des statistiques sur les documents empruntés par les adhérents, cela permettra par exemple d'établir la liste des documents populaires, mais aussi d'étudier le profil des adhérents pour pouvoir leur suggérer des documents.

---

### Note de clarification
##### RESSOURCE 
    
- code (clé), titre, date d’apparition, code de classification, éditeur, un genre
	
##### CONTRIBUTEUR 
- nom, prénom, date de naissance, nationalité
- clé artificielle à envisager pour éviter les homonymes 


##### LIVRE (classe fille de RESSOURCE)
> \+ attributs de la classe mère RESSOURCE
- ISBN, résumé, langue
- a été écrit par un ou plusieurs contributeurs (association avec CONTRIBUTEUR * -- 1..n)

##### FILM (classe fille de RESSOURCE)
> \+ attributs de la classe mère RESSOURCE
- synopsis, langue, longueur
- a été réalisé par un ou plusieurs réalisateurs (association avec CONTRIBUTEUR : * -- 1..n)
- a été joué par un ou plusieurs acteurs (association avec CONTRIBUTEUR : * -- 1..n)


##### MUSIQUE (classe fille de RESSOURCE)
> \+ attributs de la classe mère RESSOURCE
- longueur
- a été composée par un ou plusieurs compositeurs (association avec CONTRIBUTEUR : * -- 1..n)
- est interprétée par un ou plusieurs musiciens (association avec CONTRIBUTEUR : * -- 1..n)

	
#### UTILISATEUR
- nom, prénom, adresse e-mail, adresse
- a un compte utilisateur (Composition 1 -- 1 avec COMPTE)
    > Pour des raisons de sécurité, il serait préférable d'avoir une table Compte à part avec le login et mot de passe.

- clé artificelle à envisager pour éviter les homonymes

##### PERSONNEL (classe fille d'UTILISATEUR)
> \+ attributs de la classe mère UTILISATEUR

	
##### ADHÉRENT (classe fille d'UTILISATEUR)
> \+ attributs de la classe mère UTILISATEUR
- date de naissance, numéro de tél
- statut_adhésion ENUM(active, expirée, suspendue, blacklistée)

    > Pour supprimer un adhérent, il suffit de rendre son statut expiré vu que : 
    > "*La bibliothèque souhaite garder trace de toutes les adhésions, actuelles et passées.*"
- peut emprunter plusieurs exemplaires (association avec Exemplaire : * -- *)

##### SANCTION 
- Motif ENUM(retard, détérioration, perte)
- Date de début (not NULL)
- Date de fin (peut être NULL en cas de suspension indéterminée)

    > "*En cas de perte ou détérioration grave d’un document, la suspension du droit de prêt est maintenue jusqu’à ce que l’adhérent rembourse le document*"

- est liée à un adhérent (association avec ADHERENT : 1 -- *)
- date de début <= date de fin

    
##### COMPTE 
- login, mot de passe
- est lié à un UTILISATEUR (Composition)

##### PRÊT (association entre Adherent et Exemplaire)
- date de début, date de fin, rendu (booléen)

    > - Choix d'une date de fin plutôt qu'une durée, car plus simple à gérer au niveau applicatif et la durée est retrouvable à partir de la date de début et de fin.
    > - Le rendu permet de savoir si la ressource a été raménée et vérifier s'il y a un retard ou non
- date de début <= date de fin


##### EXEMPLAIRE 
- état ENUM(Neuf, Bon, Abimé, Perdu)) 
- codeRessource (composition avec Ressource 1..*, un exemplaire ne peut pas exister dans ressource), 
- disponible (Booléen)
- clé artificielle à envisager


---

## MCD : Diagramme UML / PlantUML
```plantuml


skinparam groupInheritance 2
abstract class Ressource {
    code : int {key}
    titre : string
    date_apparition : Date
    code_classification :int
    editeur: string
}

class Livre {
    ISBN : string {key}
    resume : string
    langue : string
}
class Musique {
    longueur : int
}

class Film {
    synopsis : string
    langue : string 
    longueur: string
}

Ressource <|-down- Livre
Ressource <|-down- Film
Ressource <|-down- Musique 




class Exemplaire {
  id : integer
    etat : enum 
}

Ressource *-- "1..n" Exemplaire

abstract class Utilisateur {
  nom : string
  prenom : string
  adresse : string
  mail : string
  
}



class Adherent{
    date de naissance : Date
    num_tel : int
    statut_adhesion : adhesion
}
class Sanction{
    motif : motif_sanction
    date_debut : Date
    date_fin : Date
    
    
}

Utilisateur <|-left personnel
Utilisateur *-- "1" Compte
Utilisateur <|- Adherent

Sanction "1" -- "*" Adherent : est_sanctionne
class Compte {
    login : string
    password : string
}

class Contributeur {
    nom : string
    prenom : string
    date_naissance : Date
    nationalite : string
}



Contributeur "1.*" --up- "*" Livre : a écrit >
Contributeur "1.*" --up- "*" Musique : a composé >
Contributeur "1.*" --up- "*" Musique : a interprété >
Contributeur "1.*" --up- "*" Film : a réalisé >
Contributeur "1.*" --up- "*" Film : a joué >

class Prêt {
date_debut : Date
date_fin : Date
rendu : Booleen

}


(Exemplaire, Adherent) . Prêt

enum Adhesion <<enum>>{
    active
    expiree
    suspendue
    blacklistee
}

enum Motif_sanction <<enum>>{
    retard
    deterioration
    perte
}

enum type_etat <<enum>> {
    Neuf
    Bon
    Abime
    Perdu
}
```

---