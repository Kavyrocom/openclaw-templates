# Template : Notion Projet
> Quand : toute operation sur un workspace Notion liee a un projet
> Agents : agent OPS (via dispatch orchestrateur), orchestrateur si urgent
> Skill requis : notion (API REST)

## Regle absolue
- **TOUJOURS utiliser l'API REST Notion** (curl + NOTION_API_KEY)
- **JAMAIS le browser** pour acceder a Notion (pas de session active, login impossible depuis le container)
- Si une erreur mentionne le browser ou un timeout Notion -> ignorer le browser, passer a l'API

## Checklist avant toute operation
- [ ] Lire le SKILL.md du skill notion
- [ ] Verifier que NOTION_API_KEY est disponible dans l'environnement
- [ ] Identifier l'ID de la page/base cible (via search API si besoin)
- [ ] Respecter le rate limit (sleep 0.4 entre requetes en batch)

## Operations courantes

### Rechercher une page ou base
```bash
curl -s -X POST "https://api.notion.com/v1/search" \
  -H "Authorization: Bearer $NOTION_API_KEY" \
  -H "Notion-Version: 2022-06-28" \
  -H "Content-Type: application/json" \
  -d '{"query": "MON_PROJET", "page_size": 20}'
```

### Lire une page
```bash
curl -s "https://api.notion.com/v1/pages/{page_id}" \
  -H "Authorization: Bearer $NOTION_API_KEY" \
  -H "Notion-Version: 2022-06-28"
```

### Lire les blocs d'une page
```bash
curl -s "https://api.notion.com/v1/blocks/{page_id}/children?page_size=100" \
  -H "Authorization: Bearer $NOTION_API_KEY" \
  -H "Notion-Version: 2022-06-28"
```

### Mettre a jour un statut
```bash
curl -s -X PATCH "https://api.notion.com/v1/pages/{page_id}" \
  -H "Authorization: Bearer $NOTION_API_KEY" \
  -H "Notion-Version: 2022-06-28" \
  -H "Content-Type: application/json" \
  -d '{"properties": {"Status": {"status": {"name": "Done"}}}}'
```

### Ajouter un commentaire
```bash
curl -s -X POST "https://api.notion.com/v1/comments" \
  -H "Authorization: Bearer $NOTION_API_KEY" \
  -H "Notion-Version: 2022-06-28" \
  -H "Content-Type: application/json" \
  -d '{"parent": {"page_id": "{page_id}"}, "rich_text": [{"text": {"content": "Mon commentaire"}}]}'
```

## Adapter a votre projet

Ce template est generique. Pour l'adapter a un projet specifique :

1. Copier ce fichier : `cp notion-projet.md notion-monprojet.md`
2. Ajouter les IDs connus (pages, bases de donnees)
3. Ajouter les regles de contenu specifiques (ton, structure, coherence)
4. Referencer les fichiers de contexte projet si applicable

## Erreurs frequentes
- **Browser timeout** -> Ne JAMAIS utiliser le browser pour Notion. L'API REST fonctionne sans session.
- **401 Unauthorized** -> Verifier NOTION_API_KEY. La page/base doit etre partagee avec l'integration.
- **429 Rate limit** -> Ajouter `sleep 0.4` entre les requetes en batch.
- **Page introuvable** -> Utiliser la search API avec le nom du projet.
- **"Session Notion login required"** -> IGNORER. L'API ne necessite pas de session browser.
- **python3 -c bloque** -> Ecrire le script dans un fichier `/tmp/script.py` puis executer. Ne jamais utiliser de code inline.
