# Cloudflare Kube Access

Connecteur Cloudflare Tunnel pour donner acces au reseau Kubernetes/LAN via Cloudflare Zero Trust.

Ce projet ne cree pas de tunnel Cloudflare. Il deploie uniquement des replicas `cloudflared` dans Kubernetes, avec un token de tunnel existant fourni en Secret Kubernetes.

## Objectif

Permettre l'acces prive aux machines du reseau :

- `k8s-master` : `192.168.1.165`
- `k8s-worker-1` : `192.168.1.69`
- `k8s-worker-2` : `192.168.1.61`
- `k8s-argocd` : `192.168.1.68`

Le routage prive `192.168.1.0/24` doit etre configure cote Cloudflare Zero Trust sur le tunnel existant.

## Secret requis

Ne commit jamais le token Cloudflare dans ce depot public.

Creer le secret dans le cluster :

```powershell
kubectl create namespace cloudflare-kube-access
kubectl -n cloudflare-kube-access create secret generic cloudflare-tunnel-token --from-literal=TUNNEL_TOKEN="cfut_..."
```

## Argo CD

Le dossier `argocd/` contient l'application Argo CD :

```powershell
kubectl apply -f argocd/application.yaml
```

## Connexion depuis ton PC

Une fois le tunnel connecte et la route privee approuvee/configuree dans Cloudflare Zero Trust, connecte ton poste a Cloudflare WARP, puis :

```powershell
ssh k8smaster@192.168.1.165
```

## Ressources

Le deploiement lance deux replicas `cloudflared` pour coller a ton setup Cloudflare existant, avec des limites faibles :

- requests : `25m CPU`, `32Mi RAM`
- limits : `150m CPU`, `128Mi RAM`
