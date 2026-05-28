# VOX VT20+ Tone Matcher

Web app perso pour reproduire le son de mes morceaux préférés sur mon **VOX VT20+** avec ma **Fender American Special Stratocaster** (single-coils Texas Special, médiator épais).

Powered by **GPT-5.5** (OpenAI API) — recherche n'importe quel morceau et obtiens les réglages d'ampli, le choix de micro, le tone/volume guitare, et les pédales recommandées. Inclut une **bibliothèque de presets intégrés** (Nirvana, AC/DC, Hendrix, Metallica, Pink Floyd, SRV, Muse, Daft Punk, Johnny B. Goode BTTF) et un mode **"Ajuster ce preset"** pour raffiner un son à partir d'un feedback texte.

## ✨ Features

- 🎛️ Affichage fidèle de la façade VOX VT20+ avec potards SVG animés (33 AMPS / PEDAL / MOD-DELAY / REVERB)
- ⚡ **POWER LEVEL cliquable** — 4 modes APPART 3W / CHAMBRE 8W / RÉPÈT 15W / CONCERT 25W
- 🎸 Réglages adaptés à une Strat single-coil Texas Special (compensation single-coil, choix de micro, conseils médiator)
- 🤖 Recherche IA via **GPT-5.5** — n'importe quel morceau
- ✏️ **Ajuster ce preset** — décris ce qui cloche par rapport au son visé (ou colle l'analyse d'un GPT thinking comparant ton enregistrement à une vidéo de référence), GPT-5.5 régénère le preset corrigé
- 💾 Stockage local des morceaux générés (500 max, jamais re-payés) — export/import JSON
- 📚 Bibliothèque intégrée de presets validés sur la vraie configuration
- 📱 Optimisé tablette / iPad / mobile / desktop
- 🔒 Clé API stockée uniquement en localStorage

## 🚀 Utilisation

### Local
Ouvre simplement `index.html` dans ton navigateur. C'est tout.

### GitHub Pages
1. Push ce dossier sur un repo GitHub
2. Settings → Pages → Source: `main` branch, `/root`
3. L'app est accessible sur `https://<ton-user>.github.io/<repo>/`

Démo live : [robmave33.github.io/ampli-vox](https://robmave33.github.io/ampli-vox/)

## 🔑 Clé API

Obtiens ta clé sur [platform.openai.com](https://platform.openai.com/api-keys) (commence par `sk-...`).

Elle reste **uniquement** sur ton appareil (localStorage navigateur). L'app appelle directement l'API OpenAI depuis le navigateur — donc ne déploie pas cette URL publiquement avec ta clé pré-remplie.

## 🛠️ Workflow "Ajuster ce preset"

1. Joue le preset proposé sur ton VT20+, enregistre-toi (dictaphone iPhone/iPad par exemple)
2. Compare ton enregistrement à la vidéo/morceau de référence (à l'oreille, ou en demandant à un GPT thinking d'analyser les deux audios)
3. Clique **✏️ Ajuster ce preset** dans l'app
4. Colle le feedback (ex : "trop sombre, manque le claquant du médiator, ajouter un slapback delay 50s") + URL YouTube de référence optionnelle
5. GPT-5.5 régénère le preset en intégrant ton retour
   - Preset d'usine → un nouveau morceau "(ajusté)" est ajouté à ta biblio, l'original reste intact
   - Preset custom → mis à jour en place

## 🎸 Profil guitare

L'app est calibrée pour :
- **Fender American Special Stratocaster**
- **Micros single-coil Texas Special**
- **Médiator épais / rigide**

Pour adapter à une autre guitare, modifie la constante `GUITAR_CTX` dans `index.html`.

## 📦 Stack

- HTML/CSS/JS vanilla — zéro build, zéro dépendance
- OpenAI Chat Completions API (**gpt-5.5**, `response_format: json_object`)
- Polices : Bebas Neue, JetBrains Mono, Inter (Google Fonts)
