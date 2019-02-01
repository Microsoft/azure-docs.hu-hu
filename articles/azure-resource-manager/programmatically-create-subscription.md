---
title: Az Azure Enterprise-előfizetések létrehozása programozott módon |} A Microsoft Docs
description: Megtudhatja, hogyan további Azure Enterprise vagy a Enterprise Dev/Test-előfizetések létrehozása programozott módon.
services: azure-resource-manager
author: adpick
manager: adpick
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: adpick
ms.openlocfilehash: 1b772fdbda8e58db9414e09ef3ef7c98fc9f86b8
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55486979"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Programozott módon létrehozhat az Azure Enterprise-előfizetések (előzetes verzió)

Az Azure ügyfeleként [nagyvállalati szerződés (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), és hozhat létre nagyvállalati szerződés (MS-AZR - 0017 P) nagyvállalati szerződés – fejlesztés és tesztelés (MS-AZR - 0148 P) előfizetések programozott módon. Ebből a cikkből megismerheti, hogyan hozhatnak létre előfizetéseket programozott módon az Azure Resource Manager használatával.

Az API-ból egy Azure-előfizetést hoz létre, ha ezt az előfizetést a szerződés, amely alatt a Microsofttól vagy egy meghatalmazott viszonteladói beszerzett Microsoft Azure-szolgáltatások szabályozzák. További tudnivalókért lásd: [a Microsoft Azure – jogi információk](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

A regisztrációs fiók-előfizetések létrehoz egy tulajdonosi vagy közreműködői szerepkörhöz kell rendelkeznie. Ezek a szerepkörök beolvasása a két módja van:

* A regisztráció rendszergazdájához is [győződjön meg arról, hogy fióktulajdonos](https://ea.azure.com/helpdocs/addNewAccount) (bejelentkezés szükséges), amely lehetővé teszi, hogy a regisztrációs fiók tulajdonosának. Kövesse az utasításokat a meghívó e-mailben kapott manuálisan hozzon létre egy kezdeti előfizetést. Erősítse meg a fiók tulajdonjogát, és manuálisan hozzon létre egy kezdeti, mielőtt továbblép a következő lépés a nagyvállalati szerződéses előfizetésében. Csak hozzáadása a regisztrációt a fiók nem elég.

* A regisztrációs fiók meglévő tulajdonosává is [hozzáférési](grant-access-to-create-subscription.md). Hasonlóképpen, ha azt szeretné, a nagyvállalati szerződéses előfizetésében létrehozni az egyszerű szolgáltatás használatával, meg kell [megadása egyszerű szolgáltatást az előfizetések létrehozása](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Keresse meg a fiók rendelkezik hozzáféréssel

-Fiók tulajdonosai Azure EA-regisztrációhoz hozzáadják, miután az Azure a fiók regisztrációjának kapcsolat segítségével határozza meg, hol számlázási, előfizetési díjai. A fiók alatt létrehozott összes előfizetés számlázása a regisztrált Nagyvállalati szerződéshez, amely a fiók felé. Előfizetések létrehozása, akkor meg kell adnia értékeket a regisztrációs fiók és a felhasználó rendszerbiztonsági tagok, az előfizetés tulajdonosa. 

Az alábbi parancsok futtatásához, meg kell bejelentkeznie a fiók tulajdonosának *kezdőkönyvtár*, azaz a címtár előfizetések alapértelmezés szerint létrehozott.

# <a name="resttabrest"></a>[REST](#tab/rest)

A kérelem az összes regisztrációs fiókok listáját:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure válaszol az hozzáfér az összes regisztrációs fiókok listáját:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Használja a [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) parancsmag használatával listázhatja az összes regisztrációs fiókok hozzáfér.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Az Azure-fiókok objektumazonosítók és az e-mail-címek listája, válaszol.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja a [az számlázási regisztrációs-fióklista](https://aka.ms/EASubCreationPublicPreviewCLI) paranccsal listát készíthet az összes regisztrációs fiókok hozzáfér.

```azurecli-interactive 
az billing enrollment-account list
```

Az Azure-fiókok objektumazonosítók és az e-mail-címek listája, válaszol.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

Használja a `principalName` azonosítani a fiókot, amelyet az előfizetések kiszámlázható tulajdonságot. Használja a `id` , a `enrollmentAccount` érték, amely az előfizetés létrehozása a következő lépésben használja.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>A regisztrációs fiók-előfizetések létrehozására 

A következő példában létrehozunk egy kérelem nevű előfizetés létrehozása *fejlesztői csapat előfizetés* előfizetési ajánlatok között pedig *MS-AZR - 0017P* (normál nagyvállalati szerződéssel rendelkező). A regisztrációs fiók `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (helyőrző értékét, ez az érték a GUID), amely az, hogy a regisztrációs fiók a SignUpEngineering@contoso.com. Azt is két felhasználót ad RBAC tulajdonosként az előfizetés.

# <a name="resttabrest"></a>[REST](#tab/rest)

Használja a `id` , a `enrollmentAccount` az előfizetés létrehozására vonatkozó kérelem elérési útját.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Elem neve  | Szükséges | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nem      | String | Az előfizetés megjelenített neve. Ha nincs megadva, van-e állítva a név az ajánlat, mint például a "Microsoft Azure Enterprise."                                 |
| `offerType`   | Igen      | String | Az ajánlat az előfizetés. A nagyvállalati szerződéssel rendelkező két lehetőség áll rendelkezésre [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles környezetben való használathoz) és [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztés és tesztelés, kell lennie [van kapcsolva a nagyvállalati szerződések portáljának használatával](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nem       | String | Azok a felhasználók, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor, objektum azonosítója.  |

A válaszban szerezheti vissza egy `subscriptionOperation` figyelés objektumot. Ha az előfizetés létrehozása befejeződött, a `subscriptionOperation` objektum esetén ad vissza egy `subscriptionLink` objektum, amely rendelkezik az előfizetés azonosítóját.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Ez a modul előzetes verziójának használatához futtatásával telepítse `Install-Module Az.Subscription -AllowPrerelease` első. Győződjön meg arról, hogy `-AllowPrerelease` működik, a PowerShellGet legújabb verziójának telepítése [a PowerShellGet modul beolvasása](/powershell/gallery/installing-psget).

Használja a [New-AzSubscription](/powershell/module/az.subscription) rendszerképkeresés `enrollmentAccount` objektumazonosító, a `EnrollmentAccountObjectId` paraméterrel hozhat létre egy új előfizetést. 

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Elem neve  | Szükséges | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Nem      | String | Az előfizetés megjelenített neve. Ha nincs megadva, van-e állítva a név az ajánlat, mint például a "Microsoft Azure Enterprise."                                 |
| `OfferType`   | Igen      | String | Az ajánlat az előfizetés. A nagyvállalati szerződéssel rendelkező két lehetőség áll rendelkezésre [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles környezetben való használathoz) és [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztés és tesztelés, kell lennie [van kapcsolva a nagyvállalati szerződések portáljának használatával](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Igen       | String | Az eszközregisztráció-fiókot, hogy az előfizetés alatt létrehozott, és díjat pedig objektum azonosítója. Az értéket nem egy GUID Azonosítót a, a `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Nem       | String | Azok a felhasználók, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor, objektum azonosítója.  |
| `OwnerSignInName`    | Nem       | String | Azok a felhasználók, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor, e-mail-címét. Ez a paraméter helyett használható `OwnerObjectId`.|
| `OwnerApplicationId` | Nem       | String | Bármely, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor egyszerű szolgáltatás Alkalmazásazonosítója. Ez a paraméter helyett használható `OwnerObjectId`. Ez a paraméter használatakor az rendelkeznie kell az egyszerű szolgáltatás [olvasási hozzáférés a címtárhoz](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Összes paraméter teljes listáját lásd: [New-AzSubscription](/powershell/module/az.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Ez a minta bővítmény használatához futtatásával telepítse `az extension add --name subscription` első.

Használja a [az fiók létrehozása](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) rendszerképkeresés `enrollmentAccount` objektumazonosító, a `enrollment-account-object-id` paraméterrel hozhat létre egy új előfizetést.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elem neve  | Szükséges | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Nem      | String | Az előfizetés megjelenített neve. Ha nincs megadva, van-e állítva a név az ajánlat, mint például a "Microsoft Azure Enterprise."                                 |
| `offer-type`   | Igen      | String | Az ajánlat az előfizetés. A nagyvállalati szerződéssel rendelkező két lehetőség áll rendelkezésre [MS-AZR - 0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles környezetben való használathoz) és [MS-AZR - 0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztés és tesztelés, kell lennie [van kapcsolva a nagyvállalati szerződések portáljának használatával](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Igen       | String | Az eszközregisztráció-fiókot, hogy az előfizetés alatt létrehozott, és díjat pedig objektum azonosítója. Az értéket nem egy GUID Azonosítót a, a `az billing enrollment-account list`. |
| `owner-object-id`      | Nem       | String | Azok a felhasználók, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor, objektum azonosítója.  |
| `owner-upn`    | Nem       | String | Azok a felhasználók, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor, e-mail-címét. Ez a paraméter helyett használható `owner-object-id`.|
| `owner-spn` | Nem       | String | Bármely, amelyet szeretne az előfizetés tulajdonosaként RBAC hozzáadása létrehozáskor egyszerű szolgáltatás Alkalmazásazonosítója. Ez a paraméter helyett használható `owner-object-id`. Ez a paraméter használatakor az rendelkeznie kell az egyszerű szolgáltatás [olvasási hozzáférés a címtárhoz](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Összes paraméter teljes listáját lásd: [az fiók létrehozása](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Az Azure Enterprise-előfizetés létrehozása API korlátozásai

- Csak az Azure Enterprise-előfizetések az API használatával is létrehozható.
- Fiókonkénti 50 előfizetések korlátozva van. Ezt követően előfizetéseket csak az Account Center webhelyen lehet létrehozni.
- Szükség van legalább egy nagyvállalati szerződés vagy nagyvállalati szerződéssel rendelkező fejlesztési és tesztelési előfizetések a fiókban, ami azt jelenti, hogy a fióktulajdonos legalább egyszer keresztülment manuális-előfizetés.
- Felhasználók, akik nem tulajdonosai, de egy, az RBAC-n keresztül regisztrációs fiókhoz hozzáadott előfizetések Account Center használatával nem hozható létre.
- Nem választhat ki a bérlő az előfizetés kell létrehozni. A fiók tulajdonosának saját bérlőjén mindig létrejön az előfizetés. Az előfizetés áthelyezése egy másik bérlőhöz: [előfizetés-bérlő módosítása](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>További lépések

* A .NET használatával előfizetések létrehozása egy példa: [mintát a githubon](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Most, hogy létrehozott egy előfizetést, meg lehet adni, hogy lehetősége más felhasználók és az egyszerű szolgáltatások. További információkért lásd: [hozzáférést (előzetes verzió) az Azure Enterprise-előfizetések létrehozása](grant-access-to-create-subscription.md).
* Nagyszámú felügyeleti csoportok használatával előfizetések kezelésével kapcsolatos további tudnivalókért lásd: [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](management-groups-overview.md)
