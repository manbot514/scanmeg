# ScanMeg — Guide d'implémentation WordPress
## Document de référence pour Claude Code

---

## Stack technique

| Composant | Choix | Raison |
|---|---|---|
| Thème | **Custom** (généré par Claude Code) | Structure exacte des wireframes, zéro dépendance externe |
| Catalogue | **CPT `produit`** + taxonomie `categorie-produit` | Pas de e-commerce, WooCommerce inutile |
| Champs custom | **ACF Pro** | PDFs bilingues, champs produit |
| Bilingue | **WPML** | FR/EN, switching automatique des PDFs |
| Formulaire | **Contact Form 7** | Formulaire contact simple |
| SEO | **Yoast SEO** | Meta, sitemap XML |

**Total : 5 plugins. Aucun plugin e-commerce.**

---

## Architecture du thème custom

### Fichiers du thème (à générer)

```
/wp-content/themes/scanmeg/
├── style.css                   Déclaration du thème + variables CSS
├── functions.php               Enqueue scripts/styles, enregistrement CPT, Customizer
├── index.php                   Fallback
├── header.php                  Header global (nav, switcher langue)
├── footer.php                  Footer global (4 colonnes)
├── front-page.php              Accueil (index.html)
├── page.php                    Pages statiques génériques
├── page-services.php           Services (services.html)
├── page-technologie.php        Technologie avec tabs (technologie.html)
├── page-support.php            Hub support (support.html)
├── page-support-manuels.php    Bibliothèque manuels (support-manuels.html)
├── page-entreprise.php         À propos (entreprise.html)
├── page-contact.php            Contact + CF7 (contact.html)
├── archive-produit.php         Hub catalogue (produits.html)
├── taxonomy-categorie-produit.php  Archive catégorie (categorie-infrarouge.html)
├── single-produit.php          Fiche produit (fiche-produit.html)
└── assets/
    ├── css/
    │   └── main.css            Styles principaux
    ├── js/
    │   └── main.js             Hamburger, tabs technologie, scripts globaux
    └── fonts/                  Fonts locales si nécessaire
```

---

## Variables CSS — Customizer

Ces variables sont modifiables via **Apparence → Personnaliser** sans toucher au code.

```css
:root {
  /* Couleurs — modifiables via Customizer */
  --color-primary:     #1a1a1a;   /* Couleur principale (navy) */
  --color-secondary:   #2a2a2a;   /* Couleur secondaire */
  --color-accent:      #e63946;   /* Couleur d'accentuation (CTA, liens actifs) */
  --color-bg:          #ffffff;   /* Fond général */
  --color-bg-alt:      #f5f5f5;   /* Fond sections alternées */
  --color-text:        #475569;   /* Texte courant */
  --color-text-muted:  #94a3b8;   /* Texte secondaire */
  --color-border:      #e2e8f0;   /* Bordures */

  /* Typographie — modifiable via Customizer */
  --font-heading: 'Plus Jakarta Sans', sans-serif;
  --font-body:    'DM Sans', sans-serif;

  /* Effets — modifiables via Customizer */
  --shadow-card:  0 4px 12px rgba(0,0,0,0.08);
  --shadow-image: 0 8px 24px rgba(0,0,0,0.12);
  --radius:       4px;
}
```

**Options exposées dans le Customizer :**
- Couleur principale
- Couleur d'accentuation
- Font heading (liste Google Fonts)
- Font body (liste Google Fonts)
- Ombre sur les cards (on/off + intensité)
- Ombre sur les images (on/off + intensité)

---

## CPT Produit

### Enregistrement

```php
register_post_type('produit', [
  'labels'       => ['name' => 'Produits', 'singular_name' => 'Produit'],
  'public'       => true,
  'has_archive'  => true,
  'rewrite'      => ['slug' => 'produits'],
  'supports'     => ['title', 'editor', 'thumbnail'],
  'show_in_rest' => true,
]);

register_taxonomy('categorie-produit', 'produit', [
  'labels'       => ['name' => 'Catégories', 'singular_name' => 'Catégorie'],
  'hierarchical' => true,
  'rewrite'      => ['slug' => 'produits'],
]);
```

### Règle métier : suppression catégorie
Une catégorie ne peut pas être supprimée si elle contient des produits. Comportement natif WordPress pour les taxonomies hiérarchiques — rien à coder.

### Règle métier : catégorie unique par produit
À enforcer via ACF ou un hook `save_post` qui vérifie qu'un seul terme est sélectionné.

---

## Champs ACF — Fiche produit

| Nom du champ | Type ACF | Description |
|---|---|---|
| `product_description` | Textarea | Description longue (FR) |
| `product_specs` | Repeater → Text | Liste des caractéristiques techniques |
| `pdf_1_fr` | File | PDF 1 version française |
| `pdf_1_en` | File | PDF 1 version anglaise |
| `pdf_2_fr` | File | PDF 2 version française |
| `pdf_2_en` | File | PDF 2 version anglaise |
| `pdf_3_fr` | File | PDF 3 version française |
| `pdf_3_en` | File | PDF 3 version anglaise |

