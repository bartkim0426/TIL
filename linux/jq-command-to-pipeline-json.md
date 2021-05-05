


ELB=$(kc get svc ecsdemo-frontend -o json | jq -r '.status.loadBalancer.ingress[].hostname)'


