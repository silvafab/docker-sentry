# Sentry

This project is based off the [official Docker image][sentry-docker] and serves 
as an on-premise Sentry installation on Aptible's platform.

Please refer to the [official docs][sentry-onpremise] for details on the 
configuration and setup instructions.

## Setup

The following env vars are used by sentry:

| Env                  | Description                                                                      |
| ---                  | ---                                                                              |
| `FORCE_SSL`          | Aptible-specific, enables SSL termination.                                       |
| `GITHUB_API_SECRET`  | GitHub plugin settings. API secret from OAuth App.                               |
| `GITHUB_APP_ID`      | GitHub plugin settings. OAuth App id.                                            |
| `SENTRY_SECRET_KEY`  | Sentry secret sessions key. Generated via `$ sentry config generate-secret-key`. |
| `SENTRY_ENVIRONMENT` | Environment name for installation, `production` is recommended.                  |


Additionaly, the Postgres connection requires `SENTRY_DB_NAME`, 
`SENTRY_DB_PASSWORD`, `SENTRY_DB_USER`, `SENTRY_POSTGRES_HOST` and
`SENTRY_POSTGRES_PORT` to be set.

The Redis connection uses all of the following envs: `SENTRY_REDIS_HOST`, 
`SENTRY_REDIS_PASSWORD` and `SENTRY_REDIS_PORT`.


### Outbound email

The following env vars are required for transactional email:

| Env                     | Value                 |
| ---                     | ---                   |
| `SENTRY_EMAIL_USE_TLS`  | `true`                |
| `SENTRY_SMTP_HOSTNAME`  | `smtp.mailgun.org`    |
| `SENTRY_EMAIL_USER`     | Mailgun SMTP username |
| `SENTRY_EMAIL_PASSWORD` | Mailgun SMTP password |
| `SENTRY_EMAIL_PORT`     | `465`                 |

For details, check out [Mailgun's SMTP docs][mailgun-smtp].


## Deploying

Use Aptible's [Docker deployments][aptible-docker-deployments]:

```bash
aptible deploy --app sentry --docker-image sentry:$TAG
```


## Project settings

Following is a brief description of the setup required for new projects on the 
on-premise deployment.

**Don't forget to Save your changes!**


### General

Under **Event Settings**, set **Default Environment** to `production` and 
**Auto resolve** threshold to `30 days`.


### Integrations

Enable Sentry, PagerDuty and GitHub integrations.

1. Enter GitHub repo uri as `pagerinc/$REPONAME`.
2. Input PagerDuty's _Service Key_.
3. Enter Slack's _Webhook URL_, _Icon URL_ and _Destination_ (`#mayday`). 
Check _Include Tags_ and _Include Rules_.


### Alerts

The following table contains a description of the recommended default ruleset.
If in doubt, copy the rules over from any of the other projects.

| Rule                                | Description                | Settings                                                                                           |
| ---                                 | ---                        | ---                                                                                                |
| Send a notification for new events  | Slack alert on new issues  | An event is first seen                                                                             |
| Send a notification for regressions | Slack alert on regressions | An event changes state from resolved to unresolved                                                 |
| High error rate                     | Triggers a PagerDuty event | An event is seen more than 5 times in one hour and its `environment` attribute equals `production` |


### Repositories

Go to _Manage_ -> _Repositories_ and add `pagerinc/$REPO` so we can better tag releases.



[sentry-docker]: https://hub.docker.com/_/sentry/ 
[sentry-onpremise]: https://docs.sentry.io/server/installation/
[mailgun-smtp]: https://help.mailgun.com/hc/en-us/articles/203380100-Where-can-I-find-my-API-key-and-SMTP-credentials-
[aptible-docker-deployments]: https://www.aptible.com/changelog/introducing-direct-docker-image-deploy/
