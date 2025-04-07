
# 🏛️ Publication automatisée de documents publics sur [vie-publique.sn](https://vie-publique.sn)  
## Comment traiter efficacement des milliers de documents publics ? Exemple d’implémentation, d’optimisation et de projection à plus grande échelle.

---

## ⚙️ Contexte et Objectif

**Vie-publique.sn** vise à faciliter **l’accès citoyen à l’information publique** au Sénégal : lois, décrets, arrêtés, budgets, rapports, Journaux Officiels…

Face à des **volumes importants** de documents hétérogènes et à une diversité de formats (PDF texte, PDF image, annexes scannées, métadonnées variables), nous avons mis en place une chaîne de traitement automatisée.

---

## 🔁 Pipeline de traitement automatisé (étendu)

### 🧩 Étape 1 : **Ingestion des documents**

- **Sources** : site gouvernemental, courrier électronique, dépôts physiques scannés, plateformes tierces.
- **Formats pris en charge** : PDF, TIFF, JPEG (via OCR), ZIP (lots), HTML (Web scraping en option).

🔧 **Outils** : scripts Node.js/Python, modules `fs`, `axios`, `pdf-parse`, `Tika`, `pytesseract`.

📌 **Défis** :
- Fichiers mal nommés, encodage erroné
- Variabilité des mises en page

---

### 🧠 Étape 2 : **Extraction et structuration du contenu**

- **PDF texte** → Extraction directe via `pdf.js` ou `pdf-parse`
- **PDF image** → OCR avec `Tesseract.js` ou `pytesseract` (multilangue)
- **Nettoyage** : suppression des pieds de page, en-têtes, répétitions

🔎 **Objectif** : générer un JSON propre structurant :
```json
{
  "titre": "Loi n°2023-07 relative à la cybersécurité",
  "date": "2023-05-02",
  "type": "Loi",
  "texte_integral": "...",
  "sections": [...],
  "extraits": [...],
}
```

---

### 🔍 Étape 3 : **Résumé automatique & enrichissement sémantique**

- Appel à **Mistral** via une API interne, prompt structuré :  
  > "Résume ce document juridique de façon claire pour un citoyen sénégalais non-juriste en 5 phrases."

- Résultats stockés dans `resume_auto`, possibilité de revue humaine via une interface de validation

📌 **Enrichissements possibles** :
- Classification automatique (budget, loi, décret…)
- Extraction d’entités : dates, ministères, bénéficiaires
- Génération d’un titre optimisé SEO

---

### 💧 Étape 4 : **Ajout de watermark et horodatage**

- Logo de la plateforme, date, QR code de traçabilité
- Ajout via `pdf-lib` (client-side) ou `PyPDF2` (server-side)
- Intégration du code document (`JO_2023_045`)

---

### ☁️ Étape 5 : **Stockage et publication**

- Stockage sur **MinIO** (S3-compatible, self-hosted, haute performance)
- Création automatique d’un **item dans Directus** avec champs :
  - titre, date, catégorie, résumé, URL MinIO, niveau de publication

🔗 API REST de Directus pour synchronisation avec front-office  
🔒 Authentification via token + logs d’audit pour traçabilité

---

## 📈 Temps de traitement moyen

| Type de document          | Nombre traité  | Temps moyen |
|---------------------------|----------------|-------------|
| Année de JO (150 numéros) | 150 PDFs       | 1h30        |
| Lois                      | 50 lois        | 20 min      |
| Budgets                   | 10 fichiers    | 10 min      |

---

## 🚀 Axes d’amélioration

