> References:
> https://github.com/ahmetb/kubectx



Installation:

```bash
kubectl krew install ctx
kubectl krew install ns
```

or

```bash
asdf plugin add kubectx
asdf install kubectx latest
asdf global kubectx VERSION
```



**`kubectx`** helps you switch between clusters back and forth:

```bash
kubectx             : list the contexts
kubectx NAME        : switch to context <NAME>
kubectx -           : switch to the previous context
kubectx --current   : show the current context name
kubectx --unset     : unset the current context
```



**`kubens`** helps you switch between Kubernetes namespaces smoothly:

```bash
kubens                : list the namespaces
kubens NAME           : change the active namespace
kubens -              : switch to the previous namespace
kubens --current      : show the current namespace
```
