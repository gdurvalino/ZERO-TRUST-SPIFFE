# ZERO-TRUST-SPIFFE
Laboratório prático de introdução a Zero Trust com SPIFFE/SPIRE
# Laboratório Prático: Introdução a Zero Trust com SPIRE + Kubernetes (Kind)

## Demonstração em vídeo

[![Lab SPIFFE/SPIRE](https://img.youtube.com/vi/efgsrMvJUNc/0.jpg)](https://youtu.be/efgsrMvJUNc)

## Visão Geral

Este repositório documenta a implementação prática de conceitos de **Zero Trust Architecture (ZTA)** utilizando **SPIFFE** e **SPIRE** em um ambiente local baseado em **Kubernetes**, provisionado com **Kind (Kubernetes in Docker)**.

O objetivo deste laboratório foi compreender, de forma prática, como workloads modernos podem receber identidades criptográficas dinâmicas, reduzindo confiança implícita e fortalecendo a segurança entre serviços distribuídos.

---

# Objetivos do Laboratório

* Compreender os fundamentos da arquitetura Zero Trust
* Explorar identidades de workloads com SPIFFE
* Implementar SPIRE em Kubernetes
* Validar Node Attestation
* Entender emissão dinâmica de identidades
* Construir ambiente local de testes sem cloud pública
* Consolidar conhecimento por meio de documentação prática

---

# Conceitos Abordados

## Zero Trust

Modelo de segurança baseado no princípio:

> Nunca confie, sempre verifique.

Nenhum usuário, dispositivo ou workload recebe confiança automática apenas por estar na rede interna.

---

## SPIFFE

Padrão aberto para identidade segura entre workloads.

Exemplo de identidade:

```text
spiffe://control-plane.example/workload/app
```

---

## SPIRE

Principal implementação open source do padrão SPIFFE.

Responsável por:

* emitir identidades
* assinar certificados
* renovar credenciais
* realizar attestation
* manter confiança entre workloads

---

# Ambiente Utilizado

## Sistema Operacional

* Ubuntu Server 26.04

## Ferramentas Instaladas

* Docker
* kubectl
* Kind
* Git

---

# Estrutura do Laboratório

```text
Ubuntu Server VM
└── Docker
    └── Kind Cluster
        ├── SPIRE Server
        ├── SPIRE Agent
        └── SPIFFE CSI Driver
```

---

# Etapas Executadas

# 1. Instalação de Ferramentas

## kubectl

Ferramenta oficial para administração de clusters Kubernetes.

```bash
kubectl version --client
```

## Kind

Provisionamento de cluster Kubernetes local utilizando containers Docker.

```bash
kind version
```

## Docker

Container runtime necessário para execução do Kind.

---

# 2. Download do Repositório Oficial

```bash
git clone https://github.com/lftraining/LFS183x-resources.git
cd LFS183x-resources/lab-4-simple-spire
```

---

# 3. Troubleshooting Inicial

## Erro Encontrado

```text
docker executable file not found in $PATH
```

## Correção

Instalação do Docker e ajuste de PATH.

---

## Segundo Erro Encontrado

```text
permission denied while trying to connect to docker.sock
```

## Correção

Adição do usuário ao grupo Docker:

```bash
sudo usermod -aG docker labs
```

Logout/Login aplicado.

---

# 4. Criação do Cluster Kubernetes

```bash
kind create cluster --image kindest/node:v1.27.3
```

## Resultado

Cluster single-node provisionado com sucesso.

Validação:

```bash
kubectl get nodes
docker ps
```

Saída esperada:

```text
kind-control-plane   Ready
```

---

# 5. Análise dos Manifestos YAML

Estudo detalhado dos arquivos:

* spire-server.yaml
* spire-agent.yaml
* spire-csi.yaml

## Conceitos revisados:

* Namespace
* ServiceAccount
* RBAC
* Role / RoleBinding
* ClusterRole
* StatefulSet
* Service
* ConfigMap
* VolumeClaimTemplate

---

# 6. Implantação do SPIRE

Aplicação dos manifestos:

```bash
kubectl apply -f config/spire-server.yaml
kubectl apply -f config/spire-csi.yaml
kubectl apply -f config/spire-agent.yaml
```

---

# 7. Validação do Ambiente

```bash
kubectl get pods -n spire -w
```

Resultado:

```text
spiffe-csi-driver   Running
spire-agent         Running
spire-server        Running
```

---

# 8. Verificação de Node Attestation

Comando executado:

```bash
kubectl logs -n spire <spire-agent-pod> | grep "Node attestation"
```

Resultado:

```text
Node attestation was successful
```

---

# Interpretação Técnica

O SPIRE Agent precisou provar sua identidade ao SPIRE Server antes de ser confiado.

Esse processo representa claramente o modelo Zero Trust:

* nenhuma confiança implícita
* validação obrigatória
* identidade verificável

---

# Aprendizados Obtidos

## Técnicos

* Kubernetes local com Kind
* Operação básica com kubectl
* Estrutura YAML Kubernetes
* Containers com Docker
* Logs e troubleshooting
* Conceitos de PKI moderna
* Identidade de workloads

## Segurança

* Zero Trust aplicado
* SPIFFE IDs
* Trust Domains
* Node Attestation
* Redução de confiança implícita

---

# Principais Dificuldades Enfrentadas

* Conceitos abstratos iniciais de SPIFFE/SPIRE
* Dependência de múltiplas tecnologias simultâneas
* Troubleshooting Docker
* Interpretação de manifestos extensos

---

# Como os Problemas Foram Superados

* Estudo aprofundado de fundamentos
* Leitura linha a linha dos YAMLs
* Execução prática
* Interpretação de logs
* Documentação ativa

---

# Próximos Passos

* Criar Registration Entries
* Implantar workload autenticada
* Obter SVID dinâmico
* Validar TLS automático
* Evoluir para ambiente multi-node
* Integrar com service mesh

---

# Conclusão

Este laboratório demonstrou, de forma prática, como arquiteturas modernas substituem confiança baseada em perímetro por confiança baseada em identidade verificável.

A combinação de **SPIFFE + SPIRE + Kubernetes** representa um caminho sólido para ambientes cloud-native alinhados aos princípios de Zero Trust.

Além do conhecimento técnico, o laboratório reforçou competências importantes como troubleshooting, documentação, autonomia e aprendizado contínuo.

---

# Autor

Projeto desenvolvido como parte da jornada prática de estudos em Cibersegurança, Cloud e Infraestrutura Moderna.
