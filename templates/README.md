# Templates -- Procedures metier agents OpenClaw

## Difference skill vs template
- **Skill** = outil technique + scripts + comment appeler une API
- **Template** = procedure metier + standards qualite + checklist

## Regles
1. Avant d'executer une tache recurrente, chercher si un template existe
2. Si un template existe, le LIRE et le SUIVRE integralement
3. Si une tache recurrente n'a PAS de template, en creer un apres execution
4. Mise a jour : si une etape manque ou est fausse, corriger le template IMMEDIATEMENT

## Format d'un template

```markdown
# Template : [Nom]
> Quand : [declencheur]
> Agents : [qui l'utilise]
> Skill requis : [dependance]

## Regle absolue
- La contrainte la plus importante

## Checklist
- [ ] Etape 1
- [ ] Etape 2
...

## Operations courantes
[Cas d'usage frequents avec exemples]

## Erreurs frequentes
- **Piege 1** -> solution
- **Piege 2** -> solution
```

## Index

| Template | Declencheur | Probleme resolu |
|----------|-------------|-----------------|
| `notion-projet.md` | Operations Notion sur un projet | Agent utilise le browser au lieu de l'API REST |
| `landing-page.md` | Creation de landing page / page web | Agent improvise du HTML au lieu de suivre le pipeline design |
