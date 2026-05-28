# Handoff — Ampli VOX VT20+ Tone Matcher

_Dernière mise à jour : 2026-05-28_

## 01 — Objectifs / Mission

- App web mono-fichier (`index.html`) qui génère les réglages VOX VT20+ adaptés à une Fender American Special Strat (Texas Special, médiator épais) pour n'importe quel morceau, via GPT-5.5.
- Stocke clé OpenAI + bibliothèque de presets en localStorage. Inclut presets factory, Adjust feedback-driven, modes POWER LEVEL (appart/chambre/répèt/concert).
- **Session du 2026-05-28** : audit sécurité + bugs (`/code-review` medium effort, 3 angles parallèles), puis correction de tous les findings — XSS, validation, état après delete, et bug pédale signalé par l'utilisateur (pedalValue affiché ≠ 0 alors que pedalOn=false sur un preset GPT).

## 02 — État actuel du code

**Ce qui marche**
- Toutes les corrections appliquées (voir section 03).
- Bug pédale OFF/valeur≠0 corrigé : `pedalValue` et `depthVal` forcés à 0 quand l'effet est OFF.
- XSS bloqué sur les 4 sinks (`d.tip`, `d.guitarNote`, `g.note`, `extPedals[].*`) via helper `safeHtml` (allow-list `<strong>` + `<br>`).
- `validatePreset()` clamp/nettoie tout JSON entrant (réponse GPT, import).
- État DOM cohérent après delete d'un custom : `currentSource.index` shifté ou reset, écran ampli revient au placeholder si le preset supprimé était affiché.
- `saveCustom` retourne bool et toast une erreur si quota localStorage ou troncature à 500.

**Ce qui ne marche pas / à vérifier**
- Tests manuels pas encore effectués par l'utilisateur après les patchs (preview ouverte). Golden paths à tester :
  1. Import d'un JSON malicieux → doit être rejeté
  2. Recherche GPT d'un nouveau morceau → vérifier que le knob VALUE de PEDAL est à 0 quand pedalOn=false
  3. Suppression du preset actuellement affiché → l'écran ampli doit revenir au placeholder
  4. Ajuster un preset après avoir supprimé un autre custom de la liste → doit modifier le bon preset
- Modèle API `gpt-5.5` ([index.html:1629](index.html:1629), [1731](index.html:1731)) — pas vérifié si ce nom existe vraiment côté OpenAI ; si non, toutes les requêtes échouent.
- Pas de simplification effectuée — l'utilisateur a dit « non laisse comme c'est ». Code ajoute ~80 lignes (helpers de sécurité).

## 03 — Fichiers touchés

- `index.html` — modifié (uncommitted). Ajouts/modifs :
  - Helpers `safeHtml`, `validatePreset`, `_clamp` (après [index.html:1419](index.html:1419))
  - `escapeHtml` durci (gère `null/undefined`)
  - `buildPanel` : `pedalValue` et `depthVal` à 0 si OFF
  - `render()` : `d.guitar` avec fallback ; tous les innerHTML AI passent par `safeHtml`/`escapeHtml`
  - `importLib` : valide chaque entrée via `validatePreset`, compte les rejetées
  - `searchSong` / `submitAdjust` : valident le JSON GPT, throw clean error si invalide
  - `saveCustom` : retourne bool, toast quota/troncature
  - Delete handler + `clearLib` : reset/shift `currentSource`/`currentDisplay`
  - `changePlayMode` : defensive find()

## 04 — Tentatives échouées

_Rien à signaler_ — toutes les corrections demandées ont été appliquées sans piste morte.

## 05 — Prochaines étapes

1. **Test manuel** des 4 golden paths listés en section 02 (l'utilisateur n'a pas encore validé après les patchs).
2. **Vérifier le nom du modèle OpenAI** `gpt-5.5` à [index.html:1629](index.html:1629) et [index.html:1731](index.html:1731) — si l'API renvoie « model not found », tester `gpt-4o`, `gpt-5`, ou le dernier disponible.
3. **Commit** des changements actuels : `git add index.html && git commit -m "Fix XSS sinks, add preset validation, fix pedal OFF/value bug"`.
4. **Si l'utilisateur revient sur la simplification** : 3 opportunités déjà identifiées dans la conversation — `bankColors`/`bankLed`/`bankNames` dupliqués entre [index.html:1041](index.html:1041) et [index.html:1431](index.html:1431) ; sélecteurs DOM répétés à cacher ; helper `showAppUI(visible)` pour mutualiser `bootApp`/`changeKey`.
