# VOX VT40+ Tone Matcher

Web app perso pour reproduire le son de mes morceaux préférés sur mon **VOX VT40+** avec ma **Fender American Special Stratocaster** (single-coils Texas Special, médiator épais).

Powered by **Claude** (Anthropic API) — recherche n'importe quel morceau et obtiens les réglages d'ampli, le choix de micro, le tone/volume guitare, et les pédales recommandées.

## ✨ Features

- 🎛️ Affichage fidèle de la façade VOX VT40+ avec potards SVG animés
- 🎸 Réglages adaptés à une Strat single-coil Texas Special
- 🤖 Recherche IA via Claude Sonnet 4.6 — n'importe quel morceau
- 💾 Stockage local des morceaux générés (jamais re-payés)
- 📱 Optimisé tablette / iPad / mobile / desktop
- 🔒 Clé API stockée uniquement en localStorage

## 🚀 Utilisation

### Local
Ouvre simplement `index.html` dans ton navigateur. C'est tout.

### GitHub Pages
1. Push ce dossier sur un repo GitHub
2. Settings → Pages → Source: `main` branch, `/root`
3. L'app est accessible sur `https://<ton-user>.github.io/<repo>/`

## 🔑 Clé API

Obtiens ta clé sur [console.anthropic.com](https://console.anthropic.com/settings/keys) (commence par `sk-ant-...`).

Elle reste **uniquement** sur ton appareil (localStorage navigateur). L'app appelle directement l'API Anthropic depuis le navigateur via le header `anthropic-dangerous-direct-browser-access` — donc ne déploie pas cette URL publiquement avec ta clé pré-remplie.

## 🎸 Profil guitare

L'app est calibrée pour :
- **Fender American Special Stratocaster**
- **Micros single-coil Texas Special**
- **Médiator épais / rigide**

Pour adapter à une autre guitare, modifie la constante `GUITAR_CTX` dans `index.html`.

## 📦 Stack

- HTML/CSS/JS vanilla — zéro build, zéro dépendance
- Anthropic Messages API (claude-sonnet-4-6)
- Polices : Bebas Neue, JetBrains Mono, Inter (Google Fonts)
