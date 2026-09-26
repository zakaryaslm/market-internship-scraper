# Publier Market Radar sur GitHub

## Ce qui est prêt

Le projet contient le tableau de bord, le collecteur Greenhouse, le stockage D1, les migrations et les règles de ciblage testées. Le dépôt GitHub sert à versionner et présenter le code. Aucune connexion à votre compte GitHub et aucun dépôt GitHub n’ont été créés par cette livraison.

Ciblage : stages / off-cycles, trading, sales, structuration, quant, risques de marché et gestion d’actifs, avec métiers proches identifiés séparément. Débuts à partir de janvier 2027 ; mars–avril en priorité ; janvier–février à négocier ; données inconnues visibles. France, Royaume-Uni, Luxembourg, Belgique, Allemagne, Suisse, Pays-Bas, Espagne, Portugal et Canada. Summer internships conservés avec priorité inférieure. Ce classement n’évalue pas les visas, langues ni les critères académiques détaillés.

## Importer le code

1. Créer un dépôt GitHub, privé au départ si souhaité, nommé `market-radar`.
2. Décompresser l’archive fournie. Le code ne contient ni jeton, ni base de données, ni historique Git, ni identité du site hébergé.
3. Depuis le dossier décompressé :

```bash
git init
git add .
git commit -m "Initial version of Market Radar"
git branch -M main
git remote add origin https://github.com/VOTRE_COMPTE/market-radar.git
git push -u origin main
```

Remplacer VOTRE_COMPTE par son identifiant GitHub. S’authentifier avec les outils GitHub habituels ; ne pas mettre de jeton dans le code.

## Hébergement : deux chemins possibles

### Conserver l’application actuelle

GitHub héberge le code ; le tableau de bord reste sur son hébergement actuel, qui exécute les routes serveur et stocke les offres. Publier du code sur GitHub ne déclenche pas automatiquement une mise à jour de ce site. Un déploiement externe exige aussi un Worker compatible et une base Cloudflare D1 avec les migrations appliquées. Cette archive n’installe pas ces ressources et n’inclut pas de déploiement GitHub opérationnel.

### Passer à GitHub Pages + GitHub Actions

GitHub Pages est statique : il ne peut pas exécuter directement `app/api/jobs/route.ts` ni utiliser la base D1 de cette application. Une adaptation est nécessaire :

- déplacer la collecte dans un script exécuté par une tâche GitHub Actions ;
- conserver les offres et leur première détection dans un fichier JSON ou une base ;
- produire un tableau de bord statique qui lit ce fichier ;
- publier le résultat avec un workflow Pages.

Cela permettrait de collecter lorsque le navigateur est fermé. Les horaires Actions ne garantissent pas une exécution instantanée et des retards sont possibles. Aucun workflow permanent ni déploiement Pages n’est activé dans cette livraison. Vérifier les paramètres de visibilité : un dépôt privé ne signifie pas automatiquement que son site Pages est privé.

Documentation :
- https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site
- https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site
- https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax

## Fichiers clés

- `app/page.tsx` : tableau de bord et filtres.
- `app/api/jobs/route.ts` : collecte et lecture des offres.
- `lib/matching.ts` : ciblage géographique, métiers et dates ; fonctions pures.
- `lib/sources.ts` : employeurs suivis et canonicalisation des liens.
- `db/schema.ts`, `drizzle/` : schéma et migrations.
- `tests/matching.test.mjs` : cas limites de classement.

## Validation

Avec Node 22.13 ou plus récent et les dépendances installées :

```bash
node --experimental-strip-types tests/matching.test.mjs
node node_modules/typescript/bin/tsc --noEmit
```

La collecte actuelle vérifie les huit sources à l’ouverture, puis toutes les cinq minutes si l’onglet est visible. Les localisations utilisent les pays et un dictionnaire de villes ; un lieu inconnu reste à vérifier. Les dates analysées concernent les titres et clauses de début de stage, et ne sont jamais dérivées de la date de publication. Les sources indisponibles conservent leurs dernières offres en les signalant.
