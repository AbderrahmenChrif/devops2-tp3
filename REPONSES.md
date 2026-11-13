# TP3 — Réponses

**Binôme :** Abd Errahmen Chrif — I2, Faculté des Sciences de Sfax

## Partie 1 — Pyramide des tests

**Q1.**

| Dossier | Type de test | Outil | Stack nécessaire |
|---|---|---|---|
| `tests/unit` | Unitaire | Vitest | Non |
| `tests/component` | Composant | Vitest, Testing Library, jsdom | Non |
| `tests/integration` | Intégration | Vitest | Strapi et PostgreSQL |
| `tests/e2e` | Bout en bout | Playwright et Chromium | Toute la stack |

**Q2.** Les tests unitaires sont rapides et simples à corriger. Les tests E2E sont plus lents et utilisent toute l’application. C’est pour cela qu’on écrit beaucoup de tests unitaires et moins de tests E2E.

## Partie 2 — Tests unitaires

**Q3.** J’ai lancé 17 tests. Il y avait 16 tests réussis et un test échoué. Le test en échec était `formate une heure pleine sans zéro final`. Il attendait `1 h`, mais la fonction retournait `1 h 0`.

**Q4.** La fonction affichait toujours les minutes, même quand elles étaient égales à zéro. J’ai ajouté la condition `rest === 0`. Maintenant, 60 minutes donnent `1 h`.

**Q5.** Avant l’ajout des deux tests, `cours.ts` avait 93,33 % de lignes, 78,57 % de branches et 85,71 % de fonctions couvertes. La ligne 48 n’était pas exécutée.

**Q6.** Après l’ajout, la couverture de `cours.ts` est passée à 100 % pour les lignes et les branches. Les fonctions sont restées à 85,71 %. Les nouveaux tests vérifient une liste vide et la combinaison de la recherche avec le niveau.

**Q7.** La couverture des lignes montre les lignes exécutées. La couverture des branches montre les différents chemins d’une condition. Même avec 100 % de couverture, il peut rester un bug si un cas précis n’est pas testé.

## Partie 3 — Tests de composant

**Q8.** `CoursCard.test.tsx` vérifie le titre, le lien, la durée, le niveau et les mots-clés. Il vérifie aussi qu’il n’y a pas de liste de mots-clés quand elle est vide. Le navigateur est simulé avec jsdom.

**Q9.** Les rôles et les textes représentent mieux ce que voit l’utilisateur. Ils permettent aussi de vérifier l’accessibilité. Une classe CSS peut changer sans changer le fonctionnement du composant.

**Q10.** `app/page.tsx` est un composant serveur qui dépend de Next.js et de Strapi. Il est donc plus simple et plus utile de le tester avec les tests E2E.

## Partie 4 — Tests d’intégration

**Q11.** Les tests vérifient les vrais appels HTTP vers Strapi. Ils vérifient les six cours, leurs champs, leur ordre, la recherche par identifiant et le cas d’un identifiant inexistant.

**Q12.** Quand j’ai arrêté Strapi, les quatre tests d’intégration ont échoué avec un timeout ou `fetch failed`. Les tests unitaires ont continué à passer, car ils n’utilisent pas Strapi. L’option `--no-deps` empêche Docker Compose de redémarrer Strapi.

**Q13.** Non. L’échec peut venir du code, mais aussi d’un service arrêté, d’une mauvaise URL, du réseau, des données ou d’un délai trop court.

## Partie 5 — Tests de bout en bout

**Q14.** Le test en échec était `la fiche du cours d'introduction affiche sa durée`. Le site affichait encore `1 h 0` au lieu de `1 h`. L’image Docker du frontend contenait encore l’ancienne version du code.

**Q15.** Le test unitaire a montré directement que le problème venait de `formatDuration`. Le test E2E a montré que le problème était visible par l’utilisateur.

**Q16.**

