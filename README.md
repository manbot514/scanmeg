# ScanMeg — Wireframes interactifs

Documentation des wireframes HTML/CSS du projet de refonte du site scanmeg.com.

Ces wireframes ont été produits pour définir la structure, la navigation et le contenu de chaque page avant l'intégration WordPress. Ils sont navigables dans un navigateur web et constituent la référence visuelle et fonctionnelle du projet.

---

## Contexte

ScanMeg est un développeur québécois de systèmes de détection de présence et de mesure industrielle, présent dans plus de 40 pays depuis 2001. Les nouveaux acquéreurs souhaitent refondre le site autour de trois objectifs principaux : améliorer l'acquisition de nouveaux clients, renforcer la rétention des clients existants (accès aux manuels, support), et augmenter le NPS.

---

## Structure du dépôt

```
/
├── index.html                  Accueil
├── produits.html               Hub catalogue
├── categorie-infrarouge.html   Archive catégorie (exemple : Infrarouge)
├── fiche-produit.html          Fiche produit (exemple : Type P)
├── technologie.html            Technologies de détection
├── services.html               Services offerts
├── support.html                Hub support clients
├── support-manuels.html        Bibliothèque de manuels PDF
├── entreprise.html             À propos / entreprise
├── contact.html                Contact et bureaux
├── wireframe.css               Feuille de style partagée
└── ccitb-entreprise-innovante.png  Badge de reconnaissance CCITB
```

---

## Pages

### `index.html` — Accueil

Page principale du site. Présente l'entreprise, les catégories de produits et les arguments de vente clés.

| Section | Contenu |
|---|---|
| Hero | Titre principal, texte d'accroche, 2 boutons CTA |
| KPI | 4 chiffres clés : 2001 (fondation), 40+ pays, 5 000+ usines, 24 000 produits |
| Catégories produits | Grille des 4 technologies — générée dynamiquement depuis WooCommerce |
| Engagement | Texte split + image — précision et fiabilité |
| Durabilité | 4 colonnes : résistance froid, débris, vibration, chaleur |
| Services | Aperçu des 6 services avec liens |
| Fabriqué au Québec | Section dark — fierté locale |
| Témoignages | 3 citations clients existants |
| CTA | Bande pleine largeur → contact |

---

### `produits.html` — Hub catalogue

Point d'entrée du catalogue. Liste les 4 catégories de produits.

| Section | Contenu |
|---|---|
| Page header | Titre + breadcrumb |
| Portail catégories | 4 cartes : Infrarouge, Laser, Laser 3D, Modules et périphériques |
| CTA | Lien vers contact pour aide au choix |

> Les 4 catégories sont générées dynamiquement depuis WooCommerce. L'ajout d'une nouvelle catégorie WC la fait apparaître ici automatiquement.

---

### `categorie-infrarouge.html` — Archive catégorie

Page d'exemple pour une catégorie de produits. Le même template s'applique aux 4 catégories.

| Section | Contenu |
|---|---|
| Page header | Titre de la catégorie + description technologique + breadcrumb |
| Grille produits | Cartes produits avec image, badge catégorie, titre, description courte, lien fiche |
| Autres catégories | Navigation transversale vers les 3 autres catégories |
| CTA | Contact pour conseil produit |

---

### `fiche-produit.html` — Fiche produit

Page détaillée d'un produit. Exemple basé sur le Type P (Infrarouge).

| Section | Contenu |
|---|---|
| Page header | Nom du produit + breadcrumb (Accueil > Produits > Infrarouge > Type P) |
| Fiche | Image produit · Description longue · Caractéristiques techniques (liste) · Documents PDF (Manuel, Brochure) · Bouton "Parler à un conseiller" |
| Produits liés | 4 autres produits de la même catégorie |
| CTA | Contact expert produit |

**Documents attachés :** Manuel PDF et Brochure PDF gérés via ACF sur la fiche WooCommerce.  
**Bouton principal :** "Parler à un conseiller →" — remplace le bouton panier WooCommerce standard, pointe vers la page contact.

