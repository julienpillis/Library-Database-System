# Modèle Logique de Données
![status_2](https://img.shields.io/badge/Rendu%202-in%20progress-orange)

## Tables
**Ressource**(#code,titre,date_apparition, code_classification, editeur, genre) avec titre, code_classification NOT NULL

**Film**(#code=>Ressource.code, synopsis, langue, longueur) avec langue, synopsis, longueur NOT NULL

**Musique**(#code=>Ressource.code, longueur) avec longeur NOT NULL

**Livre**(#code=>Ressource.code, ISBN, resume, langue) avec ISBN key et resume, langue NOT NULL

**Acteur**(#film=>Film.code, #contrib=><span>Contributeur.id</span>)

**Realisateur**(#film=>Film.code, #contr<span>Contributeur.id</span>)

**Auteur**(#livre=>Livre.code, #contrib=><span>Contributeur.id</span>)

**Compositeur**(#musique=>Musique.code, #contrib=><span>Contributeur.id</span>)

**Interprete**(#musique=>Musique.code, #contrib=><span>Contributeur.id</span>)

**Contributeur**(#id, nom, prenom, date_naissance, nationalite) avec * NOT NULL

**Exemplaire**(#id, #code_ressource=>Ressource.code, etat:{neuf, bon, abime, perdu}) avec etat NOT NULL

**Pret**(#adherent=>Adherent<span>.id, #exemplaire=>Exemplaire</span>.id, date_debut, date_fin, rendu) avec date_debut <= date_fin, all NOT NULL

**Sanction**(#id=><span>Utilisateur.id</span>, motif: {retard, deterioration, perte}, date_debut, date_fin) avec date_debut <= date_fin, date_debut, motif NOT NULL

**Adherent**(#id=><span>Utilisateur.id</span>, date_naissance, num_tel, statut_adhesion: {active, expiree, suspendue, blacklistee}) statut_adhestion NOT NULL

**Compte**(#id=><span>Utilisateur.id</span>, login, password) login key

> "*Si une clé candidate (globale) permet d'identifier de façon unique une partie indépendamment du tout, on préférera la conserver comme clé candidate plutôt que de la prendre pour clé primaire.
> Si on la choisit comme clé primaire cela revient à avoir transformé la composition en agrégation, en redonnant une vie propre aux objets composants.*"
> D'aprés le cours [mod4](https://moodle.utc.fr/pluginfile.php/249144/mod_resource/content/1/co/mod4c21.html) sur la transformation des compositions en relationelle, il  est donc, ici, plus judicieux de choisir login en tant que key (unique not null) et non en tant que clé primaire



**Utilisateur**(#id, nom, prenom, adresse, mail) nom, prenom not null

**Personnel**(#id=><span>Utilisateur.id</span>)
    
---

## Contraintes :

#### Héritage classe Ressource :

- INTERSECTION (PROJECTION(Film,code), PROJECTION(Musique,code), PROJECTION(Livre,code)) = NULL (Héritage exclusif classe Ressource )

- UNION (PROJECTION(Film,code), PROJECTION(Musique,code), PROJECTION(Livre,code)) = PROJECTION(Ressource,code) (classe Ressource abstraite)

#### Héritage classe Utilisateur :

- INTERSECTION (PROJECTION(Personnel,id), PROJECTION(Adherent,code)) = NULL (Héritage exclusif classe Utilisateur )

- UNION (PROJECTION(Personnel,id), PROJECTION(Adherent,id),  = PROJECTION(Utilisateur,id) (classe Utilisateur abstraite)


