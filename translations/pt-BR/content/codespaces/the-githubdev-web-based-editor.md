---
title: O editor github.dev baseado na web
intro: 'Use o github.dev {% data variables.product.prodname_serverless %} do seu repositório ou faça uma solicitação de pull para criar e confirmar as alterações.'
versions:
  feature: githubdev-editor
type: how_to
miniTocMaxHeadingLevel: 3
topics:
  - Codespaces
  - Visual Studio Code
  - Developer
shortTitle: Web-based editor
redirect_from:
  - /codespaces/developing-in-codespaces/web-based-editor
ms.openlocfilehash: 3fce192c2b0e890b2213dfd8f5f18dad1dc7fa05
ms.sourcegitcommit: 47bd0e48c7dba1dde49baff60bc1eddc91ab10c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2022
ms.locfileid: '147431853'
---
{% note %}

**Observação:** atualmente, o github.dev {% data variables.product.prodname_serverless %} está em versão prévia beta. Envie comentários [em nossas Discussões](https://github.com/community/community/discussions/categories/general).

{% endnote %}

## Sobre o {% data variables.product.prodname_serverless %}

O {% data variables.product.prodname_serverless %} introduz uma experiência leve de edição, que é executada inteiramente no seu navegador. Com o {% data variables.product.prodname_serverless %}, você pode navegar por arquivos e repositórios de código-fonte do {% data variables.product.prodname_dotcom %}, bem como efetuar e fazer comite das alterações de código. É possível abrir qualquer repositório, bifurcação ou pull request no editor.

O {% data variables.product.prodname_serverless %} está disponível para todos gratuitamente em {% data variables.product.prodname_dotcom_the_website %}.

O {% data variables.product.prodname_serverless %} fornece muitos dos benefícios de {% data variables.product.prodname_vscode %}, como pesquisa, destaque de sintaxe e uma visão de controle de origem. Você também pode usar Sincronização de Configurações para compartilhar suas próprias configurações do {% data variables.product.prodname_vscode_shortname %} com o editor. Para obter mais informações, consulte "[Sincronização de Configurações](https://code.visualstudio.com/docs/editor/settings-sync)" na documentação do {% data variables.product.prodname_vscode_shortname %}.

O {% data variables.product.prodname_serverless %} é executado inteiramente no sandbox do seu navegador. O editor não clona o repositório, mas usa a [extensão Repositórios do GitHub](https://code.visualstudio.com/docs/editor/github#_github-repositories-extension) para executar a maior parte da funcionalidade que você usará. Seu trabalho é salvo no armazenamento local do navegador até que você faça commit dele. Você deve fazer commit das alterações regularmente para garantir que estejam sempre acessíveis.

## Abrindo o {% data variables.product.prodname_serverless %}

Você pode abrir qualquer repositório de {% data variables.product.prodname_dotcom %} em {% data variables.product.prodname_serverless %} em qualquer uma das seguintes maneiras:

- Para abrir o repositório na mesma guia do navegador, pressione `.` enquanto navega por qualquer repositório ou solicitação de pull no {% data variables.product.prodname_dotcom %}.
 
   Para abrir o repositório em uma nova guia do navegador, mantenha pressionada a tecla shift e pressione `.`.

- Alterando a URL de "github.com" para "github.dev".
- Ao exibir um arquivo, use o menu suspenso ao lado de {% octicon "pencil" aria-label="The edit icon" %} e selecione **Abrir no github.dev**.

  ![Menu suspenso do botão Editar arquivo](/assets/images/help/repository/edit-file-edit-dropdown.png)

## {% data variables.product.prodname_codespaces %} e {% data variables.product.prodname_serverless %}

Tanto o {% data variables.product.prodname_serverless %} quanto o {% data variables.product.prodname_github_codespaces %} permitem que você edite seu código diretamente do seu repositório. No entanto, ambos têm benefícios ligeiramente diferentes, dependendo da sua utilização.

|| {% data variables.product.prodname_serverless %} | {% data variables.product.prodname_codespaces %}|
|-|----------------|---------|
| **Custo** | Livre.      | Custos de computação e armazenamento. Para obter informações sobre preços, confira "[Preços de codespaces](/en/billing/managing-billing-for-github-codespaces/about-billing-for-codespaces#codespaces-pricing)".|
| **Disponibilidade** | Disponível para todos no GitHub.com. | Disponível para organizações que usam o GitHub Team ou GitHub Enterprise Cloud. |
| **Inicialização** | O {% data variables.product.prodname_serverless %} abre instantaneamente com um toque de tecla e você pode começar a usá-lo imediatamente, sem ter que esperar por uma configuração ou instalação adicional. | Quando você cria ou retoma um codespace, o código é atribuído a uma VM e o contêiner é configurado com base no conteúdo de um arquivo `devcontainer.json`. Essa configuração pode levar alguns minutos para criar o ambiente. Para obter mais informações, confira "[Como criar um codespace](/codespaces/developing-in-codespaces/creating-a-codespace)". |
| **Computação**  | Não há nenhum computador associado. Portanto você não conseguirá criar e executar o seu código ou usar o terminal integrado. | Com {%  data   variables.product.prodname_codespaces %}, você obtém o poder da VM dedicada na qual você pode executar e depurar seu aplicativo.|
| **Acesso ao terminal** | Nenhum. | {% data variables.product.prodname_codespaces %} fornece um conjunto comum de ferramentas por padrão, o que significa que você pode usar o Terminal exatamente como você faria no seu ambiente local.|
| **Extensões**  | Apenas um subconjunto de extensões que podem ser executadas na web aparecerão na visualização de extensões e podem ser instaladas. Para obter mais informações, confira "[Como usar extensões](#using-extensions)".| Com os Codespaces, você pode usar a maioria das extensões do {% data variables.product.prodname_vscode_marketplace %}.|

### Continue trabalhando em {%  data   variables.product.prodname_codespaces %}

Você pode iniciar seu fluxo de trabalho no {% data variables.product.prodname_serverless %} e continuar trabalhando em um codespace, desde que tenha [acesso ao {% data variables.product.prodname_codespaces %}](/codespaces/developing-in-codespaces/creating-a-codespace#access-to-codespaces). Se você tentar acessar a janela ou terminal Executar e Depurarl, você receberá uma mensagem de que eles não estão disponíveis em {% data variables.product.prodname_serverless %}.

Para continuar seu trabalho em um codespace, clique em **Continuar Trabalhando em…** e selecione **Criar Codespace** para criar um codespace no branch atual. Antes de selecionar esta opção, você precisa fazer commit de quaisquer alterações.

![Captura de tela que mostra o botão "Continuar Trabalhando" na interface do usuário](/assets/images/help/codespaces/codespaces-continue-working.png)

## Usando controle de origem

Ao usar o {% data variables.product.prodname_serverless %}, todas as ações são gerenciadas por meio da Visualização de Controle de Origem, localizado na Barra de Atividades do lado esquerdo. Para obter mais informações sobre a Exibição de Controle do Código-Fonte, consulte "[Controle de versão](https://code.visualstudio.com/docs/editor/versioncontrol)" na documentação do {% data variables.product.prodname_vscode_shortname %}.

Como o editor da web usa a extensão dos repositórios do GitHub para melhorar suas funcionalidades, você pode alternar entre branches sem precisar ocultar alterações. Para obter mais informações, consulte "[Repositórios do GitHub](https://code.visualstudio.com/docs/editor/github#_github-repositories-extension)" na documentação do {% data variables.product.prodname_vscode_shortname %}.

### Criar uma nova ramificação

{% data reusables.codespaces.create-or-switch-branch %} Todas as alterações sem commit feitas no branch antigo ficarão disponíveis no novo branch.

### Confirmar as alterações

{% data reusables.codespaces.source-control-commit-changes %} 
5. Depois de ter feito as commit das suas alterações, elas serão automaticamente enviadas para o seu branch em {% data variables.product.prodname_dotcom %}.
### Criar uma solicitação de pull

{% data reusables.codespaces.source-control-pull-request %}

### Trabalhando com um pull request existente

Você pode usar o {% data variables.product.prodname_serverless %} para trabalhar com um pull request existente.

1. Acesse o pull request que você gostaria de abrir em {% data variables.product.prodname_serverless %}.
2. Pressione `.` para abrir a solicitação de pull no {% data variables.product.prodname_serverless %}.
3. Depois de fazer alterações, faça commit delas usando as etapas descritas em [Fazer commit das alterações](#commit-your-changes). As suas alterações serão registradas diretamente no branch. Não é necessário fazer push das alterações.

## Como usar extensões

O {% data variables.product.prodname_serverless %} é compatível com as extensões do {% data variables.product.prodname_vscode_shortname %} que foram especificamente criadas ou atualizadas para serem executadas na Web. Essas extensões são conhecidas como "extensões da web". Para saber como criar uma extensão da Web ou atualizar sua extensão existente para trabalhar na Web, consulte "[Extensões da Web](https://code.visualstudio.com/api/extension-guides/web-extensions)" na documentação do {% data variables.product.prodname_vscode_shortname %}.

As extensões que podem ser executadas no {% data variables.product.prodname_serverless %} aparecerão na vista de Extensões e poderão ser instaladas. Se você usar a Sincronização de Configurações, todas as extensões compatíveis também são instaladas automaticamente. Para obter mais informações, consulte "[Sincronização de Configurações](https://code.visualstudio.com/docs/editor/settings-sync)" na documentação do {% data variables.product.prodname_vscode_shortname %}.


## Solução de problemas

Se você tiver problemas ao abrir {% data variables.product.prodname_serverless %}, tente o seguinte:

- Verifique se você está conectado a {% data variables.product.prodname_dotcom %}.
- Desabilita qualquer bloqueador de anúncios.
- Use uma janela não anônima no seu navegador para abrir o {% data variables.product.prodname_serverless %}.

### Limitações conhecidas

- O {% data variables.product.prodname_serverless %} atualmente é compatível com o Chrome (e vários outros navegadores baseados no Chromium), Edge, Firefox e Safari. Recomendamos que você use as versões mais recentes desses navegadores.
- Algumas teclas de atalho podem não funcionar, dependendo do navegador que você estiver usando. Essas limitações de associação de teclas são documentadas na seção "[Limitações e adaptações conhecidas](https://code.visualstudio.com/docs/remote/codespaces#_known-limitations-and-adaptations)" da documentação do {% data variables.product.prodname_vscode_shortname %}.
- Talvez o `.` não funcione para abrir o {% data variables.product.prodname_serverless %} de acordo com o layout local do teclado. Nesse caso, você pode abrir qualquer repositório do {% data variables.product.prodname_dotcom %} no {% data variables.product.prodname_serverless %} alterando a URL de `github.com` para `github.dev`.
