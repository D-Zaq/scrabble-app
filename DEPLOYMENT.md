# Déploiement

Le déploiement se fait à travers une étape (_stage_) manuelle de la _CI_ qui est déclenché lorsqu'un **tag** contenant le mot clé **deploy** est rajouté sur git. Chaque tag est unique, mais vous pouvez utiliser des versions pour vos déploiements. Par exemple : **deploy_v1.0**, **deploy_v1.1**, etc.

Une fois que le tag est détecté par GitLab, un pipeline sera créé et vous verrez des options (_jobs_) pour le déploiement du client et du serveur. Ces _jobs_ doit être cependant lancés manuellement. Pour le faire, naviguez sur le menu de pipelines disponible dans `CI/CD > Pipelines`. Le pipeline aura le statut `blocked`. Cliquez dessus et lancez la _job_ que vous souhaitez.

**Attention : le pipeline de déploiement ne fait que déployer le site web et/ou server. Il n'effectue pas toutes les validations de l'intégration continue. Vous devez vous assurer que votre projet est fonctionnel et de qualité acceptable avant de le déployer.**

**Note importante : On vous conseille de tester le déploiement le plus tôt possible. Comme cela, si des complications surviennent, les chargés pourront vous aider à les résoudre le plus tôt possible. La veille de la remise du sprint n'est généralement pas un moment propice au débogage.**

# Plan et répartitions des tâches pour sur les sprints

-   La section [Déploiement du client](#déploiement-du-client) contient toutes les informations en rapport avec le déploiement du client. Ce déploiement doit être fonctionnel au SPRINT 1.

-   La section [Déploiement manuel du serveur](#déploiement-manuel-du-serveur) contient toutes les informations en rapport avec le déploiement du manuel du serveur. La procédure décrite à pour but de démystifier le déploiement d'un serveur distant. Cette procédure doit-être faite au complet au moins une fois par au moins un membre de l'équipe. Elle n'est pas corrigée, mais est obligatoire.

-   La section [Déploiement automatique du serveur](#déploiement-automatique-du-server) contient toutes les informations en rapport avec le déploiement automatique du serveur à l'aide du pipeline. Un prérequis de cette étape est d'avoir une instance en marche sur Amazon EC2. Ce déploiement doit être fonctionnel aux SPRINTS 2 et 3.

# Déploiement du client

Si la _job_ `pages` réussi, votre site web sera déployé sur GitLab Pages. Vous pouvez trouver l'URL du site dans le menu `Pages` disponible dans `Settings > Pages`.

Note pour les plus curieux : Les étapes pour le déploiement de la page statique se trouvent dans le fichier [.gitlab-ci.yml](.gitlab-ci.yml) sous la job _pages_. De façon très concise, cette _job_ minifie tout votre code et crée une page statique. Ensuite elle rend publique à partir de l'adresse GitLab pages associée les fichiers `html`, `css` et `js` générés.

# Déploiement manuel du serveur

À venir

# Déploiement automatique du server

Pour faire marcher le pipeline, 7 [variables](https://docs.gitlab.com/ee/ci/variables/) devront être définies : `EC2_HOST`, `EC2_PEM_FILE_CONTENT`, `EC2_USER`, `GITLAB_DEPLOY_TOKEN_PASSWORD`, `GITLAB_DEPLOY_TOKEN_USERNAME`, `GITLAB_REPO_URL` et `SERVER_PORT`. Toutes ces variables pourront être définies à partir de GitLab sur la page `Settings > CI/CD > Variables`. Toutes ces variables peuvent être masquées ou non (selon le niveau de confidentialité de l'information qu'elles détiennent) mais n'ont pas besoin d'être protégées.

#### EC2_HOST

Cette variable correspond à l'adresse de votre machine EC2 déployée. Vous y avez accès dans les détails de l'instance sous le nom de **Public IPv4 DNS**. Cette valeur doit avoir le schéma suivant : `ec2-<un nombre quelconque>.ca-central-1.compute.amazonaws.com`.

#### EC2_PEM_FILE_CONTENT

Cette variable correspond au fichier de permission `.pem` que vous aviez généré. Voici un exemple de fichier de permission :

```
-----BEGIN RSA PRIVATE KEY-----
MIIB9TCCAWACAQAwgbgxGTAXBgNVBAoMEFF1b1ZhZGlzIExpbWl0ZWQxHDAaBgNV
BAsME0RvY3VtZW50IERlcGFydG1lbnQxOTA3BgNVBAMMMFdoeSBhcmUgeW91IGRl
Y29kaW5nIG1lPyAgVGhpcyBpcyBvbmx5IGEgdGVzdCEhITERMA8GA1UEBwwISGFt
aWx0b24xETAPBgNVBAgMCFBlbWJyb2tlMQswCQYDVQQGEwJCTTEPMA0GCSqGSIb3
DQEJARYAMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQCJ9WRanG/fUvcfKiGl
EL4aRLjGt537mZ28UU9/3eiJeJznNSOuNLnF+hmabAu7H0LT4K7EdqfF+XUZW/2j
RKRYcvOUDGF9A7OjW7UfKk1In3+6QDCi7X34RE161jqoaJjrm/T18TOKcgkkhRzE
apQnIDm0Ea/HVzX/PiSOGuertwIDAQABMAsGCSqGSIb3DQEBBQOBgQBzMJdAV4QP
Awel8LzGx5uMOshezF/KfP67wJ93UW+N7zXY6AwPgoLj4Kjw+WtU684JL8Dtr9FX
ozakE+8p06BpxegR4BR3FMHf6p+0jQxUEAkAyb/mVgm66TyghDGC6/YkiKoZptXQ
98TwDIK/39WEB/V607As+KoYazQG8drorw==
-----END RSA PRIVATE KEY-----
```

L'intégralité du fichier devra être copiée dans la variable `EC2_PEM_FILE_CONTENT`.
Note : Étant un fichier à haut caractère confidentiel, on vous recommande fortement de garder cette variable masquée.

#### EC2_USER

Cette variable représente l'utilisateur auquel se connecter sur le serveur distant. Cette valeur doit valoir exactement `ec2-user`.

#### GITLAB_DEPLOY_TOKEN_USERNAME et GITLAB_DEPLOY_TOKEN_PASSWORD

Les [GitLab Deploy Tokens](https://docs.gitlab.com/ee/user/project/deploy_tokens/index.html) permettent d'avoir accès et contrôler votre répertoire et de façon sécuritaire sans avoir un nom d'utilisateur ou mot de passe.

Lire le [tutoriel suivant](https://docs.gitlab.com/ee/user/project/deploy_tokens/index.html) pour créer un token. Assurez de donner au moins les droits de lectures au token. Ensuite, ajouter les variables `GITLAB_DEPLOY_TOKEN_USERNAME` et `GITLAB_DEPLOY_TOKEN_PASSWORD` avec les noms d'utilisateurs et mots de passe qui vous seront fournis.

Note : Ces informations sont confidentielles, car quiconque qui à accès à ces valeurs peuvent lires et/ou controller votre répertoire. Il est donc recommandé de garder ces variables masquées.

#### GITLAB_REPO_URL

Cette variable représente l'adresse de votre répertoire git sous un format bien défini. Ce format est le suivant : `gitlab.com/polytechnique-montr-al/log2990/20213/equipe-<numéro d'équipe>/log2990-<numéro d'équipe>.git`.

Attention : Il est très important de respecter la forme le format à la lettre. N'ajouter pas de `https://` au début de l'URL par exemple.

#### SERVER_PORT

Cette variable représente le port sur lequel votre serveur opère. Donnez-y la valeur `3000`.
