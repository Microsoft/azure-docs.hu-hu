---
title: Azure-előfizetések létrehozása programozott módon, előzetes verziójú API-kkal
description: Megismerheti, hogyan hozhat létre további Azure-előfizetéseket programozott módon a REST API, az Azure CLI és az Azure PowerShell előzetes verziójának használatával.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: bc761d0bf4001fb1e3fb757b9be9e1e19689913d
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202994"
---
# <a name="programmatically-create-azure-subscriptions-with-preview-apis"></a>Azure-előfizetések létrehozása programozott módon, előzetes verziójú API-kkal

Ez a cikk segítséget nyújt az Azure-előfizetések programozott módon, a régebbi, előzetes verziójú API-k használatával történő létrehozásával kapcsolatban. Ebből a cikkből megtudhatja, hogyan hozhat létre előfizetéseket programozott módon az Azure Resource Manager használatával.

Új cikkek érhetőek el a legújabb API-verzióra vonatkozóan a különböző Azure-szerződések előfizetési típusaihoz:

- [Nagyvállalati Szerződéshez tartozó előfizetések programozott létrehozása a legújabb API-val](programmatically-create-subscription-enterprise-agreement.md)
- [Microsoft Ügyfélszerződéshez tartozó előfizetések programozott létrehozása a legújabb API-val](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Microsoft Partnerszerződéshez tartozó előfizetések programozott létrehozása a legújabb API-val](Programmatically-create-subscription-microsoft-customer-agreement.md)

Azonban az ezen cikkben szereplő információkat is használhatja, ha nem szeretné a legújabb API-verziót használni.

Az alábbi szerződéstípusok esetén a számlázási fiókkal rendelkező Azure-ügyfelek az előfizetéseket programozott módon is létrehozhatják:

- Nagyvállalati Szerződés
- Microsoft-ügyfélszerződés (Microsoft Customer Agreement, MCA)
- Microsoft-partnerszerződés (MPA)

