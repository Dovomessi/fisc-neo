# fisc-neo
Objectif global
Créer un système intelligent de recherche, d’analyse, de rédaction et de génération de contenu fiscal automatisé, s’appuyant exclusivement sur des sources juridiques officielles vectorisées et interrogeables par un LLM via un pipeline RAG robuste, modulaire et maintenable, hébergé entièrement sur un VPS Contabo. Ce système doit pouvoir fonctionner via chatbot, interface web, ou intégration avec N8N pour automatiser des tâches juridiques.

🧩 COMPOSANTS ET MISSIONS DU SYSTÈME
🗂 1. 📚 Sources à ingérer (juridiques officielles)
Le système doit inclure une base juridique complète et à jour :

Type	Source officielle	Format	Mise à jour
BOFiP	bofip.impots.gouv.fr	JSON / HTML	Quotidienne
CGI (LEGI)	LEGI via data.gouv.fr ou API DILA (Droit Légifrance)	XML	Hebdomadaire
Jurisprudence CE	ArianeWeb – Conseil d’État, CAA	XML / HTML	Hebdomadaire

APIs et téléchargements directs :

API BOFIP principal : https://data.economie.gouv.fr/explore/dataset/bofip-impots/api/
Téléchargement CSV/JSON : https://www.data.gouv.fr/fr/datasets/bofip-impots-publications-en-vigueur/ (fichiers CSV et JSON directement téléchargeables)
API publications en vigueur : https://data.economie.gouv.fr/explore/dataset/bofip-vigueur/api/ API tabulaire data.gouv.fr (beta)
API rescrits BOFIP : https://data.economie.gouv.fr/explore/dataset/bofip-rescrits/api/</parameter>

Validation des liens que vous avez fournis
✅ https://echanges.dila.gouv.fr/OPENDATA/LEGI/ - EXCELLENT
Ce lien est parfait ! Il contient des fichiers tar.gz régulièrement mis à jour Modernisation qui incluent :

Le Code général des impôts (CGI) complet
Tous les autres codes juridiques français (base LEGI)
Fichiers récents (mis à jour quotidiennement) : LEGI_20250716-211907.tar.gz, Freemium_legi_global_20250713-140000.tar.gz

✅ https://opendata.justice-administrative.fr/ - PARFAIT
Ce lien est idéal pour les jurisprudences administratives Le Code général des impôts | Ministère de l’Économie des Finances et de la Souveraineté industrielle et numérique :

Décisions du Conseil d'État, cours administratives d'appel et tribunaux administratifs ArianeWeb : 130 000 décisions de jurisprudence administrative en ligne gratuitement - Cour administrative d'appel de Paris
Format XML pour l'interopérabilité ArianeWeb : 130 000 décisions de jurisprudence administrative en ligne gratuitement - Cour administrative d'appel de Paris
Téléchargement par archives ZIP, regroupées par juridiction, année et mois ArianeWeb : 130 000 décisions de jurisprudence administrative en ligne gratuitement - Cour administrative d'appel de Paris

Sources complémentaires recommandées
Jurisprudences administratives historiques :

Base JADE (DILA) : https://www.data.gouv.fr/fr/datasets/jade/ Rechercher une décision - Tribunal administratif de Paris

Décisions du Conseil d'État depuis 1965, cours administratives d'appel depuis 1989 Rechercher une décision - Tribunal administratif de Paris
Format XML structuré
CGI sur Légifrance :

Code général des impôts : https://www.legifrance.gouv.fr/codes/texte_lc/LEGITEXT000006069577/ CODE GENERAL DES IMPOTS
Format consultable en ligne avec possibilité d'extraction

Architecture technique recommandée
Pour votre RAG, je recommande cette approche :

BOFIP : Utilisez l'API data.economie.gouv.fr + téléchargements CSV/JSON de data.gouv.fr
CGI : Base LEGI de DILA (fichiers tar.gz) + complément Légifrance
Jurisprudences : Combinaison opendata.justice-administrative.fr (récent) + JADE (historique)

Avantages de cette approche :

Sources 100% officielles et à jour
Formats structurés (XML, JSON, CSV) adaptés à la vectorisation
Mises à jour automatiques disponibles via APIs
Couverture complète du domaine fiscal français

