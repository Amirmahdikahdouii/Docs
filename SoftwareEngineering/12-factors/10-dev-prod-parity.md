# Dev/Prod parity

> Keep development, staging, and production as similar as possible

Historically, there was a gap between `development` (a developer making live edits to a local deploy of the app) and `production` (a running deploy of the app accessed by end user). These gaps manifest in three area.

1. `The time gap`: A developer may work on code that takes time (days, weeks or months) to go to production.
2. `The personal gap`: Developers write code, ops engineers deploy it.
3. `The tools gap`: Developers may be using a stack like Nginx, Sqlite or OS X, while in production there are something else.

**The 12 factor app is designed for continuous deployment by keeping the gap between development and production small**.

1. `The time gap`: a developer may write code and have it deployed hours or even just minutes later.
2. `The personal gap`: developers who wrote code are closely involved in deploying it and watching its behavior in production.
3. `The tools gap`: keep development and production as similar as possible.

> [!IMPORTANT]
> **Backing services**, such as the app's `database`, `queueing system`, or `cache`, is one area **where dev/prod parity is important**.
> Many languages offer libraries which simplify access to the backing service, including *adapters* to different types of services.

> [!CAUTION]
> The `12 factor app` do not suggests to use different backing services in **development** and **production**.

Differences between **backing services** mean that tiny incompatibilities crop up, **causing code that worked and passed tests in development or staging to fail in production**. These types of errors create **friction that disincentivizes** continuous deployment.

> [!NOTE]
> Lightweight local services are less compelling than they once were. Modern backing services such as Memcached, PostgreSQL, and RabbitMQ are not difficult to install and run thanks to modern packaging systems, such as `Homebrew` and `apt-get`. Alternatively, declarative provisioning tools such as `Chef` and `Puppet` combined with light-weight virtual environments such as `Docker` and `Vagrant` allow developers to run local environments which closely approximate production environments. **The cost of installing and using these systems is low compared to the benefit of dev/prod parity and continuous deployment.**

Adapters to different backing services are still useful, because they make porting to new backing services relatively painless. But all deploys of the app (developer environments, staging, production) should be using the same type and version of each of the backing services.
