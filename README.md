# openclaw-templates -- Systeme de templates pour agents OpenClaw

> Procedures metier reutilisables pour agents IA OpenClaw -- complementaires aux skills.

## Le probleme

Dans une architecture multi-agents OpenClaw, les agents ont des **skills** (outils techniques : API, scripts, commandes) mais pas de cadre pour les **procedures metier**. Resultat :

- Un agent charge de mettre a jour une page Notion ouvre le **browser** au lieu d'utiliser l'**API REST** -- parce que le skill dit *comment* appeler l'API, mais pas *quand* et *pourquoi* l'utiliser plutot que le browser
- Un agent improvise du HTML inline pour creer une landing page au lieu de suivre un pipeline design structure
- Un agent utilise `python3 -c "..."` inline (bloque par le gateway comme commande obfusquee) au lieu d'ecrire un script fichier

Le point commun : **l'agent connait les outils mais pas la bonne procedure**. Il prend le chemin le plus court, pas le bon.

Contexte reel : sur le projet Kavyro (communaute tech/IA), l'agent essayait d'acceder a Notion via le browser headless (qui n'a pas de session active) au lieu d'utiliser l'API REST disponible via le skill. Resultat : timeout, erreur, pas de livrable.

## La solution : templates

Un **template** est un fichier `.md` qui decrit une procedure metier complete, avec :

- **Quand** l'utiliser (declencheur)
- **Checklist** des etapes dans l'ordre
- **Regles** a respecter
- **Erreurs frequentes** et comment les eviter

### Difference skill vs template

| | Skill | Template |
|---|---|---|
| **Role** | Outil technique | Procedure metier |
| **Contenu** | API endpoints, scripts, commandes | Checklist, regles, standards qualite |
| **Exemple** | "Voici comment appeler l'API Notion" | "Quand on te demande de mettre a jour Kavyro sur Notion, voici la procedure complete" |
| **Quand le lire** | Avant d'utiliser un outil specifique | Avant d'executer une tache recurrente |

Les deux sont complementaires : le template dit **quoi faire et dans quel ordre**, le skill dit **comment le faire techniquement**.

## Structure d'un template

```markdown
# Template : [Nom]
> Quand : [declencheur -- quand cet template s'applique]
> Agents : [qui l'utilise]
> Skill requis : [dependance technique]

## Regle absolue
- La regle la plus importante, en gras

## Checklist
- [ ] Etape 1
- [ ] Etape 2
- [ ] Etape 3

## Operations courantes
[Exemples concrets des cas d'usage les plus frequents]

## Erreurs frequentes
- **Erreur X** -> Solution Y
- **Erreur Z** -> Solution W
```

## Installation

### 1. Creer le dossier templates

Le dossier doit etre accessible par le container OpenClaw. Si votre config est montee via un volume Docker (ex: `../config/openclaw:/home/node/.openclaw`), creez le dossier a l'interieur :

```bash
mkdir -p ~/config/openclaw/templates
```

Le dossier sera accessible dans le container a `/home/node/.openclaw/templates/`.

### 2. Copier les templates

```bash
# Cloner ce repo
git clone https://github.com/CapsuleWeb/openclaw-templates.git

# Copier les templates dans votre config
cp openclaw-templates/templates/*.md ~/config/openclaw/templates/
```

### 3. Ajouter la regle dans vos AGENTS.md

Ajoutez ce bloc dans le fichier AGENTS.md de **chaque agent** qui doit utiliser les templates :

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

### 4. Redemarrer le gateway

```bash
cd ~/openclaw && docker compose restart openclaw-gateway
```

## Templates inclus

### `notion-projet.md`

Template generique pour les operations Notion sur un projet.

**Probleme resolu** : l'agent tente d'ouvrir Notion dans le browser (pas de session active → timeout) au lieu d'utiliser l'API REST.

**Regle cle** : TOUJOURS l'API REST, JAMAIS le browser pour Notion.

### `landing-page.md`

Pipeline de creation de landing page en 4 etapes.

**Probleme resolu** : l'agent orchestrateur improvise du HTML inline au lieu de deleguer a l'agent specialise qui a le pipeline design complet.

**Regle cle** : dispatcher a l'agent qui a les skills design, suivre le pipeline dans l'ordre (design-system → blueprint → elementor → review).

## Creer vos propres templates

### Quand creer un template

- Une tache revient regulierement (meme type, meme contexte)
- Un agent a fait une erreur evitable (mauvais outil, etape oubliee)
- Une procedure a plus de 3 etapes et doit etre reproduite a l'identique

### Quand NE PAS creer un template

- Tache unique, ponctuelle
- Procedure deja couverte par un SKILL.md
- Information qui change souvent (mettre dans STATE.md ou MEMORY.md)

### Bonnes pratiques

1. **Un template = une procedure**. Pas de mega-template qui couvre 10 cas
2. **Checklist actionnable**. Chaque etape doit etre executable sans ambiguite
3. **Erreurs frequentes**. Documenter les pieges reels (pas les cas theoriques)
4. **Mise a jour immediate**. Un template faux est pire que pas de template
5. **Nommage clair**. `notion-kavyro.md` pas `template-003.md`

## Contribuer

Les contributions sont bienvenues. Si vous utilisez OpenClaw et avez developpe des templates utiles :

1. Fork ce repo
2. Ajoutez votre template dans `templates/`
3. Mettez a jour ce README (section "Templates inclus")
4. Ouvrez une PR avec une description du probleme resolu

## Licence

MIT
