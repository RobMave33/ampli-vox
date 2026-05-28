# Handoff — VOX VT20+ Tone Matcher (Strat Edition)

_Dernière mise à jour : 2026-05-28_

## 01 — Objectifs / Mission

App web statique (HTML+JS+CSS dans un seul `index.html`) qui aide le joueur à régler son **VOX VT20+** pour reproduire n'importe quel morceau, **adapté à sa Fender American Special Strat (single-coils Texas Special, médiator épais)**.

- Recherche de morceaux libres via **GPT-5.5** (OpenAI API, clé fournie par l'utilisateur, stockée localement)
- Bibliothèque de morceaux préréglés (Nirvana, AC/DC, Hendrix, Metallica, Pink Floyd, SRV, Muse, Daft Punk, Johnny B. Goode BTTF)
- Façade VT20+ "fidèle" rendue en SVG : sélecteurs rotatifs (33 AMPS / PEDAL / MOD-DELAY / REVERB), potards EQ chicken-head, LEDs, POWER LEVEL cliquable (4 modes)
- Sauvegarde locale des morceaux GPT (jusqu'à 500), export/import JSON
- **Nouveau cette session** : bouton "✏️ Ajuster ce preset" → modale feedback + URL YouTube → GPT-5.5 régénère le preset corrigé
- Déployé en GitHub Pages : https://robmave33.github.io/ampli-vox/
- Repo : https://github.com/RobMave33/ampli-vox

But de la session écoulée :
1. Affiner le preset Johnny B. Goode (BTTF) après comparaison audio user vs vidéo YouTube de référence
2. Ajouter un mécanisme de **correction de preset à partir d'un feedback texte** (intégrer une diagnose externe, type analyse GPT thinking comparant un enregistrement et une vidéo)
3. Basculer le modèle GPT-5 → **GPT-5.5**

## 02 — État actuel du code

**Ce qui marche :**
- Tout l'existant pré-session (recherche, biblio, façade SVG, POWER LEVEL 4 modes, scrollbar, favicon)
- **Preset Johnny B. Goode révisé** (commit `f4f1a1f`) : TREBLE 7→8, MID 6→5, BASS 4→3 ; ajout TAPE ECHO 100ms feedback 15% (slapback signature 50s) ; micro chevalet (5) au lieu d'inter-chevalet ; tones guitare 8→9 ; `guitarNote` et `tip` mis à jour. Validé à l'oreille par l'utilisateur après croisement avec analyse externe (GPT thinking + .m4a perso + vidéo YouTube Marty McFly).
- **Feature "Ajuster ce preset"** (commit `abdef31`) :
  - Bouton sous le `tipBox` ouvre une modale (overlay sombre + carte centrée)
  - Champs : textarea feedback + input URL YouTube (optionnel)
  - Appel GPT-5.5 avec prompt système d'ajustement dédié (court, focalisé sur "modifier UNIQUEMENT ce qui sert le feedback") + le preset actuel sérialisé en JSON
  - **Persistance** : preset custom → mis à jour en place (même index dans `getCustom()`) ; preset d'usine → cloné en haut de la biblio avec " (ajusté)" suffixé au titre + champs `adjustedFromKey` / `adjustedAt`
  - State `currentSource = {type:'factory'|'custom', key?, index?}` posé dans les click handlers de `renderGrid()` et après `searchSong()`
- **Modèle bumped à `gpt-5.5`** (commit `68acda6`) pour les 2 appels API (`searchSong` + `submitAdjust`) + labels UI ("GPT-5.5 analyse le son…", etc.)

**Ce qui ne marche pas / à surveiller :**
- _Rien de cassé connu_, mais à valider en condition réelle :
  - **Disponibilité de `gpt-5.5` sur la clé OpenAI de l'utilisateur** : si erreur "model not found" en prod, fallback `gpt-5` ou snapshot `gpt-5.5-2026-04-23`
  - **Coût GPT-5.5** : pas vérifié dans la doc, peut être ~2-3× plus cher que GPT-5. L'utilisateur a choisi de garder 5.5 partout et de surveiller via `platform.openai.com/usage`. Stratégie hybride possible si trop cher : `gpt-5.4-mini` pour la recherche, `gpt-5.5` pour l'ajustement uniquement.
  - **UX modale ajustement** : pas testée en condition réelle, la mise en page mobile pourrait avoir besoin de retouches (overlay 6vh top, max-width 540px)

## 03 — Fichiers touchés

- `index.html` — fichier unique de l'app (HTML + CSS + JS). Toutes les modifs de la session sont dedans :
  - **EQ + slapback Johnny B. Goode** : objet `johnnybgoode` dans la const `P` (~ligne 1223)
  - **CSS modale + bouton ajuster** : après `.err-msg.on` (~ligne 270)
  - **HTML modale + bouton** : après `<div class="tip" id="tipBox">` (~ligne 798)
  - **JS adjust** : `openAdjust()`, `closeAdjust()`, `submitAdjust()` ajoutées en fin de bloc `<script>` (~ligne 1645+)
  - **State `currentSource`** : déclaré ligne 883, mis à jour dans les click handlers `renderGrid()` (~ligne 1285) et après `searchSong()` (~ligne 1582)
  - **Modèle `gpt-5.5`** : lignes ~1629 et ~1731 dans les 2 `fetch`
- `handoff.md` — ce fichier, à la racine (untracked, jamais committé jusqu'ici)

Pas de framework, pas de build : édition directe + git push → GitHub Pages rebuild en ~30s.

## 04 — Tentatives échouées

- **Refactor du prompt système en const partagée `SYS_PROMPT`** au début de la session : tentative de hack avec `void \`...\`` puis `/* commentaire */` qui a créé du bruit dans le code. Abandonné — finalement chaque appel API garde son propre prompt inline (search = prompt long avec exemples few-shot ; adjust = prompt court focalisé sur la diff). **Ne PAS rejouer cette refacto**, ça ne sert à rien tant qu'il n'y a que 2 appels.
- **Idée d'intégrer audio direct (.m4a + extraction YouTube) dans l'app** : envisagée puis écartée. GPT-5 / GPT-5.5 standard ne prennent pas d'audio en entrée — il faudrait `gpt-4o-audio-preview` + MediaRecorder + extraction YouTube côté client (lourd). Choix retenu : passer par du **texte de feedback** (l'utilisateur fait l'analyse audio externalement via GPT thinking et colle la diagnose).
- **GPT-5 + `temperature: 0.4`** (déjà connu de la session précédente, conservé ici en mémoire) → API renvoie `Unsupported value`. GPT-5/5.5 n'acceptent QUE la valeur par défaut. **Ne pas remettre le param temperature dans le body.**
- **`commit -a` sans identité git** → bloqué. Toujours commit via `git -c user.email="robin.maveyraud@gmail.com" -c user.name="Robin Maveyraud" commit -m "…"` (consigne user : ne pas toucher au config global).

## 05 — Prochaines étapes

**À faire au démarrage si le user le demande :**

1. **Validation en condition réelle de la feature "Ajuster ce preset"** :
   - Sélectionner un preset, cliquer "✏️ Ajuster ce preset", coller un feedback (ex : copier l'analyse GPT thinking comme on a fait pour Johnny B. Goode), valider.
   - Vérifier que :
     - Si preset d'usine → un nouveau morceau "(ajusté)" apparaît en haut de la biblio
     - Si preset custom → il est mis à jour à la même place (titre conservé)
     - Le `tip` et `guitarNote` sont bien mis à jour par GPT, pas juste les valeurs numériques
   - Si bug visuel sur mobile, retoucher `.adj-modal { max-width:540px }` et `.adj-overlay { padding:6vh 14px }` (~ligne 270 du CSS).

2. **Vérifier dispo de `gpt-5.5` sur la clé** : si l'API renvoie une erreur "model not found", soit :
   - Fallback `gpt-5` (rapide, on rollback les 2 occurrences ligne ~1629 et ~1731)
   - Tester snapshot daté `gpt-5.5-2026-04-23`
   - Ou stratégie hybride : `gpt-5.4-mini` dans `searchSong` + `gpt-5.5` dans `submitAdjust`

3. **Idées en attente, non implémentées** :
   - Bouton "Reset" sur un preset d'usine ajusté pour revenir à l'original (utile si l'ajustement rate)
   - Badge visuel "AJUSTÉ" sur les morceaux clonés depuis un preset d'usine (utiliser `adjustedFromKey`)
   - Audio direct via `gpt-4o-audio-preview` (gros chantier, écarté pour l'instant)

4. **Ne PAS toucher :**
   - L'orientation des potards (startA=225)
   - Les wattages POWER LEVEL (3/8/15/25)
   - Le composant guitare 2 TONE (manche + milieu)
   - Le preset Johnny B. Goode actuel — validé à l'oreille par l'utilisateur

**Démarrage type :** utilisateur parle français, mode direct/familier, préfère qu'on lui dise ce qui ne marchera pas avant de coder. Toujours commit + push à la fin d'une série de modifs, identité inline `git -c user.email=… -c user.name=…`. Site live = https://robmave33.github.io/ampli-vox/.
