---
name: publiar-lead-magnet
description: "Génère un lead magnet LinkedIn complet suivant la méthode Publiar, de la ressource hébergée jusqu'au suivi des DM : 8 règles anti-hallucination (matière, ressource livrable, bénéfice lecteur, clarté), 8 archétypes visuels, jury de relecture (scrolleur + lecteur pressé), boucle publication → commentaires → DM → vues, inspiré du corpus de 45 lead magnets analysés. Trigger sur \"génère un lead magnet\", \"crée un post LinkedIn lead magnet\", \"écris un post style Publiar\", \"post LinkedIn pour [outil]\", \"lead magnet pour [sujet]\". S'invoque AUSSI quand l'utilisateur veut créer un post LinkedIn qui propose une ressource en échange d'un commentaire (\"Commente XXX pour recevoir...\")."
---

# Publiar Lead Magnet : méthode complète

Ce skill encode la méthode Publiar pour générer un lead magnet LinkedIn de qualité, sans hallucination, structuré comme les 45 lead magnets winners du corpus analysé.

## Workflow : la boucle complète

L'ordre compte. La ressource se règle AVANT d'écrire, parce qu'un post qui
promet une ressource inexistante est une dette, et le bénéfice lecteur se
formule AVANT le hook, parce que c'est lui que le hook doit vendre.

1. **Vérifier la matière** (règles anti-hallucination R1 à R5)
2. **Régler la ressource** (R6) : si le MCP Publiar est branché,
   `add_resource` l'héberge et rend une URL stable, testable, dont les vues
   sont comptées. Sinon, une URL publique qui répond 200.
3. **Formuler le bénéfice lecteur** (R7) : qu'est-ce que le LECTEUR y gagne,
   dit avec l'équation de la valeur (résultat concret × preuve, divisés par
   délai × effort). Pas ce que l'auteur a fait : ce que le lecteur obtient.
