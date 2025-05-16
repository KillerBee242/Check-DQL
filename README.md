# Check-DQL

## Création des tables

```
-- Table des départements
CREATE TABLE Departement (
    Num_S INT PRIMARY KEY,
    Label VARCHAR(50) NOT NULL,
    Manager_Name VARCHAR(100) NOT NULL
);

-- Table des employés
CREATE TABLE Employe (
    Num_E INT PRIMARY KEY,
    Nom VARCHAR(100) NOT NULL,
    Poste VARCHAR(50) NOT NULL,
    Salaire DECIMAL(10,2) NOT NULL CHECK (Salaire > 0),
    Department_Num_S INT NOT NULL,
    FOREIGN KEY (Department_Num_S) REFERENCES Departement(Num_S) ON DELETE CASCADE
);

-- Table des projets
CREATE TABLE Projet (
    Num_P INT PRIMARY KEY,
    Titre VARCHAR(100) NOT NULL,
    Date_Debut DATE NOT NULL,
    Date_Fin DATE NOT NULL CHECK (Date_Fin >= Date_Debut),
    Department_Num_S INT NOT NULL,
    FOREIGN KEY (Department_Num_S) REFERENCES Departement(Num_S) ON DELETE CASCADE
);

-- Table des employés affectés aux projets
CREATE TABLE Employe_Projet (
    Employee_Num_E INT NOT NULL,
    Project_Num_P INT NOT NULL,
    Role VARCHAR(50) NOT NULL,
    PRIMARY KEY (Employee_Num_E, Project_Num_P),
    FOREIGN KEY (Employee_Num_E) REFERENCES Employe(Num_E) ON DELETE CASCADE,
    FOREIGN KEY (Project_Num_P) REFERENCES Projet(Num_P) ON DELETE CASCADE
);

```

Explication des contraintes
- PRIMARY KEY assure l’unicité de chaque enregistrement.
- NOT NULL garantit que les champs essentiels ne contiennent pas de valeurs vides.
- CHECK impose des restrictions sur les valeurs (ex. salaire positif, date de fin après date de début).
- FOREIGN KEY établit les relations entre les tables et permet la suppression en cascade des enregistrements liés.

## Insertion des enregistrements

```
-- Insertion des départements
INSERT INTO Departement (Num_S, Label, Manager_Name) VALUES
(1, 'IT', 'Alice Johnson'),
(2, 'RH', 'Bob Smith'),
(3, 'Marketing', 'Clara Bennett');

-- Insertion des employés
INSERT INTO Employe (Num_E, Nom, Poste, Salaire, Department_Num_S) VALUES
(101, 'John Doe', 'Développeur', 60000.00, 1),
(102, 'Jane Smith', 'Analyste', 55000.00, 2),
(103, 'Mike Brown', 'Designer', 50000.00, 3),
(104, 'Sarah Johnson', 'Data Scientist', 70000.00, 1),
(105, 'Emma Wilson', 'Spécialiste RH', 52000.00, 2);

-- Insertion des projets
INSERT INTO Projet (Num_P, Titre, Date_Debut, Date_Fin, Department_Num_S) VALUES
(201, 'Refonte du site web', '2024-01-15', '2024-06-30', 1),
(202, 'Intégration des employés', '2024-03-01', '2024-09-01', 2),
(203, 'Étude de marché', '2024-02-01', '2024-07-31', 3),
(204, 'Mise en place infrastructure IT', '2024-04-01', '2024-12-31', 1);

-- Insertion des employés dans les projets
INSERT INTO Employe_Projet (Employee_Num_E, Project_Num_P, Role) VALUES
(101, 201, 'Développeur Frontend'),
(104, 201, 'Développeur Backend'),
(102, 202, 'Formateur'),
(105, 202, 'Coordinateur'),
(103, 203, 'Chef de recherche'),
(101, 204, 'Spécialiste réseau');

```

-- ------------------------------------------------------------------------------------

-- Langage de Requête de Données SQL (DQL)
-- Système d'Information sur la Participation des Employés

### 1. Écrire une requête pour récupérer les noms des employés qui sont affectés à plus d'un projet, incluant le nombre total de projets pour chaque employé.
```
SELECT
    E.Nom AS Nom_Employe,
    COUNT(EP.Project_Num_P) AS Nombre_Total_Projets_Affectes
FROM
    Employe E
JOIN
    Employe_Projet EP ON E.Num_E = EP.Employee_Num_E
GROUP BY
    E.Num_E, E.Nom
HAVING
    COUNT(EP.Project_Num_P) > 1
ORDER BY
    Nombre_Total_Projets_Affectes DESC, Nom_Employe;
```

-- ------------------------------------------------------------------------------------
### 2. Écrire une requête pour récupérer la liste des projets gérés par chaque département, incluant le libellé du département et le nom du responsable.
```
SELECT
    D.Label AS Libelle_Departement,
    D.Manager_Name AS Nom_Responsable_Departement,
    P.Titre AS Titre_Projet,
    P.Date_Debut,
    P.Date_Fin
FROM
    Departement D
JOIN
    Projet P ON D.Num_S = P.Department_Num_S
ORDER BY
    Libelle_Departement, Titre_Projet;
```

