# Compte rendu : tester, sécuriser et appeler l'API LogiAI

**Nom :** Ayman Zerhounn  **Groupe :** ................

## 1. Casser son API (activité 1)
| Cas | Requête (corps ou URL) | Prévu | Obtenu |
|---|---|---|---|
| champ manquant | POST `/v1/predict`, poids_kg absent | 422 | 422 |
| valeur absurde | POST `/v1/predict`, poids_kg=99999 | 422 | 422 |
| texte au lieu d'un nombre | POST `/v1/predict`, distance_km="loin" | 422 | 422 |
| champ en trop | POST `/v1/predict`, champ couleur="rouge" | 422 | 422 |
| mauvaise route | POST `/v1/inconnue` | 404 | 404 |

- Une requête acceptée alors qu'elle devait être refusée ? Laquelle ?

## 2. Les 2 bugs (activité 2)
| Bug | Test qui l'a révélé | Comportement avant | Correction |
|---|---|---|---|
| 1 | heure_depart=24 doit être refusée | Le bug acceptait 24 avec `le=24` | Limite corrigée à `le=23` |
| 2 | nb_arrets=-1 doit être refusé | Le champ acceptait les nombres négatifs car seule la borne supérieure existait | Borne corrigée avec `ge=0` |

- Sortie de `python -m pytest` à la fin de l'activité 2 : 50 tests passés

## 3. La clé API (sprint 1)
- Ma fonction `verify_api_key()` (collez le code) :
```python
if x_api_key is None or x_api_key == "":
    raise HTTPException(
        401, detail="Clé API manquante.", headers={"WWW-Authenticate": "ApiKey"}
    )
if _egal(x_api_key, config.ADMIN_KEY):
    return "admin"
if _egal(x_api_key, config.API_KEY):
    return "user"
raise HTTPException(
    401, detail="Clé API invalide.", headers={"WWW-Authenticate": "ApiKey"}
)
```

| Requête | Code obtenu |
|---|---|
| `/v1/predict` sans clé | 401 |
| `/v1/predict` fausse clé | 401 |
| `/v1/predict` clé utilisateur | 200 |
| `/v1/model-info` clé utilisateur | 403 |
| `/v1/model-info` clé administrateur | 200 |
| `/health` sans clé | 200 |

- Preuve que `.env` n'est pas dans Git (sortie de `git status`) :

## 4. Le client web (sprint 2)
- Mon `fetch` (collez le code) :
```javascript
const reponse = await fetch(API_URL, {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "X-API-Key": cle,
  },
  body: JSON.stringify(livraison),
});
```
- Ce qui s'affiche : livraison normale / pluie de 500 mm / fausse clé :
  - Livraison normale de l'exemple : réponse HTTP 200, probabilité observée 73,2 % et alerte active.
  - Pluie de 500 mm : réponse HTTP 422 avec une erreur lisible sur `pluie_mm`.
  - Fausse clé : réponse HTTP 401 avec « Clé API absente ou invalide ».
- Expérience CORS (page servie sur le port 5501) : que disent la page et la console (F12) ? Pourquoi `curl` marche-t-il quand même ?
- Où voyez-vous la clé dans F12 ?

## 5. Questions de compréhension
1. Quelle différence entre 401, 403 et 422 ?
   - 401 signifie que l'identité n'est pas authentifiée ou que la clé est absente/invalide; 403 signifie que l'identité est connue mais n'a pas le droit; 422 signifie que la requête est correctement authentifiée mais que son contenu ne respecte pas le contrat.
2. Pourquoi la clé n'est-elle ni dans le code ni dans Git ?
   - Pour éviter toute fuite d'un secret et permettre de changer les identifiants sans modifier le code. `.env` est chargé au démarrage.
3. Pourquoi comparer avec `compare_digest` plutôt qu'avec `==` ?
   - `compare_digest` réduit les risques liés aux attaques temporelles et compare les clés en temps constant.
4. Pourquoi `curl` fonctionne-t-il alors que la page du port 5501 est bloquée ?
   - CORS est appliqué par le navigateur; `curl` n'est pas un navigateur et ne bloque pas la réponse selon son origine.
5. Pourquoi la validation faite par le navigateur ne remplace-t-elle pas celle de l'API ?
   - Les données peuvent venir de `curl`, d'autres clients ou être manipulées; l'API reste la frontière de confiance et doit toujours valider.

## 6. Lien de la pull request

https://github.com/aymanzerhounn/logiai-atelier4/pull/1