4. **Déduire l'archétype visuel** (matrice preuve → archétype). Une preuve
   réelle enregistrée (vidéo, GIF d'écran) bat tout visuel généré.
5. **Écrire le post** (structure standardisée, hook discipliné). Sur un
   post important, écris 2-3 angles de hook, pas un.
6. **Faire passer le jury** (R8) : deux lecteurs simulés, le scrolleur et
   le lecteur pressé, jugent AVANT livraison (section ci-dessous). Un
   angle qui échoue se réécrit ou meurt.
7. **Produire le brief visuel** (spec JSON) ou découper la preuve réelle
8. **Publier puis boucler** (si MCP) : `publish_lead_magnet` en deux phases,
   `register_published` avec le mot-clé CTA et l'URL de la ressource, puis
   `paste_comments` → DM préparés → `mark_engagement_sent`, et
   `list_resources` pour lire combien de commentateurs ont OUVERT le lien.

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
| R7 · Bénéfice lecteur | le post dit EXPLICITEMENT ce que le lecteur y gagne | **REFUSE** de finaliser : un lecteur qui doit déduire le bénéfice ne le déduit pas |
| R8 · Clarté | une seule lecture suffit : un seul acteur nommé, aucun jargon non présenté, aucune conclusion à déduire | Réécrire avant de livrer |
| R9 · Voix vécue | toute solution recommandée est ancrée dans le vécu de l'auteur ou de ses clients (« ce qui marche pour moi »), jamais énoncée comme norme (« le meilleur X », « la bonne façon ») | Réécrire à la première personne ancrée : une pratique non vécue ne se prescrit pas |

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

**La réponse structurelle : héberger chez Publiar.** Avec le MCP branché,
`add_resource` prend le contenu en markdown et rend une URL publique stable
(`/r/<slug>`). Les trois conditions deviennent automatiques : disponible
(hébergée), vérifiable (testée avant publication), envoyable (un lien).
Bonus que rien d'autre ne donne : la page compte ses vues, donc tu sais
combien de commentateurs ont réellement ouvert le lien, la seule mesure de
conversion que LinkedIn ne fournira jamais. Et corriger une coquille se fait
en place, l'URL déjà envoyée dans 50 DM reste bonne.

### R7 en détail : le bénéfice lecteur

Un post qui raconte ce que l'auteur a construit fait une démo. Un lead
magnet dit ce que le LECTEUR obtient. Formule-le avec l'équation de la
valeur, quatre leviers dans une ou deux phrases concrètes :

| Levier | Question | Exemple |
|---|---|---|
| Résultat rêvé | qu'est-ce qui change pour lui, en image mentale ? | « tu colles tes URLs à un agent et tu passes à autre chose » |
| Probabilité perçue | pourquoi y croire ? | la preuve filmée, le prompt exact dans la ressource |
| Délai | c'est long à mettre en place ? | « à brancher une fois : une extension, un prompt » |
| Effort | qu'est-ce que ça lui coûte ? | « zéro code, ton navigateur déjà connecté suffit » |

Le concret crée une simulation mentale du résultat ; l'abstrait demande une
traduction que le cerveau du lecteur refuse de faire.

### R8 en détail : la clarté

Le lecteur accorde UNE lecture, jamais deux. Avant de livrer, vérifie :

- **Un seul acteur** : si le post met en scène un agent, il s'appelle
  « l'agent » du début à la fin. Trois noms propres qui se passent le « il »
  perdent le lecteur.
- **Aucun sigle ni jargon non présenté** : chaque outil nommé reçoit trois
  mots de contexte à sa première apparition, ou disparaît du post.
- **Aucune conclusion à déduire** : si un fait implique quelque chose, le
  post le DIT. « L'API officielle couvre X et Y » ne suffit pas ; « Google
  n'a aucune API pour ça, la seule qui existe couvre X et Y » se lit seul.
- **Le test** : relis en te donnant une seule passe. Chaque phrase qui
  demande un retour en arrière se réécrit ou saute.

### Le jury : le scrolleur et le lecteur pressé

Avant de livrer, le post passe devant deux lecteurs simulés. Joue chaque
rôle honnêtement (ou confie chacun à un sous-agent si ton environnement le
permet) : le rôle a un contrat strict sur ce qu'il a le DROIT de voir.

**Le scrolleur** ne voit QUE les deux premières lignes, sur mobile, entre
deux autres posts. Il ne connaît ni l'auteur, ni le contexte, ni la suite.
Une seule question : est-ce qu'il arrête son pouce ? Sa réponse doit citer
CE QUI l'arrête (un chiffre, un nom, une tension) ou CE QUI le fait glisser
(une ambiance, une leçon, une phrase qu'il a déjà lue cent fois). « C'est
bien écrit » n'est pas une réponse : le scrolleur ne juge pas l'écriture,
il juge l'arrêt.

**Le scrolleur est un PROSPECT calibré, pas un pair.** Il incarne la cible
qui achète des résultats (des clients, du trafic, du temps), pas le
collègue builder qui collectionne les setups. Il juge avec les hooks
gagnants du marché sous les yeux, dont le point commun est un résultat
désirable, énorme et personnel (« J'ai supprimé mon abonnement ChatGPT »,
« de 3 500 à 35 000 followers »). Il note le DÉSIR de 0 à 10 : à quel
point il VEUT ce que le post offre, indépendamment de la qualité
d'écriture. Sous 6, le post ne part pas. Leçon mesurée : une semaine de
posts à craft 6/10 mais désir 3/10 a fait 8 « ne mérite pas l'arrêt » sur
8 au jury aveugle, et 3 % de réaction en ligne. Le piège récurrent : un
hook qui vend le process du builder (« j'ai analysé 45 posts », « mon
outil refuse d'écrire ») au lieu du gain du lecteur (« ton visuel se
choisit tout seul »).

**Le lecteur pressé** lit le post entier, UNE fois, vite, sans revenir en
arrière. À la fin il doit répondre sans relire :

1. Qu'est-ce que J'Y GAGNE, moi qui lis ? (teste R7)
2. Qu'est-ce que je dois FAIRE pour l'obtenir ? (teste le CTA)
3. Pourquoi j'y crois ? (teste la preuve)

Chaque question qui reste sans réponse, ou dont la réponse demande une
relecture, pointe la ligne à réécrire. Il rapporte aussi tout endroit où il
a décroché : mot inconnu, acteur ambigu, conclusion qu'on lui laisse
déduire.

**Verdicts** : les deux doivent passer. Un post que le lecteur pressé adore
mais que le scrolleur saute n'existe pas, personne ne l'ouvrira. Un hook
qui arrête mais un corps qui perd transforme l'attention en déception. Sur
plusieurs angles de hook, le scrolleur départage ; en cas d'égalité, prends
l'angle le plus factuel.

**Le jury juge, il n'invente pas** : un juge qui propose d'ajouter un
chiffre ou une promesse absente de la matière viole R1-R2. Ses suggestions
se limitent à reformuler ce qui existe.

**Le gardien de doctrine porte aussi R9** : toute phrase normative sur une
pratique que l'auteur n'a pas vécue est recalée. « Le prompt qui fonctionne
le mieux pour moi », jamais « le meilleur prompt ». La critique sourcée
d'un tiers (études, docs) reste permise ; c'est la SOLUTION qui doit être
vécue. La voix vécue est aussi la plus solide : « chez moi ça donne X » ne
peut pas être démenti, « le meilleur X » attire la réfutation.

### Le hook : la discipline

Les deux premières lignes sont tout ce que le fil montre avant « voir plus ».

- **Un chiffre ou un nom dès la première ligne.** Une ambiance ou une leçon
  n'arrête personne.
- **60 caractères max par ligne**, sinon le mobile replie et mange le hook.
- **Deux lignes, deux idées** : la première pose le fait qui accroche, la
  seconde le rend intriguant ou nomme les acteurs avec leurs verbes.
- **Le hook ne promet que ce que le post prouve.**

**N'invente JAMAIS** : chiffres non fournis, marques non listées, clients anonymes inventés, durées de test (« j'ai testé 7 semaines »), expérience (« +10 ans d'expérience »). Si l'utilisateur ne le dit pas, ça n'existe pas.

**Les durées ne se compressent pas** : si la vidéo est un montage de 30
secondes d'une session plus longue, le post dit « montage de 30 secondes »,
jamais « fait en 30 secondes ». Un lecteur qui fait le calcul et trouve la
contradiction dans le post lui-même ne revient pas.

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

### La preuve réelle en mouvement (au-dessus des 8 archétypes)

Si l'utilisateur a FILMÉ la chose dont le post parle (enregistrement
d'écran, session d'agent, démo), ce média bat les 8 archétypes : une image
générée illustre, un enregistrement prouve.

- **Vidéo courte (≤30 s)** : publiable telle quelle en vidéo native
  LinkedIn. C'est le format le plus fort pour une preuve d'agent en action.
- **Extrait en GIF** : coupe le passage le plus parlant de
  l'enregistrement (ffmpeg, palettegen/paletteuse). LinkedIn ANIME un GIF
  publié par l'API s'il fait 250 images ou moins, vérifié en production le
  2026-08-11 ; au-delà, il est aplati en image fixe. Vise 10-20 s à 8-12
  fps.
- **L'enregistrement long** : n'en publie jamais 3 minutes. Coupe le
  moment où le résultat tombe, le reste va dans la ressource.
- **Honnêteté du montage** : un montage se dit « montage » (voir la règle
  sur les durées). Le compteur ou l'horloge visible à l'écran est ta
  meilleure preuve, ne le coupe pas.

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

Si l'utilisateur a installé `publiar-mcp` (`pip install publiar-mcp`) ET
configuré sa clé, la boucle ENTIÈRE passe par les outils, dans cet ordre :

| Moment | Outil | Ce qu'il apporte |
|---|---|---|
| Avant d'écrire | `find_similar_corpus(query, brands)` | les 5 posts du corpus les plus proches, pour la structure |
| Avant d'écrire | `add_resource(title, body_markdown)` | héberge la ressource promise, rend l'URL stable dont les vues sont comptées (R6 structurel) |
| Visuel | `render_visual(spec)` / `render_gif(spec)` | PNG 1080 ou GIF animé (LinkedIn anime les GIF publiés par l'API, vérifié) |
| Publication | `publish_lead_magnet(content, ...)` | deux phases obligatoires : aperçu montré à l'utilisateur, puis `confirmed: true`. Teste `resource_url` avant de publier |
| Rattrapage | `update_post(post_urn, content)` | réécrit un post déjà en ligne : hook raté ou fait démenti, l'URN et l'engagement survivent |
| Suivi | `register_published(post_urn, cta_keyword, resource_url, resource_message)` | fait entrer le post dans la boucle. Le gabarit DM accepte `{name}`, `{keyword}`, `{url}` |
| Récolte | `paste_comments(id, raw_text)` | l'utilisateur colle les commentaires LinkedIn, l'outil matche le CTA et prépare chaque DM |
| Envoi | `mark_engagement_sent(id)` | l'utilisateur envoie le DM à la main (LinkedIn n'a pas d'API de messagerie), puis coche |
| Mesure | `list_resources()` | combien de commentateurs ont OUVERT le lien, ressource par ressource |
| Mémoire | `add_memory(content, kind, source, source_date)` | ce que le post a appris entre dans la mémoire, la génération suivante s'améliore |

Mais l'ESSENTIEL, écrire le post + brief visuel, tu peux le faire **sans
aucun appel MCP**, juste en suivant ce skill. Le MCP ajoute ce qu'un agent
seul ne peut pas faire : héberger, publier, suivre, mesurer.

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
6. ✅ R6 (ressource) : le bundle est rédigé en markdown → `add_resource` le
   sert à une URL testée (ou l'utilisateur fournit une URL qui répond 200)
   AVANT d'écrire
7. ✅ R7 (bénéfice lecteur) : « 3h par jour rendues sur la prise de notes »,
   dit dès le hook, détaillé en bullets « ce que Claude fait pour toi »
8. ✅ R8 (jury) : scrolleur → le hook porte un chiffre (3h/jour) et deux
   noms d'outils ; lecteur pressé → gagne quoi (3h/jour), fait quoi
   (commente NOTION), y croit pourquoi (2 mois de mesure perso)

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