| Type | Nombre de tests | Durée | Stack nécessaire |
|---|---:|---:|---|
| Unitaire + composant | 19 | 2,32 s | Non |
| Intégration | 4 | 0,93 s | Strapi et PostgreSQL |
| E2E | 4 | 5,4 s | Toute la stack |

**Q17.** Un test flaky peut réussir ou échouer sans changement du code. Playwright réduit ce problème avec les attentes automatiques et les recherches par rôle ou par texte.

## Partie 6 — SonarQube

**Q18.** J’ai utilisé cette configuration :

```properties
sonar.projectKey=tp3-cours
sonar.projectName=TP3 - Cours DevOps
sonar.sources=app,components,lib
sonar.tests=tests
sonar.javascript.lcov.reportPaths=coverage/lcov.info
sonar.exclusions=node_modules/**,.next/**,coverage/**,playwright-report/**,test-results/**
sonar.coverage.exclusions=app/**,lib/api.ts,components/CoursList.tsx,components/DevOpsLoop.tsx,*.config.*
sonar.sourceEncoding=UTF-8
sonar.qualitygate.wait=true
```

`sources` indique le code à analyser. `tests` indique le dossier des tests. Le fichier `lcov.info` contient le rapport de couverture créé par Vitest.

**Q19.** Le premier Quality Gate était rouge. La couverture était de 97,8 %, donc elle passait. Le problème était la présence d’une vulnérabilité. Le rapport indiquait aussi 0 bug, 14 code smells et 5,6 % de duplication.

**Q20.** Un bug peut provoquer un mauvais fonctionnement. Une vulnérabilité est un problème de sécurité. Un security hotspot est une partie sensible à vérifier manuellement. Un code smell rend le code plus difficile à comprendre ou à maintenir.

**Q21.** La règle trouvée était `typescript:S2068`, avec une sévérité majeure. Le mot de passe était écrit directement dans le code. Je l’ai remplacé par `process.env.STRAPI_CLIENT_PASSWORD`. La valeur vient maintenant du fichier `.env` et passe par Docker Compose. Un dépôt privé peut aussi être partagé ou piraté, donc il ne faut pas y mettre de secret.

**Q22.** La correction a supprimé cinq code smells :

- deux imports inutilisés : `useEffect` et `sortBySession` (`S1128`) ;
- la complexité trop élevée de `describeResults` (`S3776`) ;
- deux blocs `else` avec des `if` imbriqués (`S6660`).

J’ai séparé la logique dans `describeEmpty` et simplifié les conditions.

**Q23.** SonarQube a trouvé 24 lignes dupliquées entre `CoursCard.tsx` et la page de détail d’un cours. On peut créer un composant commun pour afficher la séance, la durée, le niveau et les thèmes.

**Q24.** La dette technique est le temps estimé pour corriger les problèmes de qualité. Elle est passée de 63 minutes à 45 minutes après les corrections.

**Q25.** `New Code` représente le code ajouté ou modifié depuis l’analyse précédente. Cela permet de ne pas ajouter de nouveaux problèmes, même si l’ancien code n’est pas encore parfait.

**Q26.** Avec `sonar.qualitygate.wait=true`, le scanner attend le résultat du Quality Gate. Si le gate est rouge, la commande échoue. Dans une CI, cela permet de bloquer le merge ou le déploiement.

Après les corrections, le Quality Gate est passé au vert avec 0 vulnérabilité, 9 code smells, 97,8 % de couverture et 5,7 % de duplication.

## Partie 7 — Synthèse

**Q27.** Dans une CI, je lancerais les étapes dans cet ordre :

1. construire les images ;
2. lancer les tests unitaires et de composant ;
3. lancer les tests d’intégration ;
4. démarrer le frontend ;
5. lancer les tests E2E ;
6. lancer l’analyse SonarQube.

Les tests rapides sont lancés en premier pour détecter les erreurs plus tôt. Tous les tests et le Quality Gate doivent bloquer le merge en cas d’échec.
