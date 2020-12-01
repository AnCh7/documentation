## What is Terraform? 

Terraform is a tool for building, changing, and versioning infrastructure safely and efficiently. Terraform can manage existing and popular service providers as well as custom in-house solutions.

Configuration files describe to Terraform the components needed to run a single application or your entire datacenter. Terraform generates an execution plan describing what it will do to reach the desired state, and then executes it to build the described infrastructure. As the configuration changes, Terraform is able to determine what changed and create incremental execution plans which can be applied.

The infrastructure Terraform can manage includes low-level components such as compute instances, storage, and networking, as well as high-level components such as DNS entries, SaaS features, etc.

#### Infrastructure as Code

Infrastructure is described using a high-level configuration syntax. This allows a blueprint of your datacenter to be versioned and treated as you would any other code. Additionally, infrastructure can be shared and re-used.

#### Execution Plans

Terraform has a "planning" step where it generates an execution plan. The execution plan shows what Terraform will do when you call apply.

#### Resource Graph

Terraform builds a graph of all your resources, and parallelizes the creation and modification of any non-dependent resources. Because of this, Terraform builds infrastructure as efficiently as possible, and operators get insight into dependencies in their infrastructure.


## Terraform Configuration Language

The main purpose of the Terraform language is declaring [resources](https://www.terraform.io/docs/configuration/resources.html). Terraform's configuration language is based on a more general language called HCL.

A group of resources can be gathered into a [module](https://www.terraform.io/docs/configuration/modules.html), which creates a larger unit of configuration. A resource describes a single infrastructure object, while a module might describe a set of objects and the necessary relationships between them in order to create a higher-level system.

A Terraform configuration consists of a root module, where evaluation begins, along with a tree of child modules created when one module calls another.

```yaml
resource "aws_vpc" "main" {
  cidr_block = var.base_cidr_block
}

<BLOCK TYPE> "<BLOCK LABEL>" "<BLOCK LABEL>" {
  # Block body
  <IDENTIFIER> = <EXPRESSION> # Argument
}
```

- *Blocks* are containers for other content and usually represent the configuration of some kind of object, like a resource. Blocks have a *block type,* can have zero or more *labels,* and have a *body* that contains any number of arguments and nested blocks. Most of Terraform's features are controlled by top-level blocks in a configuration file. 
- *Arguments* assign a value to a name. They appear within blocks. 
- *Expressions* represent a value, either literally or by referencing and combining other values. They appear as values for arguments, or within other expressions. 

Identifiers can contain letters, digits, underscores (`_`), and hyphens (`-`). The first character of an identifier must not be a digit, to avoid ambiguity with literal numbers.

The Terraform language uses configuration files that are named with the `.tf` file extension. There is also [a JSON-based variant of the language](https://www.terraform.io/docs/configuration/syntax-json.html) that is named with the `.tf.json` file extension.

A *module* is a collection of `.tf` or `.tf.json` files kept together in a directory.

Because Terraform's configuration language is declarative, the ordering of blocks is generally not significant. 

Terraform uses plugins called [providers](https://www.terraform.io/docs/configuration/providers.html) that each define and manage a set of resource types. Most providers are associated with a particular cloud or on-premises infrastructure service, allowing Terraform to manage infrastructure objects within that service.

### Resources

```yaml
resource "aws_instance" "web" {
  ami           = "ami-a1b2c3d4"
  instance_type = "t2.micro"
}
```

A `resource` block declares a resource of a given type ("aws_instance") with a given local name ("web"). The name is used to refer to this resource from elsewhere in the same Terraform module, but has no significance outside of the scope of a module.

Within the block body (between `{` and `}`) are the configuration arguments for the resource itself. 

Each resource type in turn belongs to a [provider](https://www.terraform.io/docs/configuration/providers.html), which is a plugin for Terraform that offers a collection of resource types. A provider usually provides resources to manage a single cloud or on-premises infrastructure platform.

Terraform CLI defines the following meta-arguments, which can be used with any resource type to change the behavior of resources:

- [`depends_on`, for specifying hidden dependencies](https://www.terraform.io/docs/configuration/resources.html#depends_on-explicit-resource-dependencies) 
- [`count`, for creating multiple resource instances according to a count](https://www.terraform.io/docs/configuration/resources.html#count-multiple-resource-instances-by-count) 
- [`for_each`, to create multiple instances according to a map, or set of strings](https://www.terraform.io/docs/configuration/resources.html#for_each-multiple-resource-instances-defined-by-a-map-or-set-of-strings) 
- [`provider`, for selecting a non-default provider configuration](https://www.terraform.io/docs/configuration/resources.html#provider-selecting-a-non-default-provider-configuration) 
- [`lifecycle`, for lifecycle customizations](https://www.terraform.io/docs/configuration/resources.html#lifecycle-lifecycle-customizations) (create_before_destroy, prevent_destroy, ignore_changes)
- [`provisioner` and `connection`, for taking extra actions after resource creation](https://www.terraform.io/docs/configuration/resources.html#provisioner-and-connection-resource-provisioners) 

Local-only resource types exist for [generating private keys](https://www.terraform.io/docs/providers/tls/r/private_key.html), [issuing self-signed TLS certificates](https://www.terraform.io/docs/providers/tls/r/self_signed_cert.html), and even [generating random ids](https://www.terraform.io/docs/providers/random/r/id.html).

Some resource types provide a special `timeouts` nested block argument that allows you to customize how long certain operations are allowed to take before being considered to have failed. 

### Providers

```yaml
provider "google" {
  project = "acme-app"
  region  = "us-central1"
}
```

Terraform associates each resource type with a provider by taking the first word of the resource type name (separated by underscores), and so the "google" provider is assumed to be the provider for the resource type name `google_compute_instance`.

There are also two "meta-arguments" that are defined by Terraform itself and available for all `provider` blocks:

- [`version`, for constraining the allowed provider versions](https://www.terraform.io/docs/configuration/providers.html#provider-versions) 
- [`alias`, for using the same provider with different configurations for different resources](https://www.terraform.io/docs/configuration/providers.html#alias-multiple-provider-instances) 

Provider initialization is one of the actions of `terraform init`. Running this command will download and initialize any providers that are not already initialized.

By default, `terraform init` downloads plugins into a subdirectory of the working directory so that each working directory is self-contained.

### Input Variables 

Input variables serve as parameters for a Terraform module, allowing aspects of the module to be customized without altering the module's own source code, and allowing modules to be shared between different configurations.

When you declare variables in the root module of your configuration, you can set their values using CLI options and environment variables. When you declare them in [child modules](https://www.terraform.io/docs/configuration/modules.html), the calling module should pass values in the `module` block.

```yaml
variable "availability_zone_names" {
  type    = list(string)
  default = ["us-west-1a"]
  description = "The id of the machine image (AMI) to use for the server."
}
```

The variable declaration can optionally include a `type` argument and  `default` argument. 

Within the module that declared a variable, its value can be accessed from within [expressions](https://www.terraform.io/docs/configuration/expressions.html) as `NAME.var` .

Type constraints are created from a mixture of type keywords and type constructors. The supported type keywords are:

- [`string`](https://www.terraform.io/docs/configuration/variables.html#string) 
- [`number`](https://www.terraform.io/docs/configuration/variables.html#number) 
- [`bool`](https://www.terraform.io/docs/configuration/variables.html#bool) 

The type constructors allow you to specify complex types such as collections:

- [`list(TYPE)`](https://www.terraform.io/docs/configuration/variables.html#list-lt-type-gt-) 
- [`set(TYPE)`](https://www.terraform.io/docs/configuration/variables.html#set-lt-type-gt-) 
- [`map(TYPE)`](https://www.terraform.io/docs/configuration/variables.html#map-lt-type-gt-) 
- [`object({ATTR_NAME = TYPE, ... })`](https://www.terraform.io/docs/configuration/variables.html#object-lt-attr-name-gt-lt-type-gt-) 
- [`tuple([TYPE, ...])`](https://www.terraform.io/docs/configuration/variables.html#tuple-lt-type-gt-) 

The keyword `any` may be used to indicate that any type is acceptable.

When variables are declared in the root module of your configuration, they can be set in a number of ways:

- Individually, with the `-var` command line option. 

  ```bash
  terraform apply -var="image_id=ami-abc123"
  ```

- In variable definitions (`.tfvars`) files, either specified on the command line or automatically loaded. 

  ```bash
  terraform apply -var-file="testing.tfvars"
  ```

  A variable definitions file uses the same basic syntax as Terraform language file:

  ```yaml
  image_id = "ami-abc123"
  availability_zone_names = [
    "us-east-1a",
    "us-west-1c",
  ]
  ```

  Terraform also automatically loads a number of variable definitions files if they are present:

  - Files named exactly `terraform.tfvars` or `terraform.tfvars.json`. 
  - Any files with names ending in `.auto.tfvars` or `.auto.tfvars.json`. 

- As environment variables:

  ```bash
  export TF_VAR_image_id=ami-abc123
  terraform plan
  ```


### Output Values

Have several uses:

- A child module can use outputs to expose a subset of its resource attributes to a parent module.
- A root module can use outputs to print certain values in the CLI output after running `terraform apply`.
- When using [remote state](https://www.terraform.io/docs/state/remote.html), root module outputs can be accessed by other configurations via a [`terraform_remote_state` data source](https://www.terraform.io/docs/providers/terraform/d/remote_state.html).

```yaml
output "instance_ip_addr" {
  value = aws_instance.server.private_ip
}
```

In a parent module, outputs of child modules are available in expressions as `module.web_server.instance_ip_addr`.

`output` blocks can optionally include `description`, `sensitive`, and `depends_on` arguments.

### Local Values

If [input variables](https://www.terraform.io/docs/configuration/variables.html) are analogous to function arguments and [outputs values](https://www.terraform.io/docs/configuration/outputs.html) are analogous to function return values, then `local values` are comparable to a function's **local temporary symbols.** Local values can be helpful to avoid repeating the same values or expressions multiple times in a configuration.

```yaml
locals {
  service_name = "forum"
  owner        = "Community Team"
}
```

### Modules

A *module* is a container for multiple resources that are used together. 

Every Terraform configuration has at least one module, known as its *root module*, which consists of the resources defined in the `.tf` files in the main working directory.

A module can call other modules, which lets you include the child module's resources into the configuration in a concise way. Modules can also be called multiple times, either within the same configuration or in separate configurations, allowing resource configurations to be packaged and re-used.

Modules are called from within other modules using `module` blocks:

```yaml
module "servers" {
  source = "./app-cluster"
  servers = 5
}
```

Within the block body (between `{` and `}`) are the arguments for the module. Most of the arguments correspond to [input variables](https://www.terraform.io/docs/configuration/variables.html) defined by the module.

We recommend explicitly constraining the acceptable version numbers for each external module to avoid unexpected or unwanted changes.

Along with the `source` meta-argument described above, module blocks have some more meta-arguments: `version` and `providers`.

While in principle `provider` blocks can appear in any module, it is recommended that they be placed only in the *root* module of a configuration.

To declare that a module requires particular versions of a specific provider, use a [`required_providers`](https://www.terraform.io/docs/configuration/terraform.html#specifying-required-provider-versions) block inside a `terraform` block.

### Data Sources

*Data sources* allow data to be fetched or computed for use elsewhere in Terraform configuration. Use of data sources allows a Terraform configuration to make use of information defined outside of Terraform, or defined by another separate Terraform configuration:

```yaml
data "aws_ami" "example" {
  most_recent = true

  owners = ["self"]
  tags = {
    Name   = "app-server"
    Tested = "true"
  }
}
```

Within the block body (between `{` and `}`) are query constraints defined by the data source. Data resources cause Terraform only to *read* objects.

Local-only data sources exist for [rendering templates](https://www.terraform.io/docs/providers/template/d/file.html), [reading local files](https://www.terraform.io/docs/providers/local/d/file.html), and [rendering AWS IAM policies](https://www.terraform.io/docs/providers/aws/d/iam_policy_document.html).

### Expressions

*Expressions* are used to refer to or compute values within a configuration. The simplest expressions are just literal values, like `"hello"` or `5`, but the Terraform language also allows more complex expressions such as references to data exported by resources, arithmetic, conditional evaluation, and a number of built-in functions.

---

The Terraform language uses the following types for its values:

- [`string`](https://www.terraform.io/docs/configuration/expressions.html#string): a sequence of Unicode characters representing some text, like `"hello"`.
- [`number`](https://www.terraform.io/docs/configuration/expressions.html#number): a numeric value. The `number` type can represent both whole numbers like `15` and fractional values like `6.283185`.
- [`bool`](https://www.terraform.io/docs/configuration/expressions.html#bool): either `true` or `false`. `bool` values can be used in conditional logic.
- [`list`](https://www.terraform.io/docs/configuration/expressions.html#list) (or `tuple`): a sequence of values, like `["us-west-1a", "us-west-1c"]`. Elements in a list or tuple are identified by consecutive whole numbers, starting with zero.
- [`map`](https://www.terraform.io/docs/configuration/expressions.html#map) (or `object`): a group of values identified by named labels, like `{name = "Mabel", age = 52}`.
- [`null`](https://www.terraform.io/docs/configuration/expressions.html#null): a value that represents *absence* or *omission.*

Terraform automatically converts values from one type to another in order to produce the expected type. If this isn't possible, Terraform will produce a type mismatch error.

---

Elements of list/tuple and map/object values can be accessed using the square-bracket index notation, like `local.list[3]`. 

The attributes of the resource can be accessed using [dot or square bracket notation](https://www.terraform.io/docs/configuration/expressions.html#indices-and-attributes).

---

Within the bodies of certain expressions, or in some other specific contexts, there are other named values available beyond the global values listed above: `count.index`, `each.key`/ `each.value` and `self`.

---

To allow expressions to still be evaluated during the plan phase, Terraform uses special "unknown value" placeholders for these results. Unknown values appear in the `terraform plan` output as `(not yet known)`.

---

The Terraform language has a set of operators for both arithmetic and logic, which are similar to operators in programming languages such as JavaScript or Ruby.

---

A *conditional expression* uses the value of a bool expression to select one of two values: `condition ? true_val : false_val`

---

The Terraform language has a number of [built-in functions](https://www.terraform.io/docs/configuration/functions.html) that can be used within expressions as another way to transform and combine values, for example: `min(55, 3453, 2)`. The Terraform language does not support user-defined functions.

---

A *`for` expression* creates a complex type value by transforming another complex type value. Each element in the input value can correspond to either one or zero values in the result, and an arbitrary expression can be used to transform each input element into an output element.

```yaml
[for s in var.list : upper(s)] # tuple
{for s in var.list : s => upper(s)} # object
```

---

A *splat expression*(for lists only) provides a more concise way to express a common operation that could otherwise be performed with a `for` expression:

```yaml
[for o in var.list : o.id] # for expression
var.list[*].id # splat expression
```

---

You can dynamically construct repeatable nested blocks using a special `dynamic` block type, which is supported inside `resource`, `data`, `provider`, and `provisioner` blocks. It iterates over a given complex value, and generates a nested block for each element of that complex value.

```yaml
resource "aws_security_group" "example" {
  name = "example" # can use expressions here

  dynamic "ingress" {
    for_each = var.service_ports
    content {
      from_port = ingress.value
      to_port   = ingress.value
      protocol  = "tcp"
    }
  }
}
```

---

The `<<` marker followed by any identifier at the end of a line introduces the sequence. Terraform then processes the following lines until it finds one that consists entirely of the identifier given in the introducer.

```yaml
<<EOT
hello
world
EOT
```

Terraform also accepts an *indented* heredoc string variant that is introduced by the `<<-` sequence:

```yaml
block {
  value = <<-EOT
  hello
    world
  EOT
}
```

---

A `${ ... }` sequence is an *interpolation,* which evaluates the expression given between the markers: `"Hello, ${var.name}!"`

A `%{ ... }` sequence is a *directive*, which allows for conditional results and iteration over collections, similar to conditional and `for` expressions.

### Terraform Settings

The special `terraform` configuration block type is used to configure some behaviors of Terraform itself, such as requiring a minimum Terraform version to apply your configuration:

```
terraform {
  backend "s3" {
    # (backend-specific settings...)
  }
}
```

Within a `terraform` block, only constant values can be used; arguments may not refer to named objects such as resources, input variables, etc, and may not use any of the Terraform language built-in functions.

The `required_version` setting can be used to constrain which versions of the Terraform CLI can be used with your configuration. 

The `required_providers` setting is a map specifying a version constraint for each provider required by your configuration.

### Override Files

Terraform normally loads all of the `.tf` and `.tf.json` files within a directory and expects each one to define a distinct set of configuration objects. If two files attempt to define the same object, Terraform returns an error.

Terraform initially skips files whose name ends in `_override.tf` or `_override.tf.json` when loading configuration, and then afterwards processes each one in turn (in lexicographical order).

Use override files only in special circumstances. Over-use of override files hurts readability, since a reader looking only at the original files cannot easily see that some portions of those files have been overridden without consulting all of the override files that are present. 

### Style Conventions 

* indent two spaces for each nesting level.

* align their equals signs.
* place nested blocks below.
* empty lines to separate logical groups of arguments within a block.
* list meta-arguments first and separate them from other arguments with one blank line.
* blocks should always be separated from one another by one blank line. 

### Type Constraints 

Terraform module authors and provider developers can use detailed type constraints to validate user-provided values for their input variables and resource arguments. Type constraints are expressed using a mixture of *type keywords* and function-like constructs called *type constructors.*

A *primitive* type is a simple type that isn't made from any other types: string, number, bool.

A *complex* type is a type that groups multiple values into a single value: collection types (for grouping similar values), and structural types (for grouping potentially dissimilar values).

A *collection* type allows multiple values of *one* other type to be grouped together as a single value: list, map, set.

A *structural* type allows multiple values of *several distinct types* to be grouped together as a single value: object, tuple.

The keyword `any` is a special construct that serves as a placeholder for a type yet to be decided.

### JSON Configuration Syntax

Terraform also supports an alternative syntax that is JSON-compatible. This syntax is useful when generating portions of a configuration programmatically, since existing JSON libraries can be used to prepare the generated configuration files.

Terraform expects native syntax for files named with a `.tf` suffix, and JSON syntax for files named with a `.tf.json` suffix.

## Terraform Commands (CLI)

To get help for any specific command, pass the -h flag to the relevant subcommand.

The Terraform CLI commands interact with the HashiCorp service [Checkpoint](https://checkpoint.hashicorp.com/) to check for the availability of new versions and for critical security bulletins about the current version.

The configuration is placed in a single file `.terraformrc` (note the leading period) and placed directly in the home directory of the relevant user.

The configuration file uses the same *HCL* syntax as `.tf` files, but with different attributes and blocks.

Terraform refers to a number of environment variables to customize various aspects of its behavior. None of these environment variables are required when using Terraform:   TF_LOG,   TF_LOG_PATH, etc.

---

The `terraform apply` command is used to apply the changes required to reach the desired state of the configuration, or the pre-determined set of actions generated by a `terraform plan` execution plan.

---

The `terraform console` command provides an interactive console for evaluating [expressions](https://www.terraform.io/docs/configuration/expressions.html).

---

The `terraform destroy` command is used to destroy the Terraform-managed infrastructure. If `-auto-approve` is set, then the destroy confirmation will not be shown.

---

The `terraform fmt` command is used to rewrite Terraform configuration files to a canonical format and style. This command applies a subset of the [Terraform language style conventions](https://www.terraform.io/docs/configuration/style.html), along with other minor adjustments for readability.

---

` terraform force-unlock`manually unlock the state for the defined configuration. This will not modify your infrastructure. This command removes the lock on the state for the current configuration. The behavior of this lock is dependent on the backend being used. Local state files cannot be unlocked by another process.

---

The `terraform get` command is used to download and update [modules](https://www.terraform.io/docs/modules/index.html) mentioned in the root module. The modules are downloaded into a local `.terraform` folder. This folder should not be committed to version control. If a module is already downloaded and the `-update` flag is *not* set, Terraform will do nothing.

---

The `terraform graph` command is used to generate a visual representation of either a configuration or execution plan. The output is in the DOT format, which can be used by [GraphViz](http://www.graphviz.org) to generate charts.

```bash
terraform graph | dot -Tsvg > graph.svg
```

---

The `terraform import` command is used to [import existing resources](https://www.terraform.io/docs/import/index.html) into Terraform.

---

The `terraform init` command is used to initialize a working directory containing Terraform configuration files. This is the first command that should be run after writing a new Terraform configuration or cloning an existing one from version control. It is safe to run this command multiple times.

This command is always safe to run multiple times, to bring the working directory up to date with changes in the configuration. Though subsequent runs may give errors, this command will never delete your existing configuration or state.

By default, `terraform init` assumes that the working directory already contains a configuration and will attempt to initialize that configuration.

During init, the root configuration directory is consulted for [backend configuration](https://www.terraform.io/docs/backends/config.html) and the chosen backend is initialized using the given configuration settings.

During init, the configuration is searched for `module` blocks, and the source code for referenced [modules](https://www.terraform.io/docs/modules/) is retrieved from the locations given in their `source` arguments.

During init, Terraform searches the configuration for both direct and indirect references to providers and attempts to load the required plugins.

---

The `terraform output` command is used to extract the value of an output variable from the state file.

---

The `terraform plan` command is used to create an execution plan. Terraform performs a refresh, unless explicitly disabled, and then determines what actions are necessary to achieve the desired state specified in the configuration files.

This command is a convenient way to check whether the execution plan for a set of changes matches your expectations without making any changes to real resources or to the state.

The optional `-out` argument can be used to save the generated plan to a file for later execution with `terraform apply`.

---

The `terraform providers` command prints information about the providers used in the current configuration.

---

The `terraform refresh` command is used to reconcile the state Terraform knows about (via its state file) with the real-world infrastructure. This can be used to detect any drift from the last-known state, and to update the state file.

---

The `terraform show` command is used to provide human-readable output from a state or plan file. This can be used to inspect a plan to ensure that the planned operations are expected, or to inspect the current state as Terraform sees it.

---

The `terraform state` command is used for advanced state management. As your Terraform usage becomes more advanced, there are some cases where you may need to modify the [Terraform state](https://www.terraform.io/docs/state/index.html). Rather than modify the state directly, the `terraform state` commands can be used in many cases instead.

The Terraform state subcommands all work with remote state just as if it was local state. 

All `terraform state` subcommands that modify the state write backup files. The path of these backup file can be controlled with `-backup`.

The `terraform state list` command is used to list resources within a [Terraform state](https://www.terraform.io/docs/state/index.html).

```bash
$ terraform state list aws_instance.bar
aws_instance.bar[0]
aws_instance.bar[1]
```

The `terraform state mv` command is used to move items in a [Terraform state](https://www.terraform.io/docs/state/index.html). This command can move single resources, single instances of a resource, entire modules, and more. This command can also move items to a completely different state file, enabling efficient refactoring.

```bash
# Example: Move a Resource Into a Module
$ terraform state mv 'packet_device.worker' 'module.app'
```

The `terraform state pull` command is used to manually download and output the state from [remote state](https://www.terraform.io/docs/state/remote.html). This command also works with local state.

The `terraform state push` command is used to manually upload a local state file to [remote state](https://www.terraform.io/docs/state/remote.html). This command also works with local state.

The `terraform state rm` command is used to remove items from the [Terraform state](https://www.terraform.io/docs/state/index.html). This command can remove single resources, single instances of a resource, entire modules, and more.Items removed from the Terraform state are *not physically destroyed*. Items removed from the Terraform state are only no longer managed by Terraform. 

The `terraform state show` command is used to show the attributes of a single resource in the [Terraform state](https://www.terraform.io/docs/state/index.html).

---

The `terraform taint` command manually marks a Terraform-managed resource as tainted, forcing it to be destroyed and recreated on the next apply. This command *will not* modify infrastructure, but does modify the state file in order to mark a resource as tainted. Once a resource is marked as tainted, the next [plan](https://www.terraform.io/docs/commands/plan.html) will show that the resource will be destroyed and recreated and the next [apply](https://www.terraform.io/docs/commands/apply.html) will implement this change.

---

The `terraform validate` command validates the configuration files in a directory, referring only to the configuration and not accessing any remote services such as remote state, provider APIs, etc. Validate runs checks that verify whether a configuration is syntactically valid and internally consistent.

---

The `terraform untaint` command manually unmarks a Terraform-managed resource as tainted, restoring it as the primary instance in the state. This reverses either a manual `terraform taint` or the result of provisioners failing on a resource. This command *will not* modify infrastructure, but does modify the state file in order to unmark a resource as tainted.

---

The `terraform workspace` command is used to manage [workspaces](https://www.terraform.io/docs/state/workspaces.html):

* The ` workspace list` command is used to list all existing workspaces.
* The ` workspace select` command is used to choose a different workspace to use for further operations.
* The ` workspace new` command is used to create a new workspace.
* The ` workspace delete` command is used to delete an existing workspace.
* The ` workspace show` command is used to output the current workspace.

## Import

Terraform is able to import existing infrastructure. This allows you take resources you've created by some other means and bring it under Terraform management.

The current implementation of Terraform import can only import resources into the [state](https://www.terraform.io/docs/state). It does not generate configuration. 

Because of this, prior to running `terraform import` it is necessary to write manually a `resource` configuration block for the resource, to which the imported object will be mapped.

## State

Terraform must store state about your managed infrastructure and configuration. This state is used by Terraform to map real world resources to your configuration, keep track of metadata, and to improve performance for large infrastructures.

This state is stored by default in a local file named "terraform.tfstate", but it can also be stored remotely, which works better in a team environment.

Terraform uses this local state to create plans and make changes to your infrastructure. Prior to any operation, Terraform does a [refresh](https://www.terraform.io/docs/commands/refresh.html) to update the state with the real infrastructure.

While the format of the state files are just JSON, direct file editing of the state is discouraged. Terraform provides the [terraform state](https://www.terraform.io/docs/commands/state/index.html) command to perform basic modifications of the state using the CLI.

#### Purpose of Terraform State:

* for mapping configuration to resources
* resource dependencies metadata
* performance (querying every resource is too slow)

If supported by your [backend](https://www.terraform.io/docs/backends), Terraform will lock your state for all operations that could write state. This prevents others from acquiring the lock and potentially corrupting your state.

#### Workspaces

Each Terraform configuration has an associated [backend](https://www.terraform.io/docs/backends/index.html) that defines how operations are executed and where persistent data such as [the Terraform state](https://www.terraform.io/docs/state/purpose.html) are stored. The persistent data stored in the backend belongs to a *workspace*. Certain backends support *multiple* named workspaces, allowing multiple states to be associated with a single configuration.

A common use for multiple workspaces is to create a parallel, distinct copy of a set of infrastructure in order to test a set of changes before modifying the main production infrastructure. 

Workspaces are technically equivalent to renaming your state file. They aren't any more complex than that. Terraform wraps this simple notion with a set of protections and support for remote state.

#### Remote State

With *remote* state, Terraform writes the state data to a remote data store, which can then be shared between all members of a team. Terraform supports storing state in [Terraform Cloud](https://www.hashicorp.com/products/terraform/), [HashiCorp Consul](https://www.consul.io/), Amazon S3, Alibaba Cloud OSS, and more. Remote state is a feature of [backends](https://www.terraform.io/docs/backends).

#### Sensitive Data in State 

Encryption at rest can be enabled with the S3 backend and IAM policies and logging can be used to identify any invalid access. Requests for the state go over a TLS connection.

## Providers

Terraform is used to create, manage, and update infrastructure resources such as physical machines, VMs, network switches, containers, and more. Almost any infrastructure type can be represented as a resource in Terraform.

A provider is responsible for understanding API interactions and exposing resources. Providers generally are an IaaS (e.g. Alibaba Cloud, AWS, GCP, Microsoft Azure, OpenStack), PaaS (e.g. Heroku), or SaaS services (e.g. Terraform Cloud, DNSimple, CloudFlare).

## Provisioners

Provisioners can be used to model specific actions on the local machine or on a remote machine in order to prepare servers or other infrastructure objects for service. Provisioners should only be used as a last resort. For most common situations there are better alternatives. 

```yaml
resource "aws_instance" "web" {
  # ...

  provisioner "local-exec" {
    command = "echo The server's IP address is ${self.private_ip}"
  }
}
```

Most other provisioners must connect to the remote system using SSH or WinRM. You must include [a `connection` block](https://www.terraform.io/docs/provisioners/connection.html) so that Terraform will know how to communicate with the server.

By default, provisioners run when the resource they are defined within is created. Creation-time provisioners are only run during *creation*, not during updating or any other lifecycle. They are meant as a means to perform bootstrapping of a system. If a creation-time provisioner fails, the resource is marked as **tainted**.

If `when = "destroy"` is specified, the provisioner will run when the resource it is defined within is *destroyed*.

---

Most provisioners require access to the remote resource via SSH or WinRM, and expect a nested `connection` block with details about how to connect.

---

If you need to run provisioners that aren't directly associated with a specific resource, you can associate them with a `null_resource`.

---

The `file` provisioner is used to copy files or directories from the machine executing Terraform to the newly created resource. The `file` provisioner supports both `ssh` and `winrm` type [connections](https://www.terraform.io/docs/provisioners/connection.html).

The `local-exec` provisioner invokes a local executable after a resource is created. This invokes a process on the machine running Terraform, not on the resource.

The `remote-exec` provisioner invokes a script on a remote resource after it is created. This can be used to run a configuration management tool, bootstrap into a cluster, etc.

## Module

A *module* is a container for multiple resources that are used together. Modules can be used to create lightweight abstractions, so that you can describe your infrastructure in terms of its architecture, rather than directly in terms of physical objects.

The `.tf` files in your working directory when you run [`terraform plan`](https://www.terraform.io/docs/commands/plan.html) or [`terraform apply`](https://www.terraform.io/docs/commands/apply.html) together form the *root* module. That module may [call other modules](https://www.terraform.io/docs/configuration/modules.html#calling-a-child-module) and connect them together by passing output values from one to input values of another.

Most commonly, modules use:

- [Input variables](https://www.terraform.io/docs/configuration/variables.html) to accept values from the calling module.
- [Output values](https://www.terraform.io/docs/configuration/outputs.html) to return results to the calling module, which it can then use to populate arguments elsewhere.
- [Resources](https://www.terraform.io/docs/configuration/resources.html) to define one or more infrastructure objects that the module will manage.

A complete example of a module following the standard structure is:

```bash
$ tree complete-module/
.
├── README.md
├── main.tf
├── variables.tf
├── outputs.tf
├── ...
├── modules/
│   ├── nestedA/
│   │   ├── README.md
│   │   ├── variables.tf
│   │   ├── main.tf
│   │   ├── outputs.tf
│   ├── nestedB/
│   ├── .../
├── examples/
│   ├── exampleA/
│   │   ├── main.tf
│   ├── exampleB/
│   ├── .../
```

---

The `source` argument in [a `module` block](https://www.terraform.io/docs/configuration/modules.html) tells Terraform where to find the source code for the desired child module. Terraform uses this during the module installation step of `terraform init` to download the source code to a directory on local disk so that it can be used by other Terraform commands.

Local path references allow for factoring out portions of a configuration within a single source repository.

```
module "consul" {
  source = "./consul"
}
```

A local path must begin with either `./` or `../` to indicate that a local path is intended, to distinguish from [a module registry address](https://www.terraform.io/docs/modules/sources.html#terraform-registry).

Other sources:

* [Terraform Registry](https://registry.terraform.io/) is an index of modules shared publicly using this protocol. This public registry is the easiest way to get started with Terraform and find modules created by others in the community.

* Terraform will recognize unprefixed `github.com` URLs and interpret them automatically as Git repository sources.
* Terraform will recognize unprefixed `bitbucket.org` URLs and interpret them automatically as BitBucket repositories.
* Arbitrary Git repositories can be used by prefixing the address with the special `git::` prefix. After this prefix, any valid [Git URL](https://git-scm.com/docs/git-clone#_git_urls_a_id_urls_a) can be specified to select one of the protocols supported by Git.
* You can use arbitrary Mercurial repositories by prefixing the address with the special `hg::` prefix. 
* When you use an HTTP or HTTPS URL, Terraform will make a `GET` request to the given URL, which can return *another* source address. 
* You can use archives stored in S3 as module sources using the special `s3::` prefix.
* You can use archives stored in Google Cloud Storage as module sources using the special `gcs::` prefix.

---

In a simple Terraform configuration with only one root module, we create a flat set of resources and use Terraform's expression syntax to describe the relationships between these resources. When we introduce `module` blocks, our configuration becomes heirarchical rather than flat: each module contains its own set of resources, and possibly its own child modules, which can potentially create a deep, complex tree of resource configurations.

We call this flat style of module usage *module composition*, because it takes multiple [composable](https://en.wikipedia.org/wiki/Composability) building-block modules and assembles them together to produce a larger system.

## Backends

A "backend" in Terraform determines how state is loaded and how an operation such as `apply` is executed. This abstraction enables non-local file state storage, remote execution, etc.

Backends are configured directly in Terraform files in the `terraform` section. 

After configuring a backend, it has to be [initialized](https://www.terraform.io/docs/backends/init.html). This can be done by simply running `terraform init`.

```
terraform {
  backend "consul" {
    address = "demo.consul.io"
    scheme  = "https"
    path    = "example_app/terraform_state"
  }
}
```

Only one backend may be specified and the configuration **may not contain interpolations**.

You do not need to specify every required argument in the backend configuration. A configuration may be specified via the `init` command line. To specify a file, use the `-backend-config=PATH` option when running `terraform init`. If the file contains secrets it may be kept in a secure data store. Key/value pairs can be specified via the `init` command line too.

Backends are responsible for storing state and providing an API for [state locking](https://www.terraform.io/docs/state/locking.html). State locking is optional.

You can manually retrieve the state from the remote state using the `terraform state pull` command. This will load your remote state and output it to stdout. You can also manually write state with `terraform state push`. **This is extremely dangerous and should be avoided if possible.** 

Backend types:

* The local backend stores state on the local filesystem, locks that state using system APIs, and performs operations locally.
* The remote backend stores Terraform state and may be used to run operations in Terraform Cloud.
* etc.

## Plugins

Terraform is built on a plugin-based architecture. All providers and provisioners that are used in Terraform configurations are plugins, even the core types such as AWS and Heroku. Users of Terraform are able to write new plugins in order to support new functionality in Terraform.

Plugins are executed as a separate process and communicate with the main Terraform binary over an RPC interface. When you start Terraform, it identifies the plugin you want to use,  finds it on disk, runs the other binary, and does some handshaking to  make sure they can talk to each other (the error you may see after  upgrading is a handshake failure in the RPC code).

The code within the binaries must adhere to certain interfaces. The network communication and RPC is handled automatically by higher-level Terraform libraries.

The [provider plugins distributed by HashiCorp](https://www.terraform.io/docs/providers/index.html) are automatically installed by `terraform init`. Third-party plugins (both providers and provisioners) can be manually installed into the user plugins directory.

## The Core Terraform Workflow

The core Terraform workflow has three steps:

1. **Write** - Author infrastructure as code. 
2. **Plan** - Preview changes before applying. 
3. **Apply** - Provision reproducible infrastructure. 

This is the set of practices that we call `collaborative infrastructure as code`.

## Terraform Registry 

The [Terraform Registry](https://registry.terraform.io) is a repository of modules written by the Terraform community.

Verified modules are reviewed by HashiCorp and actively maintained by contributors to stay up-to-date and compatible with both Terraform and their respective providers.

The Terraform Registry is integrated directly into Terraform. This makes it easy to reference any module in the registry. For example:

```yaml
module "consul" {
  source = "hashicorp/consul/aws"
  version = "0.1.0"
}
```

## Terraform Internals 

Terraform has detailed logs which can be enabled by setting the `TF_LOG` environment variable to any value. This will cause detailed logs to appear on stderr.

If Terraform ever crashes (a "panic" in the Go runtime), it saves a log file with the debug logs from the session as well as the panic message and backtrace to `crash.log`.

---

Terraform builds a [dependency graph](https://en.wikipedia.org/wiki/Dependency_graph) from the Terraform configurations, and walks this graph to generate plans, refresh state, and more.

---

Resources have a strict lifecycle, and can be thought of as basic state machines. A resource roughly follows the steps below: [`ValidateResource`](https://www.terraform.io/docs/internals/lifecycle.html#validateresource) is called to do a high-level structural validation of a resource's configuration. [`Diff`](https://www.terraform.io/docs/internals/lifecycle.html#diff) is called with the current state and the configuration. [`Apply`](https://www.terraform.io/docs/internals/lifecycle.html#apply) is called with the current state and the diff. 

If an error happens at any stage in the lifecycle of a resource, Terraform stores a partial state of the resource. This behavior is critical for Terraform to ensure that you don't end up with any *zombie* resources: resources that were created by Terraform but no longer managed by Terraform due to a loss of state.

---

A **Resource Address** is a string that references a specific resource in a larger infrastructure. An address is made up of two parts:

```
[module path][resource spec]
```

A module path addresses a module within the tree of modules. It takes the form:

```
module.A.module.B.module.C...
```

A resource spec addresses a specific resource in the config. It takes the form:

```
resource_type.resource_name[resource index]
```

---

Terraform can output a machine-readable JSON representation of a plan  file's changes. It can also convert state files to the same format, to  simplify data loading and provide better long-term compatibility.

## How Terraform Works 

Terraform is logically split into two main parts: **Terraform Core** and **Terraform Plugins**. Terraform Core uses remote procedure calls (RPC) to communicate with Terraform Plugins, and offers multiple ways to discover and load plugins to use.  Terraform Plugins expose an implementation for a specific service, such as AWS, or provisioner, such as bash.

Terraform Core is a [statically-compiled binary](https://en.wikipedia.org/wiki/Static_build#Static_building) written in the [Go programming language](https://golang.org/). The compiled binary is the command line tool (CLI) `terraform`, the entrypoint for anyone using Terraform. The code is open source and hosted at github.com/hashicorp/terraform.

Terraform Plugins are written in Go and are executable binaries invoked by Terraform Core over RPC. Each plugin exposes an implementation for a specific service, such as AWS, or provisioner, such as bash. All Providers and Provisioners used in Terraform configurations are plugins. They are executed as a separate process and communicate with the main Terraform binary over an RPC interface. Terraform has several Provisioners built-in, while Providers are discovered dynamically as needed.

When `terraform init` is run, Terraform reads configuration files in the working directory to determine which plugins are necessary, searches for installed plugins in several locations, sometimes downloads additional plugins, decides which plugin versions to use, and writes a lock file to ensure Terraform will use the same plugin versions in this directory until `terraform init` runs again.

Providers are the most common type of Plugin, which expose the features that a specific service offers via its [application programming interface](https://en.wikipedia.org/wiki/Application_programming_interface) (API). Providers define **Resources** and are responsible for managing their life cycles. 

Provisioners are used to execute scripts on a local or remote machine as part of resource creation or destruction. Provisioners can be used to bootstrap a resource, cleanup before destroy, run configuration management, etc.

The official Terraform Provisioners are included in the Terraform Core codebase and are compiled into the `terraform` binary. While they are built in, Provisioners are still executed in a separate process over RPC, and benefit from the same discovery process as Terraform Providers, making custom Provisioners just as easy to create and use.
