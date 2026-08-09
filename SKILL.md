---
name: publiar-lead-magnet
description: Génère un lead magnet LinkedIn complet (post texte + brief visuel) suivant la méthode Publiar : 8 archétypes visuels, structure standardisée, règles anti-hallucination, inspiré du corpus de 45 lead magnets analysés. Trigger sur "génère un lead magnet", "crée un post LinkedIn lead magnet", "écris un post style Publiar", "post LinkedIn pour [outil]", "lead magnet pour [sujet]". S'invoque AUSSI quand l'utilisateur veut créer un post LinkedIn qui propose une ressource en échange d'un commentaire ("Commente XXX pour recevoir...").
---

# Publiar Lead Magnet : méthode complète

Ce skill encode la méthode Publiar pour générer un lead magnet LinkedIn de qualité, sans hallucination, structuré comme les 45 lead magnets winners du corpus analysé.

## Workflow

1. **Vérifier la matière** (règles anti-hallucination)
2. **Déduire l'archétype visuel** (matrice preuve → archétype)
3. **Écrire le post** (structure standardisée)
4. **Produire le brief visuel** (spec JSON pour le designer/renderer)
5. **Output structuré**

## 1. Règles anti-hallucination (à appliquer AVANT toute génération)

Avant d'écrire UN SEUL mot, l'utilisateur doit avoir fourni :

| Règle | Condition | Action si violée |
|---|---|---|
| R1 · Matière | Au moins 1 outil OU 1 chiffre OU 1 preuve uploadée | **REFUSE** : "Donne au moins un outil, un chiffre ou une preuve concrète" |
| R2 · Source du chiffre | Si chiffre annoncé, source obligatoire | **REFUSE** : "Indique d'où vient ce chiffre : mesure perso datée / source officielle / benchmark public / cas client" |
| R3 · Pairing | Si tool_pairing, au moins 2 outils | Warn : "Ajoute un 2e outil pour le pairing" |
| R4 · CTA keyword | Mot-clé CTA en majuscules | Warn : "CTA habituellement en MAJUSCULES" |
| R5 · Preuve cohérente | proof_type aligné avec uploads | **REFUSE** si selfie sans photo, etc. |
| R6 · Ressource livrable | la ressource promise est disponible, vérifiable et envoyable | **REFUSE** : "Donne l'URL de la ressource, je la teste avant d'écrire le post" |

### R6 en détail

Un lead magnet promet une ressource. Si elle n'existe pas au moment de
publier, le post est un mensonge et chaque commentaire devient une dette.
Avant d'écrire un seul mot, la ressource doit satisfaire les trois conditions :

| Condition | Ce que ça veut dire | Comment on vérifie |
|---|---|---|
| **Disponible** | elle existe déjà, à une URL stable et publique | l'URL est fournie, pas promise |
| **Vérifiable** | l'URL répond 200 et sert le bon contenu | requête réelle avant génération |
| **Envoyable** | c'est un lien collable dans un DM | pas un fichier local, pas une pièce jointe à uploader une par une |

Un lien mort, un repo privé, un Drive en accès restreint ou un fichier qui
n'existe que sur ton disque violent R6. Un zip que tu dois envoyer à la main à
chaque personne le viole aussi : à 50 commentaires, tu abandonnes.

**Teste l'URL avant d'écrire.** Une ressource qui renvoie 404 pendant que
200 personnes commentent coûte plus cher que l'absence de post.

