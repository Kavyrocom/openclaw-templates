# openclaw-templates

> Procedures metier reutilisables pour agents OpenClaw, complementaires aux skills.

## A quoi ca sert

Les agents OpenClaw ont des **skills** (outils techniques) mais pas toujours de cadre pour les **procedures metier**. Un skill dit comment appeler une API. Mais il ne dit pas dans quel ordre enchainer 4 skills, quel outil choisir quand il y en a plusieurs, ou quelles regles metier respecter avant de livrer.

Un template est un fichier `.md` qui decrit une procedure complete : declencheur, etapes dans l'ordre, regles, et erreurs a eviter. L'agent le lit avant d'executer et le suit comme une checklist.

Cas concret : sur le projet [Kavyro](https://kavyro.com) (communaute AutomatisationIA), un agent se charge de mettre à jour des pages Notion ouvrait le browser headless (pas de session, timeout, echec) au lieu d'utiliser l'API REST disponible via le skill. Le skill documentait l'API, mais rien n'interdisait le browser. Un template a resolu le probleme en imposant le bon outil.

## Un skill bien documente ne suffit pas ?

Dans la plupart des cas, **si**. Un skill avec un bon SKILL.md couvre 80% des besoins : il dit comment appeler l'API, quels scripts utiliser, quels parametres passer. Si votre procedure concerne un seul skill, documentez-la directement dans le skill (sous-fichier, section supplementaire dans le SKILL.md). Pas besoin de template.

### Quand un template devient necessaire

Un template resout des problemes qu'un skill seul ne peut pas couvrir :

**1. La tache implique plusieurs skills enchaines**

Exemple : deployer un site complet necessite plusieurs skills dans un ordre precis (creation serveur, configuration DNS, installation CMS, SEO de base, verification). Chaque skill sait faire sa partie, mais aucun ne connait le pipeline complet. Le template orchestre l'enchainement et s'assure qu'aucune etape n'est sautee.

Un sous-fichier dans un skill ne peut pas decrire les etapes qui precedent et suivent son propre usage.

**2. L'agent doit choisir entre plusieurs outils**

Exemple : un agent a acces a Notion via l'API REST (skill) ET via le browser. Le skill Notion documente l'API, mais ne dit pas "dans ce contexte, utilise l'API, pas le browser". L'agent prend le chemin le plus simple (browser), qui echoue (pas de session active). Le template impose le bon choix.

Un SKILL.md ne peut pas interdire l'usage d'un autre outil qui n'est pas dans son scope.

**3. La procedure inclut des regles metier transversales**

Exemple : avant de publier du contenu pour un projet, il faut lire le guide de style, verifier la coherence avec la strategie, inclure des elements specifiques (blocs de code, exemples concrets). Ces regles n'appartiennent a aucun skill en particulier, elles sont liees au projet.

### Arbre de decision

```
Procedure concerne un seul skill ?
    oui -> Documenter dans le SKILL.md (sous-fichier ou section)
    non -> Creer un template

L'agent doit choisir entre plusieurs outils ?
    oui -> Template (impose le bon choix)
    non -> SKILL.md suffit

La procedure inclut des regles metier hors scope technique ?
    oui -> Template
    non -> SKILL.md suffit
```

### Comparaison

| | Skill (SKILL.md) | Template |
|---|---|---|
| **Scope** | Un outil, une API | Une tache metier complete |
| **Quand l'utiliser** | Procedure mono-skill | Procedure multi-skills, choix d'outil, regles metier |
| **Ou le mettre** | `skills/nom-skill/SKILL.md` | `templates/nom-tache.md` |
| **Qui le maintient** | L'agent qui utilise le skill | Tout agent qui execute la procedure |
| **Exemple** | "Voici les commandes curl pour Notion" | "Quand on te demande de mettre a jour un projet, utilise l'API Notion (pas le browser), cherche l'ID via search, respecte le rate limit, documente les modifs" |

Les deux coexistent. Le template reference les skills ("lire SKILL.md du skill X avant l'etape Y"), le skill reste la reference technique.

## Installation

### 1. Creer le dossier templates

Le dossier doit etre accessible par le container OpenClaw. Si votre config est montee via un volume Docker (ex: `../config/openclaw:/home/node/.openclaw`), creez le dossier a l'interieur :

```bash
mkdir -p ~/config/openclaw/templates
```

Le dossier sera accessible dans le container a `/home/node/.openclaw/templates/`.

### 2. Copier le README d'index

```bash
cp templates/README.md ~/config/openclaw/templates/README.md
```

Ce fichier sert de reference aux agents pour le format et les regles des templates.

### 3. Ajouter la regle dans vos AGENTS.md

Ajoutez ce bloc dans le fichier AGENTS.md de **chaque agent** qui doit utiliser les templates :

```markdown
## Templates, OBLIGATOIRE

Chemin : `/home/node/.openclaw/templates/`

### Avant d'executer une tache recurrente
1. Lister les templates : `ls /home/node/.openclaw/templates/`
2. Si un template correspond a la tache, le LIRE et le SUIVRE integralement
3. Le template a priorite sur l'improvisation, suivre chaque etape

### Apres une tache recurrente sans template
Si tu viens d'executer une tache qui reviendra (meme type, meme contexte) et qu'il n'y a pas de template :
1. Creer le template dans `/home/node/.openclaw/templates/NOM.md`
2. Format : voir `/home/node/.openclaw/templates/README.md`
3. Signaler a l'utilisateur : "J'ai cree le template NOM pour cette procedure"

### Mise a jour
Si un template est incomplet ou faux, le corriger IMMEDIATEMENT apres avoir termine la tache.
Ne pas attendre, ne pas ignorer. Un template obsolete est pire que pas de template.
```

Le snippet complet est aussi disponible dans `templates/agents-md-snippet.md`.

### 4. Redemarrer le gateway

```bash
cd ~/openclaw && docker compose restart openclaw-gateway
```

## Structure d'un template

Chaque template est un fichier `.md` avec cette structure :

```markdown
# Template : [Nom]
> Quand : [declencheur, dans quel cas ce template s'applique]
> Agents : [qui l'utilise]
> Skill requis : [dependance technique]

## Regle absolue
- La contrainte la plus importante, en gras

## Checklist
- [ ] Etape 1
- [ ] Etape 2
- [ ] Etape 3

## Operations courantes
[Cas d'usage frequents avec exemples concrets]

## Erreurs frequentes
- **Piege 1** -> solution
- **Piege 2** -> solution
```

### Les 4 couches d'un bon template

1. **Le declencheur** : quand est-ce que ce template s'applique ? Un mot-cle dans la demande, un type de tache, un contexte precis
2. **La checklist** : etapes dans l'ordre, actionnables, sans ambiguite
3. **Les operations** : exemples concrets des commandes/actions a executer
4. **Les pieges** : erreurs reelles deja rencontrees (pas des cas theoriques)

## Creer un template

### Quand creer un template

- Une tache revient regulierement (meme type, meme contexte)
- Un agent a fait une erreur evitable (mauvais outil, etape oubliee, raccourci)
- Une procedure a plus de 3 etapes et doit etre reproduite a l'identique
- Un agent improvise au lieu de suivre un workflow prevu

### Quand NE PAS creer un template (utiliser le skill directement)

- **Tache unique, ponctuelle** : pas de recurrence, pas de template
- **Procedure mono-skill** : si ca concerne un seul skill, documenter dans le SKILL.md
- **Le SKILL.md couvre deja la procedure** : ne pas dupliquer, le skill fait reference
- **Information volatile** : donnees qui changent souvent (mettre dans STATE.md ou MEMORY.md)

### Bonnes pratiques

1. **Un template = une procedure.** Pas de mega-template qui couvre 10 cas differents
2. **Checklist actionnable.** Chaque etape doit etre executable sans ambiguite
3. **Erreurs frequentes reelles.** Documenter les pieges deja rencontres, pas les cas theoriques
4. **Mise a jour immediate.** Un template faux est pire que pas de template
5. **Nommage clair.** `notion-projet.md` pas `template-003.md`
6. **Referencer les skills.** Le template dit "lire SKILL.md du skill X avant l'etape Y"

### Cycle de vie

```
Agent rencontre tache recurrente
        |
        v
Template existe ? ──oui──> Lire et suivre
        |
       non
        |
        v
Executer la tache normalement
        |
        v
Creer le template apres execution
        |
        v
Signaler a l'utilisateur
```

Apres chaque execution d'un template existant, l'agent verifie que le template est toujours correct. Si une etape manque, est obsolete ou fausse, il le corrige immediatement.

## Exemples

Le dossier `examples/` contient des templates d'exemple pour illustrer le format et l'approche :

- `notion-projet.md` : operations Notion via API REST (illustre le cas d'un agent qui choisit le mauvais outil)
- `landing-page.md` : pipeline de creation de page web (illustre le cas d'un agent qui saute des etapes)

Ces exemples sont a adapter a votre contexte. Copiez-les dans votre dossier `templates/` et modifiez-les selon vos besoins.

## Contribuer

Les contributions sont bienvenues. Si vous utilisez OpenClaw et avez developpe des templates utiles :

1. Fork ce repo
2. Ajoutez votre template dans `examples/` ou proposez une amelioration du systeme
3. Mettez a jour ce README si necessaire
4. Ouvrez une PR avec une description du probleme resolu

## Licence

MIT, libre d'utilisation, modification et redistribution.
