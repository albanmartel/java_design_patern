# Spécifications fonctionnelles — Noyau TP Bank

## Acteurs identifiés
- **Opérateur / Testeur (via Console)** : acteur principal qui utilise l'interface en mode console pour consulter les données et déclencher des opérations bancaires.
- **Base de données MySQL** : système de persistance (acteur secondaire / externe au code applicatif) qui stocke les clients, les comptes et l'historique des opérations.
- **Canaux futurs (API web / Application mobile)** : futurs consommateurs du noyau applicatif (hors périmètre immédiat de la console, mais à préserver dans la conception).

## POUR L'OPÉRATEUR / TESTEUR (via la Console), le système permet de :
- **Consulter la liste des clients** : afficher les clients enregistrés dans la base.
- **Consulter les comptes d'un client** : afficher les comptes associés à un client (type de compte, solde).
- **Effectuer un dépôt** : déposer un montant sur un compte existant, mettre à jour le solde et enregistrer l'opération dans l'historique.
- **Effectuer un retrait** : retirer un montant sur un compte existant si les règles métier sont respectées (ex. solde suffisant), mettre à jour le solde et enregistrer l'opération dans l'historique.
- **Consulter l'historique des opérations** : afficher l'historique des opérations d'un compte.

## AVEC LA BASE MYSQL (persistance et création), le noyau permet de :
- **Lire et enregistrer les données** : charger les clients, comptes et opérations, mettre à jour les soldes et tracer chaque opération via une couche d'accès dédiée (sans SQL dans la logique métier).
- **Instancier le bon type de compte** : créer dynamiquement un *Compte Courant* ou un *Compte Épargne* (ou tout type futur) à partir d'une information issue de la base/demande via un mécanisme centralisé.
- **Gérer les connexions de façon centralisée** : éviter la multiplication des connexions à MySQL depuis chaque module.

## Règles métier et gestion des erreurs
- **Compte inexistant** : interdire toute opération ou consultation sur un compte introuvable et signaler l'erreur proprement.
- **Solde insuffisant** : refuser tout retrait dépassant le solde autorisé, sans modifier le solde ni ajouter d'opération.
- **Traçabilité** : toute opération valide met à jour le solde et s'ajoute à l'historique.
- **Séparation des responsabilités** : les règles bancaires restent dans la couche métier, hors de la console et des requêtes SQL.