#### 1. Install Sealed Secret Controller:
```bash
kubectl apply -f https://github.com/bitnami-labs/sealed-secrets/releases/download/<VERSION>/controller.yaml
```

#### 2. Create a file named secret.yaml (this file should remain local only for conversion):
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-app-secret
  namespace: default
type: Opaque
data:
  # The values here must be base64 encoded.
  username: dXNlcm5hbWU=      # "username" encoded
  password: cGFzc3dvcmQ=      # "password" encoded
```
##### To base64-encode your values:
```bash
echo -n 'your-value' | base64
```

#### 3. Install Sealed Secret Kubeseal CLI
Download from the Sealed Secrets releases page.

#### 4. Seal the Secret
Use the Kubeseal CLI to encrypt your Secret into a SealedSecret. Run:
```bash
kubeseal --controller-namespace kube-system --controller-name sealed-secrets \
  --format yaml < secret.yaml > sealedsecret.yaml
```
Open the generated sealedsecret.yaml and note the encrypted values. It should look like this:
```yaml
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  name: my-app-secret
  namespace: default
spec:
  encryptedData:
    username: AgBjE+...   # Encrypted value for username
    password: AdIC1H...   # Encrypted value for password
```

#### 5. Add Encrypted Values to Your values.yaml

```yaml
# values.yaml
data:
    username: "AgBjE+..."   # Copy the encrypted string from sealedsecret.yaml
    password: "AdIC1H..."   # Copy the encrypted string from sealedsecret.yaml
```

#### 6. Create a Templated SealedSecret Manifest
```yaml
{{- if .Values.secret.sealed.enabled }}
apiVersion: bitnami.com/v1alpha1
kind: SealedSecret
metadata:
  name: {{ .Values.secret.sealed.name | quote }}
  namespace: {{ .Values.secret.sealed.namespace | quote }}
spec:
  encryptedData:
    username: {{ .Values.secret.sealed.data.username | quote }}
    password: {{ .Values.secret.sealed.data.password | quote }}
{{- end }}
```

#### 7. Deploy Your Helm Chart
```bash
helm upgrade --install my-app ./chart-folder --namespace default
```
After deployment, verify that the Kubernetes Secret was created:
```bash
kubectl get secret my-app-secret -n default
```

#### 8. Updating the Secrets
1. Update your plain secret (if necessary) and run kubeseal to generate new encrypted values.
2. Replace the corresponding values in the `values.yaml` file.
3. Re-deploy your Helm chart with the updated values.