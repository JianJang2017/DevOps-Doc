### 6、Argo Rollouts

```shell
mkdir -p ~/argo-rollouts-yml

kubectl create ns argo-rollouts
```

```shell
# cd ~/argo-rollouts-yml && wget https://github.com/argoproj/argo-rollouts/releases/download/v1.7.1/install.yaml

# cd ~/argo-rollouts-yml && wget https://github.com/argoproj/argo-rollouts/releases/download/v1.7.1/dashboard-install.yaml
```

```shell
cd ~/argo-rollouts-yml && wget https://gitee.com/kubelsp/upload/raw/master/argo-rollouts/v1.7.1/install.yaml

cd ~/argo-rollouts-yml && wget https://gitee.com/kubelsp/upload/raw/master/argo-rollouts/v1.7.1/dashboard-install.yaml
```

```shell
sed -i 's#quay.io/argoproj/argo-rollouts:v1.7.1#ccr.ccs.tencentyun.com/huanghuanhui/argo-rollouts:v1.7.1#g' ~/argo-rollouts-yml/install.yaml

sed -i 's#quay.io/argoproj/kubectl-argo-rollouts:v1.7.1#ccr.ccs.tencentyun.com/huanghuanhui/argo-rollouts:dashboard-v1.7.1#g' ~/argo-rollouts-yml/dashboard-install.yaml 
```

```shell
kubectl apply -n argo-rollouts -f ~/argo-rollouts-yml/install.yaml

kubectl apply -n argo-rollouts -f ~/argo-rollouts-yml/dashboard-install.yaml
```

```shell
# curl -LO https://github.com/argoproj/argo-rollouts/releases/download/v1.7.1/kubectl-argo-rollouts-linux-amd64

# chmod +x ./kubectl-argo-rollouts-linux-amd64

# mv ./kubectl-argo-rollouts-linux-amd64 /usr/local/bin/kubectl-argo-rollouts

# kubectl argo rollouts version
```

```shell
wget https://gitee.com/kubelsp/upload/raw/master/argo-rollouts/v1.7.1/kubectl-argo-rollouts-linux-amd64

chmod +x ./kubectl-argo-rollouts-linux-amd64

mv ./kubectl-argo-rollouts-linux-amd64 /usr/local/bin/kubectl-argo-rollouts

kubectl argo rollouts version
```

```shell
cat > ~/argo-rollouts-yml/argo-rollouts-dashboard-Ingress.yml << 'EOF'
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argo-rollouts-dashboard-ingress
  namespace: argo-rollouts
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: 'true'
    nginx.ingress.kubernetes.io/proxy-body-size: '4G'
    nginx.ingress.kubernetes.io/auth-type: basic
    nginx.ingress.kubernetes.io/auth-secret: argo-rollouts-dashboard-auth
spec:
  ingressClassName: nginx
  rules:
  - host: argo-rollouts-dashboard.openhhh.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: argo-rollouts-dashboard
            port:
              number: 3100

  tls:
  - hosts:
    - argo-rollouts-dashboard.openhhh.com
    secretName: argo-rollouts-dashboard-ingress-tls
EOF
```

```shell
yum -y install httpd-tools

$ htpasswd -nb admin Admin@2024 > ~/argo-rollouts-yml/auth

kubectl create secret generic argo-rollouts-dashboard-auth --from-file=/root/argo-rollouts-yml/auth -n argo-rollouts
```

```shell
kubectl create secret -n argo-rollouts \
tls argo-rollouts-dashboard-ingress-tls \
--key=/root/ssl/openhhh.com.key \
--cert=/root/ssl/openhhh.com.pem
```

```shell
kubectl apply -f ~/argo-rollouts-yml/argo-rollouts-dashboard-Ingress.yml
```

> 访问地址：https://argo-rollouts-dashboard.openhhh.com
>
> 用户密码：admin、Admin@2024
