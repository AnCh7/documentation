# Terraform Cloud and Terraform Enterprise

> https://www.terraform.io/docs/cloud/index.html

# TODO...



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

### Creating Workspaces

Workspaces organize infrastructure into meaningful groups. Create new workspaces whenever you need to manage a new collection of  infrastructure resources.

Each new workspace needs a unique name (90 characters or less) and can include letters, numbers, dashes (`-`), and underscores (`_`).  A good strategy to start with is `<COMPONENT>-<ENVIRONMENT>-<REGION>`. 

When you create a new workspace:

- Terraform Cloud doesn't immediately queue a plan for the  workspace. Instead, it presents a dialog with shortcut links to either  queue a plan or edit variables.
- If you connected a VCS repository to the workspace, Terraform  Cloud automatically registers a webhook with your VCS provider.

### Terraform Configurations in Terraform Cloud Workspaces 

Each Terraform Cloud workspace is associated with a particular [Terraform configuration](https://www.terraform.io/docs/language/index.html), which is expected to change and evolve over time.

There are two ways to provide configuration versions for a workspace:

- With a connected VCS repository.
- With direct uploads - CLI or API.

### Code Organization and Repository Structure 

Most organizations either keep each Terraform configuration in a  separate repository, or keep many Terraform configurations as separate  directories in a single repository (often called a "monorepo").

Terraform Cloud works well with either approach, but monorepos require some extra configuration.

### Variables

Terraform Cloud workspaces can set values for two kinds of variables:

- [Terraform input variables](https://www.terraform.io/docs/language/values/variables.html), which define the parameters of a Terraform configuration.
- Shell environment variables.

If a workspace is configured to use Terraform 0.10.0 or later, you can commit any number of [`*.auto.tfvars` files](https://www.terraform.io/docs/language/values/variables.html#variable-files) to provide default variable values. Terraform will automatically load variables from those files.

Marking a variable as sensitive prevents anybody (including you) from  viewing its value in the variables section of the workspace in Terraform Cloud's UI or with its Variables API endpoint.

Terraform Cloud passes variables to Terraform by writing a `terraform.tfvars` file and passing the `-var-file=terraform.tfvars` option to the Terraform command.Do not commit a file named `terraform.tfvars` to version control, since Terraform Cloud will overwrite it. 

Terraform Cloud performs Terraform runs on disposable Linux worker VMs  using a POSIX-compatible shell. Before running Terraform, Terraform  Cloud populates the shell with environment variables using the `export` command.

Terraform Cloud encrypts all variable values securely using [Vault's transit backend](https://www.vaultproject.io/docs/secrets/transit/index.html) prior to saving them.

### Workspace Settings

- "General", for basic configuration.
- "Locking", for temporarily preventing new plans and applies.
- "Notifications", for configuring run notifications.
- "Run Triggers", for configuring run triggers.
- "SSH Key", for configurations that use Git-based module sources.
- "Team Access," for managing workspace permissions.
- "Version Control", for managing the workspace's VCS integration.
- "Destruction and Deletion", for removing a workspace and the infrastructure it manages.

##### Execution Mode

The default value is "Remote", which instructs Terraform Cloud to  perform Terraform runs on its own disposable virtual machines. This  provides a consistent and reliable run environment, and enables advanced features like Sentinel policy enforcement, cost estimation,  notifications, version control integration, and more.

To disable remote execution for a workspace, change its execution mode  to "Local". The workspace will store state, which Terraform can access  using the [remote backend](https://www.terraform.io/docs/language/settings/backends/remote.html).

---

Whether or not Terraform Cloud should automatically apply a successful  Terraform plan. If you choose manual apply, an operator must confirm a  successful plan and choose to apply it.

---

##### Locking 

If you need to prevent Terraform runs for any reason, you can lock a  workspace. This prevents users with permission to queue plans from  manually queueing runs, prevents automatic runs due to changes to the  backing VCS repo, and prevents the creation of runs via the API. To  enable runs again, a user must unlock the workspace.

Locking a workspace also restricts state uploads. In order to upload  state, the workspace must be locked by the user who is uploading state.

Users with permission to lock and unlock a workspace can't unlock a  workspace which was locked by another user. Users with admin access to a workspace can force unlock a workspace even if another user has locked  it.

##### Configuring Workspace VCS Connections

Any Terraform Cloud workspace can be connected to a version control  system (VCS) repository that contains its Terraform configuration.

For workspaces that specify a Terraform working directory, Terraform  Cloud assumes that only some content in the repository is relevant to  the workspace. Only changes that affect the relevant content will  trigger a run. By default, only the designated working directory is considered relevant.

##### Managing Access to Workspaces 

Terraform Cloud workspaces can only be accessed by users with the  correct permissions. You can manage permissions for a workspace on a  per-team basis.

- [Workspace-level permissions](https://www.terraform.io/docs/cloud/users-teams-organizations/permissions.html#workspace-permissions) can be granted to an individual team on a particular workspace.
- In addition, some [organization-level permissions](https://www.terraform.io/docs/cloud/users-teams-organizations/permissions.html#organization-permissions) can be granted to a team which apply to every workspace in the organization. Organization-level permissions can only be managed by  organization owners.

### Run Notifications

Terraform Cloud can use webhooks to notify external systems about the progress of runs.

Each workspace has its own notification settings, and can notify up to 20 destinations.

The destination URL must accept HTTP or HTTPS `POST` requests, and should be able to do something useful with the chosen payload type.

For a verification to be successful, the destination must respond with a `2xx` HTTP code.

Terraform Cloud can deliver either a `generic` payload, a payload formatted specifically for `Slack`, or an `Email`. 

##### Notification Authenticity

Generic notifications can include a signature for verifying the  request. For notification configurations that include a secret token,  Terraform Cloud's webhook requests will include an `X-TFE-Notification-Signature` header, which contains an HMAC signature computed from the token using  the SHA-512 digest algorithm.

### Using SSH Keys for Cloning Modules 

Terraform configurations can pull in Terraform modules from [a variety of different sources](https://www.terraform.io/docs/language/modules/sources.html), and private Git repositories are a common source for private modules. 

To access a private Git repository, Terraform either needs login  credentials (for HTTPS access) or an SSH key. Terraform Cloud can store  private SSH keys centrally, and you can easily use them in any workspace that clones modules from a Git server.

### Run Triggers

Terraform Cloud provides a way to connect your workspace to one or more  workspaces within your organization, known as "source workspaces". These connections, called run triggers, allow runs to queue automatically in  your workspace on successful apply of runs in any of the source  workspaces. You can connect your workspace to up to 20 source  workspaces.

### Terraform State in Terraform Cloud

In [remote runs](https://www.terraform.io/docs/cloud/run/index.html), Terraform Cloud automatically configures Terraform to use the  workspace's state; the Terraform configuration does not need an explicit backend configuration. 

In local runs, you can use a workspace's state by configuring [the `remote` backend](https://www.terraform.io/docs/language/settings/backends/remote.html) and authenticating with a user token that has permission to read and write state versions for the relevant workspace. 

Terraform Cloud retains historical state versions, which can be used to analyze infrastructure changes over time.

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

















