# Projeto: Implementação de um Fluxo GitOps na Prática

## 1. Visão Geral do Projeto

Este documento detalha a execução de um projeto prático para implantar aplicações em um ambiente Kubernetes. O principal objetivo foi utilizar a metodologia **GitOps**, onde o repositório Git serve como a única fonte de verdade para definir o estado desejado da infraestrutura e das aplicações, garantindo um fluxo de entrega automatizado, seguro e auditável.

## 2. Ferramentas e Conceitos-Chave

* **Kubernetes:** Orquestrador de contêineres responsável por gerenciar a execução, escalonamento e resiliência das aplicações.
* **Git (com GitHub):** Sistema de controle de versão utilizado para armazenar tanto o código-fonte quanto os manifestos declarativos da aplicação.
* **GitOps:** Metodologia que utiliza o Git como fonte da verdade. Alterações na aplicação ou infraestrutura são feitas via `git push`.
* **ArgoCD:** Ferramenta de GitOps que sincroniza o estado do cluster Kubernetes com o que está definido no repositório Git.
* **Helm:** Gerenciador de pacotes para o Kubernetes. Ele agrupa todos os recursos de uma aplicação em um pacote reutilizável chamado "Chart".
* **Rancher Desktop:** Software para executar um cluster Kubernetes em um ambiente de desenvolvimento local.

## 3. Execução do Projeto

O projeto foi dividido em um setup inicial seguido de tarefas adicionais para aprofundar os conhecimentos.

### Etapa 1: Setup Inicial - Aplicação "Online Boutique"

* **Ação:** Implantação de uma aplicação de microserviços (`Online Boutique`) a partir de um repositório público.
* **Como foi feito:**
    1.  O ArgoCD foi instalado no cluster Kubernetes local.
    2.  Uma aplicação foi criada no ArgoCD, apontando para um repositório no GitHub que continha os manifestos YAML da aplicação.
    3.  O ArgoCD sincronizou os manifestos, e a aplicação foi implantada com sucesso.
    4.  O acesso ao frontend foi realizado via `kubectl port-forward`.

### Etapa 2: Teste de Escalabilidade

* **Ação:** Um teste de escalabilidade foi realizado para validar o fluxo GitOps.
* **Como foi feito:** O número de réplicas do `Deployment` do serviço `loadgenerator` foi alterado de `1` para `3` diretamente no arquivo de manifesto. A alteração foi enviada ao GitHub com `git push`.
* **Resultado:** O ArgoCD detectou a mudança e, sem intervenção manual, escalou o número de pods para 3, provando a eficácia do ciclo de automação.

### Etapa 3: Tarefa Adicional - Acesso a Repositório Privado

* **Ação:** A segurança do projeto foi aprimorada migrando o repositório do GitHub de público para privado e configurando o acesso via SSH.
* **Como foi feito:**
    1.  A visibilidade do repositório foi alterada para "Private" no GitHub.
    2.  Um par de chaves SSH foi gerado. A chave pública foi adicionada como "Deploy Key" no GitHub, e a chave privada foi configurada como um segredo de repositório no ArgoCD.
    3.  A URL do repositório na aplicação do ArgoCD foi atualizada para o formato SSH (`git@github.com:...`).
* **Resultado:** O ArgoCD se conectou com sucesso ao repositório privado, mantendo o deploy seguro e automatizado. Em caso de vazamento acidental da chave privada, foi realizado o procedimento de limpeza do histórico do Git e a revogação da chave comprometida.

### Etapa 4: Tarefa Adicional - Implantação com Helm Chart

* **Ação:** Criação de um "Chart" Helm para empacotar e implantar uma nova aplicação (Nginx).
* **Como foi feito:**
    1.  A ferramenta de linha de comando `helm` foi instalada.
    2.  O comando `helm create nginx-chart` foi utilizado para gerar a estrutura padrão do chart.
    3.  O arquivo `values.yaml` foi editado para especificar a imagem `nginx:latest`.
    4.  A pasta `templates/` foi simplificada para conter apenas os recursos essenciais (`deployment.yaml` e `service.yaml`).
    5.  O novo chart foi adicionado ao repositório Git de forma segura.
* **Próximo Passo:** Criar uma nova aplicação no ArgoCD para implantar este chart no cluster.

## 4. Evidências

* **Aplicações Rodando**
  ![](imagens/aplicacoes-funcionando)
* **Teste de Escalabilidade (`loadgenerator`)**


