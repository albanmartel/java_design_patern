## Fonctionnalité : [Nom de la fonctionnalité]

**En tant que** [rôle : visiteur, client, administrateur...]
**Je veux** [action ou fonctionnalité souhaitée]
**Afin de** [bénéfice / objectif métier]

### Règles métier associées
- [Règle 1 : ex. Un retrait ne peut pas dépasser le solde disponible.]
- [Règle 2 : ex. Toute opération est enregistrée dans l'historique.]

### Critères d'acceptation (Scénarios de test)

**Scénario 1 : Cas nominal (tout se passe bien)**
- **Étant donné** [contexte initial, ex. un compte existant avec un solde de 100 €]
- **Quand** [action de l'utilisateur, ex. je demande un retrait de 40 €]
- **Alors** [résultat attendu, ex. le nouveau solde est de 60 € et l'opération est enregistrée]

**Scénario 2 : Cas d'erreur**
- **Étant donné** [un compte existant avec un solde de 30 €]
- **Quand** [je demande un retrait de 50 €]
- **Alors** [l'opération est refusée avec le message "Solde insuffisant" et le solde reste à 30 €]