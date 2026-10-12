# Lab 01 — Une API de commande fiable

**4 h 30 · dominante Java.** Une équipe veut afficher un panier puis créer une commande. Elle ne peut pas accepter un prix envoyé librement par le navigateur. Votre mission consiste à placer la règle de calcul dans une API, à rendre les erreurs prévisibles et à produire des preuves de son comportement.

## À construire

Créer `backend/pom.xml`, l’arborescence Maven, les sources et les tests. La copie élève ne contient pas de code exécutable à lancer. Choisir un package cohérent, par exemple `io.pilotcrew.payments`. Prévoir une classe de démarrage Spring Boot, des DTO de requête/réponse, un catalogue, un service de commandes et un dépôt mémoire. Ne pas générer une architecture de dizaines de classes : chaque séparation doit répondre à une question de test ou de responsabilité.

Le projet déclare Java 21, les starters `spring-boot-starter-webmvc`, `spring-boot-starter-validation` et `spring-boot-starter-webmvc-test` (ce dernier au scope test), ainsi que le plugin de packaging Spring Boot. Fixer le nom final du JAR à `pilotcrew-payment-api`. Utiliser l’injection par constructeur pour rendre les dépendances visibles. Faire écouter le serveur sur `127.0.0.1:8080`.

## 1. Reformuler le contrat — 20 min

Compléter les quatre premiers cas du journal : produit demandé, quantité 2, quantité 0, produit inconnu. Pour `demo-ticket` et quantité 2, le total attendu est **5000** unités mineures. Pourquoi le calcul ne doit-il pas lire un champ `amountMinor` fourni par le client ? Quelles requêtes essaieriez-vous si le formulaire empêchait déjà la quantité 0 ?

Dessiner la chaîne requête JSON → validation → catalogue et calcul → dépôt mémoire → réponse. Un contrôleur traduit le protocole ; le service décide ce qu’est une commande valide. La mémoire permet de consulter un identifiant après création et ne constitue pas une base durable.

## 2. Préparer les frontières — 40 min

Créer un DTO d’entrée dont les seules propriétés sont `productId` et `quantity`. Employer un entier nullable dans le DTO si vous voulez distinguer une quantité absente d’une valeur zéro. Les contraintes doivent couvrir champ absent, identifiant vide et limites 1 à 5. Prévoir des objets de sortie distincts afin de ne pas sérialiser involontairement la structure interne du dépôt.

Créer un catalogue immutable contenant le produit fictif du contrat. Un service reçoit un identifiant de produit et une quantité, recherche le produit, calcule le montant puis génère un identifiant de commande. Le dépôt mémoire sauvegarde l’objet et sait répondre « absent ». Garder le calcul testable sans démarrer Spring.

Checkpoint : `mvn -f backend/pom.xml -B test` compile le projet et exécute au moins un vrai test ; un résultat « 0 test » n’est pas une preuve. Expliquer à un pair quelle classe connaît HTTP et quelle classe décide le prix.

## 3. Construire le parcours nominal — 60 min

Implémenter `GET /api/products`, `POST /api/orders` et `GET /api/orders/{id}`. La création renvoie 201, le corps de la commande et `Location: /api/orders/<id-retourné>`. Une consultation renvoie exactement la commande stockée. Les montants sont des entiers Java, avec un calcul qui ne passe jamais par `double`. Le prix et la devise sont copiés depuis le catalogue au moment de la création : ils ne sont pas recalculés à partir d’une valeur du navigateur.

Les identifiants sont générés côté serveur et traités comme références opaques dans l’interface. Pour cette étape, une Map en mémoire suffit ; rendre sa mutation cohérente dans une seule JVM. Ne pas ouvrir de session utilisateur ni de réseau PSP.

Démarrer le serveur, puis exécuter dans l’autre terminal :

```bash
mvn -f backend/pom.xml spring-boot:run
```

```bash
curl -i http://127.0.0.1:8080/api/products
curl -i -X POST http://127.0.0.1:8080/api/orders   -H 'Content-Type: application/json'   --data '{"productId":"demo-ticket","quantity":2}'
```

Copier l’identifiant réellement reçu et consulter `GET /api/orders/<cet-identifiant>`. Dans le journal, relever statut, Content-Type, Location et total. Ne pas noter uniquement « ça marche ».

## 4. Rendre les erreurs prévisibles — 40 min

Déclencher la validation du DTO dans le contrôleur. Ajouter un traitement centralisé des erreurs qui distingue entrée invalide (400), référence absente (404) et méthode non prise en charge (405). Pour 405, vérifier que `Allow` annonce la méthode disponible. Configurer le rejet des propriétés JSON inconnues afin qu’un champ ajouté comme `amountMinor` produise 400. Une donnée rejetée ne crée aucune commande.

Rendre les erreurs en `application/problem+json`. Prévoir un `code` stable pour que l’interface n’ait pas à analyser le texte français. Les erreurs de JSON mal formé et de validation ont également une réponse compréhensible ; aucune exception Java ni trace interne n’est renvoyée. Ne convertir pas toutes les erreurs en 200 pour simplifier le frontend.