🛠️ 2. 🔧 Infrastructure technique complète
Élément	Choix retenu	Justification
Hébergement	VPS Contabo (388Go / 16% utilisé)	Coût faible, contrôle total, stockage large
Vector DB	Qdrant (auto-hébergé sur VPS)	Performant, stable, production-ready, open source
Embedding Model	jinaai/jina-embeddings-v3	Gratuits, multilangues, qualité supérieure à text-ada
Reranker (optional)	bge-reranker-v2-m3	Améliore la pertinence des chunks
LLM	Claude 3.5 / GPT-4 via API	Pour réponses et génération structurée
Backend	FastAPI	Rapide, facile à déployer et à dockeriser
Frontend	Modulaire : ChatbotUI / Streamlit	Permet usage libre selon le contexte
Orchestration	LangChain	Standard du RAG moderne
Automatisation	N8N (déjà installé)	Pour automatiser la recherche, MAJ, et publications
Cron / Webhook	Oui	Pour mise à jour automatique des données
GitHub	Repo fisc-neo	Pour versionner l'ensemble du projet

🧠 3. 🧩 Fonctionnalités majeures du système
🔍 A. Recherche juridique intelligente (chatbot ou agent IA)
Agent LLM capable de répondre à toute question fiscale, en utilisant uniquement les sources vectorisées.

Doit fournir :

🔸 Réponse claire et structurée

🔸 Sources précises (URL BOFiP, Légifrance, CE)

🔸 Justifications logiques (raisonnement juridique)

🔸 Possibilité d’activer un mode avocat fiscaliste (ton juridique, rigueur, citation d'articles)

✍️ B. Rédaction d’articles fiscaux automatisée
Fournir un sujet ou une problématique → l’IA :

Recherche les éléments pertinents via le RAG

Génère un article structuré (titre, plan, citations)

Cite précisément la jurisprudence, articles CGI, BOFiP

Format final :

.md ou .html

Possibilité de le pousser dans un CMS ou Notion via N8N

🔗 C. Intégration avec N8N (automation no-code déjà installée)
Intégrer un endpoint API /n8n-query dans FastAPI :

Permet à N8N de poser des questions → reçoit réponse + sources

Authentification via bearer token

Réutilisable dans Notion, Slack, Mail, blog...

Intégration de webhook maj-data pour :

Lancer ingestion

Mettre à jour automatiquement les bases

♻️ D. Pipeline de mise à jour automatique (cron ou webhook)
Automatiser ingestion / revectorisation des sources :

BOFiP : scraping + diff + vectorisation

CGI : parsing XML + vectorisation

Jurisprudence : scraping ArianeWeb + parsing + vect.

Journaliser chaque update dans changelog.json

Notifier via webhook/N8N les nouvelles données intégrées

🧱 ARCHITECTURE DU CODE – SQUELETTE À CRÉER DANS LE REPO
graphql
Copier
Modifier
fisc-neo/
├── ingestion/
│   ├── ingest_bofip.py
│   ├── ingest_legi.py
│   └── ingest_juris.py
├── update/
│   ├── update_bofip.py
│   ├── update_cgi.py
│   ├── update_juris.py
│   └── changelog.json
├── api/
│   ├── main.py
│   └── routes/
│       ├── query.py
│       ├── n8n_query.py
│       └── generate_article.py
├── rag/
│   ├── retriever.py
│   ├── reranker.py
│   └── generator.py
├── vector_db/
│   └── setup_qdrant.py
├── models/
│   └── embedding_model.py
├── frontend/
│   └── app.py  ← Streamlit ou autre interface
├── utils/
│   └── tools.py
├── docker/
│   ├── Dockerfile
│   └── docker-compose.yml
├── README.md
├── requirements.txt
└── project_prompt.md
🔐 Notes importantes :
🔒 Toutes les sources doivent être traçables (afficher l’URL exacte dans les résultats)

🛡️ Aucun hallucination autorisée → utiliser Contextual Compression Retriever ou Hypothetical Document Embedding si possible

🏁 Démarrage de FastAPI = auto-chargement des modèles, auto-index Qdrant

💾 Le système est hébergé entièrement sur le VPS → aucun appel externe sauf API LLM (Claude, GPT, etc.)

✅ À FAIRE EN PRIORITÉ
Initialiser le repo fisc-neo avec les fichiers README.md, requirements.txt, Dockerfile, main.py

Mettre en place la base Qdrant locale

Écrire les scripts d’ingestion des trois sources

Ajouter l’API de question / génération

Intégrer dans N8N

Ajouter cronjob (ou webhook) pour MAJ auto

Tester la réponse d’un article fiscal généré
