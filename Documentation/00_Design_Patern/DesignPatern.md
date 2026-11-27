# Design Patterns

### 1. Qu’est-ce qu’un Design Pattern et quel est son lien avec le couplage ?

Un **design pattern** (patron de conception) est une solution éprouvée à un problème récurrent de conception logicielle. Son objectif principal est de proposer une architecture claire, réutilisable et facile à faire évoluer.

Dans la pratique, de nombreux design patterns — comme *Strategy*, *Observer* ou *Adapter* — favorisent un **couplage faible**, c’est-à-dire une dépendance limitée entre les différentes classes du programme. Ce faible couplage rend le code plus **maintenable**, plus facile à tester et plus simple à modifier sans casser le reste de l’application.

Il faut toutefois éviter un piège courant : un design pattern n’est pas une garantie automatique de qualité ou de faible couplage. Chaque pattern répond à un besoin précis et doit être utilisé à bon escient. À titre d’exemple, le **Singleton** introduit souvent un couplage fort (une dépendance globale cachée) s’il est appelé directement un peu partout dans le code. Un pattern est donc un outil de structuration, jamais un objectif en soi.

### 2. Le pattern Adapter : faire communiquer des éléments incompatibles

Le pattern **Adapter** (Adaptateur) a pour rôle de servir d’intermédiaire ou de traducteur entre deux interfaces qui ne sont pas compatibles au départ. Il permet de convertir l’interface d’une classe existante en une autre interface attendue par le code client.

Contrairement à une idée fréquente, il ne sert pas uniquement à relier deux grandes applications complètes entre elles. Son usage le plus courant se situe au niveau des classes et des services : il permet d’intégrer une **bibliothèque externe**, une **API tierce** ou un **ancien code** (`legacy`) dans votre projet sans avoir à modifier leur code source d’origine.


### 3. Le pattern Singleton : une instance unique et ses limites

Le pattern **Singleton** garantit qu’une classe ne possède **qu’une seule et unique instance** dans toute l’application, tout en fournissant un **point d’accès global** vers cette instance. Il est particulièrement utile lorsqu’il faut centraliser l’accès à une ressource unique ou éviter de recréer plusieurs fois un objet coûteux en mémoire ou en temps d’initialisation, comme un gestionnaire de configuration, un système de logs, un cache partagé ou un gestionnaire de connexion.

L’image du planétarium partagé par dix écoles aide à visualiser l’idée d’une ressource centrale unique utilisée par plusieurs acteurs, mais elle reste une métaphore. En conception logicielle réelle, des écoles, des élèves ou des séances sont des objets métier classiques qui ont plusieurs instances ; on réserve le Singleton aux composants techniques uniques du système.

Enfin, il est essentiel de bien distinguer **unicité** et **cohérence**. Le Singleton assure que tout le code manipule la même instance, ce qui évite d’avoir plusieurs copies divergentes d’un même service. En revanche, il ne garantit pas à lui seul la cohérence des modifications : si plusieurs parties du programme modifient l’état interne de ce Singleton sans règles strictes (notamment en environnement multi-thread), cela peut provoquer des effets de bord et des états incohérents.


### Synthèse à retenir

Pour résumer ces trois notions fondamentales : les **design patterns** sont des modèles d’architecture réutilisables qui aident souvent à réduire le couplage et à améliorer la maintenabilité, à condition d’être choisis selon le contexte réel du problème. L’**Adapter** agit comme un traducteur qui permet à votre code d’utiliser une classe, une API ou un module existant dont l’interface n’était pas compatible au départ. Le **Singleton**, quant à lui, impose une instance unique accessible globalement pour centraliser une ressource partagée ou coûteuse, tout en demandant de la prudence car il peut renforcer le couplage et ne protège pas automatiquement contre les modifications concurrentes des données.

## Factory

### 1. L'exemple du bar et du Mojito (très bon)

Imaginez un bar à cocktails :

