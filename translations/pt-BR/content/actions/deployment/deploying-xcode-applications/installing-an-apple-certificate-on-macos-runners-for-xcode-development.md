---
title: Instalar um certificado da Apple em executores do macOS para desenvolvimento do Xcode
intro: 'Você pode assinar aplicativos Xcode na sua integração contínua (CI) instalando um certificado de assinatura de código da Apple nos executores de {% data variables.product.prodname_actions %}.'
redirect_from:
  - /actions/guides/installing-an-apple-certificate-on-macos-runners-for-xcode-development
  - /actions/deployment/installing-an-apple-certificate-on-macos-runners-for-xcode-development
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
  ghec: '*'
type: tutorial
topics:
  - CI
  - Xcode
shortTitle: Sign Xcode applications
ms.openlocfilehash: 47c534db1e16595af4735362c524f673376b53fe
ms.sourcegitcommit: 47bd0e48c7dba1dde49baff60bc1eddc91ab10c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/05/2022
ms.locfileid: '145084173'
---
{% data reusables.actions.enterprise-beta %} {% data reusables.actions.enterprise-github-hosted-runners %}

## Introdução

Este guia mostra como adicionar uma etapa ao fluxo de trabalho de integração contínua (CI) que instala um certificado de assinatura de código da Apple e o perfil de provisionamento em executores de {% data variables.product.prodname_actions %}. Isso permitirá que você assine seus aplicativos Xcode para publicar na Apple App Store ou distribuí-los para testar grupos.

## Pré-requisitos

Você deve estar familiarizado com o YAML e a sintaxe do {% data variables.product.prodname_actions %}. Para obter mais informações, consulte:

- "[Aprenda a usar o {% data variables.product.prodname_actions %}](/actions/learn-github-actions)"
- "[Sintaxe de fluxo de trabalho do {% data variables.product.prodname_actions %}](/actions/automating-your-workflow-with-github-actions/workflow-syntax-for-github-actions)"

