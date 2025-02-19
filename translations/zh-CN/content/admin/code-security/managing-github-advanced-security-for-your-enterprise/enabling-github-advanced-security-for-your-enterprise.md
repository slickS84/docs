---
title: 为企业启用 GitHub 高级安全性
shortTitle: Enabling GitHub Advanced Security
intro: '可配置 {% data variables.product.product_name %} 以包括 {% data variables.product.prodname_GH_advanced_security %}。 这将提供额外的功能，帮助用户发现和修复其代码中的安全问题。'
product: '{% data reusables.gated-features.ghas %}'
redirect_from:
  - /admin/advanced-security/enabling-github-advanced-security-for-your-enterprise
versions:
  ghes: '*'
type: how_to
topics:
  - Advanced Security
  - Code scanning
  - Enterprise
  - Secret scanning
  - Security
ms.openlocfilehash: bc516af0c0788eeafe1b833c5627e471982e1c05
ms.sourcegitcommit: 47bd0e48c7dba1dde49baff60bc1eddc91ab10c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2022
ms.locfileid: '147145277'
---
## 关于启用 {% data variables.product.prodname_GH_advanced_security %}

{% data reusables.advanced-security.ghas-helps-developers %}

{% ifversion ghes %} 为企业启用 {% data variables.product.prodname_GH_advanced_security %} 后，所有组织的存储库管理员都可以启用这些功能，除非你设置了限制访问的策略。 有关详细信息，请参阅“[在企业中强制实施 {% data variables.product.prodname_advanced_security %} 策略](/admin/policies/enforcing-policies-for-advanced-security-in-your-enterprise)”。
{% else %} 为企业启用 {% data variables.product.prodname_GH_advanced_security %} 后，所有组织的存储库管理员都可以启用这些功能。 {% endif %}

{% ifversion ghes %} 有关 GitHub 高级安全分阶段部署的指南，请参阅“[大规模采用 GitHub 高级安全简介](/code-security/adopting-github-advanced-security-at-scale/introduction-to-adopting-github-advanced-security-at-scale)”。
{% endif %}

## 检查您的许可是否包含 {% data variables.product.prodname_GH_advanced_security %}

{% ifversion ghes %} {% data reusables.enterprise-accounts.access-enterprise %} {% data reusables.enterprise-accounts.settings-tab %} {% data reusables.enterprise-accounts.license-tab %}
1. 如果您的许可包括 {% data variables.product.prodname_GH_advanced_security %}，则许可页面将包括显示当前使用情况详细信息的部分。
![企业许可证的 {% data variables.product.prodname_GH_advanced_security %} 部分](/assets/images/help/billing/ghas-orgs-list-enterprise-ghes.png) {% endif %}

## 启用 {% data variables.product.prodname_GH_advanced_security %} 的前提条件