Essayer quantité absente, 0, 6, décimale, chaîne, produit inconnu et champ `amountMinor`. Dans ce contrat, une quantité telle que 1.5 doit être rejetée, pas tronquée. Vérifier les options de désérialisation réellement actives ; une annotation de limite n’empêche pas nécessairement une conversion permissive avant validation.

## 5. Écrire des preuves discriminantes — 65 min

Écrire des tests unitaires du service pour quantité 1 et 5, conservation du prix catalogue et référence absente. Ajouter les tests MockMvc qui traversent sérialisation, validation et routage : création 201 avec Location, consultation 200, quantité 0/6/absente/décimale rejetée, produit inconnu 404, référence inexistante 404, champ montant injecté 400 et méthode interdite 405. Des tests paramétrés peuvent regrouper les entrées invalides, mais chacune reste visible dans les résultats.

Vérifier statut, type de contenu et champs utiles. Ne figer ni la valeur d’un UUID généré ni l’ordre de propriétés JSON. Une assertion de quantité ne suffit pas à protéger le montant : vérifier explicitement 5000 pour quantité 2.

Créer ensuite une régression volontaire : supprimer le facteur quantité du calcul. Exécuter le test pertinent, conserver l’échec, expliquer son message, remettre le calcul puis rejouer la suite. Il faut montrer l’échec d’une assertion de comportement, pas provoquer une erreur de syntaxe.

## 6. Livrer et expliquer — 45 min

Arrêter le processus Spring de développement, construire puis lancer le JAR hors de l’IDE :

```bash
mvn -f backend/pom.xml -B --no-transfer-progress clean verify
java -jar backend/target/pilotcrew-payment-api.jar
```

Rejouer la création et la consultation, puis un cas invalide. Copier les rapports utiles de `backend/target/surefire-reports/` dans les preuves remises. Relancer le processus et constater que l’ancienne référence est inconnue : documenter la volatilité au lieu de la masquer.

Relire le diff, compléter le barème, arrêter le serveur et répondre à la question : « Si deux navigateurs envoient le même prix falsifié, quelle couche protège l’invariant ? » L’extension hors temps est un schéma OpenAPI ou une base persistante ; elle ne remplace aucun critère obligatoire.

## Déroulé minuté — 270 minutes

| Plage | Activité | Durée |
|---|---|---:|
| 00:00–00:20 | Reformuler la commande fictive, ses invariants et le plan de preuves | 20 min |
| 00:20–01:00 | Créer le projet Java, le modèle et les frontières HTTP / métier / mémoire | 40 min |
| 01:00–02:00 | Implémenter catalogue, création, calcul serveur et consultation de commande | 60 min |
| 02:00–02:40 | Valider les entrées et normaliser les erreurs HTTP | 40 min |
| 02:40–03:45 | Tester invariants et contrat ; observer puis corriger une régression | 65 min |
| 03:45–04:30 | Empaqueter, rejouer les requêtes et défendre les preuves | 45 min |

## Contrat commun du fil rouge

La boutique **PilotCrew Démo** ne vend rien. Elle présente le produit `demo-ticket`, libellé « Atelier fictif », à `unitAmountMinor: 2500`, devise `EUR`, `fictional: true`. Une quantité entière de **1 à 5** produit une commande dont le montant est calculé par le serveur. Dans cette simulation EUR seulement, 2500 unités mineures s’affichent 25,00 € fictifs. Aucun autre nombre de décimales ni aucune conversion de devise n’est traité.

| Objet | Champs publics attendus |
|---|---|
| Product | `id`, `label`, `unitAmountMinor`, `currency`, `fictional` |
| Order | `id`, `productId`, `quantity`, `amountMinor`, `currency`, `fictional` |
| Payment | `id`, `orderId`, `amountMinor`, `currency`, `method`, `status`, `fictional` |

Le corps de création de commande accepte uniquement `productId` et `quantity`. Le corps de création de paiement accepte uniquement `method`. Un montant, une devise ou un statut ajoutés par le client ne doivent jamais devenir une autorité : **ce contrat les refuse en 400**. Le code utilise les identifiants retournés, jamais un identifiant d’exemple comme s’il existait déjà.

| Route | Résultat contractuel |
|---|---|
| `GET /api/products` | 200, tableau de produits fictifs |
| `POST /api/orders` | 201, commande créée et en-tête Location |
| `GET /api/orders/{id}` | 200, commande ; 404 si référence absente |
| `POST /api/orders/{id}/payments` | À partir du lab 03 : 202, tentative PENDING ; Idempotency-Key obligatoire |
| `GET /api/payments/{id}` | À partir du lab 03 : 200, état serveur ; 404 si référence absente |
| `POST /api/webhooks/mock-psp` | À partir du lab 04 : réception d’un événement signé par le simulateur local |

Les erreurs structurées emploient `application/problem+json` et au moins `type`, `title`, `status`, `detail`, `code`. `code` est une extension de ce projet. Le texte est destiné à l’utilisateur ; aucune pile d’exception ne sort dans la réponse. Les codes HTTP du cours sont des choix de contrat précis ; ils ne décrivent pas toutes les API de paiement existantes.

Toutes les données métier sont en mémoire dans une JVM : un redémarrage les efface. Cette limite est visible dans la documentation et dans les scénarios. Le cours ne met en place ni identité client, ni base durable, ni gestion de stock, ni remboursement, ni autorisation financière réelle.
