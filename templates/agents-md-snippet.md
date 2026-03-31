# Snippet AGENTS.md -- Bloc a copier dans vos fichiers AGENTS.md

Copiez ce bloc dans le fichier AGENTS.md de chaque agent qui doit utiliser les templates.

---

```markdown
## Templates -- OBLIGATOIRE

Chemin : `/home/node/.openclaw/templates/`

### Avant d'executer une tache recurrente
1. Lister les templates : `ls /home/node/.openclaw/templates/`
2. Si un template correspond a la tache -> le LIRE et le SUIVRE integralement
3. Le template a priorite sur l'improvisation -- suivre chaque etape

### Apres une tache recurrente sans template
Si tu viens d'executer une tache qui reviendra (meme type, meme contexte) et qu'il n'y a pas de template :
1. Creer le template dans `/home/node/.openclaw/templates/NOM.md`
2. Format : voir `/home/node/.openclaw/templates/README.md`
3. Signaler a l'utilisateur : "J'ai cree le template NOM pour cette procedure"

### Mise a jour
Si un template est incomplet ou faux -> le corriger IMMEDIATEMENT apres avoir termine la tache.
Ne pas attendre, ne pas ignorer. Un template obsolete est pire que pas de template.
```