Você deve ter um entendimento de criação e assinatura do aplicativo Xcode. Para obter mais informações, confira a [documentação de desenvolvedor da Apple](https://developer.apple.com/documentation/).

## Criar segredos para seu certificado e perfil de provisionamento

O processo de assinatura envolve o armazenamento de certificados e provisionamento de perfis, transferindo-os para o executor, importando-os para a keychain e usando-os na sua compilação.

Para usar seu certificado e perfil de provisionamento em um executor, é altamente recomendado que você use segredos de {% data variables.product.prodname_dotcom %}. Para obter mais informações sobre como criar segredos e usá-los em um fluxo de trabalho, confira "[Segredos criptografados](/actions/reference/encrypted-secrets)".

Crie segredos no seu repositório ou organização para os seguintes itens:

* Seu certificado de assinatura Apple.

  - Esse é o arquivo de certificado `p12`. Para obter mais informações sobre como exportar seu certificado de autenticação do Xcode, confira a [documentação do Xcode](https://help.apple.com/xcode/mac/current/#/dev154b28f09).
  
  - Você deve converter seu certificado em Base64 ao salvá-lo como um segredo. Neste exemplo, o segredo é chamado `BUILD_CERTIFICATE_BASE64`.

  - Use o comando a seguir para converter seu certificado para Base64 e copiá-lo para a sua área de transferência:

    ```shell
    base64 <em>build_certificate</em>.p12 | pbcopy
    ```
* A senha para seu certificado de assinatura da Apple.
  - Neste exemplo, o segredo é chamado `P12_PASSWORD`.

* O seu perfil de provisionamento da Apple.

  - Para obter mais informações sobre como exportar seu perfil de provisionamento do Xcode, confira a [documentação do Xcode](https://help.apple.com/xcode/mac/current/#/deva899b4fe5).

  - Você deve converter o seu perfil de provisionamento para Base64 ao salvá-lo como segredo. Neste exemplo, o segredo é chamado `BUILD_PROVISION_PROFILE_BASE64`.

  - Use o comando a seguir para converter o seu perfil de provisionamento para Base64 e copiá-lo para a sua área de transferência:
  
    ```shell
    base64 <em>provisioning_profile.mobileprovision</em> | pbcopy
    ```

* Uma senha da keychain.

  - Uma nova keychain será criada no executo. Portanto, a senha para a nova keychain pode ser qualquer nova string aleatória. Neste exemplo, o segredo é chamado `KEYCHAIN_PASSWORD`.

## Adicionar uma etapa ao seu fluxo de trabalho

Este fluxo de trabalho de exemplo inclui uma etapa que importa o certificado da Apple e o perfil de provisionamento dos segredos de {% data variables.product.prodname_dotcom %} e os instala no executor.

```yaml{:copy}
name: App build
on: push

jobs:
  build_with_signing:
    runs-on: macos-latest

    steps:
      - name: Checkout repository
        uses: {% data reusables.actions.action-checkout %}
      - name: Install the Apple certificate and provisioning profile
        env:
          BUILD_CERTIFICATE_BASE64: {% raw %}${{ secrets.BUILD_CERTIFICATE_BASE64 }}{% endraw %}
          P12_PASSWORD: {% raw %}${{ secrets.P12_PASSWORD }}{% endraw %}
          BUILD_PROVISION_PROFILE_BASE64: {% raw %}${{ secrets.BUILD_PROVISION_PROFILE_BASE64 }}{% endraw %}
          KEYCHAIN_PASSWORD: {% raw %}${{ secrets.KEYCHAIN_PASSWORD }}{% endraw %}
        run: |
          # create variables
          CERTIFICATE_PATH=$RUNNER_TEMP/build_certificate.p12
          PP_PATH=$RUNNER_TEMP/build_pp.mobileprovision
          KEYCHAIN_PATH=$RUNNER_TEMP/app-signing.keychain-db

          # import certificate and provisioning profile from secrets
          echo -n "$BUILD_CERTIFICATE_BASE64" | base64 --decode --output $CERTIFICATE_PATH
          echo -n "$BUILD_PROVISION_PROFILE_BASE64" | base64 --decode --output $PP_PATH

          # create temporary keychain
          security create-keychain -p "$KEYCHAIN_PASSWORD" $KEYCHAIN_PATH
          security set-keychain-settings -lut 21600 $KEYCHAIN_PATH
          security unlock-keychain -p "$KEYCHAIN_PASSWORD" $KEYCHAIN_PATH

          # import certificate to keychain
          security import $CERTIFICATE_PATH -P "$P12_PASSWORD" -A -t cert -f pkcs12 -k $KEYCHAIN_PATH
          security list-keychain -d user -s $KEYCHAIN_PATH

          # apply provisioning profile
          mkdir -p ~/Library/MobileDevice/Provisioning\ Profiles
          cp $PP_PATH ~/Library/MobileDevice/Provisioning\ Profiles
      - name: Build app
        ...
```

## Limpeza necessária nos executores auto-hospedados

Executores hosperados em {% data variables.product.prodname_dotcom %} são máquinas virtuais isoladas destruídas automaticamente no final da execução do trabalho. Isso significa que os certificados e o perfil de provisionamento usados no executor durante o trabalho serão destruídos com o executor quando o trabalho for concluído.

Em executores auto-hospedados, o diretório `$RUNNER_TEMP` é limpo no final da execução do trabalho, mas o conjunto de chaves e o perfil de provisionamento ainda podem existir no executor.

Se você usa executores auto-hospedados, você deve adicionar uma última etapa ao seu fluxo de trabalho para ajudar a garantir que esses arquivos sensíveis sejam excluídos no final do trabalho. A etapa do fluxo de trabalho abaixo é um exemplo de como fazer isso.

{% raw %}
```yaml
- name: Clean up keychain and provisioning profile
  if: ${{ always() }}
  run: |
    security delete-keychain $RUNNER_TEMP/app-signing.keychain-db
    rm ~/Library/MobileDevice/Provisioning\ Profiles/build_pp.mobileprovision
```
{% endraw %}
