<!-- Delete installed Calico -->
kubectl delete -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml
kubectl delete -f https://docs.projectcalico.org/v3.3/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml
kubectl delete installation default
kubectl delete -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/tigera-operator.yaml
kubectl delete -n kube-system pod -l k8s-app=kube-dns
kubectl get -n kube-system pod -l k8s-app=kube-dns -o custom-columns=name:.metadata.name,status:.status.phase,message:.status.conditions[*].message
