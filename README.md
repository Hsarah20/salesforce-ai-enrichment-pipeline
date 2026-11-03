# salesforce-ai-enrichment-pipeline

## 📖 Contexte métier
Les équipes commerciales des PME passent un temps considérable à rechercher manuellement des informations sur les leads entrants (secteur d'activité, site web, etc.) avant de pouvoir les qualifier et les contacter. Ce processus est lent, fastidieux et source d'erreurs.

## 💡 La solution
J'ai conçu et développé un pipeline d'enrichissement automatisé (ETL) qui connecte **Salesforce**, **n8n** et un modèle d'IA générative (**API Groq / Llama 3**). 

Dès qu'un lot de Leads est ciblé, le système :
1. **Extrait** les données des Leads depuis Salesforce.
2. **Envoie** le nom de l'entreprise à l'IA pour obtenir son secteur d'activité, son site web et générer un pitch de vente personnalisé.
3. **Nettoie et Parse** la réponse JSON de l'IA (via un script JavaScript).
4. **Met à jour** automatiquement les champs personnalisés du Lead dans Salesforce.

## 🛠️ Stack technique
*   **CRM** : Salesforce (API REST, OAuth 2.0, SOQL)
*   **Orchestration** : n8n (Traitement par lots / Batch Processing)
*   **Intelligence Artificielle** : Groq API (Modèle Llama 3 / GPT-OSS)
*   **Langage** : JavaScript (pour le parsing et la gestion des données)

## 🏗️ Architecture du Workflow
Le workflow est conçu pour être résilient et respecter les limites d'API (Governor Limits).

<div align="center">

   ![Workflow](Images/salesforce-ai-enrichment-pipeline_v1.png)

</div>

### Points techniques clés :
*   **Traitement par lots (Batch Processing)** : Utilisation du nœud `Loop Over Items` couplé à une limite (`Limit = 10`) sur la requête Salesforce `Get Many` pour traiter les leads par paquets, évitant ainsi de surcharger l'API.
*   **Gestion du Rate Limiting** : Intégration d'un nœud `Wait` (5 secondes) dans la boucle pour respecter les limites de tokens par minute (TPM) de l'API IA gratuite.
*   **Data Cleansing** : Un nœud `Code (JavaScript)` nettoie les balises Markdown renvoyées par l'IA et transforme le texte brut en un objet JSON exploitable par Salesforce.

## 📸 Démonstration
<div align="center">

   ![Workflow](Images/lead-sf.png)

</div>

## 🚀 Comment importer et utiliser ce workflow
1. Téléchargez le fichier `workflow.json` présent dans ce dépôt.
2. Dans votre instance n8n, cliquez sur `Import from File` et sélectionnez le fichier.
3. Configurez vos identifiants :
   *   **Salesforce** : Ajoutez vos identifiants OAuth (Client ID, Client Secret).
   *   **IA** : Ajoutez votre clé API Groq (ou OpenAI).
4. Ajustez les limites (nœud `Get Many`) et les temps d'attente (nœud `Wait`) selon votre utilisation.