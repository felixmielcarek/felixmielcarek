# Compte-rendu TP1: Félix MIELCAREK

## Question 1

```sql
\i jo.sql
```

## Question 2

#### MLD de la base de donnée

>**Pays** (<u>code</u>,nom)
>
>**Discipline** (<u>code</u>,nom)
>
>**Athlete** (<u>code</u>,nom,prenom,sexe,dateNaiss,##paysNaiss,##pays,taille)
>
>**Pratiquer** (<u>##athlete</u>,<u>##discipl</u>)
>
>**Epreuve** (<u>code</u>,##discipl,nom,dateE)
>
>**Medaille** (<u>place</u>,couleur)
>
>**Resultat** (<u>##epreuve</u>,<u>##athlete</u>,##medaille)

## Question 3

#### Requète

```sql
SELECT COUNT(code) AS nombre_athlètes
FROM Athlete;
```

#### Résultat

```
 nombre_athlètes 
-----------------
           11647
(1 ligne)
```

## Question 4

#### Requète

```sql
SELECT code
FROM Epreuve
WHERE dateE = '29-07-2021';
```

#### Résultat

```
 code 
------
 E096
 E097
 E098
 E099
 E100
 E101
 E102
 E103
 E104
 E105
 E106
 E107
 E108
 E109
 E110
 E111
 E112
(17 lignes)
```

## Question 5

#### Requète

```sql
SELECT DISTINCT nom
FROM Discipline
WHERE code IN (
    SELECT discipl
    FROM Epreuve
    WHERE dateE = '29-07-2021'
);
```

#### Résultat

```
         nom         
---------------------
 Shooting
 Judo
 Swimming
 Table Tennis
 Fencing
 Artistic Gymnastics
 Rowing
 Canoe Slalom
(8 lignes)
```

#### Remarque

*Avec le gestionnaire de base de donnée postgre sql, une référence sur une table sans préciser de nom d'attribut (comme dans la table ```Epreuve``` pour ```discipline```) indiquera l'attribut de clé primaire (contrairement à Oracle qui indiquera le même nom que l'attribut).*

## Question 6

#### Requète

```sql
SELECT nom,prenom,dateNaiss
FROM Athlete
WHERE pays='FRA';
```

#### Résultat (que le début car il y a 396 lignes)

```
         nom         |       prenom       | datenaiss  
---------------------+--------------------+------------
 ABALO               | Luc                | 1984-09-06
 ADO                 | Pauline            | 1991-02-14
 AERNOUDTS           | Violaine           | 1999-10-23
 AGBEGNENOU          | Clarisse           | 1992-10-25
 AIT SAID            | Samir              | 1989-11-01
 ALBICY              | Andrew             | 1990-03-21
 ALIEV               | Mourad             | 1995-07-31
 AMDOUNI             | Morhad             | 1988-06-21
 AMOROS              | Emile              | 1995-08-26
 ANASTASSIOU         | Lucie              | 1993-01-10
 ANDRE               | Sylvain            | 1992-10-14
 ANDRODIAS           | Matthieu           | 1990-06-11
 ANNE                | Mame-Ibra          | 1989-11-07
 APITHY              | Bolade             | 1985-08-21
 ATATOU              | Wided              | 1999-07-15
 ATSU                | Jonathan           | 1996-09-27
 AUBRY               | David              | 1996-11-08
 AUDINET             | Manon              | 1992-02-12
 AYACHE              | Alexandre          | 1982-09-20
 BAILLEUL            | Margaux            | 1999-07-05
 BAJIC               | Stefan             | 2001-12-23
 BALZER              | Sara               | 1995-04-03
 BARBANCON           | Morgan             | 1992-08-12
 BARBELIN            | Lisa               | 2000-04-10
 BARD                | Melvin             | 2000-11-06
 BARDENET            | Alexandre          | 
 BART                | Adrien             | 1991-09-04
 BATUM               | Nicolas            | 1988-12-14
 BEAUGRAND           | Cassandre          | 1997-05-23
 BEAUMONT            | Maxime             | 1982-04-23
 BEDRANI             | Djilali            | 1993-10-01
 BEKA BEKA           | Alexis             | 2001-03-29
 BELAUD              | Valentin           | 1992-09-16
 BELOCIAN            | Wilhem             | 1995-06-22
 BENNAMA             | Billal             | 1998-06-14
 BERDER              | Cecilia            | 1989-12-13
 BERGER              | Eva                | 1995-09-19
 BERGERE             | Leo                | 1996-06-28
 BERNARDONI          | Paul               | 1997-04-18
 BERNAZ              | Jean Baptiste      | 1987-07-18
 BERTAUD             | Dimitry            | 1998-06-06
...
(396 lignes)
```

## Question 7

#### Requète

```sql
SELECT count(r.medaille)
FROM Resultat r,Athlete a
WHERE r.athlete=a.code
AND a.nom='LEDECKY'
AND a.prenom='Kathleen';
```

#### Résultat

```
 count 
-------
     4
(1 ligne)
```

## Question 8

#### Requète

```sql
SELECT a.nom,a.prenom,a.pays,e.nom,e.discipl
FROM Athlete a,Resultat r,Epreuve e
WHERE r.epreuve='E059'
AND r.athlete=a.code
AND a.sexe='F'
AND r.medaille='1'
AND r.epreuve=e.code;
```

#### Résultat

```
    nom     |  prenom  | pays |     nom      | discipl 
------------+----------+------+--------------+---------
 AGBEGNENOU | Clarisse | FRA  | Women -63 kg | JUD
(1 ligne)
```

## Question 9

#### Requète

```sql
SELECT nom,prenom,dateNaiss
FROM athlete
WHERE dateNaiss=(
    SELECT max(dateNaiss)
    FROM athlete
);
```

#### Résultat

```
 nom  | prenom | datenaiss  
------+--------+------------
 ZAZA | Hend   | 2009-01-01
(1 ligne)
```

## Question 10

#### Requète

```sql
SELECT nom,prenom,dateNaiss,trunc((make_date(2021,7,21)-dateNaiss)/365.25,0) AS age
FROM athlete
WHERE dateNaiss=(
    SELECT min(dateNaiss)
    FROM athlete
);
```

#### Résultat

```
  nom  | prenom | datenaiss  | age 
-------+--------+------------+-----
 HANNA | Mary   | 1954-12-01 |  66
(1 ligne)
```

## Question 11

#### Requète

```sql
SELECT p.nom
FROM Pays p
WHERE p.nom LIKE 'A%'
AND p.code NOT IN(
    SELECT a.pays
    FROM Athlete a,Resultat r
    WHERE a.code=r.athlete
);
```

#### Résultat

```
         nom         
---------------------
 Angola
 Algeria
 Afghanistan
 Albania
 American Samoa
 Andorra
 Aruba
 Antigua and Barbuda
(8 lignes)
```
