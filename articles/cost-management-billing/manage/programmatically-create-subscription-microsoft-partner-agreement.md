---
title: Azure-előfizetések létrehozása programozott módon Microsoft Partnerszerződéshez a legújabb API-kkal
description: Megtudhatja, hogyan hozhat létre Azure-előfizetéseket Microsoft Partnerszerződés a REST API, az Azure CLI, a Azure PowerShell és a Azure Resource Manager legújabb verzióival.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/12/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 69d8910ffe0e45c4c47a035d5c32e71f19d9e04a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870660"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-partner-agreement-with-the-latest-apis"></a>Azure-előfizetések létrehozása programozott módon Microsoft Partnerszerződéshez a legújabb API-kkal

Ez a cikk segítséget nyújt az Azure-előfizetések programozott módon, a legújabb API-verziók használatával történő létrehozásában Microsoft Partnerszerződéshez. Ha még mindig a régebbi előzetes verziót használja, tekintse meg az [Azure-előfizetések programozott módon, előzetes verziójú API-kkal történő létrehozását](programmatically-create-subscription-preview.md) bemutató szakaszt. 

Ebből a cikkből megtudhatja, hogyan hozhat létre előfizetéseket programozott módon az Azure Resource Manager használatával.

Ha programozott módon hoz létre Azure-előfizetést, azt az a megállapodás szabályozza, amely keretében igénybe veszi a Microsoft vagy egy hivatalos viszonteladó által biztosított Azure-szolgáltatásokat. További információért lásd a [Microsoft Azure jogi információit](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Ha előfizetést szeretne létrehozni a számlázási fiókjához, globális rendszergazdai vagy rendszergazdai ügynök szerepkörrel kell rendelkeznie a szervezete felhőszolgáltatójának fiókjában. További információ: [Partnerközpont – Felhasználói szerepkörök és engedélyek hozzárendelése](/partner-center/permissions-overview).

Ha nem tudja, rendelkezik-e hozzáféréssel Microsoft Partnerszerződéshez, tekintse meg a [Microsoft Partnerszerződéshez való hozzáférés ellenőrzéséről](mpa-request-ownership.md#check-access-to-a-microsoft-partner-agreement) szóló cikket.

Az alábbi példák REST API-kat használnak. A PowerShell és az Azure CLI jelenleg nem támogatottak.

## <a name="find-the-billing-accounts-that-you-have-access-to"></a>Azon számlázási fiókok megkeresése, amelyekhez hozzáféréssel rendelkezik

Hajtsa végre az alábbi kérést azon számlázási fiókok felsorolásához, amelyekhez hozzáféréssel rendelkezik.

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

Az API-válasz felsorolja a számlázási fiókokat.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Partner",
        "agreementType": "MicrosoftPartnerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": true
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

A `displayName` tulajdonsággal azonosíthatja azt a számlázási fiókot, amelyhez előfizetéseket szeretne létrehozni. Győződjön meg arról, hogy a fiók agreementType tulajdonságának értéke *MicrosoftPartnerAgreement*. Másolja a vágólapra a fiók `name` értékét. Ha például a `Contoso` számlázási fiókhoz szeretne előfizetést létrehozni, másolja ki az `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` értéket. Illessze be valahova az értéket, hogy a következő lépésben használni tudja.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ezt az értéket az Azure CLI vagy REST API használhatja.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing account list
```
Megjelenik az összes olyan számlázási fiók listája, amelyekhez hozzáféréssel rendelkezik.

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Partner",
    "agreementType": "MicrosoftPartnerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

A displayName tulajdonság használatával azonosíthatja azt a számlázási fiókot, amelyhez előfizetéseket kíván létrehozni. Győződjön meg arról, hogy a fiók agreementType tulajdonságának értéke MicrosoftPartnerAgreement. Másolja ki a fiók nevét. Ha például a Contoso számlázási fiókjához létre kell hoznia egy előfizetést, másolja a 99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx-xx címeket. Illessze be valahova az értéket, hogy a következő lépésben használni tudja.

---

## <a name="find-customers-that-have-azure-plans"></a>Azure-csomaggal rendelkező ügyfelek keresése

Hajtsa végre a következő kérést az első lépésben (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) kimásolt `name` értékkel. Ezzel a kéréssel felsorolhatja a számlázási fiókban lévő összes olyan ügyfelet, akihez Azure-előfizetést hozhat létre.

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers?api-version=2020-05-01
```

Az API-válasz felsorolja a számlázási fiókban lévő, Azure-csomaggal rendelkező ügyfeleket. Ezekhez az ügyfelekhez hozhat létre előfizetéseket.

```json
{
  "totalCount": 2,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Contoso toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }
  ]
}

```

A `displayName` tulajdonsággal azonosíthatja azt az ügyfelet, amelyhez előfizetéseket szeretne létrehozni. Másolja a vágólapra az ügyfél `id` értékét. Ha például előfizetést szeretne létrehozni a `Fabrikam toys` ügyfélhez, másolja ki a `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx` értéket. Illessze be valahová az értéket, hogy a következő lépésekben használni tudja.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ezt az értéket az Azure CLI vagy REST API használhatja.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing customer list --account-name 99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

Az API-válasz felsorolja a számlázási fiókban lévő, Azure-csomaggal rendelkező ügyfeleket. Ezekhez az ügyfelekhez hozhat létre előfizetéseket.

```json
[
  {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Fabrikam toys",
    "id": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  },
  {
    "billingProfileDisplayName": "Contoso toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Contoso toys",
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "d49c364c-f866-4cc2-a284-d89f369b7951",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  }
]

```

A `displayName` tulajdonsággal azonosíthatja azt az ügyfelet, amelyhez előfizetéseket szeretne létrehozni. Másolja a vágólapra az ügyfél `id` értékét. Ha például előfizetést szeretne létrehozni a `Fabrikam toys` ügyfélhez, másolja ki a `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx` értéket. Illessze be valahová az értéket, hogy a következő lépésekben használni tudja.

---

## <a name="get-the-resellers-for-a-customer"></a>Ügyfél viszonteladóinak lekérése

Ez a lehetőség csak Indirect Providerek számára választható.

Ha Ön közvetett szolgáltató a CSP kétrétegű modelljében, megadhatja a viszonteladót, amikor előfizetést hoz létre az ügyfelek számára.

### <a name="rest"></a>[REST](#tab/rest)

Hajtsa végre a következő kérést a második lépésben (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) kimásolt `id` értékkel, hogy megjelenjen az ügyfelek számára elérhető viszonteladók listája.

```json
GET "https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx?$expand=resellers&api-version=2020-05-01"
```
Az API-válasz felsorolja az ügyfélhez tartozó viszonteladókat:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
}]
}
```

A `description` tulajdonsággal azonosíthatja a viszonteladót, aki az előfizetéshez van társítva. Másolja a vágólapra a viszonteladó `resellerId` értékét. Például a `Wingtip` társításához, másolja ki a `3xxxxx` értéket. Illessze be valahova az értéket, hogy a következő lépésben használni tudja.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ezt az értéket az Azure CLI vagy REST API használhatja.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Tegye a következő kérést az első lépésből másolt ( ) és az előző lépésből másolt ügyféllel `name` ```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx``` ( `name` ```acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ).

