# TD1 : OLTP et OLAP — Correction

Référence : **TD 1.pdf**.

---

## Étude de cas 1 : Système de réservation de billets

### 1) OLTP ou OLAP ? Justification
✅ **OLTP** : gestion des transactions (réserver, payer, annuler) en temps réel, insert/update fréquents, cohérence forte.

### 2) Données à extraire vers un Data Warehouse
- Ventes de billets (destination, date/heure, prix, quantité, canal)
- Paiements (moyen, statut, montant)
- Annulations (date, motif, pénalité)
- Référentiels (destinations, lignes, agences, promotions)
- Profil client (ville/segment si disponible)

### 3) Différence d’objectifs
- **OLTP** : exécuter correctement les opérations.
- **OLAP** : analyser tendances (par période/destination), reporting, prévision.

---

## Étude de cas 2 : Supermarché en ligne

### 1) Composants OLTP et OLAP
- **OLTP** : commandes, lignes, paiement, clients.
- **OLAP** : analyse des ventes, produits populaires, prévision du stock.

### 2) Dimensions et mesures (cube OLAP)
- **Temps** (Année→Mois→Jour), **Produit** (Catégorie→Produit),
  **Client** (Ville→Région), **Paiement**, **Canal**
- **Mesures** : Quantité, CA (= quantité×prix), panier moyen, nb transactions

### 3) Exemple de requête OLAP
- CA par catégorie et par mois ; Top produits par quantité sur 3 derniers mois.

---

## Étude de cas 3 : Gestion hospitalière

### 1) OLTP vs OLAP
- **OLTP** : admissions, rendez-vous, prescriptions
- **OLAP** : rapports direction (fréquence maladies, durée moyenne, taux occupation)

### 2) Données transférées via ETL
- Séjours (admission/sortie, service, diagnostic, durée)
- Consultations (date, médecin, spécialité)
- Occupation lits (date, service, statut)
- Référentiels (patients anonymisés, services, pathologies)

### 3) KPI possibles
- Durée moyenne d’hospitalisation, taux d’occupation des lits, nb admissions/mois, top diagnostics.

---

## Exercice 4 : Requêtes SQL OLTP

Tables :
Client(id_client, nom, ville)
Produit(id_produit, libelle, prix_unitaire)
Commande(id_commande, id_client, date_commande)
LigneCommande(id_commande, id_produit, quantite)

### 1) Toutes les commandes du client 'Ahmed'
```sql
SELECT co.*
FROM Commande co
JOIN Client c ON c.id_client = co.id_client
WHERE c.nom = 'Ahmed'
ORDER BY co.date_commande DESC;
```

### 2) Produits commandés + quantité totale vendue
```sql
SELECT p.id_produit, p.libelle, SUM(lc.quantite) AS qte_totale
FROM LigneCommande lc
JOIN Produit p ON p.id_produit = lc.id_produit
GROUP BY p.id_produit, p.libelle
ORDER BY qte_totale DESC;
```

### 3) Clients ayant passé plus de 5 commandes
```sql
SELECT c.id_client, c.nom, c.ville, COUNT(co.id_commande) AS nb_commandes
FROM Client c
JOIN Commande co ON co.id_client = c.id_client
GROUP BY c.id_client, c.nom, c.ville
HAVING COUNT(co.id_commande) > 5
ORDER BY nb_commandes DESC;
```

---

## Exercice 5 : Requêtes OLAP

Dimensions : Temps(Année,Mois,Jour), Produit(Catégorie,Libellé), Client(Ville)  
Mesure : MontantVentes = quantite × prix_unitaire

### 1) CA total par ville et par mois
```sql
SELECT c.ville,
       EXTRACT(YEAR FROM co.date_commande) AS annee,
       EXTRACT(MONTH FROM co.date_commande) AS mois,
       SUM(lc.quantite * p.prix_unitaire) AS chiffre_affaires
FROM Commande co
JOIN Client c ON c.id_client = co.id_client
JOIN LigneCommande lc ON lc.id_commande = co.id_commande
JOIN Produit p ON p.id_produit = lc.id_produit
GROUP BY c.ville, annee, mois
ORDER BY annee, mois, chiffre_affaires DESC;
```

### 2) Top 3 produits les plus vendus de l’année (ex: 2025)
```sql
SELECT p.libelle, SUM(lc.quantite) AS qte_totale
FROM Commande co
JOIN LigneCommande lc ON lc.id_commande = co.id_commande
JOIN Produit p ON p.id_produit = lc.id_produit
WHERE EXTRACT(YEAR FROM co.date_commande) = 2025
GROUP BY p.libelle
ORDER BY qte_totale DESC
LIMIT 3;
```

### 3) Évolution des ventes du produit 'Lait' par mois
```sql
SELECT EXTRACT(YEAR FROM co.date_commande) AS annee,
       EXTRACT(MONTH FROM co.date_commande) AS mois,
       SUM(lc.quantite * p.prix_unitaire) AS chiffre_affaires
FROM Commande co
JOIN LigneCommande lc ON lc.id_commande = co.id_commande
JOIN Produit p ON p.id_produit = lc.id_produit
WHERE p.libelle = 'Lait'
GROUP BY annee, mois
ORDER BY annee, mois;
```
