# Évaluation — jr-01-api · 20 points

| Critère | Points | Preuve attendue |
|---|---:|---|
| Catalogue, modèle et calcul serveur en entier | 4 | Code et test : quantité 2 donne 5000, aucun montant client accepté. |
| Contrat HTTP nominal et erreurs précises | 4 | Requêtes / MockMvc montrant 201, Location, 200, 400, 404 et 405. |
| Validation des valeurs absentes et limites | 3 | Cas 0, 6, absent, décimal, produit inconnu et propriété ajoutée. |
| Tests utiles et régression calcul détectée | 4 | Rapports réellement exécutés, même test rouge puis vert. |
| JAR exécutable et reprise du contrat hors IDE | 3 | Commande clean verify, java -jar, requêtes et versions. |
| Journal, revue et compréhension des limites | 2 | Explication du prix serveur, de la séparation et de la perte au redémarrage. |
| **Total** | **20** | |
## Règles de restitution

Un fichier présent n’obtient pas à lui seul tous les points : montrer son comportement et pouvoir l’expliquer. Les cas négatifs sont aussi importants que le nominal. Le code rendu ne contient aucune vraie donnée bancaire ou identité PSP ; tous les scénarios restent locaux et fictifs.

Associer chaque preuve à la version remise. Le journal mentionne l’acquis repris du lab précédent et toute base fournie par l’encadrant. Une revue locale est recevable lorsqu’aucun dépôt distant n’est utilisé. Ne pas créer de fausse URL de commit, de pull request ou de rapport.

En cas de panne d’outillage documentée, l’encadrant distingue les critères vérifiés sur un poste de secours, les contrôles relus mais non exécutés et les preuves reportées. Un test navigateur non exécuté ne reçoit pas l’étiquette « E2E réussi ». Une reprise de preuve peut être organisée ; le résultat obtenu reste daté et attribué au bon environnement.

Pour valider la séance, le formateur dispose d’un ensemble fonctionnel sur les objectifs obligatoires, de tests pertinents, d’un diagnostic expliqué et de limites honnêtes. Une extension ne compense pas un montant client accepté, une duplication masquée ou un faux succès affiché.

## Questions d’oral

1. Distinguer entrée bien formée, entrée valide et produit existant.
2. Expliquer pourquoi le prix vient du catalogue.
3. Dire quel comportement disparaît après un arrêt de la JVM.
