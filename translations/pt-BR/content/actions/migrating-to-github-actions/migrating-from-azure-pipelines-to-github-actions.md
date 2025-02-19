---
title: Migrar do Azure Pipelines para o GitHub Actions
intro: 'O {% data variables.product.prodname_actions %} e o Azure Pipelines compartilham várias semelhanças de configuração, o que torna a migração para {% data variables.product.prodname_actions %} relativamente simples.'
redirect_from:
  - /actions/learn-github-actions/migrating-from-azure-pipelines-to-github-actions
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
  ghec: '*'
type: tutorial
topics:
  - Azure Pipelines
  - Migration
  - CI
  - CD
shortTitle: Migrate from Azure Pipelines
ms.openlocfilehash: 5890afb4c0f0e8eae6b5981a39e68f272bff7440
ms.sourcegitcommit: 47bd0e48c7dba1dde49baff60bc1eddc91ab10c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2022
ms.locfileid: '145096125'
---
{% data reusables.actions.enterprise-beta %} {% data reusables.actions.enterprise-github-hosted-runners %}

## Introdução

O Azure Pipelines e o {% data variables.product.prodname_actions %} permitem criar fluxos de trabalho que automaticamente criam, testam, publicam, lançam e implantam códigos. O Azure Pipelines e o {% data variables.product.prodname_actions %} compartilham algumas similaridades na configuração do fluxo de trabalho:

- Os arquivos de configuração do fluxo de trabalho são gravados YAML e armazenados no repositório do código.
- Os fluxos de trabalho incluem um ou mais trabalhos.
- Os trabalhos incluem uma ou mais etapas ou comandos individuais.
- É possível reutilizar e compartilhar novamente etapas ou tarefas com a comunidade.

Para obter mais informações, confira "[Conceitos básicos do {% data variables.product.prodname_actions %}](/actions/getting-started-with-github-actions/core-concepts-for-github-actions)".

## Principais diferenças

Ao migrar do Azure Pipelines, considere as diferenças a seguir:

- O Azure Pipelines dá suporte a um _editor clássico_ herdado, que permite que você defina a configuração de CI em um editor de GUI em vez de criar a definição do pipeline em um arquivo YAML. O {% data variables.product.prodname_actions %} usa arquivos YAML para definir fluxos de trabalho e não é compatível com um editor gráfico.
- O Azure Pipelines permite que você omita algumas estruturas nas definições de trabalho. Por exemplo, se você tem apenas um único trabalho, não é necessário definir o trabalho. Você precisa definir apenas as etapas. O {% data variables.product.prodname_actions %} requer configuração explícita e não é possível omitir a estrutura do YAML.
- O Azure Pipelines dá suporte a _fases_ definidas no arquivo YAML, que pode ser usado para criar fluxos de trabalho de implantação. O {% data variables.product.prodname_actions %} exige que você que você separe as etapas em arquivos separados do fluxo de trabalho do YAML.
- É possível selecionar os agentes de criação locais do Azure Pipelines com recursos. {% data variables.product.prodname_actions %} executores auto-hospedados podem ser selecionados com etiquetas.

## Migrar trabalhos e etapas

Os trabalhos e as etapas no Azure Pipelines são muito semelhantes a trabalhos e etapas do {% data variables.product.prodname_actions %}. Em ambos os sistemas, os trabalhos têm as características a seguir:

* Os trabalhos contêm uma série de etapas executadas em sequência.
* Os trabalhos são executados em máquinas virtuais separadas ou em contêineres separados.
* Por padrão, os trabalhos executados em paralelo, mas podem ser configuradas para serem executados em sequência.

## Migrar etapas de script