### Logique d'affichage des PDFs

```php
// Dans single-produit.php
$lang = apply_filters('wpml_current_language', null); // 'fr' ou 'en'

for ($i = 1; $i <= 3; $i++) {
  $pdf = get_field("pdf_{$i}_{$lang}");
  if ($pdf) {
    // Afficher le bouton de téléchargement
  }
}
```

Les PDFs s'affichent automatiquement dans la langue de navigation. Si un PDF n'est pas uploadé, le bouton n'apparaît pas.

---

## Structure des pages

### Pages statiques — contenu éditable via Gutenberg

Ces pages sont éditables par le client directement dans WordPress (Gutenberg). Le thème fournit le gabarit (layout), Gutenberg gère le contenu.

| Page WordPress | Template thème | URL |
|---|---|---|
| Accueil | `front-page.php` | `/` |
| Services | `page-services.php` | `/services/` |
| Technologie | `page-technologie.php` | `/technologie/` |
| Support | `page-support.php` | `/support/` |
| Support — Manuels | `page-support-manuels.php` | `/support/manuels/` |
| Entreprise | `page-entreprise.php` | `/entreprise/` |
| Contact | `page-contact.php` | `/contact/` |

### Pages dynamiques — générées automatiquement

| Template thème | URL | Source des données |
|---|---|---|
| `archive-produit.php` | `/produits/` | Toutes les `categorie-produit` WP |
| `taxonomy-categorie-produit.php` | `/produits/{slug-categorie}/` | Produits de la catégorie |
| `single-produit.php` | `/produits/{slug-categorie}/{slug-produit}/` | CPT `produit` + ACF |
| `page-support-manuels.php` | `/support/manuels/` | Query CPT `produit` filtrée sur PDFs |

### Règle dynamique fondamentale
> **Tout ce qui liste des catégories ou des produits est une requête WordPress dynamique. Aucune liste ne doit être codée en dur dans un template.**

L'ajout d'une catégorie ou d'un produit se répercute automatiquement sur :
- La page `/produits/` (hub catalogue)
- La section catégories de l'accueil
- La page Technologie (tabs)
- La page Manuels (filtres + liste PDFs)

---

## Page Manuels — logique dynamique

```php
// Query : tous les produits ayant au moins un PDF dans la langue courante
$lang = apply_filters('wpml_current_language', null);

$args = [
  'post_type'  => 'produit',
  'meta_query' => [
    'relation' => 'OR',
    ['key' => "pdf_1_{$lang}", 'compare' => '!=', 'value' => ''],
    ['key' => "pdf_2_{$lang}", 'compare' => '!=', 'value' => ''],
    ['key' => "pdf_3_{$lang}", 'compare' => '!=', 'value' => ''],
  ],
];
```

Uploader un PDF sur une fiche produit → il apparaît automatiquement sur la page Manuels. Aucune maintenance manuelle.

---

## WPML — Configuration bilingue

### Langues
- Langue principale : **Français (FR)**
- Langue secondaire : **Anglais (EN)**
- Switcher : boutons FR / EN dans le header

### Ce qui est traduit via WPML
- Toutes les pages statiques (Gutenberg)
- Les CPT `produit` (titre, description, specs)
- Les termes de taxonomie `categorie-produit`
- Les strings d'interface (menus, labels, footer)

### Ce qui est géré par ACF + WPML
- Les champs PDF : `pdf_1_fr` / `pdf_1_en` etc. — pas de traduction WPML, champ séparé par langue
- L'affichage switch automatiquement selon `wpml_current_language`

### Slugs EN
| Page FR | Page EN |
|---|---|
| `/produits/` | `/en/products/` |
| `/produits/infrarouge/` | `/en/products/infrared/` |
| `/services/` | `/en/services/` |
| `/support/manuels/` | `/en/support/manuals/` |
| `/entreprise/` | `/en/about/` |
| `/contact/` | `/en/contact/` |

---

## Contenu éditable — ce que le client peut modifier

Via l'interface WordPress standard, sans toucher au code :

| Quoi | Comment |
|---|---|
| Textes des pages (accueil, services, etc.) | Éditeur Gutenberg de la page |
| Image d'un produit | Miniature de l'article (Featured Image) |
| Titre d'un produit | Titre de l'article |
| Description d'un produit | ACF `product_description` |
| Caractéristiques techniques | ACF Repeater `product_specs` |
| PDFs d'un produit | ACF `pdf_1_fr`, `pdf_1_en`, etc. |
| Ajouter un produit | Nouveau → Produit |
| Supprimer un produit | Mettre à la corbeille |
| Ajouter une catégorie | Produits → Catégories |
| Couleurs, fonts, ombres | Apparence → Personnaliser |
| Menus de navigation | Apparence → Menus |
| Informations de contact (footer) | Apparence → Personnaliser → Infos footer |

---

## Formulaire de contact

Plugin : **Contact Form 7**
Page : `/contact/` → template `page-contact.php`

Champs :
- Nom
- Courriel
- Message
- Bouton Envoyer

Destinataire : à configurer dans CF7 (email du client).

---

## Responsive

