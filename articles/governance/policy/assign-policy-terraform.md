---
title: 'Gyors útmutató: új szabályzat-hozzárendelés a Terraform'
description: Ebben a rövid útmutatóban a Terraform és a HCL szintaxis használatával hozhat létre szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 10/27/2020
ms.topic: quickstart
ms.openlocfilehash: dc4dae2dc6e43e7532117bf64af3ce97ddc7c496
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93106393"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához a Terraform használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést.

A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
- A [Terraform](https://www.terraform.io/) 0.12.0 vagy újabb verziója konfigurálva van a környezetben.
  Útmutatásért lásd: [Terraform konfigurálása Azure Cloud Shell használatával](/azure/developer/terraform/get-started-cloud-shell).
- Ehhez a rövid útmutatóhoz az Azure CLI 2.13.0 vagy újabb verzióját kell futtatnia. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>A Terraform-konfiguráció, a változó és a kimeneti fájl létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és hozzárendeli **azokat a naplózási virtuális gépeket, amelyek nem használnak Managed Disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ) definíciót. Ez a szabályzat-definíció olyan erőforrásokat azonosít, amelyek nem felelnek meg a szabályzat-definícióban meghatározott feltételeknek.

Először konfigurálja a Terraform-konfigurációt, a változót és a kimeneti fájlokat. A Azure Policy Terraform erőforrásai az Azure- [szolgáltatót](https://www.terraform.io/docs/providers/azurerm/index.html)használják.

1. Hozzon létre egy nevű új mappát `policy-assignment` , és módosítsa a címtárakat.

1. Hozzon létre `main.tf` a következő kóddal:

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. Hozzon létre `variables.tf` a következő kóddal:

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Egy felügyeleti csoportból egy adott erőforrásra terjedhet. Ügyeljen arra, hogy a `{scope}` következő minták egyikét cserélje le:

   - Előfizetés: `/subscriptions/{subscriptionId}`
   - Erőforráscsoport: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - Erőforrás `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. Hozzon létre `output.tf` a következő kóddal:

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>Terraform inicializálása és terv létrehozása

Ezután inicializálja a Terraform a szükséges szolgáltatók letöltéséhez, majd hozzon létre egy csomagot.

1. Futtassa az [Terraform init](https://www.terraform.io/docs/commands/init.html) parancsot. Ez a parancs letölti az Azure-erőforrások létrehozásához szükséges Azure-modulokat a Terraform-konfigurációban.

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="Képernyőkép a Terraform init parancs futtatásáról, amely a azurerm modul letöltését és a sikeres üzenetet mutatja.":::

1. Hitelesítés az [Azure CLI](/cli/azure/) -vel a Terraform-hez. További információkért lásd [: Azure-szolgáltató: hitelesítés az Azure CLI használatával](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html).

   ```bash
   az login
   ```

1. Hozza létre a végrehajtási tervet a [Terraform terv](https://www.terraform.io/docs/commands/plan.html) paranccsal és a **kimeneti** paraméterrel.

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="Képernyőkép a Terraform terv parancs és az out paraméter futtatásáról a létrehozandó Azure-erőforrás megjelenítéséhez.":::

   > [!NOTE]
   > A végrehajtási csomagok és a biztonság megőrzésével kapcsolatos információkért lásd [: Terraform-csomag: biztonsági figyelmeztetés](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="apply-the-terraform-execution-plan"></a>A Terraform végrehajtási terv alkalmazása

Végül alkalmazza a végrehajtási tervet.

Futtassa a [Terraform Apply](https://www.terraform.io/docs/commands/apply.html) parancsot, és adja meg a `assignment.tfplan` már létrehozott.

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="Képernyőkép a Terraform alkalmazása parancs és az eredményül kapott erőforrás-létrehozás futtatásáról.":::

Az "Apply Complete! Erőforrások: 1 hozzáadva, 0 módosítva, 0 megsemmisült. " üzenet, a szabályzat-hozzárendelés most létrejött. A fájl definiálása óta `outputs.tf` a _hozzárendelés- \_ azonosító_ is visszaadja.

## <a name="identify-non-compliant-resources"></a>A nem megfelelő erőforrások azonosítása

Az új hozzárendelés által nem megfelelő erőforrások megtekintéséhez használja a által visszaadott _hozzárendelés- \_ azonosítót_ `terraform apply` . A következő parancs futtatásával lekérheti a JSON-fájlba kimeneti nem megfelelő erőforrások erőforrás-azonosítóit:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Az eredmények a következő példához hasonlók:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Az eredmények hasonlók ahhoz, amit általában az Azure Portal nézetében a **Nem megfelelő erőforrások** területen láthat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához használja az Azure CLI-t, vagy fordítottan a Terraform végrehajtási tervét a következővel: `terraform destroy` .

- Azure CLI

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

Ha többet szeretne megtudni a szabályzatok hozzárendeléséről az új erőforrások megfelelőségének ellenőrzéséhez, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)
