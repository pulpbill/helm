# helm
How to use Helm and Helm charts

## HELM / Tiller

1. Install Helm:
```
curl https://raw.githubusercontent.com/helm/helm/master/scripts/get > get_helm.sh && chmod 700 get_helm.sh && ./get_helm.sh
```

2. Create Tiller serviceaccount:
```
kubectl --namespace kube-system create serviceaccount tiller 
```

3. Bind the tiller serviceaccount to the Kubernetes cluster-admin role:
```
kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
```

4. Install Tiller in your Kubernetes cluster:
```
helm init --service-account tiller
```

Optional, run this and you should see Tiller's pod running at kube-system namespace:
```
kubectl get pods --namespace kube-system | grep tiller
```

Bonus track, set up tiller account at once, use it carefully:
```
kubectl --namespace kube-system create serviceaccount tiller && kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller && helm init --service-account tiller
```

What helm init means:

This will validate that helm’s local environment is set up correctly (and set it up if necessary). Then it will connect to whatever cluster kubectl connects to by default (kubectl config view). Once it connects, it will install tiller into the kube-system namespace.

Helm will figure out where to install Tiller by reading your Kubernetes configuration file (usually $HOME/.kube/config). This is the same file that kubectl uses.

If your cluster has Role-Based Access Control (RBAC) enabled, you may want to configure a service account and rules before proceeding.

Refer to: https://docs.helm.sh/using_helm/#role-based-access-control 


### Charts:

Once you have everything set up, go look for a chart to test on your K8s cluster (IE: Kibana):
```
helm search | grep kibana
```
Install a chart:
```
helm install stable/kibana --name kibana-stable
```
Delete a chart:
```
helm delete --purge kibana-stable
```
Test a chart with debug:
```
helm install --dry-run --debug mychart
```
Install a chart at monitoring namespace:
```
helm install stable/kibana --name kibana-stable --namespace monitoring
```

#### Note:
I picked the release name passing --name kibana-stable Otherwise, Helm would have picked one for us.

In case of tiller issues: http://zero-to-jupyterhub.readthedocs.io/en/latest/setup-helm.html

Reference:

https://github.com/helm/helm/blob/master/docs/rbac.md

https://docs.gitlab.com/ee/install/kubernetes/preparation/tiller.html