```azurecli
 az billing customer show --expand "enabledAzurePlans,resellers" --account-name "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --name "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Az API-válasz felsorolja az ügyfélhez tartozó viszonteladókat:

```json
{
  "billingProfileDisplayName": "Fabrikam toys Billing Profile",
  "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
  "displayName": "Fabrikam toys",
  "enabledAzurePlans": [
    {
      "skuDescription": "Microsoft Azure Plan",
      "skuId": "0001"
    }
  ],
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resellers": [
    {
      "description": "Wingtip",
      "resellerId": "3xxxxx"
    }
  ],
  "type": "Microsoft.Billing/billingAccounts/customers"
}

```

A `description` tulajdonsággal azonosíthatja a viszonteladót, aki az előfizetéshez van társítva. Másolja a vágólapra a viszonteladó `resellerId` értékét. Például a `Wingtip` társításához, másolja ki a `3xxxxx` értéket. Illessze be valahova az értéket, hogy a következő lépésben használni tudja.

---

## <a name="create-a-subscription-for-a-customer"></a>Előfizetés létrehozása egy ügyfél számára

Az alábbi példa egy *Fejlesztői csapat előfizetés* nevű előfizetést hoz létre a *Fabrikam toys* ügyfélhez, és társítja a *Wingtip* viszonteladót az előfizetéshez. Használja az előző lépésben kimásolt számlázási hatókört: `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>A kérés törzse

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```
### <a name="response"></a>Reagálás

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

GET-műveletet is elvégezhet ugyanazon az URL-címen a kérelem állapotának lekéréséhez.

### <a name="request"></a>Kérés

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Reagálás

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

A rendszer folyamatban lévő állapotot ad vissza `Accepted` állapotként a `provisioningState` alatt. 

Adja meg a második lépésben kimásolt, nem kötelező *resellerId* értékét az API kéréstörzsében.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A `New-AzSubscriptionAlias` parancsmagot tartalmazó modul legújabb verziójának telepítéséhez futtassa az `Install-Module Az.Subscription` parancsot. A PowerShellGet legújabb verziójának telepítéséhez lásd a [PowerShellGet modul beszerzésével](/powershell/scripting/gallery/installing-psget) foglalkozó témakört.

Futtassa az alábbi [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) parancsot a `"/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` számlázási hatókör használatával. 

