# openclaw-templates

> Systeme de cadrage procedural pour agents OpenClaw. S'installe sur votre instance, se nourrit des erreurs de vos agents, et s'ameliore tout seul.

## Le probleme

Les agents OpenClaw ont des skills bien documentes (API, scripts, commandes). Mais ils improvisent quand meme. Ils prennent des raccourcis, sautent des etapes, choisissent le mauvais outil. Pas parce qu'ils sont mauvais, mais parce qu'un skill dit **comment** utiliser un outil, pas **quand**, **dans quel ordre**, et **avec quelles regles metier**.

Cas concret : sur le projet Kavyro (communaute Automatisation / IA), un agent charge de mettre a jour des pages Notion ouvrait le browser headless (pas de session, timeout, echec) au lieu d'utiliser l'API REST disponible via le skill. Le skill documentait l'API, mais rien n'interdisait le browser. Un template a resolu le probleme en imposant le bon outil.

**openclaw-templates** resout ca : un systeme de procedures installees sur votre instance OpenClaw, que les agents consultent avant d'agir, et qu'ils ameliorent eux-memes apres chaque execution.

## Ce que c'est (et ce que c'est pas)

**C'est** un systeme a installer sur votre OpenClaw :
- Un dossier `templates/` dans votre config agent
- Un bloc de regles a ajouter dans vos AGENTS.md
- Une boucle d'auto-apprentissage ou les agents creent et corrigent les templates

**Ce n'est pas** une bibliotheque de templates a telecharger et utiliser tels quels. Les exemples fournis illustrent le format, c'est a vos agents de construire les templates adaptes a votre contexte.

## Comment ca marche

### 1. L'agent rencontre une tache

Avant d'executer, il consulte le dossier `templates/`. Si un template correspond, il le lit et le suit comme une checklist. Le template a priorite sur l'improvisation.

### 2. Pas de template ? L'agent en cree un apres

Si la tache est recurrente et qu'aucun template n'existe, l'agent en cree un apres execution. Il signale a l'utilisateur : "J'ai cree le template X pour cette procedure."

### 3. Template existant ? L'agent le verifie et le corrige

Apres chaque execution d'un template, l'agent verifie que les etapes sont toujours correctes. Si une etape manque, est obsolete ou fausse, il corrige le template immediatement. Pas d'attente, pas de ticket. Le template s'ameliore a chaque usage.

### 4. Erreur evitable ? Le template l'integre

Quand un agent fait une erreur evitable (mauvais outil, etape oubliee, raccourci qui casse), la correction est documentee dans la section "Erreurs frequentes" du template concerne. L'erreur ne se reproduit plus.

```
Tache recurrente
    |
    v
Template existe ? ──oui──> Lire, suivre, verifier, corriger si besoin
    |
   non
    |
    v
Executer normalement
    |
    v
Creer le template ──> Signaler a l'utilisateur
    |
    v
Prochaine execution : le template existe, la boucle est fermee
```

C'est un systeme vivant : plus vos agents travaillent, plus les templates se precisent.

## Skill vs Template : quand utiliser quoi

Un skill bien documente suffit dans 80% des cas. Un template devient necessaire quand :

**La tache enchaine plusieurs skills.** Deployer un site necessite creation serveur + DNS + CMS + SEO. Chaque skill fait sa partie, aucun ne connait le pipeline complet.

**L'agent doit choisir entre plusieurs outils.** Un SKILL.md ne peut pas interdire un outil qui n'est pas dans son scope.

**La procedure inclut des regles metier.** Lire un guide de style avant de publier, respecter un format precis, inclure des elements obligatoires. Ca n'appartient a aucun skill.

```
Procedure mono-skill ?     -> Documenter dans le SKILL.md
Procedure multi-skills ?   -> Template
Choix d'outil a imposer ?  -> Template
Regles metier transverses ? -> Template
```

| | Skill (SKILL.md) | Template |
|---|---|---|
| **Scope** | Un outil, une API | Une tache metier complete |
| **Contenu** | Endpoints, scripts, commandes | Checklist, regles, ordre des etapes |
| **Maintenu par** | L'agent qui utilise le skill | Tout agent qui execute la procedure |
| **Evolue** | A la mise a jour du skill | A chaque execution par un agent |

## Installation

### 1. Creer le dossier templates

```bash
mkdir -p ~/config/openclaw/templates
```

Accessible dans le container a `/home/node/.openclaw/templates/`.

### 2. Ajouter le fichier d'index

Copier `templates/README.md` de ce repo dans votre dossier. Il sert de reference aux agents pour le format et les regles.

```bash
cp templates/README.md ~/config/openclaw/templates/README.md
```

### 3. Ajouter la regle dans vos AGENTS.md

Copier ce bloc dans le AGENTS.md de **chaque agent**. C'est le bloc qui active le systeme : sans lui, les agents ignorent le dossier templates.

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

Le snippet est aussi dans `templates/agents-md-snippet.md`.

### 4. Redemarrer le gateway

```bash
cd ~/openclaw && docker compose restart openclaw-gateway
```

A partir de la, vos agents commencent a creer et maintenir les templates automatiquement.

## Structure d'un template

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
- [ ] Etape 3

## Operations courantes
[Exemples concrets]

## Erreurs frequentes
- **Piege 1** -> solution
- **Piege 2** -> solution

## Historique des corrections
- [date] : [correction apportee par l'agent]
```

### Les 4 couches d'un bon template

1. **Le declencheur** : quand ce template s'applique (mot-cle, type de tache, contexte)
2. **La checklist** : etapes dans l'ordre, actionnables, sans ambiguite
3. **Les operations** : exemples concrets des commandes a executer
4. **Les pieges** : erreurs reelles deja rencontrees, alimentees par les agents au fil du temps

## Creer un template manuellement

### Quand creer

- Une tache revient regulierement
- Un agent a fait une erreur evitable
- Une procedure a plus de 3 etapes et doit etre reproduite a l'identique

### Quand NE PAS creer

- Tache unique, ponctuelle
- Procedure mono-skill (documenter dans le SKILL.md)
- Information volatile (mettre dans STATE.md ou MEMORY.md)

### Bonnes pratiques

1. **Un template = une procedure.** Pas de mega-template
2. **Checklist actionnable.** Chaque etape executable sans ambiguite
3. **Erreurs reelles.** Pieges deja rencontres, pas des cas theoriques
4. **Nommage clair.** `notion-projet.md` pas `template-003.md`
5. **Referencer les skills.** "Lire SKILL.md du skill X avant l'etape Y"

## Exemples

Le dossier `examples/` contient des templates d'exemple pour illustrer le format :

- `notion-projet.md` : un agent qui choisit le mauvais outil (browser vs API)
- `landing-page.md` : un agent qui saute des etapes dans un pipeline

Adaptez-les a votre contexte. Ils ne sont pas faits pour etre utilises tels quels.

## Contribuer

Ce projet est maintenu par [Kavyro](https://kavyro.com), communaute francophone Automatisation/IA.

Si vous utilisez OpenClaw et avez ameliore le systeme :

1. Fork ce repo
2. Proposez votre amelioration (nouveau format, meilleur snippet AGENTS.md, retour d'experience)
3. Ouvrez une PR avec une description du probleme resolu

## Licence

MIT, libre d'utilisation, modification et redistribution.