---

### `technologie.html` — Technologies

Présente les 4 technologies de détection ScanMeg avec un système de tabs interactifs.

| Section | Contenu |
|---|---|
| Page header | Titre + intro |
| Tabs | 4 onglets : Laser · Infrarouge · Laser 3D · Modules et périphériques |
| Contenu par tab | Description technologique + image + applications types |
| Performance / Specs | Tableau comparatif des technologies |
| Augmente le rendement | Section argumentaire split |
| Nos industries | Grille des industries servies |
| Durabilité | Résistances environnementales |
| CTA | Contact |

**Interaction :** Les tabs sont gérés en JavaScript vanilla. Le tab actif reçoit la classe `.on`. Voir la section `<!-- ══════ TABS JS ══════ -->` en bas du fichier.

---

### `services.html` — Services

Présente les 6 services de ScanMeg et met en valeur l'équipe technique.

| Section | Contenu |
|---|---|
| Page header | Titre + accroche |
| Vidéo | Embed vidéo "Voici comment nous travaillons" |
| Services détaillés | 6 cartes : Vente · Conseils · Ingénierie sur mesure · Réparation express · Optimisation après-vente · Suivi après-vente |
| Notre équipe | Section dark — R&D, perfectionnement, dévouement + photo équipe |
| CTA | Contact |

---

### `support.html` — Hub support

Point d'entrée pour les clients existants. Oriente vers les deux ressources principales.

| Section | Contenu |
|---|---|
| Contact direct | Téléphone, email, horaires |
| Manuels | Lien → `support-manuels.html` |
| Réparation / SAV | Coordonnées SAV + lien contact |
| Prospect | Renvoi vers la page contact pour les non-clients |

---

### `support-manuels.html` — Bibliothèque manuels

Permet aux clients de trouver et télécharger les manuels de leurs produits.

| Section | Contenu |
|---|---|
| Barre de recherche | Recherche par numéro de produit ou mot-clé |
| Filtres catégorie | Tabs : Infrarouge · Laser · Laser 3D · Modules |
| Note technique | Avertissement : contacter le support si le manuel est introuvable |
| Documentation | Liste des produits par catégorie avec liens de téléchargement PDF |
| CTA | Contact support technique |

> La liste est générée dynamiquement : tous les produits WooCommerce ayant un fichier PDF attaché via le champ ACF `manuel_pdf` apparaissent ici automatiquement. Aucune maintenance manuelle nécessaire.

---

### `entreprise.html` — À propos

Présente l'histoire, les valeurs et les installations de ScanMeg.

| Section | Contenu |
|---|---|
| Page header | Titre "Une entreprise fière depuis 2001" |
| KPI | 4 chiffres : 2001, 40+ pays, 5 000+ usines, 4 bureaux |
| Innovation | Texte split + image — mission et R&D |
| Badge CCITB | Reconnaissance "Entreprise Innovante" — Chambre de Commerce Thérèse-de-Blainville |
| Timeline | Jalons historiques de 2001 à aujourd'hui |
| Fabriqué au Québec | Section dark — fierté locale |
| Galerie locaux | Photos des installations de Boisbriand |
| CTA | Contact |

**Badge CCITB :** Le fichier `ccitb-entreprise-innovante.png` est inclus dans le dépôt. Placé en section dark band entre la partie "innovation" et la timeline. Le placement dans le footer est laissé à la discrétion de l'intégrateur WordPress.

---

### `contact.html` — Contact

Page de contact avec formulaire simple et informations des bureaux.

| Section | Contenu |
|---|---|
| Page header | Titre "Restons en contact" |
| Formulaire | Nom · Courriel · Message · Bouton envoi |
| Bureaux | 4 bureaux : Boisbriand QC · Surrey BC · France · Japon (Shimada via Hirotacorp) |

---

## Design system

### Palette

