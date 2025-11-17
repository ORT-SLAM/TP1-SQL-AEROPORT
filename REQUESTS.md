# Requête SQL

### Questions :

1. Quelles sont les personnes (id + nom) programmées sur un vol avec un Boeing 767 au mois d’octobre 2017.

```sql
SELECT p.idPers, p.nom
FROM personnel p
JOIN programmervol pr ON pr.numPers = p.idPers
JOIN vol v ON v.idVol = pr.numVol
JOIN avion a ON a.num = v.numAvion
JOIN typeavion ta ON ta.nom = a.nomTypeAvion
WHERE ta.nom = 'Boeing 767'
  AND v.dateVol >= '2017-10-01'
  AND v.dateVol <  '2017-11-01';
```

2. Quels sont les noms des stewards ainsi que leurs salaires ayant été programmés sur le vol n°6.

```sql
SELECT p.idPers, p.nom FROM personnel p
JOIN typepersonnel tp ON p.idType = tp.idTypePersonnel
JOIN programmervol pv ON p.idPers = pv.numPers
JOIN vol v ON v.idVol = pv.numVol
WHERE v.idVol = 6 AND tp.nomTypePersonnel = "Steward"
```

3. Combien de vols ont été réalisés au départ de Nantes par un Boeing 747 d’une capacité comprise entre 250 et 300.

```sql
SELECT count(*) FROM vol v JOIN avion a ON a.num = v.numAvion JOIN typeavion ta ON a.nomTypeAvion = ta.nom WHERE v.villeDep = "Nantes" AND ta.capacite BETWEEN 250 AND 300 AND ta.nom = "Boeing 747";
```

4. Quels sont les types d’avions (nom du type + nombre de vols) ayant réalisé plus de 5 vols. Cette liste sera triée par ordre décroissant sur le nombre de vols.

```sql
SELECT 
    ta.nom AS typeAvion,
    COUNT(v.idVol) AS nombreVols
FROM typeavion ta
JOIN avion a ON a.nomTypeAvion = ta.nom
JOIN vol v ON v.numAvion = a.num
GROUP BY ta.nom
HAVING COUNT(v.idVol) > 5
ORDER BY nombreVols DESC;
```

5. Quels sont les avions (numéro + nom du type + localisation) qui sont localisés dans la même ville que l’avion n°16.

```sql
SELECT a.num, ta.nom, a.localisation
FROM avion a
JOIN typeavion ta ON a.nomTypeAvion = ta.nom
WHERE a.localisation = (
    SELECT localisation FROM avion WHERE num = 16
)
AND a.num != 16;
```

6. Quelles sont les personnes (nom) qui n’ont jamais été programmées sur un Airbus A320.

```sql
SELECT p.nom
FROM personnel p
WHERE p.idPers NOT IN (
    SELECT pv.numPers
    FROM programmervol pv
    JOIN vol v ON v.idVol = pv.numVol
    JOIN avion a ON a.num = v.numAvion
    JOIN typeavion ta ON ta.nom = a.nomTypeAvion
    WHERE ta.nom = 'Airbus A320'
);
```

7. Quel est le total des salaires par type de personnel. On affichera le nom du type et le total des salaires.

```sql
SELECT tp.nomTypePersonnel, SUM(p.salaire) as Salaire FROM typepersonnel tp
JOIN personnel p ON tp.idTypePersonnel = p.idType
GROUP BY tp.nomTypePersonnel
```

8. Quel est le nom du type d’avion qui a réalisé le plus de vols.

```sql
SELECT ta.nom
FROM typeavion ta
JOIN avion a ON a.nomTypeAvion = ta.nom
JOIN vol v ON v.numAvion = a.num
GROUP BY ta.nom
HAVING COUNT(v.idVol) = (
    SELECT MAX(nbVols)
    FROM (
        SELECT COUNT(v2.idVol) AS nbVols
        FROM typeavion ta2
        JOIN avion a2 ON a2.nomTypeAvion = ta2.nom
        JOIN vol v2 ON v2.numAvion = a2.num
        GROUP BY ta2.nom
    ) AS temp
);
```

9. Quelle est la ville de départ (nom + nombre de vols) pour laquelle il y a le moins de vols.

```sql
SELECT v.villeDep, COUNT(v.idVol) as NombreDeVol
FROM vol v
GROUP BY v.villeDep
HAVING COUNT(v.idVol) = (
    SELECT MIN(nbVols)
    FROM (
        SELECT COUNT(v2.idVol) AS nbVols
        FROM vol v2
        GROUP BY v2.villeDep
    ) AS temp
);
```

10. Combien de personnes ont une ancienneté comprise entre 10 et 15 ans.

```sql
SELECT COUNT(*) AS nbPersonnes
FROM personnel
WHERE YEAR(CURRENT_DATE()) - YEAR(dateEmbauche) BETWEEN 10 AND 15;
```