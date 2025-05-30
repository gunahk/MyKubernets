# Tabels of Contenets:
-  [Kubernets Cheats Sheets](#Kubernets-Cheats-Sheets)
-   [Auto completion of kubernets Commands](#Auto-completion-of-kubernets-Commands)
-  [Installation of kubectx and kubens
](#Installation-of-kubectx-and-kubens
)
-  [kube-ps1: Kubernetes prompt for bash](#kube-ps1-Kubernetes-prompt-for-bash)

-  [Make Easy to execute the Command Inside the Container](#Make-Easy-to-execute-the-Command-Inside-the-Container)


<button id="demo" onclick="copyToClipboard(document.getElementById('demo').innerHTML)">This is what asdf d I want to copy</button>


---
# Kubernetes Cheats Sheets
1. [Kubernets Cheatsheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
2. [Kubernets Commands ](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)  (Kubectl commands Reference)
3. [Kubernets reference resources](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#explain)

# Auto completion of kubernets Commands 
In Bash, we can do autocompletion of kubectl commands 

For faster command Execution
s
example:
```bash
Kubectl exec -it ngin
```
Hit tab you will automatically promt 

```bash
Kubectl exec -it nginx-f89759699-8rzf8
```

we need to add lines in the ~/.bashrc

```bash
source <(kubectl completion bash)
echo "source <(kubectl completion bash)"
alias k=kubectl
complete -F __start_kubectl k
```
After Adding just reload the profile by
```bash
source ~/.bashrc
```
---
# Kubectx

kubectx is a utility to manage and switch between kubectl contexts.

![kubectx demo GIF](img/kubectx-demo.gif)

```bash
USAGE:
  kubectx                   : list the contexts
  kubectx <NAME>            : switch to context <NAME>
  kubectx -                 : switch to the previous context
  kubectx -c, --current     : show the current context name
  kubectx <NEW_NAME>=<NAME> : rename context <NAME> to <NEW_NAME>
  kubectx <NEW_NAME>=.      : rename current-context to <NEW_NAME>
  kubectx -d <NAME>         : delete context <NAME> ('.' for current-context)
                              (this command won't delete the user/cluster entry
                              that is used by the context)
  kubectx -u, --unset       : unset the current context
```

Usage

```bash
$ kubectx
oregon
minikube

$ kubectx oregon
Switched to context "oregon".

```



# kubens

kubenx is a utility to manage and switch between kubectl namespaces.

![kubens demo GIF](img/kubens-demo.gif)

```bash
USAGE:
  kubens                    : list the namespaces
  kubens <NAME>             : change the active namespace
  kubens -                  : switch to the previous namespace
  kubens -c, --current      : show the current namespace
```


usage
```
$ kubens kube-system
Context "test" set.
Active namespace is "kube-system".

$ kubens -
Context "test" set.
Active namespace is "default".
```
kubens also supports Tab completion on bash/zsh/fish shells.

---
# Installation of kubectx and kubens
```bash
sudo git clone https://github.com/ahmetb/kubectx /opt/kubectx
sudo cp /opt/kubectx/kubectx /usr/local/bin/kubectx
sudo cp /opt/kubectx/kubens /usr/local/bin/kubens
```

## Reference

- [kubectx](https://github.com/ahmetb/kubectx)
- [kubens](https://github.com/ahmetb/kubectx)

---

# kube-ps1: Kubernetes prompt for bash


Its Very difficult to know Which Cluster you are in the Kubernets. 

There  is a script By which, you can able to promt in Bash in which cluster You are in , and which namespace you are in Namespace.


## Bash

Download the script and paste in the location.

```bash
curl -o https://github.com/jonmosco/kube-ps1/blob/master/kube-ps1.sh ~/kube-ps1.sh
```
Copy below Commands in the ~/.bashrc
```bash
source ~/kube-ps1.sh>>~/.bashrc
PS1='[\u@\h \W $(kube_ps1)]\$ '>>~/.bashrc
```

After Adding just reload the profile by

```bash
source ~/.bashrc
```

For Autocompletion of kubens and kubectl 

```
git clone https://github.com/ahmetb/kubectx.git ~/.kubectx
COMPDIR=$(pkg-config --variable=completionsdir bash-completion)
ln -sf ~/.kubectx/completion/kubens.bash $COMPDIR/kubens
ln -sf ~/.kubectx/completion/kubectx.bash $COMPDIR/kubectx
cat << EOF >> ~/.bashrc

#kubectx and kubens
export PATH=~/.kubectx:\$PATH
EOF
```

Your will be promted with ***Bash*** Command promt like below

![kubens demo GIF](img/kubeps1.gif)

`reference:` 
-  [Kubernets ps1 ](https://github.com/jonmosco/kube-ps1)

---
# Make Easy to execute the Command Inside the Container

Some times you may need to execute Command, Inside the container to check services, telnet, nslookup 

```bash
kubectl run multitool --image=praqma/network-multitool -n default
```

Add below command in the ~/.bashrc
```bash
alias kip="kubectl -n default exec -it multitool --">>~/.bashrc
```
After Adding just reload the profile by
```bash
source ~/.bashrc
```
Then you can execute following Commands inside the pod. By the Below commands.

```bash
kip nslookup kubernetes
```

Kubernetes namespace is in struck

```bash
namespace=<deleting_namespace>
kubectl get namespace ${namespace} -o json \
  | tr -d "\n" | sed "s/\"finalizers\": \[[^]]\+\]/\"finalizers\": []/" \
  | kubectl replace --raw /api/v1/namespaces/stucked-namespace/finalize -f -
kubectl delete namespace ${namespace} --grace-period=15 
```
or 

```bash
kubectl get namespace ${namespace} -o json > ${namespace}.json
kubectl replace --raw "/api/v1/namespaces/${namespace}/finalize" -f ./${namespace}.json
```
K8s ingress not deleting

Get the ing

```bash
ingress=$(kubectl get ingress  -o custom-columns=":metadata.name")
```

```bash
kubectl patch ingress ${ingress} -p '{"metadata":{"finalizers":[]}}' --type=merge
```

Get the pods name running in the Node with the labels

```bash
Lable="intent=apps"
# get the pods Running
kubectl get nodes -l $Lable | tail -n +2 |awk '{print $1}' | xargs -I {} kubectl describe node {} | awk '/Non-terminated Pods:/, /Allocated resources:/'
# Get the node Events
kubectl get nodes -l $Lable | tail -n +2 | awk '{print $1}' | xargs -I {} kubectl describe node {} | awk '/Events:/, /Name:/'
```

Delete the jobs with pod failing 

```bash
errorMessage=image
kubectl delete jobs $(kubectl get po | grep -i $errorMessage | awk {'print $1'} | rev| cut -d'-' -f2- | rev)
```

Delete the pods with pod in Terminating
```bash
kubectl get pods --all-namespaces | grep Terminating | awk '{print $1,$2}' | while read ns po; do
  kubectl get pod "$po" -n "$ns" -o json | \
    jq 'del(.metadata.finalizers)' | \
    kubectl apply -n "$ns" -f - && \
  kubectl delete pod "$po" -n "$ns" --force --grace-period=0
done
```

limit is for Enforcing, Reqiests is for Allocating resourcse Cgroups(Allocate the Resources), Namespaces(Segregate the resources)

if CPU more --> throtled wait for some time to process requests
if Memory more --> OOM killed pods will restart


Get the total CPU and Memory of pods, Helpfull during the HPA

```bash
awk '{sum += $3} END {print sum}' <(kubectl top po | tail -n +2 )'
```
HPA add limits to this
```
( total resource / deseried pods ) * ( 100 / Desired HPA metrics )
```

