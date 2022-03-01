# jq command to pipeline json


https://www.44bits.io/ko/post/cli_json_processor_jq_basic_syntax

```
ELB=$(kc get svc ecsdemo-frontend -o json | jq -r '.status.loadBalancer.ingress[].hostname)'
```

