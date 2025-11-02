# nanochat

![nanochat logo](dev/nanochat.png)

> Le meilleur ChatGPT que 100 $ peuvent acheter.

Ce dépôt est une implémentation complète d'un LLM comme ChatGPT dans une base de code unique, claire, minimale, modifiable et avec peu de dépendances. nanochat est conçu pour fonctionner sur un seul nœud 8XH100 via des scripts comme [speedrun.sh](speedrun.sh), qui exécutent l'intégralité du pipeline du début à la fin. Cela inclut la tokenisation, le pré-entraînement, l'ajustement fin, l'évaluation, l'inférence et le service web via une interface utilisateur simple pour que vous puissiez parler à votre propre LLM comme avec ChatGPT. nanochat deviendra le projet phare du cours LLM101n développé par Eureka Labs.

## Parlez-lui

Pour avoir une idée du résultat final de ce dépôt, vous pouvez actuellement trouver [nanochat d32](https://github.com/karpathy/nanochat/discussions/8) hébergé sur [nanochat.karpathy.ai](https://nanochat.karpathy.ai/). "d32" signifie que ce modèle possède 32 couches dans le réseau de neurones Transformer. Ce modèle compte 1,9 milliard de paramètres, il a été entraîné sur 38 milliards de tokens en exécutant simplement le script unique [run1000.sh](run1000.sh), et le coût total d'entraînement était d'environ 800 $ (environ 33 heures de temps d'entraînement sur un nœud GPU 8XH100). Bien qu'aujourd'hui cela suffise à surpasser GPT-2 de 2019, cela reste très en deçà des grands modèles de langage modernes comme GPT-5. En parlant à ces micro-modèles, vous verrez qu'ils font beaucoup d'erreurs, ils sont un peu naïfs et idiots et ils hallucinent énormément, un peu comme des enfants. C'est plutôt amusant. Mais ce qui rend nanochat unique, c'est qu'il est entièrement à vous - entièrement configurable, ajustable, modifiable et entraîné par vous du début à la fin. Pour entraîner et parler au vôtre, nous nous tournons vers...

## Démarrage rapide

Le moyen le plus rapide de ressentir la magie est d'exécuter le script speedrun [speedrun.sh](speedrun.sh), qui entraîne et effectue l'inférence du niveau 100 $ de nanochat. Sur un nœud 8XH100 à 24 $/h, cela donne un temps d'exécution total d'environ 4 heures. Démarrez une nouvelle machine GPU 8XH100 auprès de votre fournisseur préféré (par exemple, j'utilise et j'aime [Lambda](https://lambda.ai/service/gpu-cloud)), et lancez le script d'entraînement :

```bash
bash speedrun.sh
```

Alternativement, comme le script s'exécute pendant 4 heures, j'aime le lancer comme ceci dans une nouvelle session screen `speedrun` (et également enregistrer la sortie dans `speedrun.log`) :

```bash
screen -L -Logfile speedrun.log -S speedrun bash speedrun.sh
```

Consultez l'[aide-mémoire screen](https://gist.github.com/jctosta/af918e1618682638aa82) si vous êtes moins familier. Vous pouvez le regarder fonctionner dans la session screen, ou vous détacher avec `Ctrl-a d` et `tail speedrun.log` pour voir la progression. Maintenant, attendez 4 heures. Une fois terminé, vous pouvez parler à votre LLM via l'interface web semblable à ChatGPT. Assurez-vous à nouveau que votre environnement virtuel uv local est actif (exécutez `source .venv/bin/activate`), et servez-le :

```bash
python -m scripts.chat_web
```

Puis visitez l'URL affichée. Assurez-vous d'y accéder correctement, par exemple sur Lambda utilisez l'IP publique du nœud sur lequel vous êtes, suivie du port, donc par exemple [http://209.20.xxx.xxx:8000/](http://209.20.xxx.xxx:8000/), etc. Ensuite, parlez à votre LLM comme vous parleriez normalement à ChatGPT ! Demandez-lui d'écrire des histoires ou des poèmes. Demandez-lui de vous dire qui vous êtes pour voir une hallucination. Demandez-lui pourquoi le ciel est bleu. Ou pourquoi il est vert. Le speedrun est un modèle de capacité 4e19 FLOPs donc c'est un peu comme parler à un enfant de maternelle :).

---

<img width="2672" height="1520" alt="image" src="https://github.com/user-attachments/assets/ed39ddf8-2370-437a-bedc-0f39781e76b5" />

---

Vous pouvez également `cat report.md`, fichier qui est apparu dans le répertoire du projet et contient le "bulletin de notes" de l'exécution, c'est-à-dire un ensemble d'évaluations et de métriques. À la toute fin, vous verrez un tableau récapitulatif, par exemple :

---

- Caractères : 333 989
- Lignes : 8 304
- Fichiers : 44
- Tokens (approx) : 83 497
- Dépendances (lignes uv.lock) : 2 004

| Métrique        | BASE     | MID      | SFT      | RL       |
|-----------------|----------|----------|----------|----------|
| CORE            | 0.2219   | -        | -        | -        |
| ARC-Challenge   | -        | 0.2875   | 0.2807   | -        |
| ARC-Easy        | -        | 0.3561   | 0.3876   | -        |
| GSM8K           | -        | 0.0250   | 0.0455   | 0.0758   |
| HumanEval       | -        | 0.0671   | 0.0854   | -        |
| MMLU            | -        | 0.3111   | 0.3151   | -        |
| ChatCORE        | -        | 0.0730   | 0.0884   | -        |

Temps d'horloge total : 3h51m

---

(Votre tableau pourrait manquer le numéro RL par défaut). Pour beaucoup plus d'informations sur le script speedrun et ce qu'il faut rechercher et attendre, veuillez vous référer à la présentation que j'ai publiée dans les Discussions du dépôt : ["Introducing nanochat: The best ChatGPT that $100 can buy"](https://github.com/karpathy/nanochat/discussions/1).

## Modèles plus grands

Sans surprise, 100 $ ne suffisent pas pour entraîner un clone ChatGPT très performant. En fait, les LLM sont célèbres pour leurs dépenses d'investissement de plusieurs millions de dollars. Pour nos besoins, je pense qu'il y a deux autres échelles intéressantes. La première est le modèle d26 de niveau ~300 $ (c'est-à-dire depth=26) qui s'entraîne en ~12 heures, qui surpasse légèrement le score CORE de GPT-2. La seconde est le niveau 100 $ (~41,6 heures), juste parce que c'est un nombre rond agréable. Mais ces deux niveaux ne sont pas encore entièrement pris en charge et ne sont donc pas encore attachés ici dans la branche master.

Cela dit, pour donner une idée, les exemples de modifications nécessaires pour le fichier [speedrun.sh](speedrun.sh) pour entraîner un modèle d26 de niveau GPT-2 n'impliquent que trois changements :

```bash
...
# vous devrez télécharger plus de fragments de données pour le pré-entraînement
# obtenez le nombre de paramètres, multipliez par 20 pour obtenir les tokens, multipliez par 4,8 pour obtenir les caractères,
# divisez par 250 millions pour obtenir le nombre de fragments. todo besoin d'améliorer cela...
python -m nanochat.dataset -n 450 &
...
# utilisez --depth pour augmenter la taille du modèle. pour ne pas manquer de mémoire, divisez par deux la taille du lot de périphérique 32 -> 16 :
torchrun --standalone --nproc_per_node=8 -m scripts.base_train -- --depth=26 --device_batch_size=16
...
# assurez-vous d'utiliser le même plus tard pendant le midtraining :
torchrun --standalone --nproc_per_node=8 -m scripts.mid_train -- --device_batch_size=16
```

C'est tout ! La chose la plus importante à laquelle il faut prêter attention est de s'assurer que vous avez suffisamment de fragments de données pour vous entraîner (sinon le code bouclera et fera plus d'époques sur le même ensemble d'entraînement, diminuant un peu la vitesse d'apprentissage), et de gérer votre mémoire/VRAM, principalement en diminuant le `device_batch_size` jusqu'à ce que les choses rentrent (les scripts compensent automatiquement en augmentant le nombre de boucles d'accumulation de gradient, transformant simplement le calcul parallèle en calcul séquentiel).

Et un peu plus sur les environnements informatiques qui exécuteront nanochat :

- Le code fonctionnera très bien sur le nœud GPU Ampere 8XA100 également, mais un peu plus lentement.
- Tout le code fonctionnera très bien même sur un seul GPU en omettant `torchrun`, et produira des résultats ~identiques (le code basculera automatiquement vers l'accumulation de gradient), mais vous devrez attendre 8 fois plus longtemps.
- Si votre/vos GPU ont moins de 80 Go, vous devrez ajuster certains des hyperparamètres ou vous manquerez de mémoire / VRAM. Recherchez `--device_batch_size` dans les scripts et réduisez-le jusqu'à ce que les choses rentrent. Par exemple de 32 (par défaut) à 16, 8, 4, 2, ou même 1. Moins que cela, vous devrez en savoir un peu plus sur ce que vous faites et devenir plus créatif.
- La plupart du code est du PyTorch assez standard donc il devrait fonctionner sur tout ce qui le supporte - xpu, mps, ou etc, mais je ne l'ai pas implémenté prêt à l'emploi donc cela pourrait nécessiter un peu de bricolage.

## Exécution sur CPU / MPS

nanochat peut être exécuté sur CPU ou sur MPS (si vous êtes sur Macbook), et essaiera automatiquement de détecter quel périphérique est le meilleur pour l'exécution. Vous n'irez pas très loin sans GPU, mais au moins vous pourrez exécuter les chemins de code et peut-être entraîner un petit LLM avec un peu de patience. Pour un exemple de comment rendre toutes les commandes d'exécution beaucoup plus petites (n'hésitez pas à ajuster !), vous pouvez vous référer au fichier [dev/runcpu.sh](dev/runcpu.sh). Vous verrez que je restreins essentiellement tous les scripts pour entraîner des modèles plus petits, pour s'exécuter pendant un nombre d'itérations plus court, etc. Cette fonctionnalité est nouvelle, un peu complexe (a touché beaucoup de code), et a été fusionnée dans cette [CPU|MPS PR](https://github.com/karpathy/nanochat/pull/88) le 21 octobre 2025.

## Personnalisation

Pour personnaliser votre nanochat, consultez [Guide: infusing identity to your nanochat](https://github.com/karpathy/nanochat/discussions/139) dans les Discussions, qui décrit comment vous pouvez ajuster la personnalité de votre nanochat grâce à la génération de données synthétiques et au mélange de ces données dans les étapes de midtraining et SFT.

De plus, pour ajouter de nouvelles capacités à nanochat, consultez [Guide: counting r in strawberry (and how to add abilities generally)](https://github.com/karpathy/nanochat/discussions/164).

## Questions

nanochat est conçu pour être court et agréable. Un grand avantage de cela est que nous pouvons empaqueter tous les fichiers ensemble et les copier-coller dans votre LLM préféré pour poser des questions arbitraires. Par exemple, j'aime empaqueter le dépôt en utilisant l'utilitaire [files-to-prompt](https://github.com/simonw/files-to-prompt) comme ceci :

```bash
files-to-prompt . -e py -e md -e rs -e html -e toml -e sh --ignore "*target*" --cxml > packaged.txt
```

Cela inclut tous les fichiers py, rs, html, toml, sh, exclut le dossier `rustbpe/target`, et choisit le format de sortie cxml. Tout est écrit dans le fichier `packaged.txt`, qui mesure actuellement ~330 Ko (c'est-à-dire bien en dessous de ~100 K tokens pour un LLM de pointe), et ~8 K lignes de code dans 45 fichiers.

Alternativement, je recommande d'utiliser [DeepWiki](https://deepwiki.com/karpathy/nanochat) de Devin/Cognition pour poser des questions sur ce dépôt. Dans l'URL de ce dépôt, changez simplement github.com en deepwiki.com, et c'est parti.

## Tests

Je n'ai pas trop investi ici mais quelques tests existent, en particulier pour le tokenizer. Exécutez par exemple comme :

```bash
python -m pytest tests/test_rustbpe.py -v -s
```

## Structure des fichiers

```
.
├── LICENSE
├── README.md
├── dev
│   ├── gen_synthetic_data.py       # Exemple de données synthétiques pour l'identité
│   ├── generate_logo.html
│   ├── nanochat.png
│   ├── repackage_data_reference.py # Génération de fragments de données de pré-entraînement
│   └── runcpu.sh                   # Petit exemple de comment exécuter sur CPU/MPS
├── nanochat
│   ├── __init__.py                 # vide
│   ├── adamw.py                    # Optimiseur AdamW distribué
│   ├── checkpoint_manager.py       # Sauvegarde/Chargement des points de contrôle de modèle
│   ├── common.py                   # Divers petits utilitaires, qualité de vie
│   ├── configurator.py             # Une alternative supérieure à argparse
│   ├── core_eval.py                # Évalue le score CORE du modèle de base (article DCLM)
│   ├── dataloader.py               # Chargeur de données distribué avec tokenisation
│   ├── dataset.py                  # Utilitaires de téléchargement/lecture pour les données de pré-entraînement
│   ├── engine.py                   # Inférence de modèle efficace avec KV Cache
│   ├── execution.py                # Permet au LLM d'exécuter du code Python comme outil
│   ├── gpt.py                      # Le nn.Module Transformer GPT
│   ├── logo.svg
│   ├── loss_eval.py                # Évalue les bits par octet (au lieu de la perte)
│   ├── muon.py                     # Optimiseur Muon distribué
│   ├── report.py                   # Utilitaires pour écrire le rapport nanochat
│   ├── tokenizer.py                # Wrapper de tokenizer BPE dans le style de GPT-4
│   └── ui.html                     # HTML/CSS/JS pour l'interface nanochat
├── pyproject.toml
├── run1000.sh                      # Entraîne le nanochat d32 ~800 $
├── rustbpe                         # Entraîneur de tokenizer BPE Rust personnalisé
│   ├── Cargo.lock
│   ├── Cargo.toml
│   ├── README.md                   # voir pourquoi cela existe même
│   └── src
│       └── lib.rs
├── scripts
│   ├── base_eval.py                # Modèle de base : calculer le score CORE
│   ├── base_loss.py                # Modèle de base : calculer les bits par octet, échantillon
│   ├── base_train.py               # Modèle de base : entraîner
│   ├── chat_cli.py                 # Modèle de chat (SFT/Mid) : parler via CLI
│   ├── chat_eval.py                # Modèle de chat (SFT/Mid) : tâches d'évaluation
│   ├── chat_rl.py                  # Modèle de chat (SFT/Mid) : apprentissage par renforcement
│   ├── chat_sft.py                 # Modèle de chat : entraîner SFT
│   ├── chat_web.py                 # Modèle de chat (SFT/Mid) : parler via WebUI
│   ├── mid_train.py                # Modèle de chat : midtraining
│   ├── tok_eval.py                 # Tokenizer : évaluer le taux de compression
│   └── tok_train.py                # Tokenizer : l'entraîner
├── speedrun.sh                     # Entraîne le nanochat d20 ~100 $
├── tasks
│   ├── arc.py                      # Questions scientifiques à choix multiples
│   ├── common.py                   # TaskMixture | TaskSequence
│   ├── customjson.py               # Créer une tâche à partir de conversations jsonl arbitraires
│   ├── gsm8k.py                    # 8K questions de mathématiques de l'école primaire
│   ├── humaneval.py                # Nom impropre ; Tâche de codage Python simple
│   ├── mmlu.py                     # Questions à choix multiples, sujets variés
│   ├── smoltalk.py                 # Ensemble de données conglomérat de SmolTalk de HF
│   └── spellingbee.py              # Tâche enseignant au modèle à épeler/compter les lettres
├── tests
│   └── test_rustbpe.py
└── uv.lock
```

## Contribution

nanochat est loin d'être terminé. L'objectif est d'améliorer l'état de l'art en matière de micro-modèles accessibles pour travailler de bout en bout avec des budgets de < 1000 dollars. L'accessibilité concerne le coût global mais aussi la complexité cognitive - nanochat n'est pas un "framework" LLM exhaustivement configurable ; il n'y aura pas d'objets de configuration géants, de fabriques de modèles ou de monstres if-then-else dans la base de code. C'est une base de code unique, cohérente, minimale, lisible, modifiable, maximalement forkable "base de référence solide" conçue pour s'exécuter du début à la fin et produire un clone ChatGPT concret et son bulletin de notes.

Politique LLM actuelle : divulgation. Lors de la soumission d'une PR, veuillez déclarer toutes les parties qui ont eu une contribution substantielle de LLM et que vous n'avez pas écrites ou que vous ne comprenez pas entièrement.

## Remerciements

- Le nom (nanochat) dérive de mon projet antérieur [nanoGPT](https://github.com/karpathy/nanoGPT), qui ne couvrait que le pré-entraînement.
- nanochat est également inspiré par [modded-nanoGPT](https://github.com/KellerJordan/modded-nanogpt), qui a gamifié le dépôt nanoGPT avec des métriques claires et un classement, et emprunte beaucoup de ses idées et une partie de son implémentation pour le pré-entraînement.
- Merci à [HuggingFace](https://huggingface.co/) pour fineweb et smoltalk.
- Merci [Lambda](https://lambda.ai/service/gpu-cloud) pour le calcul utilisé dans le développement de ce projet.
- Merci au chef chuchoteur de LLM 🧙‍♂️ Alec Radford pour ses conseils/orientations.

## Citation

Si vous trouvez nanochat utile dans votre recherche, citez simplement comme :

```bibtex
@misc{nanochat,
  author = {Andrej Karpathy},
  title = {nanochat: The best ChatGPT that $100 can buy},
  year = {2025},
  publisher = {GitHub},
  url = {https://github.com/karpathy/nanochat}
}
```

## Licence

MIT