Você pode executar um script ou um comando de shell como uma etapa em um fluxo de trabalho. No Azure Pipelines, as etapas de script podem ser especificadas por meio da chave `script` ou com as chaves `bash`, `powershell` ou `pwsh`. Os scripts também podem ser especificados como uma entrada para a [tarefa do Bash](https://docs.microsoft.com/azure/devops/pipelines/tasks/utility/bash?view=azure-devops) ou a [tarefa do PowerShell](https://docs.microsoft.com/azure/devops/pipelines/tasks/utility/powershell?view=azure-devops).

No {% data variables.product.prodname_actions %}, todos os scripts são especificados por meio da chave `run`. Para selecionar um shell específico, você pode especificar a chave `shell` ao fornecer o script. Para obter mais informações, confira "[Sintaxe de fluxo de trabalho do {% data variables.product.prodname_actions %}](/actions/reference/workflow-syntax-for-github-actions#jobsjob_idstepsrun)".

Abaixo, há um exemplo da sintaxe para cada sistema:

<table class="d-block">
<tr>
<th>
Azure Pipelines
</th>
<th>
{% data variables.product.prodname_actions %}
</th>
</tr>
<tr>
<td class="d-table-cell v-align-top">
{% raw %}
```yaml
jobs:
  - job: scripts
    pool:
      vmImage: 'windows-latest'
    steps:
      - script: echo "This step runs in the default shell"
      - bash: echo "This step runs in bash"
      - pwsh: Write-Host "This step runs in PowerShell Core"
      - task: PowerShell@2
        inputs:
          script: Write-Host "This step runs in PowerShell"
```
{% endraw %}
</td>
<td class="d-table-cell v-align-top">
{% raw %}
```yaml
jobs:
  scripts:
    runs-on: windows-latest
    steps:
      - run: echo "This step runs in the default shell"
      - run: echo "This step runs in bash"
        shell: bash
      - run: Write-Host "This step runs in PowerShell Core"
        shell: pwsh
      - run: Write-Host "This step runs in PowerShell"
        shell: powershell
```
{% endraw %}
</td>
</tr>
</table>

## Diferenças na manipulação de erros de script

No Azure Pipelines, os scripts podem ser configurados para erro se uma saída é enviada para `stderr`. {% data variables.product.prodname_actions %} não suporta esta configuração.

{% data variables.product.prodname_actions %} configura shells como "falha rápida" sempre que possível, que interrompe o script imediatamente caso um dos comandos em um script saia com um código de erro. Em contrapartida, o Azure Pipelines exige uma configuração explícita para sair imediatamente de um erro. Para obter mais informações, confira "[Sintaxe de fluxo de trabalho do {% data variables.product.prodname_actions %}](/actions/reference/workflow-syntax-for-github-actions#exit-codes-and-error-action-preference)".

## Diferenças no shell-padrão no Windows

No Azure Pipelines, o shell padrão para scripts nas plataformas Windows é o shell de comando (_cmd.exe_). Em {% data variables.product.prodname_actions %}, o shell-padrão para os scripts nas plataformas do Windows é o PowerShell. O PowerShell tem várias diferenças em comandos integrados, expansão de variáveis e controle de fluxo.

Se você estiver executando um comando simples, você poderá executar um script do shell do comando no PowerShell sem alterações. No entanto, na maioria dos casos, você deverá atualizar seu script com sintaxe PowerShell ou instruir {% data variables.product.prodname_actions %} para executar o script com a shell de comando em vez de executar o PowerShell. Faça isso especificando `shell` como `cmd`.

Abaixo, há um exemplo da sintaxe para cada sistema:

<table class="d-block">
<tr>
<th>
Azure Pipelines
</th>
<th>
{% data variables.product.prodname_actions %}
</th>
</tr>
<tr>
<td class="d-table-cell v-align-top">
{% raw %}
```yaml
jobs:
  - job: run_command
    pool:
      vmImage: 'windows-latest'
    steps:
      - script: echo "This step runs in CMD on Windows by default"
```
{% endraw %}
</td>
<td class="d-table-cell v-align-top">
{% raw %}
```yaml
jobs:
  run_command:
    runs-on: windows-latest
    steps:
      - run: echo "This step runs in PowerShell on Windows by default"
      - run: echo "This step runs in CMD on Windows explicitly"
        shell: cmd
```
{% endraw %}
</td>
</tr>
</table>

Para obter mais informações, confira "[Sintaxe de fluxo de trabalho do {% data variables.product.prodname_actions %}](/actions/reference/workflow-syntax-for-github-actions#using-a-specific-shell)".

## Migrar condicionais e sintaxe de expressão

O Azure Pipelines e {% data variables.product.prodname_actions %} podem executar as etapas condicionalmente. No Azure Pipelines, as expressões condicionais são especificadas por meio da chave `condition`. No {% data variables.product.prodname_actions %}, as expressões condicionais são especificadas por meio da chave `if`.

O Azure Pipelines usa funções dentro de expressões para executar as etapas condicionalmente. Em contrapartida, {% data variables.product.prodname_actions %} usa uma notação de infixo. Por exemplo, você precisa substituir a função `eq` no Azure Pipelines pelo operador `==` no {% data variables.product.prodname_actions %}.

Abaixo, há um exemplo da sintaxe para cada sistema:

<table class="d-block">
<tr>
<th>
Azure Pipelines
</th>
<th>
{% data variables.product.prodname_actions %}
</th>
</tr>
<tr>
<td class="d-table-cell v-align-top">
{% raw %}
```yaml
jobs:
  - job: conditional
    pool:
      vmImage: 'ubuntu-latest'
    steps:
      - script: echo "This step runs with str equals 'ABC' and num equals 123"
        condition: and(eq(variables.str, 'ABC'), eq(variables.num, 123))
```
{% endraw %}
</td>
<td class="d-table-cell v-align-top">
{% raw %}
```yaml
jobs:
  conditional:
    runs-on: ubuntu-latest
    steps:
      - run: echo "This step runs with str equals 'ABC' and num equals 123"
        if: ${{ env.str == 'ABC' && env.num == 123 }}
```
{% endraw %}
</td>
</tr>
</table>

Para obter mais informações, confira "[Expressões](/actions/learn-github-actions/expressions)".

## Dependências entre trabalhos

Tanto o Pipelines Azure quanto o {% data variables.product.prodname_actions %} permitem que você defina as dependências para um trabalho. Em ambos os sistemas, os trabalhos são executados em paralelo por padrão, mas as dependências do trabalho podem ser especificadas explicitamente. No Azure Pipelines, isso é feito com a chave `dependsOn`. No {% data variables.product.prodname_actions %}, isso é feito com a chave `needs`.

Abaixo, há um exemplo da sintaxe para cada sistema. Os fluxos de trabalho iniciam um primeiro trabalho chamado `initial` e, quando esse trabalho for concluído, dois trabalhos chamados `fanout1` e `fanout2` serão executados. Por fim, quando esses trabalhos forem concluídos, o trabalho `fanin` será executado.

<table class="d-block">
<tr>
<th>
Azure Pipelines
</th>
<th>
{% data variables.product.prodname_actions %}
</th>
</tr>
<tr>
<td class="d-table-cell v-align-top">
{% raw %}
```yaml
jobs:
  - job: initial
    pool:
      vmImage: 'ubuntu-latest'
    steps:
      - script: echo "This job will be run first."
  - job: fanout1
    pool:
      vmImage: 'ubuntu-latest'
    dependsOn: initial
    steps:
      - script: echo "This job will run after the initial job, in parallel with fanout2."
  - job: fanout2
    pool:
      vmImage: 'ubuntu-latest'
    dependsOn: initial
    steps:
      - script: echo "This job will run after the initial job, in parallel with fanout1."
  - job: fanin:
    pool:
      vmImage: 'ubuntu-latest'
    dependsOn: [fanout1, fanout2]
    steps:
      - script: echo "This job will run after fanout1 and fanout2 have finished."
```
{% endraw %}
</td>
<td class="d-table-cell v-align-top">
{% raw %}
```yaml
jobs:
  initial:
    runs-on: ubuntu-latest
    steps:
      - run: echo "This job will be run first."
  fanout1:
    runs-on: ubuntu-latest
    needs: initial
    steps:
      - run: echo "This job will run after the initial job, in parallel with fanout2."
  fanout2:
    runs-on: ubuntu-latest
    needs: initial
    steps:
      - run: echo "This job will run after the initial job, in parallel with fanout1."
  fanin:
    runs-on: ubuntu-latest
    needs: [fanout1, fanout2]
    steps:
      - run: echo "This job will run after fanout1 and fanout2 have finished."
```
{% endraw %}
</td>
</tr>
</table>

Para obter mais informações, confira "[Sintaxe de fluxo de trabalho do {% data variables.product.prodname_actions %}](/actions/reference/workflow-syntax-for-github-actions#jobsjob_idneeds)".

## Migrar tarefas para ações

O Azure Pipelines usa _tarefas_, que são componentes do aplicativo que podem ser reutilizados em vários fluxos de trabalho. O {% data variables.product.prodname_actions %} usa _ações_, que podem ser usadas para realizar tarefas e personalizar seu fluxo de trabalho. Em ambos os sistemas, é possível especificar o nome da tarefa ou ação a executar, junto com quaisquer entradas necessárias como pares chave/valor.

Abaixo, há um exemplo da sintaxe para cada sistema:

<table>
<tr>
<th>
Azure Pipelines
</th>
<th>
{% data variables.product.prodname_actions %}
</th>
</tr>
<tr>
<td class="d-table-cell v-align-top">
{% raw %}
```yaml
jobs:
  - job: run_python
    pool:
      vmImage: 'ubuntu-latest'
    steps:
      - task: UsePythonVersion@0
        inputs:
          versionSpec: '3.7'
          architecture: 'x64'
      - script: python script.py
```
{% endraw %}
</td>
<td class="d-table-cell v-align-top">

```yaml
jobs:
  run_python:
    runs-on: ubuntu-latest
    steps:
      - uses: {% data reusables.actions.action-setup-python %}
        with:
          python-version: '3.7'
          architecture: 'x64'
      - run: python script.py
```

</td>
</tr>
</table>

Encontre ações que você pode usar no seu fluxo de trabalho no [{% data variables.product.prodname_marketplace %}](https://github.com/marketplace?type=actions) ou crie suas ações. Para obter mais informações, confira "[Como criar ações](/actions/creating-actions)".