* **Vous (le client)** : vous êtes le **code appelant**. Vous voulez un cocktail prêt à boire. Vous ne savez pas (et vous n'avez pas envie de savoir) comment doser le rhum, piler la menthe, presser le citron vert ou quel shaker utiliser. Vous dites simplement : *« Je voudrais un Mojito »* (ou *« une Piña Colada »*).
* **Le barman** : c'est la **Factory** (la Fabrique). Il reçoit votre demande, connaît la recette exacte et les étapes de fabrication, assemble tous les ingrédients en coulisses et vous rend un verre prêt à l'emploi.
* **Le Mojito (et les autres cocktails)** : ce sont les **objets créés** (les produits). Ils respectent tous un même « contrat » de base : ce sont tous des `Cocktail` que l'on peut `boire()`.

**Pourquoi c'est le pattern Factory ?**
Parce que **la création complexe est déléguée** à un spécialiste (le barman). Si demain la recette du Mojito change (on remplace le sucre blanc par du sucre de canne liquide), **vous n'avez rien à changer à votre façon de commander** : seul le barman modifie sa façon de préparer le verre.


### 2. Un deuxième exemple complémentaire : le guichet de transport (ou la borne de livraison)

Si vous voulez un exemple centré sur le choix automatique du bon objet selon la situation :

Imaginez un site e-commerce où vous cliquez sur **« Livrer mon colis »** en choisissant votre option (*Express*, *Standard* ou *Point Relais*) :

* **Le système de commande** passe votre choix au **service d'expédition (la Factory)**.
* Selon votre choix, la Factory décide toute seule s'il faut créer un objet `LivraisonAvion`, `LivraisonCamion` ou `LivraisonVelo`.
* Pour le reste de l'application, peu importe le véhicule choisi : tous ces objets ont une méthode commune `livrer()`.


### Ce qu'il faut retenir __Factory__

Le pattern **Factory** sert à **déléguer la création des objets** à une classe ou méthode dédiée, plutôt que de faire des `new Mojito()` ou `new LivraisonAvion()` partout dans le code.

Cela apporte deux avantages directs :

1. **Masquer la complexité de fabrication** : le client demande un résultat sans connaître la « recette » interne.
2. **Réduire le couplage** : le code client dépend d'une interface générale (`Cocktail` ou `Livraison`) et non des classes précises (`Mojito`, `LivraisonAvion`), ce qui permet d'ajouter facilement un nouveau cocktail ou un nouveau mode de transport sans tout casser.

Le pattern **Builder** (Monteur) sert à **construire un objet complexe étape par étape**, en séparant la construction de la représentation finale.

Il est particulièrement utile quand un objet a :

* Beaucoup d'options ou de paramètres (dont certains facultatifs), ce qui rendrait un constructeur classique difficile à lire (`new Burger(true, false, "cheddar", null, "bbq", true)`).
* Un assemblage progressif ou plusieurs variantes possibles à partir des mêmes étapes.


### L'exemple de la vie courante : composer un burger sur mesure

Reprenons la logique d'un comptoir de restauration :

* **Le client** : il indique ce qu'il veut étape par étape : *« Je veux un burger avec pain brioché, double steak, cheddar, sans oignon, sauce barbecue et bacon »*. Il ne gère pas l'assemblage lui-même.
* **Le Builder (le préparateur au poste)** : il dispose de méthodes dédiées pour chaque étape (`avecPain(...)`, `ajouterSteak(...)`, `ajouterFromage(...)`, `ajouterSauce(...)`) et garde l'état en cours de montage.
* **Le produit final (`Burger`)** : il n'est livré complet qu'à la fin (`construire()`), une fois toutes les options assemblées.


### Builder vs Factory : quelle différence ?

* **Factory (le barman qui fait un Mojito)** : vous demandez un type précis (*« Un Mojito »*), et la fabrique produit l'objet d'un bloc selon sa recette.
* **Builder (le burger sur mesure)** : vous assemblez l'objet **étape par étape** avec des options modulaires, souvent pour construire un objet complexe ou varié sans multiplier les constructeurs.


### Ce qu'il faut retenir

Le pattern **Builder** rend la création d'objets complexes **plus lisible, progressive et souple** : on enchaîne des étapes claires pour obtenir l'objet final, sans passer une longue liste de paramètres parfois inutiles ou ambigus.

Exemple de code python qui implémente le design Pater __Builder__ : 

```Python
from dataclasses import dataclass


@dataclass
class Burger:
    """Produit final construit par le Builder."""
    pain: str | None = None
    steaks: int = 0
    fromage: str | None = None
    bacon: bool = False
    sauce: str | None = None

    def __str__(self) -> str:
        options = [f"{self.steaks} steak(s)", f"pain {self.pain}"]
        if self.fromage:
            options.append(f"fromage {self.fromage}")
        if self.bacon:
            options.append("bacon")
        if self.sauce:
            options.append(f"sauce {self.sauce}")
        return "Burger : " + ", ".join(options)


class BurgerBuilder:
    """Construit un Burger étape par étape avec validation finale."""

    def __init__(self) -> None:
        # On ne met plus de pain ni de steak par défaut afin de forcer leur choix
        self._burger = Burger()

    def avec_pain(self, type_pain: str) -> "BurgerBuilder":
        self._burger.pain = type_pain.strip() if type_pain else None
        return self

    def ajouter_steaks(self, quantite: int = 1) -> "BurgerBuilder":
        self._burger.steaks += quantite
        return self

    def avec_fromage(self, fromage: str) -> "BurgerBuilder":
        self._burger.fromage = fromage
        return self

    def avec_bacon(self, actif: bool = True) -> "BurgerBuilder":
        self._burger.bacon = actif
        return self

    def avec_sauce(self, sauce: str) -> "BurgerBuilder":
        self._burger.sauce = sauce
        return self

    def _valider(self) -> None:
        """Vérifie que les éléments obligatoires sont présents."""
        erreurs: list[str] = []

        if not self._burger.pain:
            erreurs.append("un type de pain est obligatoire")

        if self._burger.steaks <= 0:
            erreurs.append("au moins un steak est obligatoire")

        if erreurs:
            raise ValueError("Impossible de construire le burger : " + " et ".join(erreurs) + ".")

    def construire(self) -> Burger:
        """Valide, retourne le burger final et réinitialise le builder."""
        self._valider()
        burger_final = self._burger
        self._burger = Burger()
        return burger_final


# --- Utilisation ---
builder = BurgerBuilder()

# 1. Burger valide
burger_ok = (
    builder
    .avec_pain("brioché")
    .ajouter_steaks(1)
    .avec_fromage("cheddar")
    .construire()
)
print(burger_ok)

# 2. Tentative sans steak ni pain -> lève ValueError
try:
    builder.avec_sauce("ketchup").construire()
except ValueError as e:
    print("Erreur :", e)

# 3. Tentative avec pain mais 0 steak -> lève ValueError
try:
    builder.avec_pain("sésame").construire()
except ValueError as e:
    print("Erreur :", e)
```

## Adapter

Le design pattern **Adapter** (Adaptateur) permet à **deux interfaces incompatibles de collaborer** sans modifier le code source existant de l'une ou de l'autre.

Concrètement, votre application moderne attend un certain format d'appel ou de données (par exemple une méthode `get_client()` renvoyant un objet JSON ou un dictionnaire structuré), tandis qu'un module existant expose une autre interface ou un autre format. L'**Adapter** se place au milieu et **traduit** la demande et la réponse.

### L'exemple de la vie courante : l'adaptateur de prise électrique en voyage

Imaginez que vous partez à l'étranger avec un chargeur de téléphone équipé d'une prise européenne, alors que la prise murale est d'un autre format :

* **Votre chargeur** : c'est votre **code client**. Il a besoin d'électricité et possède un embout précis.
* **La prise murale** : c'est le **système existant / incompatible**. Elle fournit l'énergie, mais sa forme ne correspond pas à votre chargeur.
* **L'adaptateur de voyage** : c'est le **pattern Adapter**. Il ne fabrique pas l'électricité et ne modifie ni le mur ni le chargeur ; il fait simplement la liaison physique et sécurisée entre les deux.

---

### Est-ce une bonne solution pour exploiter des données COBOL dans un format moderne ?

**Oui, c'est une très bonne approche au niveau applicatif**, à condition de bien distinguer **le décodage/la transformation des données** et **l'interface exposée à votre application moderne**.

#### Ce que l'Adapter fait bien dans ce contexte

* **Protéger le code moderne des formats COBOL :** Les données issues de systèmes COBOL arrivent souvent sous forme de fichiers plats à largeur fixe, de copybooks ou d'enregistrements encodés avec des types spécifiques (chaînes paddées d'espaces, dates au format `AAAAMMJJ`, nombres décimaux compacts). Un **Adapter** peut lire ces structures brutes et les transformer en objets ou structures propres (JSON, objets métier, dataclasses) attendus par votre application moderne.
* **Éviter de toucher au code COBOL sensible :** Sur des systèmes critiques (banque, assurance, paie), modifier le programme COBOL d'origine est souvent coûteux ou risqué. L'Adapter permet d'avancer côté moderne sans réécrire l'existant.
* **Permettre une migration progressive :** Si un jour vous remplacez la source COBOL par une nouvelle API ou une base moderne, le reste de votre application continue d'utiliser la même interface pendant que l'Adapter (ou une nouvelle implémentation) s'adapte.

#### Les limites à garder en tête

* **Le parsing pur n'est pas tout le pattern :** Décoder un fichier binaire ou un copybook COBOL demande souvent des parseurs dédiés ou des couches d'ETL/d'intégration. L'Adapter intervient surtout pour **harmoniser l'interface et la forme des données** face au consommateur moderne.
* **Attention aux conversions de types et de précision :** Les montants financiers ou dates en COBOL ont des règles précises (arrondis, décimales implicites, encodage des caractères). L'Adapter doit gérer ces conversions fidèlement pour éviter des erreurs silencieuses.
* **Volume et temps réel :** Pour des traitements lourds par lots (millions de lignes) ou du temps réel haute performance, on combine souvent des outils d'intégration/streaming ou une couche d'accès dédiée plutôt qu'un simple adaptateur unitaire en mémoire.

Voici un exemple simple et commenté en **Python** où un **Adapter** lit une ligne à largeur fixe (typique d'un enregistrement inspiré d'un *copybook* COBOL) et l'expose sous forme d'un dictionnaire moderne (prêt pour une API JSON ou un traitement moderne).

### Exemple : lecture d'un enregistrement client à largeur fixe

Supposons qu'une ligne brute à largeur fixe représente un client sur **48 caractères** :

* Positions `0..8` (8 car.) : identifiant client, ex. `"00001042"`
* Positions `8..28` (20 car.) : nom complet complété par des espaces (`"DUPONT JEAN         "`)
* Positions `28..36` (8 car.) : date de naissance au format `AAAAMMJJ` (`"19850412"`)
* Positions `36..48` (12 car.) : solde en centimes avec signe éventuel (`"+00000125450"` -> `1254.50`)

