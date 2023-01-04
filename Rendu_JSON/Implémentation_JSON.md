# Implémentation JSON
## Réflexion sur l'intégration d'attributs de type JSON

![](https://gitlab.utc.fr/pillisju/projet-nf18-td1_g2-bibliotheque/-/raw/main/img/Integration_JSON.png)


## Implémentation de ces attributs dans les tables concernées (SQL)

``` sql
CREATE TABLE Ressource(
    code INTEGER PRIMARY KEY,
    titre VARCHAR NOT NULL,
    date_apparition DATE,
    code_classification INTEGER NOT NULL,
    editeur VARCHAR,
    genre VARCHAR,
    exemplaires JSON
);

CREATE TABLE Film(
    code INTEGER REFERENCES Ressource(code),
    synopsis VARCHAR NOT NULL,
    langue VARCHAR NOT NULL,
    longueur INTEGER NOT NULL,
    PRIMARY KEY (code)
);

CREATE TABLE Musique(
    code INTEGER REFERENCES Ressource(code),
    longueur INTEGER NOT NULL,
    PRIMARY KEY (code)
);

CREATE TABLE Livre(
    code INTEGER REFERENCES Ressource(code),
    ISBN VARCHAR(17) UNIQUE NOT NULL,
    resume VARCHAR NOT NULL,
    langue VARCHAR NOT NULL,
    PRIMARY KEY (code)
);


CREATE TABLE Contributeur(
    id SERIAL PRIMARY KEY ,
    nom VARCHAR NOT NULL,
    prenom VARCHAR NOT NULL,
    date_naissance DATE NOT NULL,
    nationalite VARCHAR NOT NULL
);

CREATE TABLE Acteur(
    film INTEGER REFERENCES Film(code) NOT NULL,
    contrib INTEGER REFERENCES Contributeur(id)NOT NULL,
    PRIMARY KEY(film,contrib)
);


CREATE TABLE Realisateur(
    film INTEGER REFERENCES Film(code) NOT NULL,
    contrib INTEGER REFERENCES Contributeur(id)NOT NULL,
    PRIMARY KEY(film,contrib)
);

CREATE TABLE Auteur(
    livre INTEGER REFERENCES Livre(code) NOT NULL,
    contrib INTEGER REFERENCES Contributeur(id)NOT NULL,
    PRIMARY KEY(livre,contrib)
);

CREATE TABLE Compositeur(
    musique INTEGER REFERENCES Musique(code) NOT NULL,
    contrib INTEGER REFERENCES Contributeur(id)NOT NULL,
    PRIMARY KEY(musique,contrib)
);

CREATE TABLE Interprete(
    musique INTEGER REFERENCES Musique(code) NOT NULL,
    contrib INTEGER REFERENCES Contributeur(id)NOT NULL,
    PRIMARY KEY(musique,contrib)
);

CREATE TYPE etat AS ENUM ('neuf', 'bon', 'abîmé','perdu');

CREATE TABLE Utilisateur(
    id SERIAL PRIMARY KEY ,
    nom VARCHAR NOT NULL,
    prenom VARCHAR NOT NULL,
    num_adresse INTEGER,
    rue VARCHAR,
    code_postal INTEGER,
    ville VARCHAR,
    mail VARCHAR,
    compte JSON NOT NULL
);

CREATE TABLE Personnel(
    id INTEGER REFERENCES Utilisateur(id),
    PRIMARY KEY(id)
);



CREATE TYPE Adhesion AS ENUM ('active', 'expirée', 'suspendue','blacklistée');

CREATE TABLE Adherent(
    id INTEGER REFERENCES Utilisateur(id),
    date_naissance DATE,
    num_tel INTEGER,
    statut_adhesion Adhesion NOT NULL,
    sanctions JSON,
    PRIMARY KEY(id)
);

CREATE TYPE Motif AS ENUM ('retard', 'deterioration', 'perte');

CREATE TABLE Pret(
    id_pret SERIAL PRIMARY KEY ,
    adherent INTEGER REFERENCES Adherent(id) NOT NULL,
    exemplaire INTEGER,
    code_ressource INTEGER,
    date_debut DATE NOT NULL,
    date_fin DATE ,
    rendu BOOLEAN NOT NULL,
    FOREIGN KEY (exemplaire, code_ressource) REFERENCES Exemplaire(id,code_ressource),
    CHECK(date_fin>=date_debut)
);
```
 
## Exemples d'insertion sur ces nouvelles tables 
 
``` sql
INSERT INTO utilisateur(nom, prenom, num_adresse, rue, code_postal, ville, mail) values(
    'Galleze',
    'Rayane',
    '21',
    'rue du dépôt',
    '60280',
    'Compiègne',
    'rgalleze@etu.utc.fr',
    {"login":"rgalleze", "password":"2908$RGnf18tropbien"}
);

INSERT INTO adherent values(
    3,
    '2000-02-02',
    0668234125,
    'blacklisté',
    [
        {"motif":"perte","date_debut":"2022-11-03","date_fin":"2022-11-05"},
        {"motif":"perte","date_debut":"2022-11-11","date_fin":null}
    ]
);

INSERT INTO Ressource VALUES(
    3,
    'The Lord of the Rings',
    '1954-07-29',
    1154,
    'Allen & Unwin',
    'Fantasy',
    [
        {"id":"1","etat":"abîmé"},
        {"id":"2","etat":"bon"},
        {"id":"3","etat":"abîmé"},
        {"id":"4","etat":"abîmé"}
    ]
);

```

## Exemples requêtes avec implémentation JSON


