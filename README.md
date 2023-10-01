# argocd-tests

This repository is to summarize what I've learned on this [video tutorial for ArgoCD](https://www.youtube.com/watch?v=MeU5_k9ssrs&list=WL&index=2)

## Sources:

* [Video](https://www.youtube.com/watch?v=MeU5_k9ssrs&list=WL&index=2)
* [ArgoCD Configuration](https://gitlab.com/nanuchi/argocd-app-config)
* [Install ArgoCD](https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd)
* [Login to ArgoCD](https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli)
* [ArgoCD Documentation](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/)


## ArgoCD Installation in k8s
```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```


*Check ArgoCD is installed on namespace*

```
kubectl get pods -n argocd
```

Response

```
NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     1/1     Running   0          2m52s
argocd-applicationset-controller-787bfd9669-7bsts   1/1     Running   0          2m52s
argocd-dex-server-bb76f899c-v5q7q                   1/1     Running   0          2m52s
argocd-notifications-controller-5557f7bb5b-pgjwf    1/1     Running   0          2m52s
argocd-redis-b5d6bf5f5-bspl2                        1/1     Running   0          2m52s
argocd-repo-server-56998dcf9c-zb7j6                 1/1     Running   0          2m52s
argocd-server-5985b6cf6f-zwlfp                      1/1     Running   0          2m52s
```


*Access ArgoCD UI*

This command returns list of services

```
kubectl get svc -n argocd
```
Response

```
NAME                                      TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
argocd-applicationset-controller          ClusterIP   10.108.130.42    <none>        7000/TCP,8080/TCP            5m35s
argocd-dex-server                         ClusterIP   10.106.215.11    <none>        5556/TCP,5557/TCP,5558/TCP   5m35s
argocd-metrics                            ClusterIP   10.105.212.249   <none>        8082/TCP                     5m35s
argocd-notifications-controller-metrics   ClusterIP   10.111.98.183    <none>        9001/TCP                     5m35s
argocd-redis                              ClusterIP   10.107.135.1     <none>        6379/TCP                     5m35s
argocd-repo-server                        ClusterIP   10.111.54.173    <none>        8081/TCP,8084/TCP            5m35s
argocd-server                             ClusterIP   10.104.125.93    <none>        80/TCP,443/TCP               5m35s
argocd-server-metrics                     ClusterIP   10.100.67.161    <none>        8083/TCP                     5m35s
```

We need to do a port forward so we can actually hit the ArgoCD UI.

```
kubectl port-forward svc/argocd-server 8080:443 -n argocd
```

*Login with admin user and below token (as in documentation):*
```
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode && echo
```

You can change and delete init password.

### Testing ArgoCD Self Healing

Edit and apply the deployment

```
kubectl edit deployment -n myapp myapp
```

After I save that `deployment.yaml` file, ArgoCD updates and then rollbacks, which is what we are expecting.

```
    automated:
      selfHeal: true
```