| Axe                      | Détail                                                                                                                                     |
|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ⚡ Optimisation IA        | - Implémenter un modèle local plus léger (Mistral-7B quantisé) <br> - Résumés par batch <br> - Embeddings pour recherche sémantique      |
| 🔁 Résilience             | - Ajout d’un orchestrateur type **Temporal**, **Airflow** ou **n8n** pour relancer les tâches échouées automatiquement                     |
| 📊 Validation humaine     | - Interface web pour relecture des résumés / titres <br> - Workflow avec rôles contributeur/validateur                                   |
| 🧾 Métadonnées enrichies | - Extraction automatique des dates, types d’acte, autorités signataires                                                                  |
| 🌐 Intégration moteur de recherche | - ElasticSearch / Meilisearch pour recherche plein texte <br> - Filtrage par mots-clés, périodes, types de document |

---

## 🔮 Cas d’usages futurs

1. **Génération automatique de fiches pédagogiques** à partir des lois et budgets
2. **Intégration vocale (TTS)** pour lecture audio des documents
3. **Traduction automatique** (français → wolof / pulaar)
4. **API publique d’open data** (accès aux documents avec métadonnées)
5. **Alertes automatiques** : "Nouvelle loi publiée sur le numérique"
6. **Tableaux de bord de suivi législatif** (ex : production annuelle des décrets)
7. **Indexation dans des assistants IA citoyens**
8. **Archivage numérique certifié (signature, blockchain)**
9. **Comparaison automatique de lois par année**
10. **Détection des doublons / redondances**

---

## 🏢 Top 20 des usages dans une entreprise ou administration

| Domaine                       | Cas d’usage                                                                                       |
|------------------------------|---------------------------------------------------------------------------------------------------|
| 1. Juridique                  | Analyse de contrats, génération de résumés, alerte sur échéances                                 |
| 2. RH                         | Automatisation des bulletins de paie, traitement des demandes de congés                          |
| 3. Communication              | Résumés d’articles de presse, génération automatique de posts internes                          |
| 4. Direction générale         | Synthèse de rapports d’activité, veille réglementaire                                            |
| 5. Finance                    | Extraction de données comptables, budgetaires ou fiscales de documents                          |
| 6. Achats                     | Analyse de cahiers des charges, résumés d’offres fournisseurs                                    |
| 7. Archivage                  | Indexation automatique, OCR et horodatage des documents papier scannés                         |
| 8. Conformité                 | Veille RGPD, conformité ISO, extraction de clauses clés                                          |
| 9. IT                         | Documentation technique résumée automatiquement                                                  |
| 10. Formation                 | Résumé de documents pédagogiques, création de quiz automatiques                                 |
| 11. Open data / Portails      | Publication automatisée de rapports publics (ex : bilan RSE, budget citoyen)                    |
| 12. Gouvernance               | Synthèse des PV de réunion, génération de plans d’action                                        |
| 13. Audit interne             | Indexation, recherche rapide dans les documents d’audit                                         |
| 14. Logistique                | Analyse de contrats transport, extraction de SLA                                                |
| 15. Qualité                   | Extraction d’indicateurs à partir de comptes-rendus                                              |
| 16. Santé / Hôpitaux          | Résumés de dossiers médicaux (avec précaution RGPD)                                             |
| 17. Education / Universités   | Traitement de publications scientifiques, génération de méta-résumés                           |
| 18. Environnement             | Suivi automatisé des rapports d’impact environnemental                                          |
| 19. Urbanisme                 | Traitement automatisé de plans et permis                                                        |
| 20. Banque / Assurances       | Lecture OCR de pièces justificatives, génération de synthèses clients                           |

---

## 🧠 Conclusion

🔍 **Les documents publics** représentent une richesse d’information souvent sous-exploitée. Grâce à une approche **automatisée, modulaire et évolutive**, il est possible de :

- Faciliter la transparence administrative
- Mieux informer les citoyens
- Automatiser des tâches internes à forte valeur ajoutée
- Intégrer des modèles IA en toute maîtrise (Mistral, TTS, NER, etc.)

Vous voulez une version industrialisée, packagée ou dockerisée de ce pipeline avec interface de monitoring, batch processing et connecteurs API ? Je peux vous générer ça aussi.

Souhaitez-vous une version PDF ou une présentation (type pitch deck / documentation technique) à partager en interne ou à vos partenaires ?
