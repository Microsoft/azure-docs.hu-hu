---
title: Azure Nagyvállalati Szerződéshez tartozó előfizetések létrehozása programozott módon a legújabb API-kkal
description: Megismerheti, hogyan hozhat létre Azure Nagyvállalati Szerződéshez tartozó előfizetéseket programozott módon a REST API, az Azure CLI és az Azure PowerShell legújabb verziójának használatával.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 4de89892d27bb811be6670c1a14ca85859342ecc
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218910"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>Azure Nagyvállalati Szerződéshez tartozó előfizetések létrehozása programozott módon a legújabb API-kkal

Ez a cikk segítséget nyújt az Azure Nagyvállalati Szerződéshez tartozó előfizetések programozott módon, a legújabb API-verziók használatával történő létrehozásában Nagyvállalati Szerződéshez tartozó számlázási fiókok esetében. Ha még mindig a régebbi előzetes verziót használja, tekintse meg az [Azure-előfizetések programozott módon, előzetes verziójú API-kkal történő létrehozását](programmatically-create-subscription-preview.md) bemutató szakaszt. 

Ebből a cikkből megtudhatja, hogyan hozhat létre előfizetéseket programozott módon az Azure Resource Manager használatával.

Ha programozott módon hoz létre Azure-előfizetést, azt az a megállapodás szabályozza, amely keretében igénybe veszi a Microsoft vagy egy hivatalos viszonteladó által biztosított Azure-szolgáltatásokat. További információért lásd a [Microsoft Azure jogi információit](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Előfizetés létrehozásához tulajdonosi szerepkörrel kell rendelkeznie egy regisztrációs fiókban. A szerepkört kétféleképpen kaphatja meg:

* A regisztráció vállalati rendszergazdája [megteszi Önt fióktulajdonosnak](https://ea.azure.com/helpdocs/addNewAccount) (ehhez bejelentkezés szükséges), amely eredményeként Ön a regisztrációs fiók tulajdonosává válik.
* A regisztrációs fiók meglévő tulajdonosa [hozzáférést biztosít Önnek](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Hasonlóképpen, ha szolgáltatásnevet szeretne használni egy EA-előfizetés létrehozásához, [lehetővé kell tennie a szolgáltatásnév számára, hogy előfizetéseket hozzon létre](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). 
  > [!NOTE]
  > Győződjön meg róla, hogy a megfelelő API-verziót használja, amikor tulajdonosi engedélyeket ad a regisztrációs fióknak. A jelen cikk és a benne dokumentált API-k esetében használja a [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) verziójú API-t. Ha az újabb API-k használatára kíván átállni, akkor a [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put) verzió használatával újra meg kell adnia a tulajdonosi engedélyt. A [2015-07-01-es verzióval](grant-access-to-create-subscription.md) végzett korábbi konfiguráció nem lesz automatikusan konvertálva az újabb API-kkal való használatra.

## <a name="find-accounts-you-have-access-to"></a>Azon fiókok megkeresése, amelyekhez hozzáféréssel rendelkezik

Miután hozzáadták Önt egy fióktulajdonoshoz társított regisztrációs fiókhoz, az Azure a fiók és a regisztráció közötti kapcsolat alapján állapítja meg, hogy hová kell kiszámlázni az előfizetési díjakat. A fiókhoz létrehozott minden előfizetést annak az EA-regisztrációnak számláz ki, amelyben a fiók található. Előfizetések létrehozásához meg kell adni a regisztrációs fiókra vonatkozó értékeket, valamint az előfizetés tulajdonosának felhasználói nevét.

A következő parancsok futtatásához be kell jelentkeznie a fióktulajdonos *kezdőkönyvtárába*, amely az a könyvtár, amelyben az előfizetések alapértelmezetten létrejönnek.

### <a name="rest"></a>[REST](#tab/rest-getEnrollments)

Kérelem azon fiókok felsorolására, amelyekhez hozzáféréssel rendelkezik:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

Az API-válasz felsorolja azokat a regisztrációs fiókokat, amelyekhez hozzáféréssel rendelkezik:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

A számlázási hatókör és az `id` értéke megegyezik. A regisztrációs fiókjához tartozó `id` az a számlázási hatókör, amelyben az előfizetésre vonatkozó kérelem el lesz küldve. Fontos, hogy ismerje az azonosítót, mivel ez egy kötelező paraméter, amelyet a cikk későbbi részében az előfizetés létrehozásához fog használni.

<!-- 
### [PowerShell](#tab/azure-powershell-getEnrollments)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

-->


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-getEnrollments)

Kérelem azon fiókok felsorolására, amelyekhez hozzáféréssel rendelkezik:

```azurecli-interactive
> az billing account list
```

Válasz felsorolja az összes olyan regisztrációs fiókot, amelyhez hozzáférése van

```json
[
  {
    "accountStatus": "Unknown",
    "accountType": "Enterprise",
    "agreementType": "EnterpriseAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": [
      {
        "accountName": "Contoso",
        "accountOwner": "",
        "costCenter": "Test",
        "department": null,
        "endDate": null,
        "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
        "name": "7654321",
        "startDate": null,
        "status": null,
        "type": "Microsoft.Billing/enrollmentAccounts"
      }
    ],
    "enrollmentDetails": null,
    "hasReadAccess": false,
    "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
    "name": "1234567",
    "soldTo": {
      "addressLine1": null,
      "addressLine2": null,
      "addressLine3": null,
      "city": null,
      "companyName": "Contoso",
      "country": "US ",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": null,
      "region": null
    },
    "type": "Microsoft.Billing/billingAccounts"
  },
```
A számlázási hatókör és az `id` értéke megegyezik. A regisztrációs fiókjához tartozó `id` az a számlázási hatókör, amelyben az előfizetésre vonatkozó kérelem el lesz küldve. Fontos, hogy ismerje az azonosítót, mivel ez egy kötelező paraméter, amelyet a cikk későbbi részében az előfizetés létrehozásához fog használni.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Előfizetések létrehozása egy adott regisztrációs fiókhoz

Az alábbi példa egy *Dev Team Subscription* (Fejlesztői csapat előfizetés) nevű előfizetést hoz létre az előző lépésben kiválasztott regisztrációs fiókban. 

### <a name="rest"></a>[REST](#tab/rest-EA)

Az előfizetés-létrehozási kérelem/alias létrehozásához hívja meg a PUT API-t.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

A kérelem törzsében adja meg az egyik `enrollmentAccounts` `id` paraméterét a `billingScope` értékeként.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

A `Workload` lehetséges értékei a `Production` és a `DevTest`.

#### <a name="response"></a>Reagálás

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-EA)

A `New-AzSubscriptionAlias` parancsmagot tartalmazó modul legújabb verziójának telepítéséhez futtassa az `Install-Module Az.Subscription` parancsot. A PowerShellGet legújabb verziójának telepítéséhez lásd a [PowerShellGet modul beszerzésével](/powershell/scripting/gallery/installing-psget) foglalkozó témakört.

Futtassa az alábbi [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) parancsot a `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"` számlázási hatókör használatával. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload 'Production"
```

A parancsra adott válasz részeként megkapja a subscriptionId paramétert.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-EA)

Első lépésként telepítse a bővítményt az `az extension add --name account` és az `az extension add --name alias` futtatásával.

Futtassa az alábbi [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create) parancsot, és adja meg az egyik `enrollmentAccounts` `billing-scope` és `id` paraméterét. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
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

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Az Azure Enterprise-előfizetések létrehozási API-jára vonatkozó korlátozások

- Az API-val csak Azure Enterprise-előfizetések hozhatók létre.
- Egy regisztrációs fiókhoz legfeljebb 2000 előfizetés hozható létre. Ha elérte ezt a mennyiséget, csak az Azure Portalon hozhatók létre előfizetések a fiókhoz. Ha több előfizetést szeretne létrehozni az API-val, hozzon létre egy másik regisztrációs fiókot. A megszakított, a törölt és az átadott előfizetések is beleszámítanak a 2000-es korlátba.
- Azok a felhasználók, akik nem fióktulajdonosok, de hozzá lettek adva egy regisztrációs fiókhoz az Azure RBAC használatával, nem hozhatnak létre előfizetéseket az Azure Portalon.
- Nem választhatja ki, hogy melyik bérlőben szeretné hozni az előfizetést. Az előfizetés mindig a fióktulajdonos otthoni bérlőjében jön létre. Ha másik bérlőbe szeretne áthelyezni egy előfizetést, tekintse meg az [előfizetés bérlőjének módosítását](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) ismertető cikket.


## <a name="next-steps"></a>Következő lépések

* Most, hogy létrehozott egy előfizetést, elérhetővé teheti ezt a képességek más felhasználók és szolgáltatásnevek számára is. További információ: [Hozzáférés biztosítása nagyvállalati Azure-előfizetés létrehozáshoz (előzetes verzió)](grant-access-to-create-subscription.md).
* Több előfizetés felügyeleti csoportok használatával történő kezeléséről az [erőforrások Azure-beli felügyeleti csoportokkal való rendszerezését](../../governance/management-groups/overview.md) ismertető részben talál további információt.
