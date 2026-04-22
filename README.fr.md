<p align="center">
  <a href="https://hitmman55.github.io/claude-code-chat-viewer/">
    <img src="https://img.shields.io/badge/▶-DÉMO%20EN%20LIGNE-2ea043?style=for-the-badge" alt="Démo en ligne" />
  </a>
</p>
<p align="center"><i>Un simple clic — aucune installation, aucun build, aucune inscription.</i></p>

<p align="center">
  <a href="README.md">English</a> ·
  <a href="README.ru.md">Русский</a> ·
  <a href="README.es.md">Español</a> ·
  <b>Français</b> ·
  <a href="README.zh-CN.md">中文</a>  ·
  <a href="README.ar.md">العربية</a>
</p>

# Claude Code Chat Viewer

<p align="center">
  <img src="https://img.shields.io/badge/license-Unlicense-blue" alt="Licence : Unlicense" />
  <img src="https://img.shields.io/badge/runtime%20deps-0-brightgreen" alt="Zéro dépendance runtime" />
  <img src="https://img.shields.io/badge/offline-first-success" alt="Fonctionne hors ligne" />
  <img src="https://img.shields.io/badge/i18n-6%20languages-informational" alt="6 langues d'interface" />
</p>

<p align="center">
  <img src="screenshot.png" alt="Capture d'écran" width="900" />
</p>

