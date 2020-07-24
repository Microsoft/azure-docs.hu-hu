---
title: Oktatóanyag – Azure Spring Cloud-példány kiépítése a Terraform
description: Azure Spring Cloud-példány kiépítése a Terraform-mel.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 817a7c132657ba0ad8910a334b571f9d05481a0d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87021222"
---
# <a name="tutorial-provision-an-azure-spring-cloud-instance-with-terraform"></a>Oktatóanyag: Azure Spring Cloud-példány kiépítése a Terraform

Ez az oktatóanyag egy Azure Spring Cloud-példányt hoz létre a Terraform használatával. Az eljárások végigvezetik a következő erőforrások létrehozásán:

> [!div class="checklist"]
> * Resource Group (Erőforráscsoport)
> * Azure Spring Cloud-példány
> * Azure Storage Log Analytics

> [!NOTE]
> A Terraform-specifikus támogatáshoz használja a HashiCorp közösségi támogatási csatornáinak egyikét a Terraform-re:
>
> * Kérdések, használati esetek és hasznos minták: [a HashiCorp közösségi portál Terraform szakasza](https://discuss.hashicorp.com/c/terraform-core)
> * Szolgáltatóval kapcsolatos kérdések: [a HashiCorp közösségi portál Terraform-szolgáltatók szakasza](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>Előfeltételek

- **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-configuration-file"></a>Konfigurációs fájl létrehozása

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nyissa meg a [Azure Cloud Shell](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-java#use-azure-cloud-shell).

1. A Cloud Shell-szerkesztő elindítása:

    ```bash
    code main.tf
    ```

1. Az ebben a lépésben szereplő konfiguráció Azure-erőforrásokat, köztük egy Azure-erőforráscsoportot és egy Azure Spring Cloud-példányt modellez.

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. Mentse a fájlt (** &lt; CTRL>S**), és lépjen ki a szerkesztőből (** &lt; CTRL>Q**).

## <a name="apply-the-configuration"></a>A konfiguráció alkalmazása

Ebben a szakaszban számos Terraform-parancsot használ a konfiguráció futtatásához.

1. A [Terraform init](https://www.terraform.io/docs/commands/init.html) parancs inicializálja a munkakönyvtárat. Futtassa a következő parancsot a Cloud Shellban:

    ```bash
    terraform init
    ```

1. A [Terraform terv](https://www.terraform.io/docs/commands/plan.html) a konfigurációs szintaxis érvényesítésére szolgál. A `-out` paraméter egy fájlba irányítja az eredményeket. A kimeneti fájl később is használható a konfiguráció alkalmazásához. Futtassa a következő parancsot a Cloud Shellban:

    ```bash
    terraform plan --out plan.out
    ```

1. A [Terraform](https://www.terraform.io/docs/commands/apply.html) parancs a konfiguráció alkalmazására szolgál. A parancs az előző lépésben megadott kimeneti fájlt adja meg. Ez a parancs létrehozza az Azure-erőforrásokat. Futtassa a következő parancsot a Cloud Shellban:

    ```bash
    terraform apply plan.out
    ```

1. A Azure Portal belüli eredmények ellenőrzéséhez keresse meg az új erőforráscsoportot. Az új **Azure Cosmos db példány** az új erőforráscsoporthoz mutat.

## <a name="update-configuration-to-config-logs-and-metrics"></a>Konfiguráció frissítése a konfigurációs naplókra és a mérőszámokra

Ebből a szakaszból megtudhatja, hogyan frissítheti a konfigurációt, hogy az Azure Spring Cloud egy Azure Storage-fiókkal való naplózását és mérőszámait engedélyezze.

1. A Cloud Shell-szerkesztő elindítása:

    ```bash
    code main.tf
    ```

1. Adja hozzá a következő konfigurációt a fájl végén:

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. Mentse a fájlt (** &lt; CTRL>S**), és lépjen ki a szerkesztőből (** &lt; CTRL>Q**).

1. Ahogy az előző szakaszban is, futtassa a következő parancsot a módosítások elvégzéséhez:

    ```bash
    terraform plan --out plan.out
    ```

1. Futtassa a `terraform apply` parancsot a konfiguráció alkalmazásához.

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs rá szükség, törölje a cikkben létrehozott erőforrásokat.

Futtassa az [Terraform Destroy](https://www.terraform.io/docs/commands/destroy.html) parancsot az oktatóanyagban létrehozott Azure-erőforrások eltávolításához:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Terraform telepítése és konfigurálása Azure-erőforrások](https://docs.microsoft.com/azure/developer/terraform/getting-started-cloud-shell)kiépítéséhez.