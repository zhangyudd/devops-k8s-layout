# nginx-ingress
## 项目地址
[传送门](https://github.com/kubernetes/ingress-nginx/tree/nginx-0.30.0 "nignx-ingress项目地址")
## 部署步骤
下方README.md
> See /docs/deploy.
>> ingress-nginx/docs/deploy/index.md
---
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.30.0/deploy/static/mandatory.yaml

kubectl apply -f ingress-service.yaml

如需要修改nginx-ingress配置参数
`nginx-config.yaml ` 创建配置文件，配置全局生效

---
`nginx-deploy.yaml` 部署nginx应用，包含deployment，service，ingress

annotations配置可以针对某个应用单独配置
* 保持session
  * 部署文件中配置
    ``` 
    annotations:
    nginx.ingress.kubernetes.io/affinity: cookie
    nginx.ingress.kubernetes.io/session-cookie-hash: sha1
    nginx.ingress.kubernetes.io/session-cookie-name: route
    ```

---
`tcp-sv-config.yaml` 转发tcp端口应用
`tcp-kuard-dome.yaml` tcp应用实例
注意：需要在ingress-service中开启对应的端口