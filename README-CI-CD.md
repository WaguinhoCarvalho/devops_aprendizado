## Integração Contínua CI/CD no GitHub Actions
Kubectl get nodes - verificar cluster funcionando
kubectl get all - aplicação vazia

## Abrir projeto no vs code testar deploy no cluster:
kubectl apply -f 02-review.deployment.yaml 02-review/k8s/deployment.yaml

kubectl get all

## testar no navegador com o IP external 
Sera exibido no navegador

## Validando processo manual
kubectl delete -f 02-review/k8s/deployment.yaml

## Criação da automação
No GitHub ir em Action-> workflow

Dicas pra montar o workflow - navegador marketplace git hub actions
https://github.com/marketplace

Configurações do repositório do GitHub
Secrets/Variables -> new secret -> inserir nome da variável

## Gerar IP externo:
 cmd: LoadBalancer
 wath "kubectl get pods"