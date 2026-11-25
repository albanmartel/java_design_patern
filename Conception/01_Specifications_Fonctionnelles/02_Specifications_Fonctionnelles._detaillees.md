# Spécifications Fonctionnelles — Noyau Bancaire TP Bank

## 1. Contexte et Objectifs
- **Objectif du système :** Concevoir le noyau fiable, évolutif et maintenable d'une banque en ligne (**TP Bank**), connecté à une base relationnelle **MySQL**, afin de servir de socle à de futures extensions (application mobile, API web, nouveaux produits bancaires).
- **Acteurs / Profils :**
  - **Utilisateur de la console (opérateur / testeur) :** utilise l'interface en mode console pour exécuter et vérifier les actions prévues.
  - **Système externe / Extension future (API web, application mobile) :** consommera le noyau applicatif dans les évolutions ultérieures (hors périmètre immédiat de la console).
  - **Base de données relationnelle MySQL :** stocke les informations des clients, des comptes et des opérations bancaires.
- **Périmètre :**
  - **Inclus :** gestion des clients (consultation et rattachement aux comptes), gestion des comptes (*Compte Courant* et *Compte Épargne* avec création centralisée/dynamique), opérations bancaires (dépôt, retrait, mise à jour du solde, historique), gestion des erreurs, séparation des responsabilités (métier / accès aux données / connexion MySQL) et interface console de test.
  - **Exclu :** développement final de l'application mobile ou de l'API web.

## 2. Besoins Fonctionnels (par module)

### Module : Gestion des clients
- **RF-01 — Consultation des clients :** Le système doit permettre de récupérer et d'afficher la liste des clients stockés dans la base MySQL.
- **RF-02 — Consultation des comptes d'un client :** Le système doit permettre de récupérer et d'afficher les comptes associés à un client donné.

### Module : Gestion des comptes
- **RF-03 — Types de comptes :** Le système doit gérer au moins le **Compte Courant** et le **Compte Épargne**, chacun pouvant porter des caractéristiques propres (par exemple la production d'intérêts pour le *Compte Épargne*).
- **RF-04 — Création dynamique et centralisée :** Le système doit instancier dynamiquement le bon type de compte à partir d'une information simple (ex. une chaîne de caractères issue de la base de données) via un point de création centralisé, afin que le reste de l'application ne dépende pas directement des classes concrètes et puisse accueillir de nouveaux types de comptes.

### Module : Gestion des opérations bancaires
- **RF-05 — Dépôt d'argent :** Le système doit permettre d'effectuer un dépôt d'argent sur un compte existant, en mettant à jour le solde et en enregistrant l'opération dans l'historique.
- **RF-06 — Retrait d'argent :** Le système doit permettre d'effectuer un retrait d'argent sur un compte existant si les règles métier (notamment la disponibilité du solde) sont respectées, en mettant à jour le solde et en enregistrant l'opération dans l'historique.
- **RF-07 — Consultation de l'historique :** Le système doit permettre d'afficher l'historique des opérations enregistrées pour un compte donné.

### Module : Interface console de test
- **RF-08 — Parcours console :** L'interface en mode console doit permettre :
  1. D'afficher la liste des clients ;
  2. D'afficher les comptes d'un client ;
  3. D'effectuer un dépôt ;
  4. D'effectuer un retrait ;
  5. D'afficher l'historique des opérations d'un compte.

## 3. Règles de Gestion (Contraintes métier et architecture)
- **RG-01 — Compte inexistant :** Il ne doit pas être possible d'effectuer une opération (dépôt, retrait) ou de consulter l'historique d'un compte inexistant.
- **RG-02 — Solde insuffisant :** Un retrait ne peut pas être exécuté si le solde disponible du compte ne permet pas de couvrir le montant demandé.
- **RG-03 — Enregistrement systématique dans l'historique :** Chaque opération bancaire valide doit mettre à jour le solde du compte concerné et être tracée dans l'historique des opérations.
- **RG-04 — Séparation métier / accès aux données :** Les règles bancaires (dépôt, retrait, consultation, vérifications) doivent être isolées dans la couche métier et ne doivent pas contenir directement de requêtes SQL. La lecture, l'écriture et l'exécution des requêtes SQL doivent être isolées dans la couche d'accès aux données (DAO).
- **RG-05 — Connexion MySQL centralisée :** L'accès à la base MySQL doit passer par un mécanisme centralisé (et non par des créations de connexions dispersées dans chaque module) afin d'éviter la surcharge du serveur, la perte de performance et les difficultés de maintenance.

## 4. Gestion des Cas Limites et Erreurs
- **Compte introuvable :** Refuser l'action et retourner/afficher un message d'erreur explicite indiquant que le compte n'existe pas.
- **Client introuvable ou sans compte :** Signaler proprement l'absence de client ou l'absence de compte associé lors de la consultation.
- **Solde insuffisant :** Refuser le retrait, laisser le solde et l'historique inchangés, et afficher un message d'erreur clair.
- **Montant invalide ou type de compte inconnu :** Rejeter proprement un montant nul/négatif ou un type de compte non reconnu lors de la création dynamique.
- **Erreur technique MySQL :** Intercepter et signaler proprement les erreurs d'accès aux données sans mélanger de logique SQL dans la couche métier ou dans la console.

## 5. Matrice de Validation (Scénarios de test)
| ID | Scénario | Données d'entrée | Résultat attendu |
| :--- | :--- | :--- | :--- |
| **TEST-01** | Affichage des clients | Demande de liste des clients en console | Liste des clients issue de MySQL affichée sans erreur. |
| **TEST-02** | Affichage des comptes d'un client | Identifiant d'un client existant | Liste de ses comptes (Courant / Épargne) et soldes affichée. |
| **TEST-03** | Dépôt valide | Compte existant + montant positif | Solde augmenté du montant, opération ajoutée à l'historique, confirmation affichée. |
| **TEST-04** | Retrait valide | Compte existant + montant inférieur ou égal au solde autorisé | Solde diminué du montant, opération ajoutée à l'historique, confirmation affichée. |
| **TEST-05** | Retrait refusé pour solde insuffisant | Compte existant + montant supérieur au solde disponible | Retrait refusé, solde et historique inchangés, message d'erreur affiché. |
| **TEST-06** | Opération ou historique sur compte inexistant | Identifiant de compte absent en base | Action refusée avec message d'erreur indiquant que le compte est introuvable. |
| **TEST-07** | Consultation de l'historique | Identifiant d'un compte existant | Historique des opérations du compte affiché. |