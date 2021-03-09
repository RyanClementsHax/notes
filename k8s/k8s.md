# k8s

## Get logs
```bash
kubectl logs -l app=my_app -c container -f
```

## Port forward
```bash
kubectl port-forward container 22022:22

kubectl port-forward deployment/your_deployment 22022:22 --context=your_k8s_context
```

## Reseal secrets
```bash
kubectl get secret your_secret -o yaml --context first_context \
	| kubeseal --cert path_to_cert --format yaml \
	| tr -s '\n' > output_file
```

## Seal and reseal a file
```bash
cat sealed_file \
	| kubeseal -o yaml --recovery-unseal --recovery-private-key <(kubectl get secret -n kube-system sealed-secrets-key -o yaml --context your_context) \
	| kubeseal --cert path_to_cert --format yaml | tr -s '\n' > output_file
```

## Restart deployment
```bash
kubectl rollout restart deploy/your_deployment --context your_context
```

## Delete replicaset
```bash
kubectl delete replicaset $NAME
```
- this can be useful when the old versions are still running and the new ones failed
- this will allow the new ones to take over without a completely new rollout

## [Zsh plugin shortcuts](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins/kubectl)