Visionneuse HTML pour les transcriptions de sessions [Claude Code](https://claude.com/claude-code) au format JSONL. S'ouvre dans un navigateur — sans serveur, sans build, une seule dépendance vendorée en local. Fonctionne hors ligne immédiatement.

## Pourquoi

Claude Code enregistre chaque session dans `~/.claude/projects/<project>/<session-uuid>.jsonl` — une ligne par enregistrement (message utilisateur, réponse du modèle, thinking, tool_use, tool_result, attachment, etc.). Le fichier brut est illisible ; les commandes intégrées comme `/resume` affichent la conversation mais ne permettent ni de l'exporter ni de l'inspecter après coup.

Cette visionneuse transforme un tel `.jsonl` en un flux lisible avec des rôles codés par couleur, des blocs de service repliables (thinking / tools / results) et des filtres.

## Ce qui est affiché

- **user** (bleu) — messages réels de l'utilisateur
- **assistant** (vert) — réponses textuelles de Claude
- **thinking** (violet) — extended thinking, replié par défaut
- **tool_use** (ambre) — appels d'outils avec aperçu des arguments
- **tool_result** (cyan / rouge pour les erreurs) — réponses des outils
- **meta / task-note** (jaune) — injections système et `<task-notification>` des sous-agents
- **system / attachment / ui-state** — enregistrements de service (cachés par défaut)

Chaque bloc est une ligne distincte avec une barre colorée à gauche. Pas de bulles de messagerie : c'est un log, pas un chat.

## Comment ouvrir

Choisissez ce qui vous convient :

**A. Télécharger un seul fichier** — le plus simple. Prenez le HTML autonome depuis la [dernière release](https://github.com/hitmman55/claude-code-chat-viewer/releases/latest), double-cliquez dessus. Un fichier, fonctionne hors ligne pour toujours.

**B. Cloner le dépôt** — si vous voulez modifier le code. Il vous faut `index.html` et le dossier `lib/`.

**C. En ligne** — ouvrez <https://hitmman55.github.io/claude-code-chat-viewer/>. Rien à télécharger.

Une fois ouvert, cliquez sur le sélecteur de fichier (ou déposez-en un, ou utilisez le bouton « Essayer avec l'exemple ») et choisissez une transcription `.jsonl`.

Les transcriptions de Claude Code se trouvent dans :

```
~/.claude/projects/<project-slug>/<session-uuid>.jsonl
```

où `<project-slug>` est votre répertoire de travail avec `/` remplacé par `-`. Exemple : `/home/user/myproj` → `-home-user-myproj`.

## Fonctionnalités

### Comment charger un fichier

- **Sélecteur de fichier** — cliquez sur « Choisir un fichier » et sélectionnez un `.jsonl`.
- **Glisser-déposer** — déposez un fichier n'importe où sur la page. Un contour pointillé signale la zone. Les dossiers et les éléments qui ne sont pas des fichiers sont rejetés proprement.
- **Essayer avec l'exemple** — dans l'état initial (utilisation en ligne), un bouton apparaît qui charge un `demo.jsonl` intégré montrant tous les types de blocs.

### Confort de lecture

- **Mode lecture** — bouton dans l'en-tête qui masque tout sauf les messages réels de l'utilisateur et les réponses textuelles de l'assistant. Pas de thinking, pas d'appels d'outils, pas d'enregistrements de service.
- **Copier par message** — bouton `📋` dans l'en-tête de chaque entrée. Copie le texte dans le presse-papiers avec une confirmation `✓` pendant 1.2 s. Utilise `navigator.clipboard.writeText` avec un fallback `document.execCommand` pour les contextes `file://`.
- **Noms d'outils conviviaux** — les outils connus reçoivent des icônes (par ex. `📖 Read file`, `🖥️ Shell`, `📝 Edit file`). Outils inconnus / MCP → `🔧 {raw_name}`.
- **Thème automatique** — la visionneuse suit le `prefers-color-scheme` du système par défaut. Cliquez sur le bouton soleil/lune pour épingler un choix ; le choix épinglé persiste entre les sessions.
- **Six langues d'interface** — English, Русский, Español, Français, 中文, العربية. L'arabe bascule en RTL. Sélecteur dans l'en-tête, préférence sauvegardée.

### Performance et sécurité

- **Analyse en streaming** — `.jsonl` est lu via `file.stream()` + `TextDecoderStream`, pas chargé comme une seule chaîne.
- **Virtualisation native** — `content-visibility: auto` sur chaque enregistrement : le navigateur ignore layout et paint pour les entrées hors écran. Tient la charge sur des milliers d'enregistrements.
- **Rendu par lots** — 500 enregistrements par lot, bouton « Afficher plus » pour le reste.
- **Filtres** — cinq cases à cocher (thinking / tools / results / system / ui-state), activent/désactivent les catégories via une seule classe CSS sur le conteneur (pas de reflow du DOM).
- **Rendu sûr contre XSS** — chaque bloc de texte est échappé _avant_ le parseur markdown. Les images Markdown sont neutralisées (texte inerte, jamais récupérées). Les liens sont limités à `http(s)` avec `rel="noopener noreferrer nofollow"`.
- **Plafonds de taille** — blocs de prose tronqués à 20 Ko, blocs de service à 5 Ko. Les opérations de copie héritent de ces plafonds — la page n'envoie jamais des mégaoctets au presse-papiers et ne peut pas faire planter le navigateur.
- **Fallback pour `.json`** — si le fichier n'est pas du JSONL mais un tableau/objet JSON simple, il est parsé comme une liste d'enregistrements.

## Prérequis navigateur

- Chrome / Edge 85+
- Safari 18+
- Firefox 125+

Tout ceci est requis pour `content-visibility: auto`. Sur des navigateurs plus anciens, la visionneuse s'ouvre mais le scroll sur les gros fichiers sera sensiblement plus lent.

## Dépendances

Une seule, vendorée en local dans `lib/` :

- [marked](https://github.com/markedjs/marked) — markdown → HTML (~35 Ko)

Pas de CDN, pas de réseau, pas de Subresource Integrity. Clonez et lancez.

## Vie privée

Tout fonctionne localement dans votre navigateur. La visionneuse elle-même **n'effectue aucune requête réseau automatique** — pas de CDN, pas d'analytics, pas de polices distantes. Les images Markdown incorporées dans les transcriptions sont neutralisées : affichées comme texte inerte avec l'URL visible, mais jamais récupérées. Les liens externes (uniquement `http(s)`) ne s'ouvrent dans un nouvel onglet que lorsque vous cliquez dessus, avec `rel="noopener noreferrer nofollow"`. Vos transcriptions restent sur votre machine.

## Limitations connues

- Les fichiers de plus de ~100 Mo nécessitent un chargement indexé par offsets de lignes avec rendu par fenêtres (non implémenté).
- Pas d'export en Markdown/HTML (l'objectif est la visualisation, pas la conversion).
- Pas de coloration syntaxique dans les blocs de code (choix assumé pour garder les dépendances minimales).

## Développement

Tout le code tient dans un seul fichier HTML. Modifiez-le directement — styles dans `<style>`, logique dans `<script>`, traductions dans l'objet `I18N` en tête du script.

Vérifier la syntaxe JS sans navigateur :

```bash
sed -n '/^<script>$/,/^<\/script>$/p' index.html | sed '1d;$d' | node --check /dev/stdin
```

## Licence

[Unlicense](LICENSE) — domaine public. Utilisez-la comme bon vous semble, aucune attribution requise.
