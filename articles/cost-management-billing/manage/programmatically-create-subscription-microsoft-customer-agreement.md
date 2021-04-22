---
title: Azure-előfizetések létrehozása programozott módon Microsoft Ügyfélszerződéshez a legújabb API-kkal
description: Megtudhatja, hogyan hozhat létre Azure-előfizetéseket Microsoft Ügyfélszerződés a REST API, az Azure CLI, a Azure PowerShell és a Azure Resource Manager legújabb verzióival.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 324ca849e0f9c1282dc4b47ceba4654c76c07b35
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870822"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-customer-agreement-with-the-latest-apis"></a>Azure-előfizetések létrehozása programozott módon Microsoft Ügyfélszerződéshez a legújabb API-kkal

Ez a cikk segítséget nyújt az Azure-előfizetések programozott módon, a legújabb API-verziók használatával történő létrehozásában Microsoft Ügyfélszerződéshez. Ha még mindig a régebbi előzetes verziót használja, tekintse meg az [Azure-előfizetések programozott módon, előzetes verziójú API-kkal történő létrehozását](programmatically-create-subscription-preview.md) bemutató szakaszt. 

Ebből a cikkből megtudhatja, hogyan hozhat létre előfizetéseket programozott módon az Azure Resource Manager használatával.

Ha programozott módon hoz létre Azure-előfizetést, azt az a megállapodás szabályozza, amely keretében igénybe veszi a Microsoft vagy egy hivatalos viszonteladó által biztosított Azure-szolgáltatásokat. További információért lásd a [Microsoft Azure jogi információit](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Előfizetések létrehozásához tulajdonosi, közreműködői vagy Azure-előfizetések létrehozói szerepkörével kell rendelkeznie egy számlaszakaszra, vagy tulajdonos vagy közreműködői szerepkörrel egy számlázási profilra vagy számlázási fiókra vonatkozóan. Ugyanezt a szerepkört egy egyszerű szolgáltatásnévnek (SPN) is meg lehet adni. A szerepkörökkel és az engedélyek hozzárendelésével kapcsolatos további információkért lásd: Előfizetés [számlázási szerepkörei és -feladatai.](understand-mca-roles.md#subscription-billing-roles-and-tasks)

Ha egyszerű szolgáltatásnévvel hoz létre előfizetéseket, használja az Azure AD-alkalmazásregisztráció ObjectId objektumazonosítóját szolgáltatásnév objectId-ként a [Azure Active Directory PowerShell](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) vagy [az Azure CLI használatával.](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_list) 

Ha nem tudja, rendelkezik-e hozzáféréssel Microsoft Ügyfélszerződéshez, tekintse meg a [Microsoft Ügyfélszerződéshez való hozzáférés ellenőrzéséről](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement) szóló cikket.

## <a name="find-billing-accounts-that-you-have-access-to"></a>Azon számlázási fiókok megkeresése, amelyekhez hozzáféréssel rendelkezik

Hajtsa végre az alábbi kérést az összes számlázási fiók felsorolásához.

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01

```
Az API-válasz felsorolja azokat számlázási fiókokat, amelyekhez hozzáféréssel rendelkezik.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

A `displayName` tulajdonsággal azonosíthatja azt a számlázási fiókot, amelyhez előfizetéseket szeretne létrehozni. Győződjön meg arról, hogy a fiók agreementType tulajdonsága *MicrosoftCustomerAgreement*. Másolja ki a fiók `name` elemét.  Ha például a `Contoso` számlázási fiókhoz szeretne előfizetést létrehozni, másolja ki az `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` értéket. Illessze be valahova az értéket, hogy a következő lépésben használni tudja.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzBillingAccount
```
Megjelenik az összes olyan számlázási fiók listája, amelyekhez hozzáféréssel rendelkezik 

```json
Name          : 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
DisplayName   : Contoso
AccountStatus : Active
AccountType   : Enterprise
AgreementType : MicrosoftCustomerAgreement
HasReadAccess : True
```
A `displayName` tulajdonsággal azonosíthatja azt a számlázási fiókot, amelyhez előfizetéseket szeretne létrehozni. Győződjön meg arról, hogy a fiók agreementType tulajdonsága *MicrosoftCustomerAgreement*. Másolja ki a fiók `name` elemét.  Ha például a `Contoso` számlázási fiókhoz szeretne előfizetést létrehozni, másolja ki az `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` értéket. Illessze be valahova az értéket, hogy a következő lépésben használni tudja.


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing account list
```
Megjelenik az összes olyan számlázási fiók listája, amelyekhez hozzáféréssel rendelkezik 

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Enterprise",
    "agreementType": "MicrosoftCustomerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

A `displayName` tulajdonsággal azonosíthatja azt a számlázási fiókot, amelyhez előfizetéseket szeretne létrehozni. Győződjön meg arról, hogy a fiók agreementType tulajdonsága *MicrosoftCustomerAgreement*. Másolja ki a fiók `name` elemét.  Ha például a `Contoso` számlázási fiókhoz szeretne előfizetést létrehozni, másolja ki az `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` értéket. Illessze be valahova az értéket, hogy a következő lépésben használni tudja.

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>Számlázási profilok és számlaszakaszok keresése előfizetések létrehozásához

Az előfizetésért felszámolt díjak a számlázási profilok számláinak egy szakaszában vannak feltüntetve. Az alábbi API-val kérheti le azon számlázási profilok és számlaszakaszok listáját, amelyeken engedéllyel rendelkezik Azure-előfizetések létrehozásához.

Először le kell kapnia azon számlázási profilok listáját azon számlázási fiók alatt, amelyhez hozzáféréssel rendelkezik (használja az előző lépésben `name` kapottet).

### <a name="rest"></a>[REST](#tab/rest)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```
Az API-válasz felsorolja az összes olyan számlázási profilt, amelyen hozzáféréssel rendelkezik előfizetések létrehozásához:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 Másolja ki az `id` paramétert, hogy következő lépésként azonosíthassa a számlázási profilhoz tartozó számlaszakaszokat. Például másolja ki a `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx` paramétert, és hívja meg a következő API-t.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```
### <a name="response"></a>Reagálás

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

Az `id` tulajdonsággal azonosíthatja azt a számlaszakaszt, amelyhez előfizetéseket szeretne létrehozni. Másolja ki az egész sztringet. Például: `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzBillingProfile -BillingAccountName 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

A válasz részeként le fogja kapni a fiókhoz alatti számlázási profilok listáját.

```json
Name              : AW4F-xxxx-xxx-xxx
DisplayName       : Contoso Billing Profile
Currency          : USD
InvoiceDay        : 5
InvoiceEmailOptIn : True
SpendingLimit     : Off
Status            : Active
EnabledAzurePlans : {0002, 0001}
HasReadAccess     : True
BillTo            :
CompanyName       : Contoso
AddressLine1      : One Microsoft Way
AddressLine2      : 
City              : Redmond
Region            : WA
Country           : US
PostalCode        : 98052
```

Jegyezze `name` fel a fenti válaszból származó számlázási profilt. A következő lépés annak a számlázási szakasznak a be szereznie, amelyhez hozzáféréssel rendelkezik a számlázási profil alatt. Szüksége lesz a `name` számlázási fiók és a számlázási profil profiljára

```azurepowershell
Get-AzInvoiceSection -BillingAccountName 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx -BillingProfileName AW4F-xxxx-xxx-xxx
```

Visszaadja a számlaszakaszt

```json
Name        : SH3V-xxxx-xxx-xxx
DisplayName : Development
```

A `name` fenti a számlaszakasz neve, amely alatt létre kell hoznia egy előfizetést. A számlázási hatókört a következő formátumban építi fel: "/providers/Microsoft.Billing/billingAccounts/ <BillingAccountName> /billingProfiles/ <BillingProfileName> /invoiceSections/ <InvoiceSectionName> ". Ebben a példában ez az érték a következő lesz: `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"` .

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az billing profile list --account-name "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --expand "InvoiceSections"
```

Ez az API visszaadja a megadott számlázási fiókhoz tartozó számlázási profilok és számlaszakaszok listáját.

```json
[
  {
    "billTo": {
      "addressLine1": "One Microsoft Way",
      "addressLine2": "",
      "addressLine3": null,
      "city": "Redmond",
      "companyName": "Contoso",
      "country": "US",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": "98052",
      "region": "WA"
    },
    "billingRelationshipType": "Direct",
    "currency": "USD",
    "displayName": "Contoso Billing Profile",
    "enabledAzurePlans": [
      {
        "skuDescription": "Microsoft Azure Plan for DevTest",
        "skuId": "0002"
      },
      {
        "skuDescription": "Microsoft Azure Plan",
        "skuId": "0001"
      }
    ],
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
    "indirectRelationshipInfo": null,
    "invoiceDay": 5,
    "invoiceEmailOptIn": true,
    "invoiceSections": {
      "hasMoreResults": false,
      "value": [
        {
          "displayName": "Field_Led_Test_Ace",
          "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
          "labels": null,
          "name": "SH3V-xxxx-xxx-xxx",
          "state": "Active",
          "systemId": "SH3V-xxxx-xxx-xxx",
          "targetCloud": null,
          "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
        }
      ]
    },
    "name": "AW4F-xxxx-xxx-xxx",
    "poNumber": null,
    "spendingLimit": "Off",
    "status": "Warned",
    "statusReasonCode": "PastDue",
    "systemId": "AW4F-xxxx-xxx-xxx",
    "targetClouds": [],
    "type": "Microsoft.Billing/billingAccounts/billingProfiles"
  }
]
```
A számlaszakasz objektum alatti tulajdonság használatával azonosíthatja azt a számlázási szakaszt, amelyhez `id` előfizetéseket szeretne létrehozni. Másolja ki az egész sztringet. Például: /providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx.

---

## <a name="create-a-subscription-for-an-invoice-section"></a>Előfizetés létrehozása számlaszakaszhoz

Az alábbi példa egy *Fejlesztői csapat előfizetése* nevű előfizetést hoz létre a *Fejlesztés* számlaszakaszhoz. Az előfizetés a *Contoso számlázási profil* nevű számlázási profilon lesz kiszámlázva, és a számla *Fejlesztés* szakaszában jelenik meg. Használja az előző lépésben kimásolt számlázási hatókört: `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>A kérés törzse

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A `New-AzSubscriptionAlias` parancsmagot tartalmazó modul legújabb verziójának telepítéséhez futtassa az `Install-Module Az.Subscription` parancsot. A PowerShellGet legújabb verziójának telepítéséhez lásd a [PowerShellGet modul beszerzésével](/powershell/scripting/gallery/installing-psget) foglalkozó témakört.

Futtassa az alábbi [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) parancsot és a `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"` számlázási hatókört. 

```azurepowershell
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" -Workload 'Production"
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

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Első lépésként telepítse a bővítményt az `az extension add --name account` és az `az extension add --name alias` futtatásával.

Futtassa az alábbi [az account alias create](/cli/azure/account/alias#az_account_alias_create) parancsot.

```azurecli
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" --display-name "Dev Team Subscription" --workload "Production"
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

---

## <a name="use-arm-template"></a>ARM-sablon használata

Az előző szakasz azt mutatta be, hogyan hozhat létre előfizetést a PowerShell, a parancssori felület vagy a REST API. Ha automatizálni kell az előfizetések létrehozását, érdemes lehet egy Azure Resource Manager (ARM-sablon) használni.

Az alábbi sablon egy előfizetést hoz létre. A `billingScope` esetén adja meg a számlaszakasz azonosítóját. A `targetManagementGroup` esetén adja meg azt a felügyeleti csoportot, amelyben létre szeretné hozni az előfizetést.

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
        "value": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"
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
  -billingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" `
  -targetManagementGroup mg2
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx' targetManagementGroup=mg2
```

---

## <a name="next-steps"></a>Következő lépések

* Most, hogy létrehozott egy előfizetést, elérhetővé teheti ezt a képességek más felhasználók és szolgáltatásnevek számára is. További információ: [Hozzáférés biztosítása nagyvállalati Azure-előfizetés létrehozáshoz (előzetes verzió)](grant-access-to-create-subscription.md).
* Több előfizetés felügyeleti csoportok használatával történő kezeléséről az [erőforrások Azure-beli felügyeleti csoportokkal való rendszerezését](../../governance/management-groups/overview.md) ismertető részben talál további információt.
* Az előfizetés felügyeleti csoportjának módosítása: [Előfizetések áthelyezése.](../../governance/management-groups/manage.md#move-subscriptions)
