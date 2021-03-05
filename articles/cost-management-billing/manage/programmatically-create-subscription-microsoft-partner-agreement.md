---
title: Azure-előfizetések létrehozása programozott módon Microsoft Partnerszerződéshez a legújabb API-kkal
description: Megismerheti, hogyan hozhat létre Azure-előfizetéseket programozott módon Microsoft Partnerszerződéshez a REST API, az Azure CLI és az Azure PowerShell legújabb verziójának használatával.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 21fdd85c2b2a73ed5fd0bf65c5d745ac0cc97c9c
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200546"
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

### <a name="rest"></a>[REST](#tab/rest-getBillingAccount-MPA)

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

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts-MPA)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts-MPA)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="find-customers-that-have-azure-plans"></a>Azure-csomaggal rendelkező ügyfelek keresése

Hajtsa végre a következő kérést az első lépésben (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) kimásolt `name` értékkel. Ezzel a kéréssel felsorolhatja a számlázási fiókban lévő összes olyan ügyfelet, akihez Azure-előfizetést hozhat létre.

### <a name="rest"></a>[REST](#tab/rest-getCustomers)

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

<!--
### [PowerShell](#tab/azure-powershell-getCustomers)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getCustomers)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="get-the-resellers-for-a-customer"></a>Ügyfél viszonteladóinak lekérése

Ez a lehetőség csak Indirect Providerek számára választható.

Ha Ön közvetett szolgáltató a CSP kétrétegű modelljében, megadhatja a viszonteladót, amikor előfizetést hoz létre az ügyfelek számára.

Hajtsa végre a következő kérést a második lépésben (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) kimásolt `id` értékkel, hogy megjelenjen az ügyfelek számára elérhető viszonteladók listája.

### <a name="rest"></a>[REST](#tab/rest-getIndirectResellers)

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

<!--
### [PowerShell](#tab/azure-powershell-getIndirectResellers)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getIndirectResellers)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-for-a-customer"></a>Előfizetés létrehozása egy ügyfél számára

Az alábbi példa egy *Fejlesztői csapat előfizetés* nevű előfizetést hoz létre a *Fabrikam toys* ügyfélhez, és társítja a *Wingtip* viszonteladót az előfizetéshez. Használja az előző lépésben kimásolt számlázási hatókört: `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

### <a name="rest"></a>[REST](#tab/rest-MPA)

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-MPA)

A `New-AzSubscriptionAlias` parancsmagot tartalmazó modul legújabb verziójának telepítéséhez futtassa az `Install-Module Az.Subscription` parancsot. A PowerShellGet legújabb verziójának telepítéséhez lásd a [PowerShellGet modul beszerzésével](/powershell/scripting/gallery/installing-psget) foglalkozó témakört.

Futtassa az alábbi [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) parancsot a `"/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"` számlázási hatókör használatával. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -Workload 'Production"
```

A parancsra adott válasz részeként megkapja a subscriptionId paramétert.

```azurepowershell
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

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-MPA)

Első lépésként telepítse a bővítményt az `az extension add --name account` és az `az extension add --name alias` futtatásával.

Futtassa az alábbi [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create) parancsot. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --display-name "Dev Team Subscription" --workload "Production"
```

A parancsra adott válasz részeként megkapja a subscriptionId paramétert.

```azurecli
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

## <a name="next-steps"></a>Következő lépések

* Most, hogy létrehozott egy előfizetést, elérhetővé teheti ezt a képességek más felhasználók és szolgáltatásnevek számára is. További információ: [Hozzáférés biztosítása nagyvállalati Azure-előfizetés létrehozáshoz (előzetes verzió)](grant-access-to-create-subscription.md).
* Több előfizetés felügyeleti csoportok használatával történő kezeléséről az [erőforrások Azure-beli felügyeleti csoportokkal való rendszerezését](../../governance/management-groups/overview.md) ismertető részben talál további információt.
