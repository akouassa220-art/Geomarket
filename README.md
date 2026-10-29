# Geomarket — site web

Site vitrine de données géospatiales pour la Côte d'Ivoire : catalogue, démographie (RGPH 2021), carte des commodités et formules d'abonnement.

Site statique (HTML, CSS, JavaScript). Aucune étape de build.

## Structure

```
index.html                       Page unique du site
netlify.toml                     Configuration de publication Netlify
data/
  demographie.json               Données RGPH 2021 (districts, régions, villes, départements)
  commodites.geojson             Équipements issus d'OpenStreetMap
scripts/
  update_demographie.py          Régénère demographie.json depuis la publication de l'INS
  update_commodites.py           Régénère commodites.geojson depuis l'API Overpass
.github/workflows/
  update-demographie.yml         Mise à jour mensuelle (le 1er du mois)
  update-commodites.yml          Mise à jour mensuelle (le 2 du mois)
```

## Prévisualiser en local

Les données sont chargées avec `fetch()`, qui ne fonctionne pas en ouvrant directement `index.html` depuis le disque. Lancez un petit serveur à la racine du projet :

```
python3 -m http.server 8000
```

Puis ouvrez http://localhost:8000.

## Mettre à jour les données manuellement

Prérequis : Python 3.9+ et `pdftotext` (paquet `poppler-utils`).

```
python3 scripts/update_demographie.py            # télécharge la source officielle de l'INS
python3 scripts/update_commodites.py             # interroge OpenStreetMap (quelques minutes)
```

Les scripts vérifient leurs résultats avant d'écrire. Si un contrôle échoue, le fichier existant n'est pas modifié.

## Mises à jour automatiques

Les workflows GitHub Actions lancent les scripts selon leur planning et ne publient un commit que si les données ont changé. Chaque commit déclenche un nouveau déploiement Netlify.

Lancement manuel : onglet **Actions** du dépôt, puis **Run workflow**.

## Déploiement

Netlify, connecté au dépôt GitHub. Réglages de build lus dans `netlify.toml` (aucune commande, publication à la racine).

## Sources et licences

- **Démographie** : RGPH 2021, résultats globaux, Institut national de la statistique (INS).
- **Commodités** : © contributeurs OpenStreetMap, licence ODbL.
- **Fond de carte** : © OpenStreetMap contributors. Le trafic doit rester raisonnable selon la politique d'usage des tuiles OpenStreetMap ; pour un trafic élevé, utiliser un fournisseur avec clé.
- **Bibliothèques** : Leaflet 1.9.4, Leaflet.markercluster 1.5.3, polices Google Fonts (Fraunces, IBM Plex).

## Points à compléter

- Sous-préfectures : seules celles de plus de 100 000 habitants sont incluses dans `demographie.json`.
- Abonnements : les boutons ouvrent un e-mail vers contact@geomarket.ci. Aucun paiement en ligne n'est intégré.
- Les scripts ne sont pas encore testés sur les sources réelles depuis cet environnement : exécuter une première fois en local ou via l'onglet Actions.