Le thème est mobile-first. Breakpoints identiques aux wireframes :

| Breakpoint | Comportement |
|---|---|
| `> 1024px` | Desktop complet |
| `≤ 1024px` | Grilles 2 colonnes |
| `≤ 640px` | 1 colonne, hamburger, barre top masquée |

Le hamburger toggle la classe `.nav-open` sur le `<body>` via `main.js`.

---

## Ce que le DA peut personnaliser sans toucher au code

Via **Apparence → Personnaliser** :
- Toutes les couleurs (variables CSS)
- Les fonts (Google Fonts)
- L'intensité des ombres cards et images
- Le logo
- Les informations du footer (adresse, téléphone, email, réseaux sociaux)

Via **style.css** du thème enfant (si modifications plus avancées) :
- Espacements
- Tailles de texte
- Animations
- Tout le reste

---

## Animations

Trois effets à implémenter via JavaScript vanilla + CSS. Aucune dépendance lourde sauf Lenis.

### 1. Scroll physique avec inertie — Lenis

Librairie : **Lenis** (légère, ~3kb, MIT).

```js
import Lenis from '@studio-freight/lenis'

const lenis = new Lenis({
  duration: 1.2,        // Inertie — ajuster pour plus/moins de fluidité
  easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
  smooth: true,
})

function raf(time) {
  lenis.raf(time)
  requestAnimationFrame(raf)
}
requestAnimationFrame(raf)
```

S'applique à toutes les pages automatiquement via `main.js`.

---

### 2. Fade-in on scroll — IntersectionObserver

Smooth, pas brutal. Threshold bas pour déclencher tôt, transition longue pour l'élégance.

**CSS :**
```css
.reveal {
  opacity: 0;
  transform: translateY(20px);
  transition: opacity 0.7s ease, transform 0.7s ease;
}

.reveal.visible {
  opacity: 1;
  transform: translateY(0);
}
```

**JS :**
```js
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      entry.target.classList.add('visible')
      observer.unobserve(entry.target) // Se déclenche une seule fois
    }
  })
}, {
  threshold: 0.1,     // Déclenche dès 10% visible — pas trop strict
  rootMargin: '0px 0px -40px 0px'  // Légèrement avant le bas du viewport
})

document.querySelectorAll('.reveal').forEach(el => observer.observe(el))
```

**Éléments qui reçoivent la classe `.reveal` dans les templates :**
- Cards produits (`.p-card`)
- Cards catégories (`.cat-c`)
- Cards services (`.svc-c`)
- Sections split texte/image
- Photos et galeries
- Témoignages

**Éléments qui ne reçoivent PAS `.reveal` :** header, hero, footer — ils sont toujours visibles.

---

### 3. Compteur KPI — JS vanilla

Déclenché quand la bande KPI entre dans le viewport via IntersectionObserver.

```js
function animateCounter(el) {
  const target = parseInt(el.dataset.target)  // ex: data-target="5000"
  const duration = 1800 // ms
  const start = performance.now()

  function update(now) {
    const elapsed = now - start
    const progress = Math.min(elapsed / duration, 1)
    const eased = 1 - Math.pow(1 - progress, 3) // ease-out cubic
    el.textContent = Math.floor(eased * target).toLocaleString('fr-CA')
    if (progress < 1) requestAnimationFrame(update)
  }
  requestAnimationFrame(update)
}

const kpiObserver = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      animateCounter(entry.target)
      kpiObserver.unobserve(entry.target)
    }
  })
}, { threshold: 0.5 })

document.querySelectorAll('.kpi-number').forEach(el => kpiObserver.observe(el))
```

**HTML dans `front-page.php` :**
```html
<span class="kpi-number" data-target="5000">5 000</span>
<!-- Affiche "5 000" par défaut (si JS désactivé), remplacé par le compteur sinon -->
```

Les suffixes (`+`, `ans`) sont gérés séparément en CSS (`::after`) pour ne pas interférer avec le compteur.

---

### Ordre de chargement dans `main.js`

1. Lenis (scroll inertie) — init immédiate
2. IntersectionObserver fade-in — après DOMContentLoaded
3. Compteurs KPI — après DOMContentLoaded
4. Hamburger menu toggle
5. Tabs technologie

---

## Ordre d'implémentation suggéré pour Claude Code

1. Scaffolding du thème (`style.css`, `functions.php`, `header.php`, `footer.php`)
2. Variables CSS + Customizer (couleurs, fonts, ombres)
3. Enregistrement CPT `produit` + taxonomie `categorie-produit`
4. Champs ACF (specs, PDFs bilingues)
5. `front-page.php` — Accueil
6. `archive-produit.php` — Hub catalogue
7. `taxonomy-categorie-produit.php` — Archive catégorie
8. `single-produit.php` — Fiche produit + logique PDFs WPML
9. `page-support-manuels.php` — Manuels (query dynamique)
10. `page-services.php` + `page-technologie.php` (tabs JS)
11. `page-support.php` + `page-entreprise.php`
12. `page-contact.php` + intégration CF7
13. Configuration WPML + traduction des slugs
14. Tests responsive (mobile, tablette, desktop)
