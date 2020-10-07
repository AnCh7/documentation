#### Recreate

Stop all the running application instances and then spin up the instances with the new version.

#### Ramped deployments (Rolling update)

Instances running the old version get retired as new instances with the new version get spun up.

Applicable when your service is horizontally scaled (more than one instance)

In Kubernetes:

- Set `.spec.strategy.type` to `RollingUpdate` (the default value).
- Set `.spec.strategy.rollingUpdate.maxUnavailable` and `.spec.strategy.rollingUpdate.maxSurge` to some reasonable value.
- Configure the `readinessProbe`.

#### Blue/Green deployments

We always manage 2 versions of our production environment. One of them  is considered **blue** — i.e this is the version that is now live. The new versions are always deployed to the **green replica** of the environment. After we run the necessary tests and verifications to make sure the **blue** environment is ready we just flip over the traffic, so **green** becomes **blue** and **blue** becomes **green**.

In Kubernetes it can be done via labels, for example:

```yaml
labels:
	app: guestbook
  tier: frontend
  track: canary
```

It's not recommended to use Helm for blue/green deployment.

#### Canary releases

A small portion of the fleet is updated to the new version of your application.

In Kubernetes it can be done via labels, for example:

```yaml
labels:
	app: guestbook
  tier: frontend
  track: canary
```

It's not recommended to use Helm for canary deployment.