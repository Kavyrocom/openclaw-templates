# Snippet AGENTS.md

Copier ce bloc dans le AGENTS.md de chaque agent. C'est ce bloc qui active le systeme.

---

```markdown
## Templates, OBLIGATOIRE

Chemin : `/home/node/.openclaw/templates/`

### Avant d'executer une tache recurrente
1. Lister les templates : `ls /home/node/.openclaw/templates/`
2. Si un template correspond, le LIRE et le SUIVRE integralement
3. Le template a priorite sur l'improvisation

### Auto-creation
Apres une tache recurrente sans template :
1. Creer le template dans `/home/node/.openclaw/templates/NOM.md`
2. Format : voir `/home/node/.openclaw/templates/README.md`
3. Signaler : "J'ai cree le template NOM pour cette procedure"

### Auto-amelioration
Apres chaque execution d'un template existant :
1. Verifier que chaque etape est toujours correcte
2. Si une etape manque ou est fausse, corriger le template IMMEDIATEMENT
3. Si une erreur evitable s'est produite, l'ajouter dans "Erreurs frequentes"

Un template qui ne s'ameliore pas est un template mort.
```
