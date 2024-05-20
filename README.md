# Encontros de Kubernetes do Módulo 10 do Curso de Engenharia da Computação (Inteli)

Este repositório possui os materiais de estudo, códigos e slides referentes a parte de Kubernetes do Módulo 10, cuja temática é **Aplicações Hiperescaláveis**.


## Encontros de Kubernetes

### Encontro 02 - Orquestração com Kubernetes ###

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