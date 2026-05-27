# VOX VT20+ Tone Matcher

Web app perso pour reproduire le son de mes morceaux préférés sur mon **VOX VT20+** avec ma **Fender American Special Stratocaster** (single-coils Texas Special, médiator épais).

Powered by **GPT-4o** (OpenAI API) — recherche n'importe quel morceau et obtiens les réglages d'ampli, le choix de micro, le tone/volume guitare, et les pédales recommandées.

## ✨ Features

- 🎛️ Affichage fidèle de la façade VOX VT20+ avec potards SVG animés
- 🎸 Réglages adaptés à une Strat single-coil Texas Special
- 🤖 Recherche IA via GPT-4o — n'importe quel morceau
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

Obtiens ta clé sur [platform.openai.com](https://platform.openai.com/api-keys) (commence par `sk-...`).

Elle reste **uniquement** sur ton appareil (localStorage navigateur). L'app appelle directement l'API OpenAI depuis le navigateur — donc ne déploie pas cette URL publiquement avec ta clé pré-remplie.

## 🎸 Profil guitare

L'app est calibrée pour :
- **Fender American Special Stratocaster**
- **Micros single-coil Texas Special**
- **Médiator épais / rigide**

Pour adapter à une autre guitare, modifie la constante `GUITAR_CTX` dans `index.html`.

## 📦 Stack

- HTML/CSS/JS vanilla — zéro build, zéro dépendance
- OpenAI Chat Completions API (gpt-4o)
- Polices : Bebas Neue, JetBrains Mono, Inter (Google Fonts)