```python
from dataclasses import dataclass
from datetime import date
from decimal import Decimal
from typing import Protocol


# 1. Interface attendue par l'application moderne
class FournisseurClientModerne(Protocol):
    def obtenir_client(self) -> dict:
        """Retourne les données client dans un format structuré moderne."""
        ...


# 2. Classe existante (Adaptee) : lit une ligne brute à largeur fixe type COBOL
class LecteurCobolFixe:
    def __init__(self, ligne_brute: str) -> None:
        self.ligne_brute = ligne_brute

    def lire_enregistrement_brut(self) -> str:
        return self.ligne_brute


# 3. Modèle moderne optionnel pour structurer et valider les champs
@dataclass
class ClientModerne:
    id_client: int
    nom_complet: str
    date_naissance: date
    solde_euros: Decimal

    def to_dict(self) -> dict:
        return {
            "id": self.id_client,
            "nomComplet": self.nom_complet,
            "dateNaissance": self.date_naissance.isoformat(),
            "soldeEuros": float(self.solde_euros),
        }


# 4. Adapter : traduit le format fixe COBOL vers l'interface moderne
class CobolClientAdapter(FournisseurClientModerne):
    def __init__(self, lecteur_cobol: LecteurCobolFixe) -> None:
        self._lecteur_cobol = lecteur_cobol

    def _parser_date(self, brut: str) -> date:
        # Format AAAAMMJJ -> YYYY-MM-DD
        return date(int(brut[0:4]), int(brut[4:6]), int(brut[6:8]))

    def _parser_montant_centimes(self, brut: str) -> Decimal:
        # Ex. "+00000125450" -> 125450 centimes -> 1254.50 euros
        centimes = int(brut.strip())
        return Decimal(centimes) / Decimal("100")

    def obtenir_client(self) -> dict:
        ligne = self._lecteur_cobol.lire_enregistrement_brut()

        id_client = int(ligne[0:8].strip())
        nom_complet = " ".join(ligne[8:28].strip().split())
        date_naissance = self._parser_date(ligne[28:36])
        solde_euros = self._parser_montant_centimes(ligne[36:48])

        client = ClientModerne(
            id_client=id_client,
            nom_complet=nom_complet,
            date_naissance=date_naissance,
            solde_euros=solde_euros,
        )
        return client.to_dict()


# --- Utilisation côté application moderne ---
def afficher_fiche_client(fournisseur: FournisseurClientModerne) -> None:
    donnees = fournisseur.obtenir_client()
    print(donnees)


ligne_cobol = "00001042DUPONT JEAN         19850412+00000125450"
lecteur = LecteurCobolFixe(ligne_cobol)
adaptateur = CobolClientAdapter(lecteur)

afficher_fiche_client(adaptateur)

```

### Ce que montre cet exemple

* **Isolation du format legacy :** `afficher_fiche_client()` ne connaît ni les positions d'octets, ni les espaces de remplissage, ni le format `AAAAMMJJ`, ni la conversion des centimes en `Decimal`.
* **Précision financière avec `Decimal` :** Pour des montants monétaires, utiliser `Decimal` évite les erreurs d'arrondi propres aux flottants binaires (`float`) avant de formater la sortie.
* **Évolutivité :** Si demain la source passe d'un fichier fixe à une API REST renvoyant déjà du JSON, vous pouvez créer un autre adaptateur (ou une nouvelle implémentation de `FournisseurClientModerne`) sans réécrire le reste de l'application.

## SOLID

Les principes **SOLID** sont cinq règles de conception orientée objet qui aident à écrire du code plus lisible, évolutif, testable et facile à maintenir. Ils complètent naturellement les design patterns : SOLID guide la **structure des classes et dépendances**, tandis que les patterns fournissent des **solutions concrètes** à des problèmes récurrents.

### 1. S — Single Responsibility Principle (Responsabilité unique)

Une classe (ou un module) ne doit avoir **qu'une seule raison de changer**, autrement dit une seule responsabilité métier ou technique bien définie.

* **En pratique :** Évitez les « classes fourre-tout » qui gèrent à la fois le calcul d'une facture, son formatage PDF et son envoi par e-mail. Séparez ces rôles dans des classes distinctes.
* **Bénéfice :** Si la règle de calcul change, vous ne touchez pas à la logique d'envoi d'e-mail, et les tests unitaires sont plus simples.


### 2. O — Open/Closed Principle (Ouvert à l'extension, fermé à la modification)

Les entités logicielles (classes, modules, fonctions) doivent être **ouvertes à l'extension** (on peut ajouter de nouveaux comportements), mais **fermées à la modification** (on n'a pas besoin de réécrire le code existant qui fonctionne déjà).

* **En pratique :** Au lieu d'empiler des `if / elif` à chaque nouveau type de paiement ou de transporteur, on s'appuie sur une interface ou classe abstraite et on ajoute une nouvelle classe pour chaque nouveau cas.
* **Bénéfice :** Ajouter une fonctionnalité risque moins de casser l'existant.


### 3. L — Liskov Substitution Principle (Substitution de Liskov)

Si `S` est un sous-type de `T`, alors on doit pouvoir remplacer un objet de type `T` par un objet de type `S` **sans altérer le comportement attendu** du programme.

* **En pratique :** Une sous-classe ne doit pas affaiblir les préconditions, renforcer les postconditions ou lever des exceptions inattendues. L'exemple classique est le rectangle et le carré : forcer un `Carré` à hériter de `Rectangle` en modifiant la largeur dès qu'on change la hauteur casse les attentes d'un code qui manipule un rectangle indépendant.
* **Bénéfice :** Le polymorphisme reste fiable et prévisible.


### 4. I — Interface Segregation Principle (Ségrégation des interfaces)

Il vaut mieux plusieurs **interfaces petites et ciblées** qu'une **grosse interface unique** qui force les classes à implémenter des méthodes dont elles n'ont pas besoin.

* **En pratique :** Si une `ImprimanteSimple` n'imprime que du papier, elle ne devrait pas être forcée d'implémenter `scanner()` ou `envoyer_fax()` simplement parce qu'une interface `MachineBureau` regroupe tout.
* **Bénéfice :** Les classes n'ont pas de méthodes vides ou levant `NotImplementedError` par défaut.


### 5. D — Dependency Inversion Principle (Inversion des dépendances)

* Les modules de haut niveau (la logique métier) ne doivent pas dépendre des modules de bas niveau (détails techniques comme une base de données ou un client HTTP précis) : **les deux doivent dépendre d'abstractions**.
* Les abstractions ne doivent pas dépendre des détails ; ce sont les détails qui doivent dépendre des abstractions.
* **En pratique :** Votre service de commande dépend d'une abstraction `DepotCommandes` plutôt que d'une classe `PostgresDepotCommandes` instanciée directement à l'intérieur. C'est ici que l'on retrouve le lien direct avec le **couplage faible** évoqué au début de vos notes.
* **Bénéfice :** Vous pouvez changer de stockage ou injecter un faux dépôt (`mock`) pour les tests sans modifier la logique métier.

### Vue d'ensemble rapide

| Lettre | Principe | Idée centrale |
| --- | --- | --- |
| **S** | Single Responsibility | Une classe = une seule responsabilité / raison de changer. |
| **O** | Open/Closed | Ajouter du comportement par extension sans modifier l'existant. |
| **L** | Liskov Substitution | Une sous-classe peut remplacer sa classe parente sans casser la logique. |
| **I** | Interface Segregation | Préférer des interfaces précises plutôt qu'une interface trop large. |
| **D** | Dependency Inversion | Dépendre d'abstractions plutôt que d'implémentations concrètes. |

