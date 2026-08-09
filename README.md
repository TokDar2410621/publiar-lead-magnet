# publiar-lead-magnet

**Un skill Claude qui écrit des lead magnets LinkedIn et refuse d'inventer.**

> **EN.** A Claude Code skill that writes LinkedIn lead magnets and refuses to
> fabricate facts. It ships the method behind 45 analyzed posts: 8 visual
> archetypes, a proof-to-archetype matrix, and 6 blocking anti-hallucination
> rules. Generated posts are in French. MIT.

## Le problème

Demande un post LinkedIn à un LLM, il te sort « après 7 semaines de tests
intensifs, +340 % d'engagement ». Tu n'as testé rien pendant 7 semaines et le
chiffre sort de nulle part. Tu le publies, quelqu'un demande la source, et tu
n'en as pas.

## Ce que fait ce skill

Il refuse d'écrire tant que tu ne lui as pas donné de la matière réelle.

| Règle | Condition | Si violée |
|---|---|---|
| R1 | au moins 1 outil, 1 chiffre ou 1 preuve | refus |
| R2 | tout chiffre annoncé a une source déclarée | refus |
| R3 | un pairing d'outils en compte au moins 2 | avertissement |
| R4 | le mot-clé du CTA est en majuscules | avertissement |
| R5 | le type de preuve correspond aux fichiers fournis | refus |

Pas de chiffre inventé, pas de marque non citée, pas de client anonyme
fictif, pas de durée de test imaginaire, pas d'années d'expérience sorties du
chapeau. Si tu ne l'as pas dit, ça n'existe pas.

## Installation

```bash
mkdir -p ~/.claude/skills/publiar-lead-magnet
curl -o ~/.claude/skills/publiar-lead-magnet/SKILL.md \
  https://raw.githubusercontent.com/TokDar2410621/publiar-lead-magnet/main/SKILL.md
```

Rien d'autre. Pas de compte, pas de clé, pas de dépendance.

## Utilisation

Dans Claude Code ou Claude Desktop :

```
génère un lead magnet pour Claude + Notion, j'ai gagné 3h par jour
sur ma prise de notes depuis 2 mois, j'offre un bundle de prompts,
CTA NOTION
```

Tu récupères trois blocs : le post prêt à coller dans LinkedIn, les hashtags,
et un `visual_spec` en JSON qui décrit le visuel à produire.

## Les 8 archétypes visuels

Le skill déduit l'archétype depuis ta matière, tu n'as pas à le choisir.

| Archétype | Déclencheur |
|---|---|
| `tool_pairing` | 2 ou 3 outils combinés |
| `benchmark_table` | comparaison chiffrée de modèles ou de produits |
| `dark_thumbnail` | annonce de produit ou de lancement |
| `youtube_thumbnail` | audience grand public, titre fort |
| `selfie_workspace` | une photo de toi est fournie |
| `system_workflow_screenshot` | un screenshot de workflow est fourni |
| `file_tree_diagram` | une arborescence de stack ou de skills |
| `agent_role_diagram` | une équipe de 3 à 5 agents |

Une preuve réelle uploadée bat toujours une preuve générée. Si tu fournis une
photo, l'archétype est `selfie_workspace`, même si tu as aussi nommé deux
outils.

## Rendre le visuel (optionnel)

Le skill produit le `visual_spec`. Il ne peut pas le transformer en image
depuis ton terminal : le rendu tourne sur un serveur, en Pillow, 1080×1080.

Si tu veux le PNG, branche le MCP Publiar :

```bash
pip install publiar-mcp
```

Génère une clé sur https://publiar.app/profile, section « Clés MCP », puis dans
la config de ton agent :

```json
{
  "mcpServers": {
    "publiar": {
      "command": "publiar-mcp",
      "env": {
        "PUBLIAR_API_KEY": "mcp_pub_xxxxx",
        "PUBLIAR_API_URL": "https://api.publiar.app/api"
      }
    }
  }
}
```

Redémarre ton agent. Il voit alors `render_visual` et les autres outils.

## Ce qui n'est pas dans ce repo

Le corpus des 45 lead magnets analysés. Le skill décrit la méthode qui en a été
tirée, pas les données. Le retrieval sur le corpus passe par le MCP.

## License

MIT.
