# Terraform Cloud and Terraform Enterprise

> https://www.terraform.io/docs/cloud/index.html









# Workspaces

Workspaces are how Terraform Cloud organizes infrastructure. Separate workspaces function like completely separate working directories.

| Component               | Local Terraform                                              | Terraform Cloud                                              |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Terraform configuration | On disk                                                      | In linked version control repository, or periodically uploaded via API/CLI |
| Variable values         | As `.tfvars` files, as CLI arguments, or in shell environment | In workspace                                                 |
| State                   | On disk or in remote backend                                 | In workspace                                                 |
| Credentials and secrets | In shell environment or entered at prompts                   | In workspace, stored as sensitive variables                  |

Terraform Cloud keeps some additional data for each workspace:

- **State versions:** Each workspace retains backups of  its previous state files.
- **Run history:** When Terraform Cloud manages a  workspace's Terraform runs, it retains a record of all run activity,  including summaries, logs, a reference to the changes that caused the  run, and user comments.

### Terraform Runs

For workspaces with [remote operations](https://www.terraform.io/docs/cloud/run/index.html) enabled (the default), Terraform Cloud performs Terraform runs on its  own disposable virtual machines, using that workspace's configuration,  variables, and state.











# Run Notifications

Terraform Cloud can use webhooks to notify external systems about the progress of runs.

Each workspace has its own notification settings, and can notify up to 20 destinations.

The destination URL must accept HTTP or HTTPS `POST` requests, and should be able to do something useful with the chosen payload type.

For a verification to be successful, the destination must respond with a `2xx` HTTP code.







# Terraform Runs and Remote Operations

Terraform Cloud is designed as an execution platform for Terraform, and  can perform Terraform runs on its own disposable virtual machines. 

Terraform runs managed by Terraform Cloud are called *remote operations.* Remote runs can be initiated by webhooks from your VCS provider, by UI  controls within Terraform Cloud, by API calls, or by Terraform CLI. 

Terraform Cloud always performs Terraform runs in the context of a [workspace](https://www.terraform.io/docs/cloud/run/index.html). The workspace serves the same role that a persistent working directory  serves when running Terraform locally: it provides the configuration,  state, and variables for the run.

Terraform Cloud manages configurations as a series of *configuration versions.*

Each workspace in Terraform Cloud maintains its own queue of runs, and processes those runs in order.

---

Starting runs:

- The [UI/VCS-driven run workflow](https://www.terraform.io/docs/cloud/run/ui.html), which is the primary mode of operation.
- The [API-driven run workflow](https://www.terraform.io/docs/cloud/run/api.html), which is more flexible but requires you to create some tooling.
- The [CLI-driven run workflow](https://www.terraform.io/docs/cloud/run/cli.html), which uses Terraform's standard CLI tools to execute runs in Terraform Cloud.

---

Speculative plans are plan-only runs: they show a set of possible  changes, but cannot apply  those changes. They can begin at any time without waiting for other  runs, since they don't affect real infrastructure. Speculative plans do not appear in a workspace's list of runs; viewing them requires a direct link, which is provided when the plan is initiated.

