```azurepowershell
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -Workload 'Production"
```

A parancsra adott válasz részeként megkapja a subscriptionId paramétert.

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

Adja meg a második lépésben kimásolt, nem kötelező *resellerId* értékét a `New-AzSubscriptionAlias` hívásban.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Első lépésként telepítse a bővítményt az `az extension add --name account` és az `az extension add --name alias` futtatásával.

Futtassa az alábbi [az account alias create](/cli/azure/account/alias#az_account_alias_create) parancsot. 

```azurecli
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --display-name "Dev Team Subscription" --workload "Production"
```

A parancsra adott válasz részeként megkapja a subscriptionId paramétert.

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

Adja meg a második lépésben kimásolt, nem kötelező *resellerId* értékét a `az account alias create` hívásban.

---

## <a name="use-arm-template"></a>ARM-sablon használata

Az előző szakasz azt mutatta be, hogyan hozhat létre előfizetést a PowerShell, a parancssori felület vagy a REST API. Ha automatizálni kell az előfizetések létrehozását, érdemes lehet egy Azure Resource Manager (ARM-sablon) használni.

Az alábbi sablon egy előfizetést hoz létre. A `billingScope` esetén adja meg az ügyfél azonosítóját. A `targetManagementGroup` esetén adja meg azt a felügyeleti csoportot, amelyben létre szeretné hozni az előfizetést.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        },
        "targetManagementGroup": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the target management group to place the subscription."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]",
                "managementGroupId": "[tenantResourceId('Microsoft.Management/managementGroups/', parameters('targetManagementGroup'))]"
            }
        }
    ],
    "outputs": {}
}
```

Telepítse a sablont a [felügyeleti csoport szintjén.](../../azure-resource-manager/templates/deploy-to-management-group.md)

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

A kérelem törzsével:

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
      },
      "targetManagementGroup": {
        "value": "mg2"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
  -targetManagementGroup mg2
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx' targetManagementGroup=mg2
```

---

## <a name="next-steps"></a>Következő lépések

* Most, hogy létrehozott egy előfizetést, elérhetővé teheti ezt a képességek más felhasználók és szolgáltatásnevek számára is. További információ: [Hozzáférés biztosítása nagyvállalati Azure-előfizetés létrehozáshoz (előzetes verzió)](grant-access-to-create-subscription.md).
* Több előfizetés felügyeleti csoportok használatával történő kezeléséről az [erőforrások Azure-beli felügyeleti csoportokkal való rendszerezését](../../governance/management-groups/overview.md) ismertető részben talál további információt.