**N'invente JAMAIS** : chiffres non fournis, marques non listées, clients anonymes inventés, durées de test (« j'ai testé 7 semaines »), expérience (« +10 ans d'expérience »). Si l'utilisateur ne le dit pas, ça n'existe pas.

## 2. Les 8 archétypes visuels

Chaque lead magnet a UN archétype visuel. Tu le déduis depuis la matière :

| Archétype | Trigger | Format visuel |
|---|---|---|
| `tool_pairing` | 2-3 outils combinés | Logos sur fond crème + connecteur ×/+/→/= |
| `benchmark_table` | Comparaison chiffrée modèles/produits | Tableau peach-pink highlight, style research paper |
| `dark_thumbnail` | Annonce produit/lancement | Laptop sombre cinematic + annotations manuscrites |
| `youtube_thumbnail` | Audience grand public + titre fort | Fond clair + titre HUGE surligné fluo + flèche stylo |
| `selfie_workspace` | Photo utilisateur uploadée | Vraie photo + overlay annotation jaune + barré/winner |
| `system_workflow_screenshot` | Screenshot workflow uploadé | Fond magenta + screenshot card + logos pills |
| `file_tree_diagram` | Arborescence stack/skills | Code-style tree avec chips pastel colorés |
| `agent_role_diagram` | Équipe d'agents (3-5 rôles) | Cards rôles satellites + pill central orange |

### Matrice preuve → archétype (déduction)

```
Photo de l'auteur fournie               → selfie_workspace
Screenshot workflow fourni              → system_workflow_screenshot
Liste hiérarchique stack (3+ niveaux)   → file_tree_diagram
Liste de 3-5 rôles                       → agent_role_diagram
Tableau benchmark chiffré               → benchmark_table
Annonce produit + lien officiel         → dark_thumbnail
   ↳ Si audience grand_public           → youtube_thumbnail
2 outils nommés (default)               → tool_pairing
```

La preuve réelle uploadée **bat toujours** la preuve générée. Si l'utilisateur fournit une photo, c'est `selfie_workspace`, même s'il a aussi mentionné 2 outils.

## 3. Structure du post (séquence stricte du corpus)

Le post LinkedIn lead magnet suit toujours ces 7 sections :

```
1. HOOK (1-2 lignes)
   Claim chiffré + outil(s) nommé(s).
   Exemples du corpus :
   - "Claude + Google Maps = 500 leads qualifiés en 48h. C'est dingue."
   - "J'ai supprimé mon abonnement ChatGPT il y a 10 jours."
   - "Anthropic vient de sortir un guide de 31 pages."

2. CRÉDIBILITÉ (1-2 lignes)
   D'où vient la matière. Pas d'invention.
   - "J'ai documenté tout dans un guide complet."
   - "Après 7 semaines de tests obsessionnels..."  ← UNIQUEMENT si fourni

3. PROBLÈME (3-5 lignes)
   Pattern : "99% des gens font X" OU "Avant je faisais Y, maintenant Z".
   - "99% des gens utilisent Claude comme un simple chatbot..."
   - "Avant ? Je payais 2000€/mois pour des bases B2B périmées..."

4. SOLUTION STRUCTURÉE (5-12 bullets)
   Bullets avec → ou ✅. Étapes ou bénéfices concrets.
   Format : "→ [action verbe] [outil/fait concret]"
   Pas de bullshit, pas de superlatifs creux.

5. STACK / BONUS (3-7 bullets)
   Ce qui est DEDANS la ressource offerte.
   Format : "✅ [fichier/contenu spécifique]"

6. CTA DOUBLE
   a. Principal : "Commente {CTA_KEYWORD} pour recevoir" + "Like / Reposte / Connecte-toi"
   b. Secondaire (si atelier) : "Si tu veux aller plus loin... atelier {DATE}. Commente ATELIER."

7. PS (optionnel)
   "Sauvegarde ce post. Dans 6 mois tu me remercieras 🔥"
   OU "♻️ Reposts prioritaires"
```

### Ton imposé

- **Première personne** ("j'ai", "je", "moi")
- **Direct, sans bullshit**
- **Pas de superlatifs creux** ("incroyable", "fou", "ça change tout" → autorisé MAIS rare)
- **Pas de hashtags dans le corps** (ils sortent dans un block séparé)
- **Aucun chiffre non sourcé**

## 4. Brief visuel : spec JSON

À la fin du post, produis un `visual_spec` JSON utilisable par `publiar-mcp` ou n'importe quel renderer Pillow. Format selon l'archétype déduit :

```typescript
// tool_pairing
{ archetype: "tool_pairing", brands: ["Claude", "Notion"], connector: "×" }

// benchmark_table
{ archetype: "benchmark_table", title: "Opus 4.8", subjects: ["Opus 4.8","Opus 4.7","GPT-5.5"], metrics: [...], highlight_index: 0 }

// dark_thumbnail
{ archetype: "dark_thumbnail", subject_description: "cinematic laptop with [product] wordmark", annotation_top_left: "...", annotation_top_right: "...", caption_bottom: "Commente XXX et reçois le guide" }

// youtube_thumbnail
{ archetype: "youtube_thumbnail", primary_headline: "LE GUIDE COMPLET", secondary_accent: "TOP 1%", brand_sticker_label: "CLAUDE × COWORK" }

// selfie_workspace (upload requis, donne le champ vide à remplir par l'user)
{ archetype: "selfie_workspace", base_photo_url: "", top_annotation: "...", crossed_out_brand: "...", highlighted_brand: "...", caption_bottom: "..." }

// system_workflow_screenshot (upload requis)
{ archetype: "system_workflow_screenshot", screenshot_url: "", title: "...", tool_brands: ["n8n","Brevo"], cta_keyword: "..." }

// file_tree_diagram
{ archetype: "file_tree_diagram", title: "...", subtitle: "...", nodes: [{path:"...",depth:0,annotation:"...",chip_color:"peach"}, ...], caption_bottom: "..." }

// agent_role_diagram
{ archetype: "agent_role_diagram", center_label: "Claude : Agents IA MARKETING", roles: [{name:"Le Stratège",description:"..."}, ...], caption_bottom: "..." }
```

Couleurs chip pour `file_tree_diagram` : `peach`, `mint`, `lavender`, `butter`, `sky`, `rose`.
Connecteurs pour `tool_pairing` : `none`, `×`, `+`, `→`, `=`.

## 5. Output format standardisé

Quand tu génères un lead magnet, structure ta réponse en 3 blocks :

```markdown
## 📝 Post LinkedIn

[le post complet, prêt à copier-coller dans LinkedIn]

## 🏷 Hashtags

#Claude #IA #LeadGen ...

## 🎨 Visual spec (pour rendu Pillow ou publiar-mcp)

\`\`\`json
{
  "archetype": "tool_pairing",
  "brands": ["Claude", "Notion"],
  ...
}
\`\`\`

## 🤔 Notes

- Archétype déduit : `tool_pairing` car 2 outils nommés
- Niveau de preuve : `standard` (outils + chiffre sourcé)
- [autres notes pertinentes]
```

## 6. Inspirations du corpus (few-shot)

Quand tu écris, garde en tête ces patterns winners (45 lead magnets analysés) :

**Top engagement (likes), par archétype :**
- `tool_pairing` : "J'ai supprimé mon abonnement ChatGPT..." (1896 likes)
- `tool_pairing` : "N8N c'est terminé, place aux workflows agentiques" (1181 likes)
- `youtube_thumbnail` : "Claude Code 3500 → 35000 followers" (970 likes)
- `tool_pairing` : "J'ai construit une équipe marketing avec Claude. 8 agents." (697 likes)
- `agent_role_diagram` : "Anthropic a créé une certification Architectes Claude" (687 likes)

**Hooks récurrents** :
- "X + Y = [chiffre] [résultat] en [temps]"
- "J'ai annulé/supprimé mon abonnement [concurrent]"
- "[Marque] vient de sortir [produit]. Voici ce que ça change."
- "Comment [bénéfice concret] avec [outil]"
- "[Chiffre] [unité] en [timeframe] sans [contrainte typique]"

**CTA keywords typiques** : CLAUDE, MAPS, PAPPERS, DATAGOUV, AGENTS, COWORK, ANTIGRAVITY, SKILLS, HERMES, NOTEBOOK, CLAUDE, REMOTE, LOCAL, OPUS.

## 7. Intégration avec publiar-mcp (optionnel)

Si l'utilisateur a installé `publiar-mcp` ET configuré sa clé MCP dans ton client, tu peux ÉGALEMENT :

- Appeler `find_similar_corpus(query, brands)` pour récupérer les 5 corpus les plus similaires (RAG retrieval)
- Appeler `render_visual(spec)` pour obtenir le PNG du visuel en base64
- Appeler `generate_lead_magnet(input)` si tu préfères déléguer toute la génération au backend Publiar (consomme un crédit Claude API côté Publiar)
- Appeler `register_published(post_urn, ...)` après que l'utilisateur a publié sur LinkedIn

Mais l'ESSENTIEL, écrire le post + brief visuel, tu peux le faire **sans aucun appel MCP**, juste en suivant ce skill. Le MCP est pour les actions backend (rendu PNG, persistance, etc.).

## 8. Garde-fous

- **Ne génère JAMAIS** un lead magnet si la matière R1-R5 n'est pas claire. Demande à l'utilisateur les informations manquantes.
- **Ne mélange JAMAIS** la matière de plusieurs utilisateurs. Le post est strictement dérivé des inputs courants.
- **Ne copie JAMAIS** un post du corpus à l'identique. Tu t'inspires de la STRUCTURE, pas des FAITS.
- **Vérifie** que l'archétype déduit colle bien à la matière. En cas d'ambiguïté, demande à l'utilisateur.

## Exemple complet

**Input utilisateur** :
> "Lead magnet pour Claude + Notion, j'ai gagné 3h par jour sur ma prise de notes depuis 2 mois, je propose un bundle de prompts, CTA NOTION."

**Application du skill** :

1. ✅ R1 (matière) : 2 outils + chiffre + ressource → OK
2. ✅ R2 (source chiffre) : "3h par jour depuis 2 mois" = mesure perso datée
3. ✅ R3 (pairing) : 2 outils → tool_pairing
4. ✅ R4 (CTA) : NOTION en majuscules
5. ✅ R5 (preuve) : aucune preuve uploadée, `proof_type: "none"`, archétype = `tool_pairing`

**Output** :

```markdown
## 📝 Post LinkedIn

Claude + Notion m'ont rendu 3h par jour sur ma prise de notes. 2 mois plus tard, je ne reviens pas en arrière.

Avant ? Je passais mes journées à retaper des notes brutes, à re-formater, à chercher où j'avais mis "ce paragraphe sur X".
Maintenant ? Claude lit ma capture vocale ou texte brouillon, structure tout dans Notion avec le bon template, lie aux pages existantes. En 30 secondes.

99% des gens utilisent Notion comme un Google Docs un peu plus joli. Et Claude juste pour reformuler des trucs.

Avec une bonne config et les bons prompts ?
→ Claude détecte automatiquement le type de note (meeting, idée, todo, recherche)
→ Il applique le bon template Notion sans que tu lui dises
→ Il lie aux pages existantes (clients, projets, books) via les relations Notion
→ Il génère un résumé exécutif en haut de page si la note fait > 200 mots
→ Il ajoute les tags pertinents pour tes views Notion
→ Il programme un follow-up dans Notion Calendar si la note contient un engagement

J'ai compilé tous mes prompts dans un bundle prêt à l'emploi :
✅ 12 prompts Claude testés sur des centaines de notes perso
✅ Les 4 templates Notion à dupliquer (Meeting / Idée / Todo / Recherche)
✅ Le système de tagging automatique
✅ Le workflow vocal (Apple Voice Memos → Claude → Notion)
✅ Les 3 erreurs qui m'ont fait perdre 2 semaines au début

Pour recevoir le bundle complet :
- Like ce post ❤️
- Commente "NOTION"
- Connecte-toi à moi pour que je puisse t'envoyer le lien

PS : Disponible 7 jours uniquement. ♻️ Reposts prioritaires.

## 🏷 Hashtags

#Claude #Notion #Productivité #PriseDeNotes #IA

## 🎨 Visual spec

\`\`\`json
{
  "archetype": "tool_pairing",
  "brands": ["Claude", "Notion"],
  "connector": "×"
}
\`\`\`

## 🤔 Notes

- Archétype déduit : `tool_pairing` (2 outils, aucune preuve uploadée)
- Niveau de preuve : `standard` (outils + chiffre avec source perso datée)
- Tone : direct, première personne, structure 7-sections du corpus
- 0 hallucination : aucun chiffre ni outil inventé au-delà de ce qui était fourni
```
