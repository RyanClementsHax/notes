---
layout: page
title: How To
permalink: /k8s/how-to
---

## Get logs
```shell
kubectl logs -l app=my_app -c container -f
```

## Port forward
```shell
kubectl port-forward container 22022:22

kubectl port-forward deployment/your_deployment 22022:22 --context=your_k8s_context
```

## Reseal secrets
```shell
kubectl get secret your_secret -o yaml --context first_context \
	| kubeseal --cert path_to_cert --format yaml \
	| tr -s '\n' > output_file
```

## Seal and reseal a file
```shell
cat sealed_file \
	| kubeseal -o yaml --recovery-unseal --recovery-private-key <(kubectl get secret -n kube-system sealed-secrets-key -o yaml --context your_context) \
	| kubeseal --cert path_to_cert --format yaml | tr -s '\n' > output_file
```

## Restart deployment
```shell
kubectl rollout restart deploy/your_deployment --context your_context
```