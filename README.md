# Encontros de Kubernetes do Módulo 10 do Curso de Engenharia da Computação (Inteli)

Este repositório possui os materiais de estudo, códigos e slides referentes a parte de Kubernetes do Módulo 10, cuja temática é **Aplicações Hiperescaláveis**.


## Encontros de Kubernetes
<details>
  <summary>Encontro 02 - Orquestração com Kubernetes</summary>

  <br/>

  **Objetivo do Encontro:**

  Pilares: 
  - Configurações com ConfigMaps e Secrets.
  - Volumes e Persistent Storage.
  - Liveness e Readiness Probes.
  - Namespaces.
  - Helm: gerenciador de pacotes para Kubernetes.
  - Estratégias de deployment: Blue/Green e Canary

  Prática/Discussão:
  - Implementação do cenário anterior (3 aplicações + mongo + redis + Load balancer + aplicações que consomem da queue do redis) usando Kubernetes em algum provedor de Nuvem.
  - Criação de Deployments para cada aplicação.
  - Uso de Services para exposição e comunicação entre aplicações.
  - Configuração de Persistent Volumes para MongoDB e Redis.
  - Introdução ao Helm e instalação de algumas aplicações usando charts.

  <br/>

  **Material de Auto-Estudo:**

  - [How To Use minikube for Local Kubernetes Development and Testing](https://www.digitalocean.com/community/tutorials/how-to-use-minikube-for-local-kubernetes-development-and-testing)
  - [O que é orquestração de containers?](https://www.redhat.com/pt-br/topics/containers/what-is-container-orchestration)
  - [Descubra como o Helm facilita a instalação de aplicações no Kubernetes](https://blog.4linux.com.br/o-gerenciador-de-pacotes-helm/)
  - [Helm | Quickstart Guide](https://helm.sh/docs/intro/quickstart/)

  <br/>

  **Materiais Complementares:**

  - [Kubernetes | Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)
  - [Kubernetes | ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)


  <br/>

  **Roteiro do Lab 01:**

  Este Lab irá apresentar o conceito de Deployment e do ReplicaSet dentro do Kubernetes.

  > **Dica:** É bom trabalhar em pequenos grupos para discutir como adaptar os Deployments para o Projeto do Módulo.

  > **Dica:** Certifique-se de que o Minikube esteja no ar antes de começar o Lab, na dúvida, execute o comando `minikube start` no seu Terminal.

  1. Vamos começar realizando um Deployment (se não está confiante de como funciona, veja os slides da instrução à respeito), neste caso, de uma Pod contendo o Nginx e com 3 réplicas. Segue o arquivo de Deployment de nome `nginx-deployment.yaml`.

  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: nginx-deployment
    labels:
      app: nginx
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: nginx
    template:
      metadata:
        labels:
          app: nginx
      spec:
        containers:
        - name: nginx
          image: nginx:1.14.2
          ports:
          - containerPort: 80
  ```
  > **Pergunta:** Analise esse YAML e verifique que configurações estão sendo realizadas por meio desse Deployment.

  > **Dica:** Salve todos os arquivos YAML em uma mesma pasta para ter todos os arquivos em um local só, vai facilitarr para usar o `kubectl` ao longo do Lab.

  2. Vamos executar o Deployment no `kubectl`, por meio do comando:

  ```
  kubectl apply -f nginx-deployment.yaml
  ```

  > **Dica:** Certifique-se que está executando o comando na pasta aonde o YAML se encontra.

  3. O Kubernetes agendará o processo de Deployment de 3 Pods do Nginx, para verificar se o agendamento foi criado e o andamento do processo, utilize o comando:

  ```
  kubectl get deployments
  ```

  4. O comando irá mostrar um output semelhante a esse, onde os Pods do Nginx ainda em processo de criação:

  ```
  NAME               READY   UP-TO-DATE   AVAILABLE   AGE
  hello-minikube     1/1     1            1           10d
  nginx-deployment   0/3     3            0           10s
  ```

  > **Pergunta:** Analise as colunas do output que obtiver, o que cada um quer dizer?

  5. É também possível visualizar o status do Rollout (Processo de Deployment) por meio do comando:

  ```
  kubectl rollout status deployment/nginx-deployment
  ```

  6. Um Deployment pode embutir uma configuração de um ReplicaSet, no exemplo acima temos um ReplicaSet com 3 Pods desejadas, é possível saber o status dos ReplicaSets utilizando o comando:

  ```
  kubectl get rs
  ```

  7. O resultado será algo assim:

  ```
  NAME                          DESIRED   CURRENT   READY   AGE
  hello-minikube-5c898d8489     1         1         1       10d
  nginx-deployment-77d8468669   3         3         3       48s
  ```

  > **Pergunta:** Analise as colunas do output que obtiver, o que cada um quer dizer?

  8. Para listar as Pods em execução no momento, é possível fazê-lo por meio desse comando:

  ```
  kubectl get pods --show-labels
  ```

  9. Com o seguinte resultado:

  ```
  NAME                                READY   STATUS    RESTARTS      AGE
  hello-minikube-5c898d8489-kxg55     1/1     Running   1 (10d ago)   10d
  nginx-deployment-77d8468669-hqwh6   1/1     Running   0             13m
  nginx-deployment-77d8468669-ltjbd   1/1     Running   0             13m
  nginx-deployment-77d8468669-zlqk6   1/1     Running   0             13m
  ```

  10. Uma última etapa para essa parte do Lab é o cenário onde precisamos atualizar a imagem de uma Pod (no caso, do Nginx). Isso é facilmente obtido por meio do comando:

  ```
  kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1
  ```

  11. Que dará um output:

  ```
  deployment.apps/nginx-deployment image updated
  ```

  12. Podemos ver o andamento da atualização verificando o processo de Rollout, com o comando:

  ```
  kubectl rollout status deployment/nginx-deployment
  ```

  13. Que mostrará uma saída nessa linha, conforme as Pods são recicladas com a nova versão do Nginx:

  ```
  Waiting for deployment "nginx-deployment" rollout to finish: 2 out of 3 new replicas have been updated...
  Waiting for deployment "nginx-deployment" rollout to finish: 2 out of 3 new replicas have been updated...
  Waiting for deployment "nginx-deployment" rollout to finish: 2 out of 3 new replicas have been updated...
  Waiting for deployment "nginx-deployment" rollout to finish: 1 old replicas are pending termination...
  Waiting for deployment "nginx-deployment" rollout to finish: 1 old replicas are pending termination...
  deployment "nginx-deployment" successfully rolled out
  ```

  14. E voilá, o Deployment da nova versão do Nginx foi realizado com sucesso! :blush:

  > **Discussão:** Como dito na Instrução, um Deployment pode conter nele uma configuração de um ReplicaSet, existe alguma situação onde seria importante ter apenas a configuração do ReplicaSet como um Kind específico e separado do seu Deployment? Por exemplo, uma configuração assim? (Repare que o Kind é do tipo ReplicaSet)

  ```
  apiVersion: apps/v1
  kind: ReplicaSet
  metadata:
    name: frontend
    labels:
      app: guestbook
      tier: frontend
  spec:
    # modify replicas according to your case
    replicas: 3
    selector:
      matchLabels:
        tier: frontend
    template:
      metadata:
        labels:
          tier: frontend
      spec:
        containers:
        - name: php-redis
          image: us-docker.pkg.dev/google-samples/containers/gke/gb-frontend:v5
  ```

  > **Dica:** Quando quiser deletar o seu Deployment, basta usar o comando `kubectl delete deployment nginx-deployment` para fazer o Kubernetes remover as Pods relacionadas a esse Deployment.

  <br/>

  **Roteiro do Lab 02:**

  Este Lab irá apresentar o conceito de Dashboard do Minikube e do Service dentro do Kubernetes.

  > **Dica:** Esse Lab é importante para quem não teve tempo de configurar o ambiente na última instrução ou para quem está com dificuldades de compreender o funcionamento básico do Minikube.

  1. Vamos inicializar o Dashboard do Kubernetes, onde podemos visualizar as métricas básicas de monitoramento e ver o status do ambiente, o comando para a inicialização é o:

  ```
  minikube dashboard
  ```

  > **Dica:** Dê uma olhada nos itens dentro do Dashboard, existem inúmeros componentes que podemos configurar em nosso ambiente de orquestração.

  2. Agora vamos fazer o deploy de um Service, que serve como uma camada de desacoplamento entre os usuários e as Pods, primeiro precisamos subir uma ou mais Pods antes de expor o serviço:

  ```
  kubectl create deployment hello-minikube --image=kicbase/echo-server:1.0
  kubectl expose deployment hello-minikube --type=NodePort --port=8080
  ```

  3. Para verificar se o Service foi deployado corretamente, basta executar o comando:

  ```
  kubectl get services hello-minikube
  ```

  4. Podemos agora acessar essa Pod indiretamente por meio do Service que acabamos de criar, uma das maneiras é pedir para o Minikube abrir o browser no endpoint criado pelo Service:

  ```
  minikube service hello-minikube
  ```

  5. Deverá aparecer um output no browser com cabeçalho semelhante a esse:

  ```
  Request served by hello-minikube-5c898d8489-kxg55

  HTTP/1.1 GET /
  ```
</details>
<details>
  <summary>Encontro 04 - Gerenciamento e Soluções Autogerenciadas</summary>
  <br/>

  **Objetivo do Encontro:**

  Pilares: 
  - Escalabilidade em Kubernetes: Horizontal Pod Autoscaling.
  - Realizando a configuração e Deploy do via Kubernetes na Nuvem.
  - Monitoramento e Logging: Cloud especific.
  - Atualizações e Rollbacks.

  Prática/Discussão:
  - Preparação para o deploy de todo o sistema que está no MiniKube para a Nuvem.
  - Configuração do Horizontal Pod Autoscaling para uma das aplicações.
  - Integração do cluster com o sistema de monitoramento do provedor de Nuvem para monitoramento e logs.
  - Demonstração de atualização e rollback usando Deployments.

  <br/>

  **Material de Auto-Estudo:**

  - [Platform Architecture](https://www.oreilly.com/library/view/open-source-data/9781492074281/ch04.html)
  - [OpenShift Container Platform architecture](https://docs.openshift.com/container-platform/4.15/architecture/architecture.html)
  - [Horizontal Pod Autoscaling](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
  - [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
  - [Configure a Pod to Use a PersistentVolume for Storage](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/)
  - [Guide to Understanding Kubernetes Deployments](https://konghq.com/blog/learning-center/guide-to-understanding-kubernetes-deployments)
  - [Building a Kubernetes Application with Amazon EKS](https://medium.com/edureka/amazon-eks-ac646c23abf8)
</details>