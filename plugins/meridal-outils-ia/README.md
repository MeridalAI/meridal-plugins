# meridal-outils-ia

Un plugin qui apprend à un assistant à **choisir entre un abonnement d'IA d'équipe et l'API d'un modèle**, avec les prix relevés et datés par Meridal.

Il contient deux choses :

- une **skill**, `choisir-un-abonnement` : la méthode en six temps (séparer les usages, mesurer un échange, lire les prix, calculer, ajouter ce que l'API demande, essayer un mois sous plafond) ;
- le **serveur MCP des relevés de Meridal** (`https://meridal.fr/mcp`), en lecture seule : le prix d'un modèle, les abonnements d'équipe, les notes aux classements, le chemin d'un réglage, les dates des relevés. Chaque réponse porte sa source et sa date.

## Qui publie, et ce que le plugin ne fait pas

Meridal est un cabinet de conseil et de formation à l'IA pour les PME. Il vend du conseil : c'est un intérêt, et la skill le dit. Elle ne recommande pas Meridal, ne le classe dans aucune liste, et cite la source et la date de chaque chiffre. Le serveur MCP n'envoie rien à Meridal : il lit des relevés publics, sans authentification, et ne reçoit que la question posée à l'outil.

## Installer

**Claude Code**

```bash
claude plugin marketplace add https://github.com/MeridalAI/meridal-plugins
claude plugin install meridal-outils-ia@meridal
```

**Un autre assistant** qui lit le standard [Agent Plugins](https://agent-plugins.org/) : le dossier du plugin porte `plugin.json`, `skills/` et `mcp.json`. Une skill seule se copie telle quelle, c'est le standard [Agent Skills](https://agentskills.io/specification).

**Sans plugin**, le serveur seul : `claude mcp add --transport http meridal https://meridal.fr/mcp`

## Licences

La skill est sous licence CC BY 4.0 : reprenez-la en citant Meridal. La configuration du plugin est sous licence MIT. Les relevés appartiennent à leurs sources ; leur sélection et leur mise en forme sont sous CC BY 4.0 (voir `https://meridal.fr/apprendre/releves-en-telechargement/`).
