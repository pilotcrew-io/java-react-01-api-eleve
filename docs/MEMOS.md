# Mémos 01 — Qui décide de quoi ?

## Une frontière pour chaque responsabilité

Une fonction qui décide `montant = prixCatalogue × quantité` ne devrait pas dépendre d’un objet de requête HTTP. On peut ainsi vérifier la règle avec deux entrées en mémoire. Le contrôleur, lui, connaît les routes, les statuts et la transformation JSON. Le dépôt répond à une question différente : retrouver une commande créée. Cette séparation permet d’identifier la couche responsable d’un échec sans imposer une architecture excessive.

Un DTO d’entrée décrit ce que le consommateur a le droit de demander. Un DTO de sortie décrit ce que le service promet de rendre. Dans l’exercice, les deux objets diffèrent volontairement : le client choisit un produit et une quantité ; le serveur ajoute référence, prix calculé et devise.

## Une unité explicite évite un calcul ambigu

`amountMinor: 5000` signifie ici 5000 centimes fictifs EUR. Le domaine conserve un entier et n’arrondit pas un calcul en virgule flottante. Le format « 50,00 € » appartient à l’affichage. Ce choix est limité à notre catalogue EUR ; il ne définit pas la précision de toutes les monnaies. La borne de quantité garde les valeurs de la séance petites ; un modèle général doit aussi traiter dépassement numérique et règles d’arrondi.

Le navigateur est contrôlable par la personne qui l’utilise. Même un formulaire excellent n’empêche pas une requête construite avec curl. Notre invariant doit donc rester vrai en appelant directement l’API : un montant ajouté dans le JSON est refusé, et le serveur choisit toujours son catalogue.

## Trois contrôles différents

| Contrôle | Exemple du cours | Ce qu’il ne remplace pas |
|---|---|---|
| Désérialisation | JSON valide, quantité entière | Existence du produit |
| Validation de l’entrée | quantité présente entre 1 et 5 | Règle de prix du catalogue |
| Règle métier | le produit existe et fournit la devise | Rendu accessible dans React |

Avec Spring MVC, la validation s’applique aux objets annotés et les erreurs se traitent à la frontière HTTP. Les annotations seules n’établissent pas tout le contrat : tester la sérialisation effective, notamment une valeur décimale envoyée à un entier. [Validation Spring](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-validation.html).

## Une erreur fait partie de l’interface

La réponse de création et l’erreur doivent être stables pour leurs consommateurs. Le cours utilise Problem Details pour les erreurs et ajoute un code applicatif. Le message peut évoluer sans obliger React à chercher une sous-chaîne. Centraliser la traduction évite qu’une route rende une pile Java et une autre un texte imprévisible. [Réponses d’erreur Spring](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-rest-exceptions.html).

## Ce que démontre chaque test

Un test du calcul vérifie une règle isolée. Un test MockMvc traverse l’adaptation HTTP sans ouvrir un vrai serveur TCP. Lancer le JAR puis utiliser curl vérifie en plus le packaging et le démarrage. Aucun de ces essais ne démontre la durabilité après arrêt : le dépôt mémoire n’en offre pas. C’est une limite du produit construit, pas un échec de Maven.

## Questions de compréhension

1. Pourquoi ne pas sérialiser directement la Map du dépôt ?
2. Une quantité 1.5 est-elle encore observable après sa conversion éventuelle en entier ?
3. Quel test échoue si le prix est multiplié deux fois par la quantité ?
4. Pourquoi une réponse 201 et une réponse 400 demandent-elles des assertions différentes ?
5. Que faudrait-il changer pour conserver les commandes après redémarrage ?
