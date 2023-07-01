# Helm Cheatsheet

[Helm Auto completion](https://helm.sh/docs/helm/helm_completion/) 
```
helm completion bash
```

To list all the helm charts 
```
Helm list
```
To list all the helm charts in all name spaces

```
helm list -a
```

## helm create a sample chart 
```
helm create guna
```

Helm cheatsheet to template the helm chart [Helm-Cheat-Sheet](https://gist.github.com/tuannvm/4e1bcc993f683ee275ed36e67c30ac49)


Sample templates for if-else, while loop

To check the yaml formate errors
```
helm lint guna
```

To Check all the templates are working
```
helm template guna
```

Dry run to check the helm is Deployable
```
helm install --upgrade --dry-run guna
```

Get the template files after Installing
```
helm ls --> guna
helm get manifest guna
```

here 

```
guna = hem_repo/heim_chart_name
```

Sample adding the datadog repo with repo_name as datadog
````
helm repo add datadog_repo https://helm.datadoghq.com
helm repo update
````

Search all  charts in repo datadog
```
helm repo search datadog_repo
```


Search all  charts in repo datadog with versions
```
helm search repo datadog --versions
```

upgrade or install at a time
```
helm upgrade -i datadog/datadog <name of the helm chart>
```


helm show 
```
  all        
  chart      
  readme     
  values    
```

to get details of helm chart
```
helm show all  datadog_repo/datadog_helm_chart
helm show values  datadog_repo/datadog_helm_chart
```

History of  helm chart
```
helm history guna
```

Rollback to the previous version
```
helm rollback guna
``` 
versionName-status 

versionName-status

## Rollback to the particular version

```
helm rollback guna versionName
```

## helm upgrade with existing values
```
helm upgrade  -i datadog/datadog <name of the helm chart> 

--reuse-values  use the existing values

--atomic  if fail automatically rollback

--dry-run resources are not submitted to the k8s
```

## Use Existing varibles
```
.Chart
.Template
.Release
```
Functions: [Helm Functions](https://helm.sh/docs/chart_template_guide/function_list/)

Note:
```
Loops:
With will just place the contents as it is 
With range we can add fuctions or pipes on that
```

```
Only include we can add pipes, we cannot add to templates 
```
example: 
```
{{- include "zluri.selectorLabels" . | nindent 8 }}  --> works
{{- template "zluri.selectorLabels" . | nindent 8 }}  --> will not work

```
TO add the helm Chart to the Github follow blog [private github hosting with private Access token](https://blog.softwaremill.com/hosting-helm-private-repository-from-github-ff3fa940d0b7)



# Helm Usefull Links

[Helm hooks](https://helm.sh/docs/topics/charts_hooks/)

[Helm tips and trick](https://helm.sh/docs/howto/charts_tips_and_tricks/)

[Helm Build-in Objects](https://helm.sh/docs/chart_template_guide/builtin_objects/)

[Helm ignore file](helmignore)

[Donot do  mistakes](https://helm.sh/docs/chart_template_guide/yaml_techniques/)

[Tips and Tricks](https://helm.sh/docs/howto/charts_tips_and_tricks/)
