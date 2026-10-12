# Sources officielles et choix du laboratoire

Pages primaires consultées le **12 octobre 2026**. Les tutoriels, scénarios, contrats et barèmes sont des créations pédagogiques originales de PilotCrew. Les pages ci-dessous documentent les outils ou principes utilisés ; elles ne décrivent pas notre mock PSP.

| Source primaire | Usage dans le cours |
|---|---|
| [Spring Boot 4.1.1 — prérequis](https://docs.spring.io/spring-boot/system-requirements.html) | Compatibilité Java 21 et prérequis Maven de la version pédagogique retenue. |
| [Spring Framework — validation MVC](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-validation.html) | Validation des DTO de requête et traitement des erreurs associées. |
| [Spring Framework — réponses d’erreur](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-rest-exceptions.html) | ProblemDetail et centralisation des erreurs HTTP. |
| [IETF — RFC 9110](https://www.rfc-editor.org/rfc/rfc9110.html) | Sémantique HTTP ; distinguer réponse acceptée et résultat métier. |
| [IETF — RFC 9457](https://www.rfc-editor.org/rfc/rfc9457.html) | Format application/problem+json ; le champ code est une extension propre au laboratoire. |
| [Spring — Testing the Web Layer](https://spring.io/guides/gs/testing-web) | Tests HTTP de la couche MVC avec JUnit et MockMvc. |
| [Spring Boot — tests d’application](https://docs.spring.io/spring-boot/reference/testing/spring-boot-applications.html) | Annotations et imports de tests compatibles avec Spring Boot 4.1.1 ; version de JUnit gérée par le parent. |
| [JUnit — guide utilisateur](https://docs.junit.org/current/user-guide/) | Tests Jupiter, assertions et outillage ; utiliser la version gérée par le projet. |

Les versions et noms d’API pouvant évoluer, l’encadrant vérifie les liens et la combinaison exacte avant une nouvelle promotion. Java 21 et Spring Boot 4.1.1 sont fixés pour ce cursus ; les versions JavaScript sont celles du package-lock.json validé. La version de JUnit est gérée par le parent Spring Boot ; le cours ne prescrit pas de mélanger une ancienne configuration de tests avec Boot 4.

La répartition Java / React, les interfaces de paiement, l’intégration et la rigueur sont des thèmes issus de [l’annonce de référence](https://consultant.jean-michel.io/annonces/31738). Le choix Spring Boot, TypeScript, Vite, tests et protocole HMAC local appartient à la conception pédagogique. Aucune source ne transforme les 27 heures de ce parcours en expérience senior.