-- ------------------------------------------------------------------------------------
### 3. Écrire une requête pour récupérer les noms des employés travaillant sur le projet "Refonte Site Web," incluant leurs rôles dans le projet.
--  (Note : Remplacez "Refonte Site Web" par un titre de projet réel de vos données si différent)
```
SELECT
    E.Nom AS Nom_Employe,
    EP.Role AS Role_Employe_Dans_Projet,
    P.Titre AS Titre_Projet
FROM
    Employe E
JOIN
    Employe_Projet EP ON E.Num_E = EP.Employee_Num_E
JOIN
    Projet P ON EP.Project_Num_P = P.Num_P
WHERE
    P.Titre = 'Refonte Site Web' -- Assurez-vous que ce titre de projet existe dans votre table Projet
ORDER BY
    Nom_Employe;
```

-- ------------------------------------------------------------------------------------
### 4. Écrire une requête pour récupérer le département ayant le plus grand nombre d'employés, incluant le libellé du département, le nom du responsable, et le nombre total d'employés.
-- Pour MySQL, PostgreSQL, SQLite :
```
SELECT
    D.Label AS Libelle_Departement,
    D.Manager_Name AS Nom_Responsable,
    COUNT(E.Num_E) AS Nombre_Total_Employes
FROM
    Departement D
JOIN
    Employe E ON D.Num_S = E.Department_Num_S
GROUP BY
    D.Num_S, D.Label, D.Manager_Name
ORDER BY
    Nombre_Total_Employes DESC
LIMIT 1;
```

-- Pour SQL Server :
```
-- SELECT TOP 1
--     D.Label AS Libelle_Departement,
--     D.Manager_Name AS Nom_Responsable,
--     COUNT(E.Num_E) AS Nombre_Total_Employes
-- FROM
--     Department D
-- JOIN
--     Employee E ON D.Num_S = E.Department_Num_S
-- GROUP BY
--     D.Num_S, D.Label, D.Manager_Name
-- ORDER BY
--     Nombre_Total_Employes DESC;
```

-- Pour Oracle (en utilisant une sous-requête) :
```
-- SELECT Libelle_Departement, Nom_Responsable, Nombre_Total_Employes
-- FROM (
--     SELECT
--         D.Label AS Libelle_Departement,
--         D.Manager_Name AS Nom_Responsable,
--         COUNT(E.Num_E) AS Nombre_Total_Employes,
--         ROW_NUMBER() OVER (ORDER BY COUNT(E.Num_E) DESC) as rn
--     FROM
--         Department D
--     JOIN
--         Employee E ON D.Num_S = E.Department_Num_S
--     GROUP BY
--         D.Num_S, D.Label, D.Manager_Name
-- )
-- WHERE rn = 1;
```

-- ------------------------------------------------------------------------------------
### 5. Écrire une requête pour récupérer les noms et postes des employés gagnant un salaire supérieur à 60.000, incluant les noms de leurs départements.
```
SELECT
    E.Nom AS Nom_Employe,
    E.Poste AS Poste_Employe,
    E.Salaire,
    D.Label AS Nom_Departement
FROM
    Employe E
JOIN
    Departement D ON E.Department_Num_S = D.Num_S
WHERE
    E.Salaire > 60000 -- En supposant que le salaire est stocké comme un type numérique
ORDER BY
    Salaire DESC, Nom_Employe;
```

-- ------------------------------------------------------------------------------------
### 6. Écrire une requête pour récupérer le nombre d'employés affectés à chaque projet, incluant le titre du projet.
```
SELECT
    P.Titre AS Titre_Projet,
    COUNT(EP.Employee_Num_E) AS Nombre_Employes_Affectes
FROM
    Projet P
LEFT JOIN -- Utilisation de LEFT JOIN pour inclure les projets avec zéro employé affecté
    Employe_Projet EP ON P.Num_P = EP.Project_Num_P
GROUP BY
    P.Num_P, P.Titre
ORDER BY
    Nombre_Employes_Affectes DESC, Titre_Projet;
```

-- ------------------------------------------------------------------------------------
### 7. Écrire une requête pour récupérer un résumé des rôles que les employés ont dans différents projets, incluant le nom de l'employé, le titre du projet et le rôle.
```
SELECT
    E.Nom AS Nom_Employe,
    P.Titre AS Titre_Projet,
    EP.Role AS Role_Employe
FROM
    Employe E
JOIN
    Employe_Projet EP ON E.Num_E = EP.Employee_Num_E
JOIN
    Projet P ON EP.Project_Num_P = P.Num_P
ORDER BY
    Nom_Employe, Titre_Projet;
```

-- ------------------------------------------------------------------------------------
### 8. Écrire une requête pour récupérer la dépense salariale totale pour chaque département, incluant le libellé du département et le nom du responsable.
```
SELECT
    D.Label AS Libelle_Departement,
    D.Manager_Name AS Nom_Responsable,
    SUM(E.Salaire) AS Depense_Salariale_Totale
FROM
    Departement D
JOIN
    Employe E ON D.Num_S = E.Department_Num_S
GROUP BY
    D.Num_S, D.Label, D.Manager_Name
ORDER BY
    Depense_Salariale_Totale DESC, Libelle_Departement;
```
