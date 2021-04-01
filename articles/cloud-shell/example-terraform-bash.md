---
title: Üzembe helyezés a Terraform-ból Azure Cloud Shell | Microsoft Docs
description: Üzembe helyezés a Terraform-ből Azure Cloud Shell
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.custom: devx-track-terraform
ms.openlocfilehash: e13e6d9ac2f4600e41f221efd23997f712dffc54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92745433"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Üzembe helyezés a Terraform a Bashből Azure Cloud Shell
Ez a cikk bemutatja, hogyan hozhat létre egy erőforráscsoportot a [Terraform AzureRM-szolgáltatóval](https://www.terraform.io/docs/providers/azurerm/index.html).

A [Hashicorp Terraform](https://www.terraform.io/) egy nyílt forráskódú eszköz, amely az API-kat olyan deklaratív konfigurációs fájlokba kodifikálja, amelyek megoszthatók a csapattagok között a Szerkesztés, a felülvizsgálat és a verziószámozás céljából. A Microsoft AzureRM-szolgáltató a AzureRM API-kon keresztül a Azure Resource Manager által támogatott erőforrásokkal való interakcióra szolgál.

## <a name="automatic-authentication"></a>Automatikus hitelesítés
A Terraform alapértelmezés szerint a bash-ben van telepítve Cloud Shellban. Emellett a Cloud Shell automatikusan hitelesíti az alapértelmezett Azure CLI-előfizetést, hogy erőforrásokat helyezzen üzembe a Terraform Azure-modulokon keresztül.

A Terraform a beállított alapértelmezett Azure CLI-előfizetést használja. Az alapértelmezett előfizetések frissítéséhez futtassa a következőt:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Útmutatás
### <a name="launch-bash-in-cloud-shell"></a>Bash indítása Cloud Shell
1. Cloud Shell elindítása az előnyben részesített helyről
2. Ellenőrizze, hogy az Ön által választott előfizetés be van-e állítva

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Terraform-sablon létrehozása
Hozzon létre egy main.tf nevű új Terraform-sablont a kívánt szövegszerkesztővel.

```
vim main.tf
```

Másolja/illessze be a következő kódot a Cloud Shellba.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Mentse a fájlt, és lépjen ki a szövegszerkesztőből.

### <a name="terraform-init"></a>Terraform init
Kezdje a futtatásával `terraform init` .

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

A [Terraform init parancs](https://www.terraform.io/docs/commands/init.html) a Terraform konfigurációs fájljait tartalmazó munkakönyvtár inicializálására szolgál. A `terraform init` parancs az első parancs, amelyet egy új Terraform-konfiguráció megírása vagy egy meglévő verzió-vezérlőelem klónozása után kell futtatni. A parancs többször is futtatható.

### <a name="terraform-plan"></a>Terraform plan
Tekintse meg a Terraform-sablon által a használatával létrehozandó erőforrásokat `terraform plan` .

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

A [terraform plan parancs](https://www.terraform.io/docs/commands/plan.html) egy végrehajtási tervet hoz létre. A Terraform csak akkor végez frissítést, ha nincs explicit módon letiltva, majd meghatározza, hogy milyen műveletek szükségesek a konfigurációs fájlokban megadott kívánt állapot eléréséhez. A csomag a-out használatával menthető, majd a Terraform alkalmazásban elérhetővé teheti, hogy csak az előre tervezett műveletek legyenek végrehajtva.

### <a name="terraform-apply"></a>Terraform apply
Az Azure-erőforrások kiépítése az-val `terraform apply` .

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

A [Terraform Apply parancs](https://www.terraform.io/docs/commands/apply.html) a konfiguráció kívánt állapotának eléréséhez szükséges módosítások alkalmazására szolgál.

### <a name="verify-deployment-with-azure-cli"></a>Az Azure CLI-vel való üzembe helyezés ellenőrzése
A futtatásával `az group show -n myRgName` ellenőrizze, hogy az erőforrás sikeresen kiépítve lett-e.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Tisztítás a Terraform destroytel
Törölje a [Terraform Destroy paranccsal](https://www.terraform.io/docs/commands/destroy.html) létrehozott erőforráscsoportot a Terraform által létrehozott infrastruktúra törléséhez.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Sikeresen létrehozott egy Azure-erőforrást a Terraform-on keresztül. A Cloud Shell megismerésének folytatásához látogasson el a következő lépésekre.

## <a name="next-steps"></a>Következő lépések
[További tudnivalók az Azure-szolgáltató Terraform](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash Cloud Shell rövid útmutatóban](quickstart.md)