### Nuance importante pour bien appliquer SOLID

SOLID n'est pas un dogme rigide : créer dix petites interfaces ou dix classes pour un script de trente lignes ajouterait de la complexité inutile. On applique SOLID **là où le code évolue, se complexifie ou a besoin d'être testé et maintenu dans la durée**.

Voici un exemple simple et complet en **Python** autour d'un système de **notification de commande** (paiement, confirmation, export), d'abord écrit « à la va-vite » en violant les 5 principes **SOLID**, puis refactorisé proprement.

---

### 1. Avant refactorisation : ce qui pose problème

```python
class GestionnaireCommande:
    """Classe 'fourre-tout' qui viole plusieurs principes SOLID."""

    def traiter(self, mode_paiement: str, montant: float, email: str, exporter_csv: bool = False) -> None:
        # Violation de O (Open/Closed) : il faut modifier cette méthode pour chaque nouveau moyen de paiement
        if mode_paiement == "carte":
            print(f"Débit carte bancaire de {montant:.2f} EUR")
        elif mode_paiement == "paypal":
            print(f"Paiement PayPal de {montant:.2f} EUR")
        else:
            raise ValueError("Mode de paiement inconnu")

        # Violation de S (Single Responsibility) et D (Dependency Inversion) :
        # la logique de commande gère directement l'envoi d'e-mail et l'export fichier
        print(f"Envoi e-mail à {email} : commande de {montant:.2f} EUR confirmée.")

        if exporter_csv:
            with open("commandes.csv", "a", encoding="utf-8") as f:
                f.write(f"{email};{montant}\n")


class NotificationBasique:
    """Violation de I (Interface Segregation) et L (Liskov Substitution)."""

    def envoyer_email(self, destinataire: str, message: str) -> None:
        print(f"[EMAIL -> {destinataire}] {message}")

    def envoyer_sms(self, telephone: str, message: str) -> None:
        print(f"[SMS -> {telephone}] {message}")


class NotificationEmailSeulement(NotificationBasique):
    # Violation de L : casse le contrat de NotificationBasique en levant une erreur sur envoyer_sms
    def envoyer_sms(self, telephone: str, message: str) -> None:
        raise NotImplementedError("Ce canal ne gère pas les SMS")

```

#### Pourquoi ce code est fragile ?

* **S** : `GestionnaireCommande` calcule/valide le traitement, envoie des e-mails et écrit un fichier CSV.
* **O** : Ajouter un paiement par *virement* ou *Apple Pay* oblige à modifier `GestionnaireCommande.traiter()`.
* **L** : Si un appelant attend un `NotificationBasique` capable d'envoyer un SMS, `NotificationEmailSeulement` lève une `NotImplementedError` inattendue.
* **I** : Une classe qui ne fait que de l'e-mail est forcée d'exposer `envoyer_sms`.
* **D** : `GestionnaireCommande` dépend directement de `print` / `open("commandes.csv")` au lieu d'abstractions injectées.

---

### 2. Après refactorisation : application de SOLID

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass


@dataclass
class Commande:
    client_email: str
    montant: float


# --- I : Interfaces ciblées (ségrégation) ---
class NotificateurEmail(ABC):
    @abstractmethod
    def envoyer_email(self, destinataire: str, message: str) -> None:
        ...


class NotificateurSms(ABC):
    @abstractmethod
    def envoyer_sms(self, telephone: str, message: str) -> None:
        ...


class DepotCommandes(ABC):
    @abstractmethod
    def enregistrer(self, commande: Commande) -> None:
        ...


# --- O & L : Moyens de paiement extensibles et substituables ---
class MoyenPaiement(ABC):
    @abstractmethod
    def payer(self, montant: float) -> None:
        ...


class PaiementCarte(MoyenPaiement):
    def payer(self, montant: float) -> None:
        print(f"Débit carte bancaire de {montant:.2f} EUR")


class PaiementPaypal(MoyenPaiement):
    def payer(self, montant: float) -> None:
        print(f"Paiement PayPal de {montant:.2f} EUR")


class PaiementVirement(MoyenPaiement):
    """Ajouté sans modifier le code existant (Open/Closed)."""

    def payer(self, montant: float) -> None:
        print(f"Virement bancaire initié de {montant:.2f} EUR")


# --- S : Classes spécialisées (chacune sa responsabilité) ---
class ServiceEmail(NotificateurEmail):
    def envoyer_email(self, destinataire: str, message: str) -> None:
        print(f"[EMAIL -> {destinataire}] {message}")


class DepotCsvCommandes(DepotCommandes):
    def __init__(self, chemin_fichier: str) -> None:
        self.chemin_fichier = chemin_fichier

    def enregistrer(self, commande: Commande) -> None:
        with open(self.chemin_fichier, "a", encoding="utf-8") as f:
            f.write(f"{commande.client_email};{commande.montant}\n")


# --- D : Orchestration dépendante d'abstractions ---
class ProcesseurCommande:
    def __init__(
        self,
        moyen_paiement: MoyenPaiement,
        notificateur: NotificateurEmail,
        depot: DepotCommandes,
    ) -> None:
        self._moyen_paiement = moyen_paiement
        self._notificateur = notificateur
        self._depot = depot

    def valider(self, commande: Commande) -> None:
        self._moyen_paiement.payer(commande.montant)
        self._depot.enregistrer(commande)
        self._notificateur.envoyer_email(
            commande.client_email,
            f"Commande de {commande.montant:.2f} EUR confirmée.",
        )


# --- Utilisation ---
commande = Commande(client_email="alice@exemple.fr", montant=89.50)

