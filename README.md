# Customizing Upstream Helm Charts with Kustomize
https://testingclouds.wordpress.com/2018/07/20/844/


# ArgoCD Kustomizing Helm charts¶
It's possible to render Helm charts with Kustomize. Doing so requires that you pass the --enable-helm flag to the kustomize build command.
This flag is not part of the Kustomize options within Argo CD. If you would like to render Helm charts through Kustomize in an Argo CD application, you have two options:
You can either create a custom plugin, or modify the argocd-cm ConfigMap to include the --enable-helm flag globally for all Kustomize applications:

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
data:
  kustomize.buildOptions: --enable-helm
```


# Update ArgoCD Plugin 
configure kustomized-helm tool in argocd-cm ConfigMap:
```
  configManagementPlugins: |
    - name: kustomized-helm
      init:
        command: ["/bin/sh", "-c"]
        args: ["helm dependency build"]
      generate:
        command: ["/bin/sh", "-c"]
        args: ["helm template . --name-template $ARGOCD_APP_NAME --namespace $ARGOCD_APP_NAMESPACE --kube-version $KUBE_VERSION > all.yaml && kustomize build"]

```

```
argocd app create kustomized-helm \
    --config-management-plugin kustomized-helm \
    --repo https://github.com/argoproj/argocd-example-apps \
    --path plugins/kustomized-helm \
    --dest-server https://kubernetes.default.svc \
    --dest-namespace default
```
# ArgoCD Example Apps

This repository contains example applications for demoing ArgoCD functionality. Feel free
to register this repository to your ArgoCD instance, or fork this repo and push your own commits
to explore ArgoCD and GitOps!

| Application | Description |
|-------------|-------------|
| [guestbook](guestbook/) | A hello word guestbook app as plain YAML |
| [helm-guestbook](helm-guestbook/) | The guestbook app as a Helm chart |
| [jsonnet-guestbook](jsonnet-guestbook/) | The guestbook app as a raw jsonnet |
| [jsonnet-guestbook-tla](jsonnet-guestbook-tla/) | The guestbook app as a raw jsonnet with support for top level arguments |
| [kustomize-guestbook](kustomize-guestbook/) | The guestbook app as a Kustomize 2 app |
| [pre-post-sync](pre-post-sync/) | Demonstrates Argo CD PreSync and PostSync hooks |
| [sync-waves](sync-waves/) | Demonstrates Argo CD sync waves with hooks |
| [helm-dependency](helm-dependency/) | Demonstrates how to customize an OTS (off-the-shelf) helm chart from an upstream repo |
| [sock-shop](sock-shop/) | A microservices demo app (https://microservices-demo.github.io) |
| [plugins](plugins/) | Apps which demonstrate config management plugins usage |
| [blue-green](blue-green/) | Demonstrates how to implement blue-green deployment using [Argo Rollouts](https://github.com/argoproj/argo-rollouts)
| [apps](apps/) | An app composed of other apps |
