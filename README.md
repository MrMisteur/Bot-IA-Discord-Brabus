# 🌴 Brabus — Bot Discord IA

Bot Discord en français, personnalité « tonton antillais grande gueule et attachant » aux couleurs de la Guadeloupe et de la Martinique. Il discute par mention, réponse ou prénom, chambre, juge, anime des jeux, sort du shatta en direct et garde la mémoire de chaque salon. Tout tient dans un seul fichier : `bot.py`.

## Sommaire

- [Fonctionnalités](#fonctionnalités)
- [Prérequis](#prérequis)
- [Installation](#installation)
- [Configuration (`.env`)](#configuration-env)
- [Permissions et intents Discord](#permissions-et-intents-discord)
- [Commandes](#commandes)
- [Données et sauvegarde](#données-et-sauvegarde)
- [Dépannage](#dépannage)
- [Crédits](#crédits)

## Fonctionnalités

- **Chat IA** : répond aux mentions, aux réponses à ses messages, quand on écrit « Brabus » et en MP ; réponse affichée en direct pendant la génération ; mémoire par salon (SQLite) ; il regarde les images jointes.
- **Spontané** : répond aux simples « Bonjour » sans appel à l'IA et pose de petites réactions emoji aléatoires.
- **Personnalité réglable** : style, dialecte (Guadeloupe ou Martinique) et intensité des piques, par serveur.
- **Pour rire** : goulag avec casier et corvées, coup de ceinture, tribunal, clash de punchlines, exorcisme, adoption.
- **Jeux** : ki-moun (devine le personnage), imposteur (bluff avec mots secrets), sondage-chaos, tous annulables avec `/annuler`.
- **Musique** : du shatta trouvé en direct sur Deezer.
- **Mode silence** : le bot arrête les réactions et réponses spontanées quand tu veux du calme.
- **Salons interdits** : salons, forums ou catégories où Brabus se tait complètement.
- **Auto-diagnostic** : `/test` vérifie que tout fonctionne en quelques secondes.
- Pas de modération intégrée.

## Prérequis

- Python **3.10 ou plus**
- Un bot Discord (token) créé sur le [Developer Portal](https://discord.com/developers/applications)
- Une clé API [Groq](https://console.groq.com) (gratuite)

Dépendances Python utilisées par `bot.py` :

```
discord.py
openai
python-dotenv
aiohttp
```

> Le client `openai` sert uniquement à parler à Groq, qui propose une API compatible.

## Installation

```bash
# 1. Récupérer le projet
git clone https://github.com/MrMisteur/Bot-IA-Discord-Brabus.git
cd Bot-IA-Discord-Brabus

# 2. (Recommandé) environnement virtuel
python -m venv .venv
source .venv/bin/activate        # Windows : .venv\Scripts\activate

# 3. Installer les dépendances
pip install -r requirements.txt

# 4. Créer le fichier de configuration
cp .env.example .env             # puis édite-le (voir ci-dessous)

# 5. Lancer
python bot.py
```

Dans `requirements.txt`, mets **une dépendance par ligne**, sans `pip install` devant, sinon pip rejette tout le fichier.

Au premier démarrage, les commandes slash se synchronisent automatiquement. Elles ne sont resynchronisées ensuite que si la liste des commandes change.

## Configuration (`.env`)

### Obligatoires

| Variable | Description |
| --- | --- |
| `DISCORD_TOKEN` | Token du bot Discord |
| `GROQ_API_KEY` | Clé API Groq |

### Optionnelles

| Variable | Défaut | Description |
| --- | --- | --- |
| `GROQ_MODEL` | `openai/gpt-oss-120b,openai/gpt-oss-20b` | Modèles texte (plusieurs possibles, séparés par des virgules : le premier disponible est utilisé) |
| `GROQ_VISION_MODEL` | `qwen/qwen3.8-27b,qwen/qwen3.6-27b` | Modèles pour les images |
| `SYSTEM_PROMPT` | personnalité intégrée | Remplace la personnalité de Brabus |
| `BOT_NICK` | `Brabus` | Pseudo du bot sur les serveurs (vide = ne rien changer) |
| `DARON_DB` | `daron.db` à côté du bot | Chemin de la base SQLite |
| `COOLDOWN_SECONDS` | `4` | Anti-spam par utilisateur |
| `DARON_REACT_CHANCE` | `0.02` | Probabilité d'une réaction emoji spontanée (`0` pour désactiver) |
| `NAME_TRIGGER` | `1` | `1` : répond aussi quand on écrit « Brabus » ; `0` : désactivé |
| `AUTO_GREET` | `1` | `1` : répond aux « Bonjour » sans mention ; `0` : désactivé |
| `GREET_COOLDOWN_SECONDS` | `30` | Délai entre deux salutations à la même personne |
| `CLASH_ROUNDS` | `3` | Punchlines par combattant |
| `CLASH_TURN_SECONDS` | `60` | Temps par tour de clash |
| `CLASH_VOTE_SECONDS` | `45` | Durée du vote du clash |
| `TRIBUNAL_VOTE_SECONDS` | `45` | Durée du vote du tribunal |
| `GOULAG_VOTE_SECONDS` | `30` | Durée du vote du goulag |
| `KIMOUN_CLUE_SECONDS` | `20` | Délai entre deux indices de ki-moun |
| `CHAOS_VOTE_SECONDS` | `40` | Durée du sondage-chaos |
| `IMPOSTEUR_LOBBY_SECONDS` | `60` | Durée du lobby d'imposteur |
| `IMPOSTEUR_VOTE_SECONDS` | `45` | Durée du vote d'imposteur |
| `ADOPTION_SECONDS` | `40` | Durée de l'adoption |
| `DEV_GUILD_ID` | *(vide)* | Synchronise les commandes instantanément sur un serveur de test |
| `SYNC_ON_START` | *(vide)* | `1` : force la resynchronisation des commandes à chaque démarrage |

Exemple minimal :

```env
DISCORD_TOKEN=ton_token_discord
GROQ_API_KEY=ta_cle_groq
```

> Les modèles Groq sont retirés régulièrement : consulte la [liste des modèles](https://console.groq.com/docs/models) si tu obtiens une erreur « modèle introuvable ».
>
> Le style, le dialecte et l'intensité ne passent **pas** par le `.env` : ils se règlent par serveur avec `/style`.

## Permissions et intents Discord

Dans le **Developer Portal → Bot → Privileged Gateway Intents**, active :

- **Message Content Intent** (obligatoire)

Pour l'invitation du bot, utilise les scopes `bot` et `applications.commands`. Donne-lui ces permissions : envoyer des messages, intégrer des liens, ajouter des réactions, voir les anciens messages, et modifier son pseudo (facultatif, pour afficher « Brabus »).

> Pour que `/resume` puisse lire l'historique et que `/test` passe au vert, vérifie que le bot a ces permissions dans les salons concernés.

## Commandes

Les commandes marquées *(modo)* ou *(admin)* sont réservées. `/aide` affiche tout ce que le bot sait faire, `/aideadmin` la partie réservée aux admins.

### 💬 IA et discussion

| Commande | Description |
| --- | --- |
| `/ask` | Pose une question au Tonton |
| `/resume` | Résume les derniers messages du salon |
| `/traduis` | Traduit un texte (avec l'accent du Tonton) |
| `/conseil [sujet]` | Un conseil de vie bourru |
| `/roast_image` | Le Tonton juge ta photo (chambre, bureau, plat…) |

### 🎭 Pour rire

| Commande | Description |
| --- | --- |
| `/goulag <membre> [motif]` | Casier, corvées et vote (pour rire) |
| `/coup_de_ceinture` | Menace 100 % théâtrale |
| `/tribunal <membre> [affaire]` | Brabus juge quelqu'un, le salon vote |
| `/clash <membre> [thème]` | Un vrai clash de punchlines écrit dans le salon, jugé par Brabus |
| `/exorcisme` | Chasse le démon du mauvais gameplay |
| `/adoption <membre> [détail]` | Cherche une famille virtuelle à un membre « abandonné » |

### 🎲 Jeux

| Commande | Description |
| --- | --- |
| `/ki-moun [catégorie]` | Devine le personnage grâce aux indices, plus tu trouves tôt, plus tu gagnes de points |
| `/imposteur [thème]` | Jeu de bluff : chacun reçoit un mot en privé, l'un d'eux a un mot différent |
| `/sondage-chaos` | Sondage absurde entre membres |
| `/annuler` | Annule le jeu en cours dans le salon (celui qui l'a lancé ou un modérateur) |

### 🎶 Musique

| Commande | Description |
| --- | --- |
| `/shatta` | Du shatta trouvé en direct sur Deezer |

### 🔇 Calme et salons

| Commande | Description |
| --- | --- |
| `/silence [action] [duree]` *(modo)* | Plus de réactions, de salutations ni de réponse au prénom ; la mention directe marche toujours. Sans option, bascule. Durée en minutes en option |
| `/salons interdire\|autoriser\|liste` *(admin)* | Salons, forums ou catégories où Brabus se tait complètement (messages, réactions et commandes) |
| `/reset` *(modo)* | Efface la mémoire du salon |

### 🛠️ Admin et outils

| Commande | Description |
| --- | --- |
| `/style` *(admin)* | Règle le style, le dialecte (Guadeloupe ou Martinique) et l'intensité des piques. Sans option : affiche les réglages |
| `/envoyer` *(admin)* | Fait parler Brabus dans un salon au choix, avec réponse possible à un message |
| `/stats` *(admin)* | Appels API du jour, membres les plus bavards, modèles en service |
| `/test` *(admin)* | Auto-diagnostic : base, Groq (texte, flux, JSON, vision), Deezer, permissions, commandes |
| `/aideadmin` *(admin)* | Guide des commandes et réglages réservés aux admins |
| `/aide` | Toutes les commandes, expliquées |
| `/lien` | Lien GitHub du projet |
| `/ping` | Latence du bot |

> `/silence` demande la permission « Gérer les messages », `/salons`, `/style`, `/stats`, `/envoyer` et `/test` celle d'administrateur.

## Données et sauvegarde

Tout est stocké dans un fichier SQLite (`daron.db` par défaut, modifiable via `DARON_DB`) : mémoire des salons, compteurs d'appels pour `/stats`, casier du goulag, réglages par serveur, salons interdits et mode silence. Les tables sont créées automatiquement, y compris sur une base existante : une mise à jour ne supprime rien.

Pour sauvegarder : arrête le bot et copie `daron.db` (ainsi que les éventuels fichiers `daron.db-wal` et `daron.db-shm`). Si ton hébergeur efface les fichiers à chaque redémarrage, place la base sur un volume persistant.

## Dépannage

- **Le bot ne répond pas aux messages** : vérifie que le *Message Content Intent* est activé dans le Developer Portal.
- **Il ne démarre pas** : `DISCORD_TOKEN` et `GROQ_API_KEY` doivent être définis dans `.env`.
- **Les commandes n'apparaissent pas** : redémarre le bot avec `SYNC_ON_START=1` une fois, ou renseigne `DEV_GUILD_ID` pour une synchronisation instantanée sur ton serveur de test. En synchronisation globale, la propagation côté Discord peut prendre quelques minutes.
- **Erreur « modèle introuvable »** : les modèles Groq changent souvent, mets à jour `GROQ_MODEL` (ou `GROQ_VISION_MODEL` pour les images).
- **« Brabus est essoufflé »** : la limite de débit de Groq est atteinte, attends une minute.
- **`/roast_image` ou la lecture d'images échoue** : vérifie `GROQ_VISION_MODEL` et que l'image fait moins de 3 Mo (jpeg, png, gif ou webp, 3 images maximum).
- **Il ne répond plus dans un salon** : regarde `/salons liste` (salon ou catégorie interdits) et `/silence État` (mode silence actif).
- **« Un jeu est déjà en cours »** : lance `/annuler` dans le salon, ou attends la fin du jeu.
- **`/shatta` ne trouve rien** : Deezer peut être momentanément indisponible, réessaie plus tard ; `/test` indique l'état du service.
- **Le pseudo « Brabus » ne s'applique pas** : donne-lui la permission « Modifier son pseudo », ou mets `BOT_NICK=` vide pour ne rien changer.
- **Un doute sur l'installation** : lance `/test`, il repère en quelques secondes ce qui ne va pas.

## Crédits

- [discord.py](https://github.com/Rapptz/discord.py)
- [Groq](https://groq.com/) — IA
- [Deezer API](https://developers.deezer.com/api) — shatta en direct
- [Claude](https://claude.ai/new) — IA pour le code

Fait par [MrMisteur](https://github.com/MrMisteur) — *range ta case, ti moun.*
