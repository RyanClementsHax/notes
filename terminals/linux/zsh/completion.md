# Completion

## Custom k8s alias completion example

    ```bash
    kubesecretdecodetemplate="{{\"-----------------------------------\n\"}}{{.metadata.name}}{{\"\n-----------------------------------\n\"}}{{range \$k,\$v := .data}}{{printf \"%s: \" \$k}}{{if not \$v}}{{\$v}}{{else}}{{\$v | base64decode}}{{end}}{{\"\n\n\"}}{{end}}"
    alias decode="kubectl get secret -o go-template='{{if .items}}{{range .items}}$kubesecretdecodetemplate{{\"\n\"}}{{end}}{{else}}$kubesecretdecodetemplate{{end}}'"
    complete -F __start_kubectl decode
    ```
