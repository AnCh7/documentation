## k9s

> References:
> https://github.com/derailed/k9s
> https://k9scli.io

##### Installation

```bash
brew install k9s
```

##### Docker

```bash
docker run --rm -it -v $KUBECONFIG:/root/.kube/config quay.io/derailed/k9s
```

##### The Command Line

```bash
# List all available CLI options
k9s help

# To get info about K9s runtime (logs, configs, etc..)
k9s info

# To run K9s in a given namespace
k9s -n mycoolns

# Start K9s in an existing KubeConfig context
k9s --context coolCtx

# Start K9s in readonly mode - with all cluster modification commands disabled
k9s --readonly
```

##### Logs

```bash
k9s info
# Configuration:   /Users/fernand/.k9s/config.yml
# Logs:            /var/folders/8c/hh6rqbgs5nx_c_8k9_17ghfh0000gn/T/k9s-fernand.log
# Screen Dumps:    /var/folders/8c/hh6rqbgs5nx_c_8k9_17ghfh0000gn/T/k9s-screens-fernand

# To view k9s logs
tail -f /var/folders/8c/hh6rqbgs5nx_c_8k9_17ghfh0000gn/T/k9s-fernand.log

# Start K9s in debug mode
k9s -l debug
```

##### Key Bindings

| Action                                                       | Command                       | Comment                                                      |
| ------------------------------------------------------------ | ----------------------------- | ------------------------------------------------------------ |
| Show active keyboard mnemonics and help                      | `?`                           |                                                              |
| Show all available resource alias                            | `ctrl-a`                      |                                                              |
| To bail out of K9s                                           | `:q`, `ctrl-c`                |                                                              |
| View a Kubernetes resource using singular/plural or short-name | `:`po⏎                        | accepts singular, plural, short-name or alias ie pod or pods |
| View a Kubernetes resource in a given namespace              | `:`alias namespace⏎           |                                                              |
| Filter out a resource view given a filter                    | `/`filter⏎                    | Regex2 supported ie `fred                                    |
| Inverse regex filer                                          | `/`! filter⏎                  | Keep everything that *doesn't* match.                        |
| Filter resource view by labels                               | `/`-l label-selector⏎         |                                                              |
| Fuzzy find a resource given a filter                         | `/`-f filter⏎                 |                                                              |
| Bails out of view/command/filter mode                        | `<esc>`                       |                                                              |
| Key mapping to describe, view, edit, view logs,...           | `d`,`v`, `e`, `l`,...         |                                                              |
| To view and switch to another Kubernetes context             | `:`ctx⏎                       |                                                              |
| To view and switch to another Kubernetes context             | `:`ctx context-name⏎          |                                                              |
| To view and switch to another Kubernetes namespace           | `:`ns⏎                        |                                                              |
| To view all saved resources                                  | `:`screendump or sd⏎          |                                                              |
| To delete a resource (TAB and ENTER to confirm)              | `ctrl-d`                      |                                                              |
| To kill a resource (no confirmation dialog!)                 | `ctrl-k`                      |                                                              |
| Launch pulses view                                           | `:`pulses or pu⏎              |                                                              |
| Launch XRay view                                             | `:`xray RESOURCE [NAMESPACE]⏎ | RESOURCE can be one of po, svc, dp, rs, sts, ds, NAMESPACE is optional |
| Launch Popeye view                                           | `:`popeye or pop⏎             | See https://popeyecli.io                                     |

##### K9s Configuration

```
$HOME/.k9s/config.yml
```

##### Node Shell

By enabling the nodeShell feature gate on a given cluster, K9s allows you to shell into your cluster nodes.

##### Command Aliases

k9s command mode supports auto suggestions.

| Key                | Description                              |
| ------------------ | ---------------------------------------- |
| ⬆️ ⬇️                | Navigate up or down thru the suggestions |
| `Ctrl-w`, `Ctrl-u` | Clear out the command                    |
| `Tab`, `Ctrl-f`, ➡️ | Accept the suggestion                    |

In K9s, you can define your very own command aliases (shortnames) to access your resources. In your `$HOME/.k9s` define a file called `alias.yml`

##### Resource Custom Columns

You can change which columns shows up for a given resource via custom views. To surface this feature, you will need to create a new  configuration file, namely `$HOME/.k9s/views.yml`

##### Plugins

K9s allows you to extend your command line and tooling by defining your very own cluster commands via plugins. K9s will look at `$HOME/.k9s/plugin.yml`

##### Benchmark Your Applications

K9s integrates [Hey](https://github.com/rakyll/hey). `Hey` is a CLI tool to benchmark HTTP endpoints similar to AB bench. This  preliminary feature currently supports benchmarking port-forwards and  services.

##### K9s RBAC

On RBAC enabled clusters, you would need to give your users/groups  capabilities so that they can use K9s to explore their Kubernetes  cluster. K9s needs minimally read privileges at both the cluster and  namespace level to display resources and metrics.

##### Skins

Skins are YAML files, that enable a user to change the K9s presentation layer. K9s skins are loaded from `$HOME/.k9s/skin.yml`.