Ha programozott módon hoz létre Azure-előfizetést, az előfizetést az a megállapodás szabályozza, amely keretében igénybe veszi a Microsoft vagy egy hivatalos viszonteladó által biztosított Azure-szolgáltatásokat. További információért lásd a [Microsoft Azure jogi információit](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-for-an-ea-billing-account"></a>Előfizetések létrehozása EA számlázási fiókhoz

Az alábbi szakaszokban ismertetett információk segítenek az EA-előfizetések létrehozásában.

### <a name="prerequisites"></a>Előfeltételek

Előfizetés létrehozásához tulajdonosi szerepkörrel kell rendelkeznie egy regisztrációs fiókban. A szerepkört kétféleképpen kaphatja meg:

* A regisztráció vállalati rendszergazdája [megteszi Önt fióktulajdonosnak](https://ea.azure.com/helpdocs/addNewAccount) (ehhez bejelentkezés szükséges), amely eredményeként Ön a regisztrációs fiók tulajdonosává válik.
* A regisztrációs fiók meglévő tulajdonosa [hozzáférést biztosít Önnek](grant-access-to-create-subscription.md). Hasonlóképpen, ha szolgáltatásnevet szeretne használni egy EA-előfizetés létrehozásához, [lehetővé kell tennie a szolgáltatásnév számára, hogy előfizetéseket hozzon létre](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Azon fiókok megkeresése, amelyekhez hozzáféréssel rendelkezik

Miután hozzáadták Önt egy fióktulajdonoshoz társított regisztrációs fiókhoz, az Azure a fiók és a regisztráció közötti kapcsolat alapján állapítja meg, hogy hová kell kiszámlázni az előfizetési díjakat. A fiókhoz létrehozott minden előfizetést annak az EA-regisztrációnak számláz ki, amelyben a fiók található. Előfizetések létrehozásához meg kell adni a regisztrációs fiókra vonatkozó értékeket, valamint az előfizetés tulajdonosának felhasználói nevét.

A következő parancsok futtatásához be kell jelentkeznie a fióktulajdonos *kezdőkönyvtárába*, amely az a könyvtár, amelyben az előfizetések alapértelmezetten létrejönnek.

### <a name="rest"></a>[REST](#tab/rest)

Kérelem azon fiókok felsorolására, amelyekhez hozzáféréssel rendelkezik:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Az API-válasz felsorolja azokat a regisztrációs fiókokat, amelyekhez hozzáféréssel rendelkezik:

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

A `principalName` tulajdonsággal megadhatja azt a fiókot, amelyhez az előfizetések ki lesznek számlázva. Másolja ki a fiók `name` elemét. Ha például előfizetéseket szeretne létrehozni a SignUpEngineering@contoso.com regisztrációs fiókhoz, másolja ki a ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` értéket. Az azonosító a regisztrációs fiók objektumazonosítója. Illessze be valahova az értéket, hogy a következő lépésben használni tudja az `enrollmentAccountObjectId` értékeként.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nyissa meg az [Azure Cloud Shellt](https://shell.azure.com/), majd válassza a PowerShell lehetőséget.

A [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) parancsmaggal jelenítse meg az Ön által hozzáférhető összes regisztrációs fiókot tartalmazó listát.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Az Azure megadja azon regisztrációs fiókok listáját, amelyekhez hozzáféréssel rendelkezik:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
A `principalName` tulajdonsággal megadhatja azt a fiókot, amelyhez az előfizetések ki lesznek számlázva. Másolja ki a fiók `ObjectId` elemét. Ha például előfizetéseket szeretne létrehozni a SignUpEngineering@contoso.com regisztrációs fiókhoz, másolja ki a ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` értéket. Illessze be valahova az objektumazonosítót, hogy a következő lépésben használni tudja az `enrollmentAccountObjectId` értékeként.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Használja az [az billing enrollment-account list](/cli/azure/billing) parancsot azon regisztrációs fiókok listázásához, amelyekhez hozzáféréssel rendelkezik.

```azurecli-interactive
az billing enrollment-account list
```

Az Azure megadja azon regisztrációs fiókok listáját, amelyekhez hozzáféréssel rendelkezik:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]
```

A `principalName` tulajdonsággal megadhatja azt a fiókot, amelyhez az előfizetések ki lesznek számlázva. Másolja ki a fiók `name` elemét. Ha például előfizetéseket szeretne létrehozni a SignUpEngineering@contoso.com regisztrációs fiókhoz, másolja ki a ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` értéket. Az azonosító a regisztrációs fiók objektumazonosítója. Illessze be valahova az értéket, hogy a következő lépésben használni tudja az `enrollmentAccountObjectId` értékeként.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Előfizetések létrehozása egy adott regisztrációs fiókhoz

Az alábbi példa egy *Dev Team Subscription* (Fejlesztői csapat előfizetés) nevű előfizetést hoz létre az előző lépésben kiválasztott regisztrációs fiókban. Az előfizetési ajánlat az *MS-AZR-0017P* (normál Microsoft Nagyvállalati Szerződés). Két felhasználót is hozzáad az előfizetéshez Azure RBAC-tulajdonosként (ez nem kötelező).

### <a name="rest"></a>[REST](#tab/rest)

Hajtsa végre a következő kérést, amelyben cserélje le az `<enrollmentAccountObjectId>` értéket az előző lépésben kimásolt `name` értékével (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Tulajdonosok megadásához tekintse meg a [felhasználói objektumazonosítók lekérésének módját](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

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

| Elem neve  | Kötelező | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nem      | Sztring | Az előfizetés megjelenített neve. Ha nincs megadva, a rendszer az ajánlat nevét használja névként, például „Microsoft Azure Enterprise”.                                 |
| `offerType`   | Igen      | Sztring | Az előfizetési ajánlat. Az EA-nál elérhető két lehetőség az [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles használatra) és az [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztési és tesztelési célokra, [be kell kapcsolni az EA Portalon](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nem       | Sztring | Annak a felhasználónak az objektumazonosítója, akit az előfizetés létrehozásakor Azure RBAC-tulajdonosként szeretne megadni.  |

A válaszban, a `Location` fejléc részeként visszakap egy URL-címet, amellyel lekérdezheti az előfizetést létrehozó művelet állapotát. Ha az előfizetés létrehozása befejeződött, a GET művelet a `Location` URL-címen egy `subscriptionLink` objektumot ad vissza, amely tartalmazza az előfizetés azonosítóját. További részletekért lásd a [Subscription API dokumentációját](/rest/api/subscription/).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A `New-AzSubscription` parancsmagot tartalmazó modul legújabb verziójának telepítéséhez futtassa az `Install-Module Az.Subscription` parancsot. A PowerShellGet legújabb verziójának telepítéséhez lásd a [PowerShellGet modul beszerzésével](/powershell/scripting/gallery/installing-psget) foglalkozó témakört.

Futtassa az alábbi [New-AzSubscription](/powershell/module/az.subscription) parancsot, amelyben cserélje le az `<enrollmentAccountObjectId>` értéket az `ObjectId` első lépésben gyűjtött értékére (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Tulajdonosok megadásához tekintse meg a [felhasználói objektumazonosítók lekérésének módját](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Elem neve  | Kötelező | Típus   | Leírás |
|---------------|----------|--------|----|
| `Name` | Nem      | Sztring | Az előfizetés megjelenített neve. Ha nincs megadva, a rendszer az ajánlat nevét használja névként, például: *Microsoft Azure Enterprise*. |
| `OfferType`   | Igen      | Sztring | Az előfizetési ajánlat. Az EA-nál elérhető két lehetőség az [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles használatra) és az [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztési és tesztelési célokra, [be kell kapcsolni az EA Portalon](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Igen       | Sztring | Azon regisztrációs fiók objektumazonosítója, amelyhez az előfizetés létre lett hozva, és amelyhez ki lesz számlázva. Az érték egy GUID, amelyet a `Get-AzEnrollmentAccount` parancs ad vissza. |
| `OwnerObjectId`      | Nem       | Sztring | Annak a felhasználónak az objektumazonosítója, akit az előfizetés létrehozásakor Azure RBAC-tulajdonosként szeretne megadni.  |
| `OwnerSignInName`    | Nem       | Sztring | Annak a felhasználónak az e-mail-címe, akit az előfizetés létrehozásakor Azure RBAC-tulajdonosként szeretne megadni. A paramétert az `OwnerObjectId` helyett használhatja.|
| `OwnerApplicationId` | Nem       | Sztring | Bármely olyan szolgáltatásnév alkalmazásazonosítója, amelyet az előfizetés létrehozásakor Azure RBAC-tulajdonosként szeretne megadni. A paramétert az `OwnerObjectId` helyett használhatja. Ha használja ezt a paramétert, a szolgáltatásnévnek [olvasási hozzáféréssel kell rendelkeznie a könyvtárhoz](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

A paraméterek teljes listáját lásd a [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription) témakörben.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Első lépésként telepítse ezt az előzetes verziójú bővítményt az `az extension add --name subscription` parancs futtatásával.

Futtassa az alábbi [az account create](/cli/azure/ext/subscription/account#-ext-subscription-az-account-create) parancsot, amelyben cserélje le az `<enrollmentAccountObjectId>` értéket a `name` első lépésben kimásolt értékére (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Tulajdonosok megadásához tekintse meg a [felhasználói objektumazonosítók lekérésének módját](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Elem neve  | Kötelező | Típus   | Leírás |
|---------------|----------|--------|------------|
| `display-name` | Nem      | Sztring | Az előfizetés megjelenített neve. Ha nincs megadva, a rendszer az ajánlat nevét használja névként, például: *Microsoft Azure Enterprise*.|
| `offer-type`   | Igen      | Sztring | Az előfizetési ajánlat. Az EA-nál elérhető két lehetőség az [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (éles használatra) és az [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (fejlesztési és tesztelési célokra, [be kell kapcsolni az EA Portalon](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Igen       | Sztring | Azon regisztrációs fiók objektumazonosítója, amelyhez az előfizetés létre lett hozva, és amelyhez ki lesz számlázva. Az érték egy GUID, amelyet a `az billing enrollment-account list` parancs ad vissza. |
| `owner-object-id`      | Nem       | Sztring | Annak a felhasználónak az objektumazonosítója, akit az előfizetés létrehozásakor Azure RBAC-tulajdonosként szeretne megadni.  |
| `owner-upn`    | Nem       | Sztring | Annak a felhasználónak az e-mail-címe, akit az előfizetés létrehozásakor Azure RBAC-tulajdonosként szeretne megadni. A paramétert az `owner-object-id` helyett használhatja.|
| `owner-spn` | Nem       | Sztring | Bármely olyan szolgáltatásnév alkalmazásazonosítója, amelyet az előfizetés létrehozásakor Azure RBAC-tulajdonosként szeretne megadni. A paramétert az `owner-object-id` helyett használhatja. Ha használja ezt a paramétert, a szolgáltatásnévnek [olvasási hozzáféréssel kell rendelkeznie a könyvtárhoz](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

A paraméterek teljes listáját lásd az [az account create](/cli/azure/ext/subscription/account#-ext-subscription-az-account-create) témakörben.

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Az Azure Enterprise-előfizetések létrehozási API-jára vonatkozó korlátozások

- Az API-val csak Azure Enterprise-előfizetések hozhatók létre.
- Egy regisztrációs fiókhoz legfeljebb 2000 előfizetés hozható létre. Ha elérte ezt a mennyiséget, csak az Azure Portalon hozhatók létre előfizetések a fiókhoz. Ha több előfizetést szeretne létrehozni az API-val, hozzon létre egy másik regisztrációs fiókot. A megszakított, a törölt és az átadott előfizetések is beleszámítanak a 2000-es korlátba.
- Azok a felhasználók, akik nem fióktulajdonosok, de hozzá lettek adva egy regisztrációs fiókhoz az Azure RBAC használatával, nem hozhatnak létre előfizetéseket az Azure Portalon.
- Nem választhatja ki, hogy melyik bérlőben szeretné hozni az előfizetést. Az előfizetés mindig a fióktulajdonos otthoni bérlőjében jön létre. Ha másik bérlőbe szeretne áthelyezni egy előfizetést, tekintse meg az [előfizetés bérlőjének módosítását](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) ismertető cikket.


## <a name="create-subscriptions-for-an-mca-account"></a>Előfizetések létrehozása MCA-fiókhoz

Az alábbi szakaszokban ismertetett információk segítenek az MCA-fiókhoz tartozó előfizetések létrehozásában.

### <a name="prerequisites"></a>Előfeltételek

Előfizetések létrehozásához tulajdonosi, közreműködői vagy Azure-előfizetések létrehozói szerepkörével kell rendelkeznie egy számlaszakaszra, vagy tulajdonos vagy közreműködői szerepkörrel egy számlázási profilra vagy számlázási fiókra vonatkozóan. További információkért lásd [az előfizetés számlázási szerepköreit és azok feladatait](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Az alábbi példák REST API-kat használnak. A PowerShell és az Azure CLI jelenleg nem támogatottak.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Azon számlázási fiókok megkeresése, amelyekhez hozzáféréssel rendelkezik

Hajtsa végre az alábbi kérést az összes számlázási fiók felsorolásához.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
Az API-válasz felsorolja azokat számlázási fiókokat, amelyekhez hozzáféréssel rendelkezik.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
A `displayName` tulajdonsággal azonosíthatja azt a számlázási fiókot, amelyhez előfizetéseket szeretne létrehozni. Győződjön meg arról, hogy a fiók agreementType tulajdonsága *MicrosoftCustomerAgreement*. Másolja ki a fiók `name` elemét.  Ha például a `Contoso` számlázási fiókhoz szeretne előfizetést létrehozni, másolja ki az `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` értéket. Illessze be valahova az értéket, hogy a következő lépésben használni tudja.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Számlaszakaszok keresése előfizetések létrehozásához

Az előfizetésért felszámolt díjak a számlázási profilok számláinak egy szakaszában vannak feltüntetve. Az alábbi API-val kérje le azon számlaszakaszok és számlázási profilok listáját, amelyeken engedéllyel rendelkezik Azure-előfizetések létrehozásához.

Hajtsa végre a következő kérést, amelyben cserélje le a `<billingAccountName>` értéket az előző lépésben kimásolt `name` értékével (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```

Az API-válasz felsorolja az összes számlaszakaszt és a hozzájuk tartozó azon számlázási profilokat, amelyeken hozzáféréssel rendelkezik előfizetések létrehozásához:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

Az `invoiceSectionDisplayName` tulajdonsággal azonosíthatja azt a számlaszakaszt, amelyhez előfizetéseket szeretne létrehozni. Másolja ki a számlaszakasz `invoiceSectionId`, `billingProfileId` és `skuId` értékét. Például `Microsoft Azure plan` típusú előfizetés a `Development` számlaszakaszhoz történő létrehozásához másolja ki a `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` és `0001` értéket. Illessze be valahová ezeket az értékeket, hogy a következő lépésben használni tudja őket.

### <a name="create-a-subscription-for-an-invoice-section"></a>Előfizetés létrehozása számlaszakaszhoz

Az alábbi példa egy *Microsoft Azure-csomag típusú*, *Fejlesztői csapat előfizetés* nevű előfizetést hoz létre a *Fejlesztés* számlaszakaszhoz. Az előfizetés a *Contoso pénzügyi* számlázási profilon lesz kiszámlázva, és a számla *Fejlesztés* szakaszában jelenik meg.

Hajtsa végre a következő kérést, amelyben cserélje le az `<invoiceSectionId>` értéket a második lépésben kimásolt `invoiceSectionId` értékével (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Adja meg a második lépésben kimásolt `billingProfileId` és `skuId` értéket az API kérelemparamétereiben. Tulajdonosok megadásához tekintse meg a [felhasználói objektumazonosítók lekérésének módját](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Elem neve  | Kötelező | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Igen      | Sztring | Az előfizetés megjelenített neve.|
| `billingProfileId`   | Igen      | Sztring | Azon számlázási profil azonosítója, amelyre az előfizetés költségei ki vannak számlázva.  |
| `skuId` | Igen      | Sztring | Az Azure-csomag típusát meghatározó termékváltozat-azonosító. |
| `owners`      | Nem       | Sztring | Annak a felhasználónak vagy szolgáltatásnévnek az objektumazonosítója, akit az előfizetés létrehozásakor Azure RBAC-tulajdonosként szeretne megadni.  |
| `costCenter` | Nem      | Sztring | Az előfizetéssel társított költséghely. A használati adatokat tartalmazó CSV-fájlban jelenik meg. |
| `managementGroupId` | Nem      | Sztring | Azon felügyeleti csoport azonosítója, amelyhez hozzá lesz adva az előfizetés. A felügyeleti csoportok listájának lekéréséhez tekintse meg a [Felügyeleti csoportok – List API](/rest/api/resources/managementgroups/list) című cikket. Használja a felügyeleti csoport az API-ban lévő azonosítóját. |

A válaszban egy `subscriptionCreationResult` objektumot kap vissza a monitorozáshoz. Ha az előfizetés létrehozása befejeződött, a `subscriptionCreationResult` objektum egy `subscriptionLink` objektumot ad vissza, amely tartalmazza az előfizetés azonosítóját.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Előfizetések létrehozása MPA számlázási fiókhoz

Az alábbi szakaszokban ismertetett információk segítenek az MCA-számlázási fiókhoz tartozó előfizetések létrehozásában.

### <a name="prerequisites"></a>Előfeltételek

Ha előfizetést szeretne létrehozni a számlázási fiókjához, globális rendszergazdai vagy rendszergazdai ügynök szerepkörrel kell rendelkeznie a szervezete felhőszolgáltatójának fiókjában. További információ: [Partnerközpont – Felhasználói szerepkörök és engedélyek hozzárendelése](/partner-center/permissions-overview).

Az alábbi példák REST API-kat használnak. A PowerShell és az Azure CLI jelenleg nem támogatottak.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Azon számlázási fiókok megkeresése, amelyekhez hozzáféréssel rendelkezik

Hajtsa végre az alábbi kérést azon számlázási fiókok felsorolásához, amelyekhez hozzáféréssel rendelkezik.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

Az API-válasz felsorolja a számlázási fiókokat.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
A `displayName` tulajdonsággal azonosíthatja azt a számlázási fiókot, amelyhez előfizetéseket szeretne létrehozni. Győződjön meg arról, hogy a fiók agreementType tulajdonságának értéke *MicrosoftPartnerAgreement*. Másolja a vágólapra a fiók `name` értékét. Ha például a `Contoso` számlázási fiókhoz szeretne előfizetést létrehozni, másolja ki az `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` értéket. Illessze be valahova az értéket, hogy a következő lépésben használni tudja.

### <a name="find-customers-that-have-azure-plans"></a>Azure-csomaggal rendelkező ügyfelek keresése

Hajtsa végre a következő kérést, és cserélje le a `<billingAccountName>` értéket az első lépésben kimásolt `name` értékkel (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```). Ezzel a kéréssel felsorolhatja a számlázási fiókban lévő összes ügyfelet, akihez Azure-előfizetést hozhat létre.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
Az API-válasz felsorolja a számlázási fiókban lévő, Azure-csomaggal rendelkező ügyfeleket. Ezekhez az ügyfelekhez hozhat létre előfizetéseket.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

A `displayName` tulajdonsággal azonosíthatja azt az ügyfelet, amelyhez előfizetéseket szeretne létrehozni. Másolja a vágólapra az ügyfél `id` értékét. Ha például előfizetést szeretne létrehozni a `Fabrikam toys` ügyfélhez, másolja ki a `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx` értéket. Illessze be valahová az értéket, hogy a következő lépésekben használni tudja.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Választható lehetőség közvetett szolgáltatók számára: Ügyfél viszonteladóinak lekérése

Ha Ön közvetett szolgáltató a CSP kétrétegű modelljében, megadhatja a viszonteladót, amikor előfizetést hoz létre az ügyfelek számára.

Hajtsa végre a következő kérést, és cserélje le a `<customerId>` értéket a második lépésben kimásolt `id` értékkel (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```), hogy megjelenjen az ügyfelek számára elérhető viszonteladók listája.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
Az API-válasz felsorolja az ügyfélhez tartozó viszonteladókat:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```

A `description` tulajdonsággal azonosíthatja az előfizetéshez társítandó viszonteladót. Másolja a vágólapra a viszonteladó `resellerId` értékét. Például a `Wingtip` társításához, másolja ki a `3xxxxx` értéket. Illessze be valahova az értéket, hogy a következő lépésben használni tudja.

### <a name="create-a-subscription-for-a-customer"></a>Előfizetés létrehozása egy ügyfél számára

Az alábbi példa egy *Fejlesztői csapat előfizetése* nevű előfizetést hoz létre a *Fabrikam toys* ügyfélhez, és társítja a *Wingtip* viszonteladót az előfizetéshez. 

Hajtsa végre a következő kérést, amelyben cserélje le az `<customerId>` értéket a második lépésben kimásolt `id` értékével (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Adja meg a második lépésben kimásolt, nem kötelező *resellerId* értéket az API kérelemparamétereiben.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Elem neve  | Kötelező | Típus   | Leírás                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Igen      | Sztring | Az előfizetés megjelenített neve.|
| `skuId` | Igen      | Sztring | Az Azure-csomag termékváltozat-azonosítója. A Microsoft Azure-csomag típusú előfizetésekhez használja a *0001* értéket |
| `resellerId`      | Nem       | Sztring | Azon viszonteladó MPN-azonosítója, aki az előfizetéshez lesz társítva.  |

A válaszban egy `subscriptionCreationResult` objektumot kap vissza a monitorozáshoz. Ha az előfizetés létrehozása befejeződött, a `subscriptionCreationResult` objektum egy `subscriptionLink` objektumot ad vissza. Ez tartalmazza az előfizetés azonosítóját.

## <a name="next-steps"></a>Következő lépések

- Egy Nagyvállalati Szerződéssel (EA) rendelkező előfizetés .NET használatával történő létrehozását bemutató példa megtekintéséért tekintse meg a [GitHubon elérhető mintakódot](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Most, hogy létrehozott egy előfizetést, elérhetővé teheti ezt a képességek más felhasználók és szolgáltatásnevek számára is. További információ: [Hozzáférés biztosítása nagyvállalati Azure-előfizetés létrehozáshoz (előzetes verzió)](grant-access-to-create-subscription.md).
* Több előfizetés felügyeleti csoportok használatával történő kezeléséről az [erőforrások Azure-beli felügyeleti csoportokkal való rendszerezését](../../governance/management-groups/overview.md) ismertető részben talál további információt.