processeur = ProcesseurCommande(
    moyen_paiement=PaiementVirement(),
    notificateur=ServiceEmail(),
    depot=DepotCsvCommandes("commandes.csv"),
)
processeur.valider(commande)
```

---

### 3. Ce qui change concrètement au quotidien

* **S (Single Responsibility) :** `DepotCsvCommandes` gère l'enregistrement, `ServiceEmail` gère l'e-mail, `ProcesseurCommande` coordonne la validation.
* **O (Open/Closed) :** Ajouter `PaiementVirement` ne demande aucune modification dans `ProcesseurCommande`.
* **L (Liskov Substitution) :** N'importe quel `MoyenPaiement` (`PaiementCarte`, `PaiementPaypal`, `PaiementVirement`) honore le contrat `payer(montant)` sans lever d'exception inattendue.
* **I (Interface Segregation) :** `ServiceEmail` n'a pas à implémenter `envoyer_sms` parce que `NotificateurEmail` et `NotificateurSms` sont séparés.
* **D (Dependency Inversion) :** `ProcesseurCommande` reçoit ses dépendances (`MoyenPaiement`, `NotificateurEmail`, `DepotCommandes`) de l'extérieur, ce qui facilite énormément les tests unitaires avec de fausses implémentations en mémoire.

## DAO

Le **DAO** (**Data Access Object** — Objet d'accès aux données) est un pattern qui **isole la logique d'accès aux données** (requêtes SQL, appels à une API externe, lecture de fichiers ou de bases NoSQL) derrière une interface dédiée.

Au lieu d'écrire des requêtes SQL ou des détails de stockage directement dans votre logique métier, le reste de l'application passe par un objet DAO qui expose des opérations claires comme `trouver_par_id()`, `sauvegarder()` ou `supprimer()`.


### L'exemple de la vie courante : le guichet d'archives d'une mairie

Imaginez que vous travaillez à la mairie et que vous avez besoin du dossier d'un habitant :

* **Le service d'état civil (le code métier)** : il veut traiter une demande (ex. délivrer un acte de naissance). Il n'a pas besoin de savoir si les registres sont rangés dans un classeur papier au sous-sol, sur un serveur local ou dans une base archivée.
* **L'archiviste au guichet (le DAO)** : il connaît l'emplacement exact et la méthode de recherche. Le service lui demande *« Donne-moi le dossier n° 402 »* ; l'archiviste va chercher le dossier, l'ouvre et le remet sous une forme exploitable.
* **Les registres (la source de données)** : c'est le stockage physique ou la base de données. Si la mairie change de système d'archivage, le service d'état civil continue de passer par l'archiviste sans changer sa façon de travailler.


### Pourquoi utiliser un DAO ?

* **Séparation des responsabilités (lien avec SOLID) :** La logique métier reste focalisée sur les règles du domaine, tandis que le DAO gère les détails techniques de persistence ou d'accès.
* **Facilité de changement de source de données :** Si vous passez d'un fichier, d'une base SQLite ou d'une ancienne source à une base PostgreSQL ou à un service distant, vous adaptez ou remplacez l'implémentation du DAO sans réécrire toute votre logique métier.
* **Tests plus simples :** Vous pouvez remplacer le DAO réel par un DAO en mémoire ou un *mock* pour tester vos règles métier rapidement, sans démarrer une vraie base de données.


### DAO vs Repository : quelle nuance ?

Dans les discussions d'architecture, **DAO** et **Repository** sont proches et parfois confondus :

* **DAO** est souvent plus proche du **stockage et des opérations CRUD** (lire, créer, mettre à jour, supprimer un enregistrement ou une table).
* **Repository** est souvent orienté **métier / agrégats** : il donne l'impression de manipuler une collection d'objets du domaine en mémoire, en masquant la façon dont ils sont persistés ou reconstruits.

Dans beaucoup de projets applicatifs courants, l'idée clé reste la même : **ne mélangez pas vos requêtes d'accès aux données avec votre logique métier**.

Voici un exemple simple et commenté en **Java** illustrant le pattern **DAO** sur une entité `Utilisateur`.

Il sépare clairement :

1. **L'objet métier (`Utilisateur`)** : représente la donnée manipulée par l'application.
2. **L'interface DAO (`UtilisateurDao`)** : définit les opérations d'accès aux données (CRUD) sans révéler comment elles sont réalisées.
3. **Une implémentation en mémoire (`UtilisateurDaoMemoire`)** : utile pour démarrer ou tester sans base de données.
4. **Une implémentation type base relationnelle (`UtilisateurDaoJdbc`)** : montre où se trouvent les détails techniques (connexion, requêtes SQL) sans polluer le reste du code.


### 1. L'objet métier et l'interface DAO

```java
import java.util.List;
import java.util.Optional;

// Modèle métier simple
class Utilisateur {
    private final int id;
    private String nom;
    private String email;

    public Utilisateur(int id, String nom, String email) {
        this.id = id;
        this.nom = nom;
        this.email = email;
    }

    public int getId() {
        return id;
    }

    public String getNom() {
        return nom;
    }

    public void setNom(String nom) {
        this.nom = nom;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    @Override
    public String toString() {
        return "Utilisateur{id=" + id + ", nom='" + nom + "', email='" + email + "'}";
    }
}

// Contrat DAO : ce que le code métier peut demander, sans connaître la source de données
interface UtilisateurDao {
    Optional<Utilisateur> trouverParId(int id);
    List<Utilisateur> listerTous();
    void enregistrer(Utilisateur utilisateur);
    void mettreAJour(Utilisateur utilisateur);
    boolean supprimerParId(int id);
}
```

---

### 2. Deux implémentations concrètes (Mémoire et JDBC)

```java
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Optional;

// Implémentation 1 : stockage en mémoire (pratique pour les tests ou prototypage)
class UtilisateurDaoMemoire implements UtilisateurDao {
    private final Map<Integer, Utilisateur> baseMemoire = new HashMap<>();

    @Override
    public Optional<Utilisateur> trouverParId(int id) {
        return Optional.ofNullable(baseMemoire.get(id));
    }

    @Override
    public List<Utilisateur> listerTous() {
        return new ArrayList<>(baseMemoire.values());
    }

    @Override
    public void enregistrer(Utilisateur utilisateur) {
        baseMemoire.put(utilisateur.getId(), utilisateur);
    }

    @Override
    public void mettreAJour(Utilisateur utilisateur) {
        baseMemoire.put(utilisateur.getId(), utilisateur);
    }

    @Override
    public boolean supprimerParId(int id) {
        return baseMemoire.remove(id) != null;
    }
}

// Implémentation 2 : squelette JDBC (le SQL reste confiné ici)
class UtilisateurDaoJdbc implements UtilisateurDao {
    private final Connection connection;

    public UtilisateurDaoJdbc(Connection connection) {
        this.connection = connection;
    }

    @Override
    public Optional<Utilisateur> trouverParId(int id) {
        String sql = "SELECT id, nom, email FROM utilisateurs WHERE id = ?";
        try (PreparedStatement stmt = connection.prepareStatement(sql)) {
            stmt.setInt(1, id);
            try (ResultSet rs = stmt.executeQuery()) {
                if (rs.next()) {
                    Utilisateur u = new Utilisateur(
                        rs.getInt("id"),
                        rs.getString("nom"),
                        rs.getString("email")
                    );
                    return Optional.of(u);
                }
            }
        } catch (SQLException e) {
            throw new RuntimeException("Erreur lors de la recherche de l'utilisateur " + id, e);
        }
        return Optional.empty();
    }

    @Override
    public List<Utilisateur> listerTous() {
        throw new UnsupportedOperationException("À implémenter avec SELECT * FROM utilisateurs");
    }

    @Override
    public void enregistrer(Utilisateur utilisateur) {
        throw new UnsupportedOperationException("À implémenter avec INSERT INTO utilisateurs...");
    }

    @Override
    public void mettreAJour(Utilisateur utilisateur) {
        throw new UnsupportedOperationException("À implémenter avec UPDATE utilisateurs...");
    }

    @Override
    public boolean supprimerParId(int id) {
        throw new UnsupportedOperationException("À implémenter avec DELETE FROM utilisateurs...");
    }
}
```

---

### 3. Utilisation par un service métier

Le service métier ne connaît que `UtilisateurDao` : il n'écrit aucune requête SQL et ne dépend pas d'une base précise.

```java
// Service métier : dépend uniquement de l'interface UtilisateurDao
class ServiceUtilisateur {
    private final UtilisateurDao utilisateurDao;

    public ServiceUtilisateur(UtilisateurDao utilisateurDao) {
        this.utilisateurDao = utilisateurDao;
    }

    public void inscrire(int id, String nom, String email) {
        Utilisateur nouvelUtilisateur = new Utilisateur(id, nom, email);
        utilisateurDao.enregistrer(nouvelUtilisateur);
    }

