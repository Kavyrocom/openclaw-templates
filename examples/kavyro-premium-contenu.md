# Template : Contenu Premium (exemple Kavyro)
> Quand : creation de contenu premium type tuto, feature, amelioration, guide
> Agents : orchestrateur, agent SEO (si angle contenu), agent OPS (si deploiement)
> Skill requis : notion ou CMS (pour publication)

## Regle absolue
Chaque contenu premium DOIT contenir un **bloc code pret a copier-coller** que le lecteur peut deployer directement dans son agent OpenClaw ou son Claude Code. Pas d'exception.

## Checklist
- [ ] Lire les fichiers de contexte projet (strategie, funnel, cible)
- [ ] Lire le guide de style pour le ton et le format
- [ ] Rediger le contenu (tuto, feature, guide)
- [ ] Inclure le bloc code deployable (voir section ci-dessous)
- [ ] Verifier que le bloc code fonctionne tel quel (pas de placeholder non documente)
- [ ] Publier via API (pas browser)

## Bloc code deployable, OBLIGATOIRE

Chaque contenu premium doit inclure au minimum un bloc de ce type :

### Pour une feature OpenClaw (skill, config, cron)
Le bloc doit etre un fichier complet ou un snippet que le lecteur copie-colle dans son setup :
- Skill : le SKILL.md complet
- Config openclaw.json : le bloc JSON a ajouter
- Cron : le job JSON complet
- Prompt : le prompt systeme ou utilisateur pret a l'emploi

### Pour Claude Code (CLAUDE.md, hook, config)
Le bloc doit etre un extrait directement injectable :
- Section CLAUDE.md a copier
- Hook a ajouter dans settings.json
- Commande a executer

### Regles du bloc code
- **Pret a l'emploi** : le lecteur copie, colle, ca marche
- **Variables claires** : si le lecteur doit adapter quelque chose, utiliser des placeholders explicites comme `VOTRE_CLE_API`, `VOTRE_DOMAINE`, `VOTRE_PROJET`
- **Commentaires en francais** dans le code
- **Pas de dependance cachee** : si le bloc necessite une installation prealable, le documenter avant le bloc

## Format du contenu

### Structure type
1. **Intro** : le probleme ou le besoin (2-3 phrases max)
2. **Ce que ca fait** : description concrete du resultat
3. **Le bloc code** : pret a copier-coller
4. **Comment l'adapter** : quoi modifier selon son contexte
5. **Resultat attendu** : ce que le lecteur doit voir apres deploiement

### Ton
- Direct, technique, pas de fluff
- Tutoiement
- Exemples concrets plutot qu'explications abstraites

## Erreurs frequentes
- **Pas de bloc code** -> INTERDIT, c'est la regle numero 1
- **Bloc code avec des placeholders non documentes** -> chaque variable a remplacer doit etre listee
- **Bloc code qui ne marche pas tel quel** -> tester avant de publier
- **Contenu trop theorique** -> le premium est actionnable, pas encyclopedique
- **Publication via browser** -> utiliser l'API REST du CMS/Notion
