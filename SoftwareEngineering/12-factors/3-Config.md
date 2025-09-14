# 3. Config

> Store config in the environment

An app’s config is everything that is likely to **vary** between deploys (**staging**, **production**, **developer environments**, etc).

The config includes:

- Resource handles to the database, Memcached, and other backing services
- Credentials to external services such as Amazon S3 or Twitter
- Per-deploy values such as the canonical hostname for the deploy

> [!IMPORTANT]
> Apps sometimes store config as **constants** in the code. This is a **violation of twelve-factor**, which **requires strict separation of config from code**.
> **Config varies substantially across deploys, code does not.**

Another approach is to store configs files. For example we could have `config/database.yml`.
This is good, also you can keep them out of track by revidion control, but still have some weaknesses. This is a huge improvement over using constants which are checked into the code repo, but it’s easy to **mistakenly check in a config file to the repo**; there is a **tendency for config files to be scattered about in different places and different formats**, making it **hard to see and manage all the config in one place**. Further, these formats tend to be language- or framework-specific.

The twelve-factor app stores config in environment variables (often shortened to env vars or env). **Env vars are easy to change between deploys without changing any code**; **unlike config files**, there is little chance of them being checked into the code repo accidentally; and unlike custom config files, or other config mechanisms such as Java System Properties, they are a language- and OS-agnostic standard.

> [!NOTE]
> In development, we do have some files like `.env`. These files are our configs because we are running application in development mode and it's hard to set a bunch of variables every time we open a new session terminal or after turning on the system.
> The key note is that application should read Envs from environment not from a file, in different stages, and we don't want to modify files in different stages, instead we want to set variables in different environments.

Grouping config into named environments (like development, test, or production) does not scale well, as more deploys and custom environments lead to complexity and brittleness. The twelve-factor approach treats environment variables as independent, granular controls, managed separately for each deploy. This method scales smoothly as the app grows and avoids the pitfalls of grouped config.