    public String afficherFiche(int id) {
        return utilisateurDao.trouverParId(id)
            .map(Utilisateur::toString)
            .orElse("Utilisateur introuvable (id=" + id + ")");
    }
}

public class MainDao {
    public static void main(String[] args) {
        UtilisateurDao dao = new UtilisateurDaoMemoire();
        ServiceUtilisateur service = new ServiceUtilisateur(dao);

        service.inscrire(1, "Camille Laurent", "camille@exemple.fr");
        System.out.println(service.afficherFiche(1));
        System.out.println(service.afficherFiche(99));
    }
}
```

### Ce qu'il faut retenir de cet exemple Java

* **Isolation du SQL / stockage :** `ServiceUtilisateur` appelle `trouverParId` et `enregistrer`, sans savoir s'il s'agit d'une `Map` en mémoire, de JDBC ou d'un appel REST.
* **Retour `Optional<Utilisateur>` :** Évite les `null` non maîtrisés lorsqu'un identifiant n'existe pas.
* **Testabilité :** Vous pouvez passer `UtilisateurDaoMemoire` dans vos tests unitaires sans démarrer de base de données.

---

Voici un exemple très courant dans les applications de gestion : **la fiche produit d'un site e-commerce**.


### Le scénario : consulter et mettre à jour le stock d'un produit

Imaginez un site de vente en ligne. Lorsqu'un client ouvre la page d'un produit ou passe commande, l'application a besoin de :

* **Lire** les informations du produit (nom, prix, stock disponible),
* **Mettre à jour** la quantité en stock après un achat,
* **Ajouter** ou **supprimer** un produit dans le catalogue.


### Comment les rôles se répartissent avec un DAO

* **Le service de commande / catalogue (la logique métier) :** Il vérifie si le stock suffit pour valider le panier et demande une mise à jour. Il ne sait pas écrire du SQL ni gérer les connexions à la base de données.
* **Le `ProduitDao` (l'objet d'accès aux données) :** C'est lui qui porte les opérations d'accès aux données : `trouverParId(id)`, `enregistrer(produit)`, `mettreAJourStock(id, nouvelleQuantite)`, `supprimer(id)`.
* **La base de données (ex. PostgreSQL ou MySQL) :** C'est là où les lignes de la table `produits` sont réellement stockées.


### Pourquoi c'est un cas d'école très fréquent ?

* **Centralisation des requêtes :** Si la structure de la table `produits` change (par exemple, une colonne renommée ou une jointure ajoutée), vous modifiez **le `ProduitDao**` à un seul endroit, plutôt que de chercher des requêtes dispersées partout dans l'application.
* **Changement de stockage plus souple :** Si demain les produits sont lus depuis une autre source ou une nouvelle base, le reste du code continue d'appeler les mêmes méthodes du DAO.
* **Tests facilités :** Pour tester le calcul d'une commande sans toucher à la vraie base de données, on peut brancher un `ProduitDao` simulé (en mémoire) qui renvoie des produits fictifs.

## Repository

Le pattern **Repository** (Dépôt) est une approche de conception qui **donne l'impression au reste de l'application de manipuler une collection d'objets métier en mémoire** (comme une liste ou un dictionnaire), tout en cachant la façon dont ces objets sont enregistrés, retrouvés ou reconstruits.

Il est très proche du **DAO** évoqué précédemment, mais son angle est davantage orienté **métier et domaine** que « table ou requête technique ».

---

### L'exemple de la vie courante : la bibliothèque de prêt d'une ville

Imaginez le service de prêt d'une bibliothèque :

* **Le bibliothécaire au comptoir (le code métier)** : il veut savoir si le roman *« 1984 »* est disponible ou enregistrer le retour d'un livre. Il ne veut pas se demander s'il faut fouiller dans les rayonnages de la réserve, vérifier une fiche carton ou interroger un registre informatique.
* **Le catalogue et le service de gestion des rayons (le Repository)** : il donne au bibliothécaire l'impression de gérer un ensemble cohérent de **livres** (`trouverLivreParTitre()`, `enregistrerPret()`). En coulisses, il sait où se trouve physiquement l'ouvrage et comment mettre à jour son statut.
* **Les étagères et le registre (la persistence)** : c'est le stockage réel. Si la bibliothèque passe d'un registre papier à une base informatique, le bibliothécaire continue de travailler avec les mêmes opérations sur les livres.


### Ce que fait concrètement un Repository

Un **Repository** expose généralement des méthodes parlantes pour le domaine métier, par exemple :

* `trouver_commande_en_cours(id_client)`
* `rechercher_clients_actifs()`
* `enregistrer(commande)`

Derrière ces méthodes, l'implémentation concrète peut s'appuyer sur **Spring Data JPA**, **JDBC**, un **ORM** (comme SQLAlchemy ou Hibernate), un cache ou même un **appel REST** — sans exposer ces détails au service métier.

### Repository vs DAO : comment les distinguer en pratique ?

| Critère | DAO (Data Access Object) | Repository |
| --- | --- | --- |
| **Angle principal** | Accès aux données et opérations techniques/CRUD (`insert`, `select`, `update`, `delete`). | Collection d'objets du domaine métier (`ajouter`, `trouverParCritereMetier`). |
| **Granularité** | Souvent proche d'une table, d'un fichier ou d'un endpoint précis. | Souvent centré sur un **objet métier complet ou un agrégat** (ex. une `Commande` et ses lignes cohérentes). |
| **Usage fréquent** | Couches d'accès aux données classiques, bases relationnelles, wrappers de stockage. | Architecture orientée domaine (DDD), services applicatifs modernes, Spring / ORM. |

Dans beaucoup de projets courants, les développeurs utilisent les deux termes de façon proche lorsque l'objectif est le même : **garder la logique métier propre et isolée des détails de stockage ou de réseau**.


### Quand le Repository est particulièrement utile

* **Logique métier riche :** Quand votre application manipule des règles métier précises plutôt que de simples formulaires CRUD.
* **Tests rapides :** Vous pouvez fournir une implémentation `InMemoryRepository` (en mémoire) pour tester vos services métier sans base de données.
* **Évolution de la persistence :** Changer de moteur de base, optimiser une requête ou ajouter un cache se fait dans l'implémentation du Repository sans toucher aux règles métier.

À l'inverse, pour une application très simple qui fait uniquement du CRUD direct sans règles métier complexes, ajouter une couche `Repository` supplémentaire peut parfois être superflu.

## Différences entre DAO et Repository

Les patterns **DAO** (*Data Access Object*) et **Repository** poursuivent un objectif commun : **isoler l'accès aux données de la logique métier**. Pourtant, ils ne partent pas du même point de vue et ne s'emploient pas au même niveau d'abstraction.

---

### 1. L'angle de vue : technique de stockage vs domaine métier

* **DAO :** Il est orienté **accès aux données et persistance**. Il s'intéresse à la façon dont on lit, écrit, met à jour ou supprime des enregistrements (souvent alignés sur une table, un fichier ou un appel technique).
* **Repository :** Il est orienté **domaine métier**. Il simule une **collection d'objets métier en mémoire** et cache la manière dont ils sont persistés ou reconstruits (tables multiples, ORM, cache, service externe).

---

### 2. Granularité et périmètre des données

* **DAO :** Il manipule fréquemment des **entités simples ou des tables/structures précises** (par exemple un `ProduitDao` pour la table `produits`, ou un DAO dédié à une requête ou un fichier).
* **Repository :** Il manipule souvent une **entité complète ou un agrégat** (par exemple un `CommandeRepository` qui charge et persiste une `Commande` avec ses lignes tout en préservant la cohérence des règles métier).

---

### 3. Vocabulaire des méthodes

* **DAO :** Les noms de méthodes rappellent souvent les opérations d'accès ou de stockage : `insert`, `update`, `delete`, `findById`, `getAll`, ou des méthodes très liées à la source de données.
* **Repository :** Les noms de méthodes parlent le **langage du métier** : `trouverCommandesActivesClient(idClient)`, `rechercherFacturesImpayees()`, `ajouter(commande)`.

---

### 4. Comparaison côte à côte

| Critère | DAO | Repository |
| --- | --- | --- |
| **Objectif principal** | Encapsuler l'accès physique aux données (SQL, fichiers, API, etc.). | Fournir une interface proche d'une collection d'objets du domaine métier. |
| **Point de départ** | La source de données / le stockage / le CRUD. | Le modèle métier / les besoins du domaine. |
| **Complexité cachée** | Requêtes SQL, connexions, curseurs, formats d'enregistrement. | Assemblage d'objets complexes, cohérence d'agrégat, persistance/ORM. |
| **Contexte typique** | Couches d'accès aux données classiques, JDBC, bases relationnelles, wrappers de stockage. | Architecture orientée domaine (DDD), logique métier riche, frameworks/ORM. |

---

### 5. Nuance importante sur le terrain

Dans de nombreux projets quotidiens — notamment avec des frameworks modernes — les deux termes sont employés de façon proche, voire interchangeable, pour désigner simplement **la couche qui dialogue avec la base de données ou le stockage**. Ce qui compte vraiment en pratique, c'est que **votre logique métier ne contienne pas de requêtes SQL ou de détails d'infrastructure dispersés partout**..

Le design pattern **Decorator** (Décorateur) permet d'**ajouter dynamiquement de nouvelles responsabilités ou comportements à un objet**, en l'enveloppant dans d'autres objets qui partagent la même interface.

Au lieu de créer une multitude de sous-classes pour chaque combinaison d'options (ex. *CafeAvecLait*, *CafeAvecSucre*, *CafeAvecLaitEtSucre*, *CafeAvecLaitSucreEtCaramel*), on empile des décorateurs autour de l'objet de base.

---

### L'exemple de la vie courante : personnaliser un café au comptoir

Imaginez que vous commandez un café simple :

* **L'objet de base (`CafeSimple`)** : un espresso nature. Il a un prix et une description (`cout()`, `description()`).
* **Les décorateurs (`AvecLait`, `AvecSucre`, `AvecCaramel`)** : chacun entoure le café reçu et ajoute son propre supplément au prix et à la description, tout en restant lui-même un `Cafe` (même interface).
* **L'assemblage** : vous prenez un `CafeSimple`, vous l'enveloppez dans `AvecLait`, puis le tout dans `AvecCaramel`. Pour l'appelant, le résultat reste un `Cafe` avec les mêmes méthodes `cout()` et `description()`.


### Pourquoi utiliser le Decorator plutôt que l'héritage ?

* **Éviter l'explosion du nombre de classes :** Avec 4 options combinables, l'héritage statique demanderait vite des dizaines de sous-classes. Avec le Decorator, vous créez une classe par option et vous les combinez à la demande.
* **Combinaisons dynamiques à l'exécution :** Le choix des options se fait au moment où l'application tourne (selon le choix de l'utilisateur ou la configuration), pas figé dans une hiérarchie rigide.
* **Respect du principe Open/Closed (SOLID) :** Ajouter une nouvelle option (ex. `AvecChantilly`) consiste à ajouter une nouvelle classe décorateur sans modifier les classes existantes.


### Decorator vs Adapter : quelle différence ?

* **Decorator** garde **la même interface** que l'objet d'origine et **enrichit** son comportement (ou ajoute des options/étapes avant ou après l'appel).
* **Adapter** change **l'interface** pour rendre deux systèmes incompatibles capables de communiquer.

### Limite à garder en tête

Empiler trop de décorateurs les uns dans les autres peut rendre le débogage et la lecture de la pile d'appels (*call stack*) un peu plus délicats, ainsi que l'ordre d'exécution si deux décorateurs modifient le même résultat (par exemple une remise appliquée avant ou après une taxe).

## Decorator

Le design pattern **Decorator** (Décorateur) permet d'**ajouter dynamiquement de nouvelles responsabilités ou comportements à un objet**, en l'enveloppant dans d'autres objets qui partagent la même interface.

Au lieu de créer une multitude de sous-classes pour chaque combinaison d'options (ex. *CafeAvecLait*, *CafeAvecSucre*, *CafeAvecLaitEtSucre*, *CafeAvecLaitSucreEtCaramel*), on empile des décorateurs autour de l'objet de base.


### L'exemple de la vie courante : personnaliser un café au comptoir

Imaginez que vous commandez un café simple :

* **L'objet de base (`CafeSimple`)** : un espresso nature. Il a un prix et une description (`cout()`, `description()`).
* **Les décorateurs (`AvecLait`, `AvecSucre`, `AvecCaramel`)** : chacun entoure le café reçu et ajoute son propre supplément au prix et à la description, tout en restant lui-même un `Cafe` (même interface).
* **L'assemblage** : vous prenez un `CafeSimple`, vous l'enveloppez dans `AvecLait`, puis le tout dans `AvecCaramel`. Pour l'appelant, le résultat reste un `Cafe` avec les mêmes méthodes `cout()` et `description()`.

### Pourquoi utiliser le Decorator plutôt que l'héritage ?

* **Éviter l'explosion du nombre de classes :** Avec 4 options combinables, l'héritage statique demanderait vite des dizaines de sous-classes. Avec le Decorator, vous créez une classe par option et vous les combinez à la demande.
* **Combinaisons dynamiques à l'exécution :** Le choix des options se fait au moment où l'application tourne (selon le choix de l'utilisateur ou la configuration), pas figé dans une hiérarchie rigide.
* **Respect du principe Open/Closed (SOLID) :** Ajouter une nouvelle option (ex. `AvecChantilly`) consiste à ajouter une nouvelle classe décorateur sans modifier les classes existantes.

### Decorator vs Adapter : quelle différence ?

* **Decorator** garde **la même interface** que l'objet d'origine et **enrichit** son comportement (ou ajoute des options/étapes avant ou après l'appel).
* **Adapter** change **l'interface** pour rendre deux systèmes incompatibles capables de communiquer.

### Limite à garder en tête

Empiler trop de décorateurs les uns dans les autres peut rendre le débogage et la lecture de la pile d'appels (*call stack*) un peu plus délicats, ainsi que l'ordre d'exécution si deux décorateurs modifient le même résultat (par exemple une remise appliquée avant ou après une taxe).

**Oui, c'est tout à fait ça !** L'œuf mayonnaise est une excellente image culinaire pour comprendre le pattern **Decorator**.

Voici comment les rôles se répartissent dans cette assiette :

* **L'œuf dur nature** : c'est votre **objet de base**. Il se suffit déjà à lui-même, il est comestible et possède ses propriétés de base (goût, calories).
* **La mayonnaise** : c'est le **décorateur**. Elle vient *envelopper* l'œuf. Elle ne le transforme pas en un autre plat (ce n'est pas devenu une omelette ou un gâteau, ça reste un plat à base d'œuf que l'on mange avec une fourchette), mais elle lui ajoute une couche de saveur et quelques calories en plus.
* **Empiler les décorateurs** : si l'envie vous prend d'ajouter une pincée de ciboulette par-dessus, puis un peu de paprika, vous continuez simplement d'envelopper votre plat initial avec de nouvelles couches, à la carte, sans avoir eu besoin d'inventer une recette figée à l'avance pour chaque combinaison possible.

En programmation, c'est exactement pareil : on prend un objet qui fonctionne déjà, et on l'enveloppe dans d'autres objets qui ajoutent des fonctionnalités (du « goût » supplémentaire), tout en conservant exactement la même façon de l'utiliser.

La nuance, c’est que le Décorateur ajoute généralement un comportement ou une responsabilité à un objet en conservant la même interface. Par exemple, un décorateur de compte pourrait ajouter une fonctionnalité de journalisation autour des opérations du compte. La mayonnaise illustre bien l’idée d’« ajouter sans remplacer », mais moins celle de conserver la même interface.

## Proxy

Le design pattern **Proxy** (Mandataire / Procurateur) consiste à placer un **objet intermédiaire** (le Proxy) entre le code client et un **objet réel** (`RealSubject`), tout en gardant **la même interface**.

Au lieu d'appeler directement l'objet réel, le client appelle le Proxy. Celui-ci décide s'il transmet la demande à l'objet réel, quand le faire, ou s'il peut déjà répondre (par exemple via un cache ou un contrôle d'accès).


### La métaphore : l'agent immobilier ou l'assistant de direction

Imaginez un dirigeant très occupé ou un propriétaire qui souhaite vendre un bien :

* **Le client** : veut consulter un dossier confidentiel ou visiter un bien.
* **L'assistant / l'agent immobilier (le Proxy)** : il se présente avec le même rôle apparent d'interlocuteur. Il vérifie d'abord si la demande est légitime, filtre les questions simples, évite de déranger le dirigeant pour rien, et ne sollicite la personne réelle que lorsque c'est vraiment nécessaire.
* **Le dirigeant / le propriétaire (l'objet réel)** : il n'intervient qu'au bon moment, protégé des appels inutiles ou non autorisés.

### Un exemple courant : le chargement différé d'images lourdes (ou le cache d'accès)


Dans une application ou une page contenant beaucoup d'images haute définition ou de fichiers volumineux :

* **L'objet réel (`ImageHauteDefinition`)** : charger l'image depuis le disque ou le réseau coûte du temps et de la mémoire.
* **Le Proxy (`ImageProxy`)** : il expose la même méthode `afficher()`. Tant que l'utilisateur n'a pas besoin de voir l'image, le Proxy ne charge pas le fichier lourd (ou affiche un aperçu léger). Dès que `afficher()` est réellement invoqué, il charge l'image réelle une fois, la garde en mémoire (cache) et l'affiche.

Autre exemple très fréquent : un **proxy de sécurité / contrôle d'accès** qui vérifie les droits de l'utilisateur avant d'autoriser la suppression ou la modification d'une ressource sensible.


### Proxy vs Decorator et Adapter : repères rapides

* **Proxy** : contrôle **l'accès** à un objet existant (chargement paresseux, mise en cache, droits d'accès, accès distant) avec la même interface.
* **Decorator** : **enrichit** dynamiquement le comportement ou les options d'un objet (comme ajouter de la mayonnaise ou des épices sur un plat).
* **Adapter** : **traduit** une interface incompatible vers une autre interface attendue.

## Strategy

Le design pattern **Strategy** (Stratégie) permet de **définir une famille d'algorithmes ou de façons de faire**, de **placer chacun dans une classe ou un composant distinct**, et de **les rendre interchangeables** pendant l'exécution du programme.

Au lieu d'écrire de grands blocs `if / elif / else` ou `switch` pour choisir comment exécuter une tâche, le contexte (l'objet principal) délègue l'exécution à un objet stratégie qui respecte une interface commune.


### La métaphore : aller du point A au point B selon le contexte

Imaginez que vous devez vous rendre de votre domicile au centre-ville :

* **Votre objectif (le contexte)** : arriver à destination (`seDeplacer(destination)`).
* **Les stratégies (`Voiture`, `Velo`, `Bus`, `APied`)** : chaque moyen de transport est une façon différente d'atteindre le même but, avec ses propres avantages (rapidité, coût, météo, embouteillages).
* **Le choix dynamique** : s'il pleut et que vous êtes pressé, vous prenez la stratégie `Bus` ou `Voiture` ; s'il fait beau et que le trafic est dense, vous passez à la stratégie `Velo`. Le but reste le même, seule la méthode change, sans réécrire votre trajet de vie.


### Un exemple courant en développement : le calcul des frais de livraison (ou d'une remise)

Dans une application e-commerce, le calcul du coût de livraison ou d'une réduction peut varier selon des règles distinctes :

* **Livraison standard** : tarif fixe ou basé sur le poids.
* **Livraison express** : majoration pour livraison rapide.
* **Retrait en magasin / Click & Collect** : gratuit (`0 €`).

Avec le pattern **Strategy** :

1. Le service de commande reçoit ou sélectionne une `StrategieLivraison` (par exemple `LivraisonExpress`).
2. Il appelle simplement `strategie.calculer_frais(panier)`.
3. Ajouter demain une stratégie `LivraisonPointRelais` consiste à créer une nouvelle classe de stratégie, sans modifier les règles des autres modes de livraison ni alourdir le service de commande.


### Strategy vs Factory : repères rapides

* **Strategy** porte sur **la façon d'exécuter un comportement ou un calcul interchangeable** (comment payer, comment trier, comment calculer un tarif).
* **Factory** porte sur **la création d'un objet** (fabriquer le bon objet selon une demande ou un type).


## Architecture en couche

L'**architecture en couches** (*Layered Architecture*) organise une application en blocs horizontaux superposés, chacun ayant un rôle précis. Elle répond directement à votre besoin de séparer les fonctions métier des autres préoccupations techniques.


### Les couches classiques d'une application

La découpe la plus courante comporte trois couches principales (parfois complétées par une couche d'accès aux données ou d'intégration externe) :

* **Couche Présentation (*Presentation Layer*) :** Reçoit les requêtes de l'utilisateur ou d'un système externe (écran web, application mobile, contrôleur REST) et formate la réponse. Elle ne contient pas de règles métier complexes ni de requêtes SQL.
* **Couche Métier (*Business Logic Layer*) :** Contient les règles du domaine, les calculs, les validations et l'orchestration des opérations (par exemple vérifier le stock, appliquer une remise ou valider une commande). C'est le cœur fonctionnel de l'application.
* **Couche Persistance / Accès aux données (*Persistence / Data Layer*) :** Gère la lecture, l'écriture et la mise à jour des données via une base relationnelle, un ORM, un système de fichiers ou un service externe (via des patterns comme **DAO** ou **Repository**).


### La règle d'or : la dépendance vers le bas

Dans une architecture en couches stricte, **une couche ne communique qu'avec la couche située directement en dessous** :

1. La **Présentation** appelle la **Logique métier**.
2. La **Logique métier** appelle la **Persistance** (ou un service d'accès aux données).
3. La **Persistance** ne dépend pas de la **Logique métier** ni de la **Présentation**.

Cette discipline évite qu'un écran ou une route HTTP exécute du SQL directement, ou qu'une règle métier dépende du format d'affichage d'un formulaire.


### Avantages et limites à garder en tête

* **Avantages :** Clarté du rangement, répartition des rôles entre développeurs, maintenance plus simple quand un détail technique change (changer de requête SQL sans toucher à l'interface).
* **Limites :** Si chaque opération traverse systématiquement toutes les couches pour de simples lectures sans logique métier, le code peut devenir verbeux (« pass-through »). De plus, dans les architectures modernes, la couche métier dépend parfois d'interfaces définies par le domaine plutôt que de la couche technique inférieure (idée proche de l'**architecture hexagonale**).

## Consignes pour réaliser les exercices prévus pour le module

Exercice surtout faire le UML.

Prendre le temps de faire l'architecture. 

Faire une veille SOLID.