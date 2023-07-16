# aks-workload-identity

To create aks pod workload identity, do this:

1. Create service account
2. Create federated identity
3. Create pods
   
apiVersion: v1
kind: ServiceAccount
metadata:
  annotations:
    azure.workload.identity/client-id: "<client-id>"
  labels:
    azure.workload.identity/use: "true"
  name: sademo
  namespace: default
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: access-blob
spec:
  selector:
    matchLabels:
      app: access-blob
  template:
    metadata:
      labels:
        app: access-blob
        # aadpodidbinding: access-blob-id
        azure.workload.identity/use: "true"
    spec:
      serviceAccount: sademo
      containers:
        - name: azure-cli
          image:  mcr.microsoft.com/azure-cli
          command: ["/bin/bash", "-c", "sleep inf"]
    

az login --federated-token "$(cat $AZURE_FEDERATED_TOKEN_FILE)" --service-principal -u $AZURE_CLIENT_ID -t $AZURE_TENANT_ID

  
