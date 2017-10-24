# kubernetes-blog-edits

These are the edits that I believe need to be made to the blog post at: https://blog.alexellis.io/kubernetes-in-10-minutes/

1. Update the output to the `kubeadm init` command to the following (or similar), which is the output of version 1.8.1:

https://raw.githubusercontent.com/ericstoekl/kubernetes-blog-edits/master/output_v1.8.1.txt

2. In the code block that starts with:

>Switch into the new user account with: `sudo su packet`


You could change the code to:


```
$ cd $HOME
$ sudo whoami
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

The `export KUBECONFIG=$HOME/admin.conf` is no longer necessary.

3. `kube-dashboard` permissions changes are needed. The user should:

```
$ curl -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml > kubernetes-dashboard.yaml
```
```
$ echo "kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: kubernetes-dashboard-rb
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: kubernetes-dashboard
  namespace: kube-system
" >> kubernetes-dashboard.yaml
```
```
kubectl apply -f kubernetes-dashboard.yaml --validate=false
```

This will update the permissions to allow for `kube-dashboard` to work on the setup. This is not recommended for production systems. Source: https://github.com/kubernetes/dashboard/issues/1803#issuecomment-335533274

4. The new link for the `kube-dashboard` is: http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login
