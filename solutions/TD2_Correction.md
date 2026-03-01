# TD2 : Modélisation Multidimensionnelle — Correction (Module M237)

Référence : **TD 2.pdf**.

---

## Étude de cas 1 — Vente de produits (e-commerce)

### 1) Fait & dimensions
- **Fact_Ventes** : `quantite`, `chiffre_affaires`
- **Dim_Produit**, **Dim_Client**, **Dim_Temps**, **Dim_Region**

### 2) Modèle en étoile
- Fact : `Fact_Ventes(id_temps, id_produit, id_client, id_region, quantite, chiffre_affaires)`
- Dimensions : `Dim_Produit`, `Dim_Client`, `Dim_Temps`, `Dim_Region`

### 3) PK/FK
- PK dimensions : `id_*`
- FK dans Fact_Ventes : `id_temps`, `id_produit`, `id_client`, `id_region`

### 4) Requêtes analytiques
- CA par mois & catégorie produit
- Top clients par CA sur une période
- CA par région & jour

---

## Étude de cas 2 — Gestion du stock (flocon)

### 1) Fait & dimensions
- **Fact_Stock** : `stock_qte` (et `stock_valeur`)
- Hiérarchie Produit : Famille→Catégorie→Produit
- Hiérarchie Localisation : Région→Ville→Entrepôt
- Temps : Année→Mois→Jour

### 2) Modèle en flocon
- `Fact_Stock(id_temps, id_produit, id_entrepot, stock_qte, stock_valeur)`
- Famille/Catégorie/Produit et Région/Ville/Entrepôt (tables séparées)

### 3) Requêtes analytiques
- Stock total par région et famille
- Stock faible/ruptures par entrepôt

---

## Étude de cas 3 — Analyse complète (constellation)

### 1) Deux faits
- **Fact_Ventes**
- **Fact_Achats**

### 2) Dimensions communes / spécifiques
- Communes : Produit, Temps, Région
- Spécifiques : Client (ventes), Fournisseur (achats), etc.

### 3) Modèle en constellation
Deux tables de faits partageant des dimensions communes.

### 4) Analyses
- Comparer ventes vs achats par produit et mois (marge)
- Analyse fournisseurs par région/période

### 5) Inclure le stock
Ajouter une table **Fact_Stock** (photo de stock) est le plus fiable.

---

## Exercice 4 — DW Ventes (étoile)

### 1) Définitions
- **Table de faits** : mesures + clés vers dimensions.
- **Table de dimension** : attributs descriptifs.
- **Indicateur** : mesure numérique (CA, quantité…).
- **Hiérarchie** : niveaux (Jour→Mois→Année).

### 2) Schéma étoile (proposition)
- **Fact_Vente**(code_date FK, code_produit FK, code_client FK, code_vendeur FK, chiffre_affaires)
- **Dim_Produit**(code_produit PK, code_famille, libelle, ...)
- **Dim_Client**(code_client PK, nom, CSP, ...)
- **Dim_Vendeur**(code_vendeur PK, nom, code_service, ...)
- **Dim_Date**(code_date PK, jour, semaine, mois, annee, ...)

---

## Exercice 5 — DW Réussite examens (étoile)

### Dimensions
- **Dim_Temps**(date, mois, annee, semestre)
- **Dim_Cours**(nom_cours, type_cours : obligatoire/option)
- **Dim_Etudiant**(sexe, age)

### Fait
- **Fact_Examens**(id_temps FK, id_cours FK, id_etudiant FK, note, reussite)

### Hiérarchies
- Temps : Jour→Mois→Année (+ semestre)
- Cours : TypeCours→Cours
- Étudiant : TrancheAge→Âge (optionnel)
