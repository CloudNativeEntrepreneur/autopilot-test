# example-gitops-local

Gitops config for a developing with a local cluster.

Everything your services need to run, deployed to the cluster, but not the services themselves. Run those locally.

The cluster tooling is preconfigured to use `host.docker.internal` when running in local mode to access your local network.

From your local network you may want to access the Kubernetes network, for that use `localizer`.

```
sudo localizer --namespace example-local-env
```

This will create tunnels, and update your `/etc/hosts` for accessing those tunnels via the same URL that would be used internally – `http://<service-name>.<namespace>.svc.cluster.local`, so `http://example-hasura.example-local-env.svc.cluster.local` for example.

To get started, first log in to GHCR, then apply the gitops config using argocd autopilot.

# Private container registry (such as on ghcr.io)?

Log in to your container registry with:

```
kubectl create ns example-local-env
kubectl -n example-local-env create secret docker-registry ghcr --docker-server=https://ghcr.io --docker-username=<your-github-username> --docker-password=<github_PAT_packages:read> --docker-email=<your-github-email>
kubectl -n example-local-env patch serviceaccount default -p '{"imagePullSecrets": [{"name": "ghcr"}]}'
```

# Apply this config with argocd autopilot

```
export GIT_REPO=https://github.com/CloudNativeEntrepreneur/example-gitops-local
export GIT_TOKEN=<paste in your github PAT created here: https://github.com/settings/tokens/new?scopes=repo>

argocd-autopilot repo bootstrap --recover
```