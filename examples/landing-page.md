# Template : Landing Page
> Quand : creation de landing page, page de vente, page d'accueil, refonte visuelle
> Agents : agent OPS ou agent dedie design (dispatch obligatoire depuis l'orchestrateur)
> Skills requis : design-system, landing-page-blueprint, elementor-page, design-review

## Regle absolue
- L'orchestrateur ne fait PAS de landing page lui-meme -> dispatcher a l'agent qui a les skills design
- L'agent design suit le pipeline dans l'ordre, sans sauter d'etape
- Jamais de HTML inline improvise via exec

## Pipeline (dans cet ordre)

### 1. Design System
- [ ] Verifier si un design system existe deja pour ce projet (chercher dans le workspace)
- [ ] Si non, lire SKILL.md design-system et en generer un
- [ ] Output : fichier design tokens (couleurs, typographie, spacing)

### 2. Landing Page Blueprint
- [ ] Lire SKILL.md landing-page-blueprint
- [ ] Creer l'architecture : sections, contenu, CTA, mots-cles SEO
- [ ] Input : design system + brief utilisateur
- [ ] Output : fichier blueprint structurel

### 3. Elementor Page (ou equivalent)
- [ ] Lire SKILL.md elementor-page
- [ ] Generer le template JSON depuis blueprint + design system
- [ ] Output : fichier JSON importable

### 4. Design Review
- [ ] Lire SKILL.md design-review
- [ ] Auditer le resultat (UX, performance, accessibilite, conversion)
- [ ] Output : rapport avec scores et recommandations

## Raccourcis autorises
- L'utilisateur fournit deja un design system -> sauter etape 1
- L'utilisateur fournit un brief detaille -> integrer dans etape 2
- Modification mineure (bouton, couleur, texte) -> intervention directe, PAS de pipeline complet

## Communication
- Prevenir l'utilisateur au debut : "Je lance le pipeline design complet (4 etapes)"
- Accuse de reception si > 1 minute
- Livrer a la fin : design system + blueprint + template JSON + rapport review

## Erreurs frequentes
- **L'orchestrateur fait le HTML lui-meme** -> INTERDIT, toujours dispatcher l'agent design
- **L'agent saute des etapes** -> chaque etape produit un fichier qui sert d'input a la suivante
- **Pas de review** -> etape 4 obligatoire, meme si le resultat semble bon
- **exec approval bloque** -> ecrire les scripts dans /tmp/, pas de python3 -c inline
- **Fichier HTML trop gros pour edit** -> utiliser write (ecriture complete) plutot que edit (remplacement partiel)