1. 升级 {% data variables.product.product_name %} 的许可证，以包含 {% data variables.product.prodname_GH_advanced_security %}。{% ifversion ghes %}有关许可的信息，请参阅“[关于 {% data variables.product.prodname_GH_advanced_security %} 的计费](/billing/managing-billing-for-github-advanced-security/about-billing-for-github-advanced-security)”。{% endif %}
2. 下载新的许可文件。 有关详细信息，请参阅“[下载 {% data variables.product.prodname_enterprise %} 的许可证](/billing/managing-your-license-for-github-enterprise/downloading-your-license-for-github-enterprise)”。
3. 将新许可文件上传到 {% data variables.product.product_location %}。 有关详细信息，请参阅“[将新许可证上传到 {% data variables.product.prodname_ghe_server %}](/billing/managing-your-license-for-github-enterprise/uploading-a-new-license-to-github-enterprise-server)”。{% ifversion ghes %}
4. 审查您计划启用的功能的先决条件。

    - {% data variables.product.prodname_code_scanning_capc %}，请参阅“[为设备配置 {% data variables.product.prodname_code_scanning %}](/admin/advanced-security/configuring-code-scanning-for-your-appliance#prerequisites-for-code-scanning)”。
    - {% data variables.product.prodname_secret_scanning_caps %}，请参阅“[为设备配置 {% data variables.product.prodname_secret_scanning %}](/admin/advanced-security/configuring-secret-scanning-for-your-appliance#prerequisites-for-secret-scanning)”。{% endif %}
    - {% data variables.product.prodname_dependabot %}，请参阅“[为企业启用 {% data variables.product.prodname_dependabot %}](/admin/configuration/configuring-github-connect/enabling-dependabot-for-your-enterprise)”。 

## 启用和禁用 {% data variables.product.prodname_GH_advanced_security %} 功能

{% data reusables.enterprise_management_console.enable-disable-security-features %}

{% data reusables.enterprise_site_admin_settings.access-settings %} {% data reusables.enterprise_site_admin_settings.management-console %} {% data reusables.enterprise_management_console.advanced-security-tab %}{% ifversion ghes %}
1. 在“安全”下，选择要启用的功能，取消选择要禁用的任何功能。
{% ifversion ghes %}![用于启用或禁用 {% data variables.product.prodname_advanced_security %} 功能的复选框](/assets/images/enterprise/3.2/management-console/enable-security-checkboxes.png){% else %}![用于启用或禁用 {% data variables.product.prodname_advanced_security %} 功能的复选框](/assets/images/enterprise/management-console/enable-advanced-security-checkboxes.png){% endif %}{% else %}
1. 在“{% data variables.product.prodname_advanced_security %}”下，单击“{% data variables.product.prodname_code_scanning_capc %}”。
![用于启用或禁用 {% data variables.product.prodname_code_scanning %} 的复选框](/assets/images/enterprise/management-console/enable-code-scanning-checkbox.png){% endif %} {% data reusables.enterprise_management_console.save-settings %}

当 {% data variables.product.product_name %} 完成重启后，您可以设置新启用功能所需的任何额外资源。 有关详细信息，请参阅“[为设备配置 {% data variables.product.prodname_code_scanning %}](/admin/advanced-security/configuring-code-scanning-for-your-appliance)”。

## 通过管理 shell (SSH) 启用或禁用 {% data variables.product.prodname_GH_advanced_security %} 功能

您可以通过编程方式在 {% data variables.product.product_location %} 上启用或禁用功能。 有关 {% data variables.product.prodname_ghe_server %} 的管理 shell 和命令行实用程序的详细信息，请参阅“[访问管理 shell (SSH)](/admin/configuration/accessing-the-administrative-shell-ssh)”和“[命令行实用程序](/admin/configuration/command-line-utilities#ghe-config)”。

例如，当您部署用于暂存或灾难恢复的实例时，可以使用基础架构即代码工具启用任何 {% data variables.product.prodname_GH_advanced_security %}。

1. 通过 SSH 连接到 {% data variables.product.product_location %}。
1. 启用 {% data variables.product.prodname_GH_advanced_security %} 的功能。

    - 要启用 {% data variables.product.prodname_code_scanning_capc %}，请输入以下命令。
    ```shell
    ghe-config app.minio.enabled true
    ghe-config app.code-scanning.enabled true
    ```
    - 要启用 {% data variables.product.prodname_secret_scanning_caps %}，请输入以下命令。
    ```shell
    ghe-config app.secret-scanning.enabled true
    ```
    - 若要启用依赖项关系图，请输入以下{% ifversion ghes %}命令{% else %}命令{% endif %}。
    {% ifversion ghes %}```shell ghe-config app.dependency-graph.enabled true
    ```
    {% else %}```shell
    ghe-config app.github.dependency-graph-enabled true
    ghe-config app.github.vulnerability-alerting-and-settings-enabled true
    ```{% endif %}
2. Optionally, disable features for {% data variables.product.prodname_GH_advanced_security %}.

    - To disable {% data variables.product.prodname_code_scanning %}, enter the following commands.
    ```shell
    ghe-config app.minio.enabled false
    ghe-config app.code-scanning.enabled false
    ```
    - 要禁用 {% data variables.product.prodname_secret_scanning %}，请输入以下命令。
    ```shell
    ghe-config app.secret-scanning.enabled false
    ```
    - 若要禁用依赖项关系图，请输入以下{% ifversion ghes %}命令{% else %}命令{% endif %}。
    {% ifversion ghes %}```shell ghe-config app.dependency-graph.enabled false
    ```
    {% else %}```shell
    ghe-config app.github.dependency-graph-enabled false
    ghe-config app.github.vulnerability-alerting-and-settings-enabled false
    ```{% endif %}
3. Apply the configuration.
    ```shell
    ghe-config-apply
    ```
