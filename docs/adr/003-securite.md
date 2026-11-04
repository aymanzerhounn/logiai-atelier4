# ADR 003 : sécurité de l'API LogiAI

## Décision 1 : où transmettre la clé (en-tête, URL ou corps) ?
- **Choix :** transmettre la clé dans l'en-tête HTTP `X-API-Key`.
- **Ce que l'autre option ferait perdre :** une clé placée dans l'URL serait enregistrée dans les historiques et journaux, tandis qu'une clé placée dans le corps mélangerait l'authentification avec les données métier et resterait exclue de certaines requêtes. L'en-tête sépare explicitement les identifiants du contrat JSON.

## Décision 2 : quelles origines autoriser (CORS) ? Une liste précise ou « * » ?
- **Choix :** autoriser uniquement les origines locales documentées, notamment `http://localhost:5500` et `http://127.0.0.1:5500`.
- **Ce que l'autre option ferait perdre :** `*` autoriserait n'importe quelle page web à appeler l'API depuis le navigateur, élargissant inutilement la surface d'abus. Une liste explicite limite les appels navigateur aux clients approuvés tout en préservant les clients non navigateur.
