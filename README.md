# openclaw-templates

> Systeme de templates pour agents OpenClaw, complementaire aux skills.

## Pourquoi des templates en plus des skills ?

Les skills OpenClaw documentent **comment** utiliser un outil : endpoints API, scripts, commandes, parametres. C'est technique et necessaire.

Mais un skill ne dit pas :

- **Quand** choisir cet outil plutot qu'un autre
- **Dans quel ordre** enchainer les etapes d'une tache metier
- **Quels pieges** eviter dans un contexte precis
- **Quel standard de qualite** respecter pour le livrable

Resultat concret : un agent qui a le skill Notion (API REST) decide quand meme d'ouvrir le browser pour acceder a Notion, parce que le skill ne lui dit pas "dans ce contexte, utilise l'API, pas le browser". Un agent qui a les skills de design saute 2 etapes sur 4 parce que rien ne lui impose l'ordre.

**Le template comble ce vide.** Il decrit la procedure metier complete : quand l'appliquer, quelles etapes suivre, dans quel ordre, avec quels standards. Le skill reste la reference technique, le template est le cadre operationnel.

### En resume

| | Skill | Template |
|---|---|---|
| **Repond a** | "Comment j'appelle cette API ?" | "Quelle procedure je suis pour cette tache ?" |
| **Contenu** | Endpoints, scripts, commandes | Checklist, regles, ordre des etapes |
| **Scope** | Un outil | Une tache metier (peut impliquer plusieurs skills) |
| **Exemple** | "Voici les commandes curl pour Notion" | "Quand on te demande de mettre a jour un projet sur Notion : API REST uniquement, chercher l'ID via search, respecter le rate limit, documenter les modifs" |

Les deux sont complementaires et se referencent mutuellement.

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

### Quand NE PAS creer un template

- Tache unique, ponctuelle
- Procedure deja entierement couverte par un SKILL.md existant
- Information volatile qui change souvent (mettre dans STATE.md ou MEMORY.md)

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
