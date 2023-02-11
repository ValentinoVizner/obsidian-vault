# Kubectx and Kubens

## What are `kubectx` and `kubens`?

**kubectx** is a tool to switch between contexts (clusters) on kubectl
faster.<br/>
**kubens** is a tool to switch between Kubernetes namespaces (and
configure them for kubectl) easily.

Here's a **`kubectx`** demo:
![kubectx demo GIF](assets/kubectx-demo.gif)

...and here's a **`kubens`** demo:
![kubens demo GIF](assets/kubens-demo.gif)

### Examples

```sh
# switch to another cluster that's in kubeconfig
$ kubectx minikube
Switched to context "minikube".

# switch back to previous cluster
$ kubectx -
Switched to context "oregon".

# create an alias for the context
$ kubectx dublin=gke_ahmetb_europe-west1-b_dublin
Context "dublin" set.
Aliased "gke_ahmetb_europe-west1-b_dublin" as "dublin".

# change the active namespace on kubectl
$ kubens kube-system
Context "test" set.
Active namespace is "kube-system".

# go back to the previous namespace
$ kubens -
Context "test" set.
Active namespace is "default".
```

If you have [`fzf`](https://github.com/junegunn/fzf) installed, you can also
**interactively** select a context or cluster, or fuzzy-search by typing a few
characters. To learn more, read [interactive mode &rarr;](#interactive-mode)

Both `kubectx` and `kubens` support <kbd>Tab</kbd> completion on bash/zsh/fish
shells to help with long context names. You don't have to remember full context
names anymore.

-----