| Variable | Valeur | Usage |
|---|---|---|
| `--navy` | `#1a1a1a` | Fond principal, header, footer, sections dark |
| `--navy-light` | `#2a2a2a` | Cartes sombres, fonds secondaires |
| `--white` | `#ffffff` | Texte sur fond sombre, surfaces claires |
| `--slate-600` | `#475569` | Texte courant |
| `--slate-400` | `#94a3b8` | Labels, texte secondaire, muted |
| `--slate-200` | `#e2e8f0` | Bordures légères |

### Typographie

| Variable | Famille | Usage |
|---|---|---|
| `--font-heading` | Plus Jakarta Sans | Titres, navigation, labels uppercase |
| `--font-body` | DM Sans | Corps de texte, descriptions |

Les deux polices sont chargées depuis Google Fonts.

### Variantes de section

| Classe | Apparence | Usage |
|---|---|---|
| `.sec` | Fond blanc, padding 64px | Section standard |
| `.sec--gray` | Fond gris clair | Alternance visuelle |
| `.sec--dark` | Fond navy, textes blancs | Mise en valeur, CTA, fabriqué au Québec |

---

## Navigation

### Structure du header

```
[ Barre supérieure : téléphone · email · langue FR/EN ]  ← masquée mobile
[ Logo SCANMEG ]  [ Nav : 7 items ]  [ Switcher FR/EN ]  [ ☰ hamburger ]
```

### Items de navigation

Accueil · Produits · Services · Technologie · Support · À propos · Contact

### Comportement mobile (< 640px)

- La barre supérieure est masquée
- La nav est masquée, le hamburger `☰` s'affiche
- Clic hamburger → toggle classe `.open` sur `.nav` → menu en colonne
- Clic en dehors → fermeture automatique
- Le JavaScript est inclus en bas de chaque page HTML

### Switcher de langue

Boutons FR / EN dans le header. Le bouton actif reçoit la classe `.on`. À connecter à WPML en intégration.

---

## Composants récurrents

| Composant | Classe CSS | Description |
|---|---|---|
| Carte catégorie | `.cat-c` | Image + titre + lien → archive WC |
| Carte produit | `.p-card` | Image + badge + titre + desc + lien → fiche WC |
| Carte service | `.svc-c` | Fond sombre, titre + texte |
| Carte industrie | `.ind-c` | Image 200px + corps texte |
| Témoignage | `.testi` | Citation italique + bordure gauche colorée + auteur |
| KPI band | `.kpi` + `.kpi-row` | Bande de statistiques clés |
| CTA band | `.cta` | Bande pleine largeur, titre + sous-titre + bouton(s) |
| Page header | `.pg-hdr` | Fond navy, breadcrumb + H1, commun à toutes les pages intérieures |
| Timeline | `.tl` + `.tl-item` | Jalons historiques zigzag (desktop) / vertical (mobile) |

---

## Responsive

Breakpoints définis dans `wireframe.css` :

| Breakpoint | Comportement |
|---|---|
| `> 1024px` | Layout desktop complet |
| `≤ 1024px` | Grilles passent en 2 colonnes |
| `≤ 640px` | Mobile : 1 colonne, hamburger, barre supérieure masquée |

---

## Notes pour l'intégrateur

- **Catégories WooCommerce :** tout ce qui liste des catégories ou des produits est dynamique. Ne jamais coder ces listes en dur — l'ajout d'une catégorie WC doit se répercuter automatiquement sur toutes les pages qui les affichent (accueil, hub produits, technologie, support-manuels).
- **Badge CCITB :** fichier `ccitb-entreprise-innovante.png` inclus. Placement prévu dans `entreprise.html`. Le placement footer est laissé à la discrétion de l'équipe.
- **Bouton panier WooCommerce :** remplacer par "Parler à un conseiller →" pointant vers la page contact.
- **Prix produits :** afficher "Sur demande" — catalogue B2B sans transaction en ligne.
- **Lien "Réparation express"** dans `services.html` : pointe vers la page contact.
