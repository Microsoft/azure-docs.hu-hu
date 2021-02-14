---
title: Azure-fiók csatolása egy partnerazonosítóhoz
description: Kövesse nyomon az Azure-ügyfelekkel való együttműködéseket a partnerazonosító csatolásával ahhoz a felhasználói fiókhoz, amelyet az ügyfélerőforrások kezelésére használ.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 10/05/2020
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.openlocfilehash: a214e91307308e191ce92b6461c1454d2cc7dd2b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370478"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Partnerazonosítók csatolása az Azure-fiókhoz

A Microsoft-partnerek olyan szolgáltatásokat biztosítanak, amelyek segítségével az ügyfelek a Microsoft termékeinek használatával elérhetik üzleti és stratégiai célkitűzéseiket. Ha egy ügyfél nevében az Azure-szolgáltatásokat kezeli, konfigurálja és támogatja, akkor a partnerfelhasználóknak hozzá kell férniük az ügyfél környezetéhez. A partnerrendszergazdai hivatkozás (Partner Admin Link, PAL) használatával a partnerek társítani tudják a partnerhálózati azonosítójukat a szolgáltatás teljesítéséhez használt hitelesítő adatokkal.

A PAL lehetővé teszi, hogy a Microsoft azonosítsa és felismerje az Azure-ügyfelek sikerességért felelős partnereket. A Microsoft a fiók engedélyei (Azure-szerepkör) és a hatókör (előfizetés, erőforráscsoport, erőforrás) alapján biztosíthat a szervezet számára befolyást, illetve az Azure által felhasznált bevételt.

## <a name="get-access-from-your-customer"></a>Hozzáférés kérése az ügyféltől

A partnerazonosító csatolása előtt ügyfelének hozzáférést kell adnia Önnek az Azure-erőforrásokhoz a következő lehetőségek valamelyikének használatával:

- **Vendégfelhasználó**: Ügyfele hozzáadhatja Önt vendégfelhasználóként, és bármilyen Azure-szerepkört hozzárendelhet. További információkért tekintse meg a [Vendégfelhasználók hozzáadása másik címtárból](../../active-directory/external-identities/what-is-b2b.md) szakaszt.

- **Címtárfiók**: Az ügyfél létrehozhat egy felhasználói fiókot az Ön számára a saját címtárában, és bármilyen Azure-szerepkört hozzárendelhet.

- **Egyszerű szolgáltatás**: Az ügyfél a címtárában hozzáadhat az Ön szervezetéből származó alkalmazást vagy szkriptet, és hozzárendelhet bármilyen Azure-szerepkört. Az alkalmazás vagy szkript identitása szolgáltatásnévként ismert.

- **Azure Lighthouse**: Ügyfele delegálhat egy előfizetést (vagy erőforráscsoportot), így a felhasználói képesek használni a bérlőjéből. További információkért lásd az [Azure által delegált erőforrás-kezelést](../../lighthouse/concepts/azure-delegated-resource-management.md).

## <a name="link-to-a-partner-id"></a>Csatolás partnerazonosítóhoz

Amikor hozzáfér az ügyfelek erőforrásaihoz, az Azure Portalt, a PowerShellt vagy az Azure CLI-t használja, hogy Microsoft Partner Network-azonosítóját (MPN ID) a fiókazonosítóhoz vagy a szolgáltatásnévhez csatolja. Partnerazonosító csatolása az egyes ügyfélbérlőkben.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Az Azure Portal használata egy új partnerazonosítóhoz való csatolásához

1. Lépjen a [Csatolás partnerazonosítóhoz](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) területre az Azure Portalon.

2. Jelentkezzen be az Azure portálra.

3. Adja meg a Microsoft-partnerazonosítót. A partnerazonosító megegyezik szervezet [Microsoft Partner Network](https://partner.microsoft.com/)-azonosítójával. Ügyeljen arra, hogy a partnerprofilon szereplő **társított MPN-azonosítót** használja.

   ![A Csatolás partnerazonosítóhoz funkciót megjelenítő képernyőkép](./media/link-partner-id/link-partner-id01.png)

4. Ha egy másik ügyfélhez szeretne partnerazonosítót kapcsolni, váltson át a címtárra. A **Címtár váltása** területen válassza ki a címtárat.

   ![A Címtár váltása funkciót mutató képernyőkép](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>A PowerShell használata egy új partnerazonosítóhoz való csatolásához

1. Telepítse az [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) PowerShell-modult.

2. Jelentkezzen be a felhasználói fiókkal vagy a szolgáltatásnévvel az ügyfél bérlőjébe. További információkért lásd: [Bejelentkezés a PowerShell-lel](/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Csatolás az új partnerazonosítóhoz. A partnerazonosító megegyezik szervezet [Microsoft Partner Network](https://partner.microsoft.com/)-azonosítójával. Ügyeljen arra, hogy a partnerprofilon szereplő **társított MPN-azonosítót** használja.


    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>A csatolt partnerazonosító lekérése
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>A csatolt partnerazonosító frissítése
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>A csatolt partnerazonosító törlése
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Az Azure CLI használata egy új partnerazonosítóhoz való csatolásához
1. Telepítse az Azure CLI-bővítményt.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Jelentkezzen be a felhasználói fiókkal vagy a szolgáltatásnévvel az ügyfél bérlőjébe. További információkért lásd: [Bejelentkezés az Azure CLI-vel](/cli/azure/authenticate-azure-cli).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Csatolás az új partnerazonosítóhoz. A partnerazonosító megegyezik szervezet [Microsoft Partner Network](https://partner.microsoft.com/)-azonosítójával.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>A csatolt partnerazonosító lekérése
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>A csatolt partnerazonosító frissítése
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>A csatolt partnerazonosító törlése
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>További lépések

A hírekért és visszajelzések küldéséhez csatlakozzon a [Microsoft-partnerközösség](https://aka.ms/PALdiscussion) vitafórumához.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Ki csatolhat partnerazonosítót?**

A partnerszervezet bármely, az ügyfél Azure-erőforrásait kezelő felhasználója csatolhat a fiókhoz partnerazonosítót.

**A csatolás után módosítható a partnerazonosító?**

Igen. A csatolt partnerazonosító módosítható, hozzáadható vagy eltávolítható.

**Mi történik, ha egy felhasználó több ügyfélbérlőben is rendelkezik fiókkal?**

A partnerazonosító és a fiók közötti hivatkozás minden ügyfél bérlője számára elkészül. Partnerazonosító csatolása az egyes ügyfélbérlőkben.

Ha azonban az Azure Lighthouse-on keresztül kezeli az ügyfelek erőforrásait, akkor létre kell hoznia a hivatkozást a szolgáltató bérlőjében egy olyan fiók használatával, amely hozzáféréssel rendelkezik az ügyfél erőforrásaihoz. További információért lásd [a partnerazonosító a delegált erőforrásokra gyakorolt hatás nyomon követéséhez történő csatolását](../../lighthouse/how-to/partner-earned-credit.md) ismertető részt.

**Más partnerek vagy ügyfelek is szerkeszthetik vagy eltávolíthatják a partnerazonosítóra mutató hivatkozást?**

A hivatkozás a felhasználói fiók szintjéhez kapcsolódik. Csak Ön szerkesztheti vagy távolíthatja el a partnerazonosítóra mutató hivatkozást. Az ügyfél vagy más partnerek nem szerkeszthetik vagy távolíthatják el a partnerazonosítóra mutató hivatkozást.

**Melyik MPN-azonosítót kell használnom, ha a vállalat többel is rendelkezik?**

Ügyeljen arra, hogy a partnerprofilban szereplő **társított MPN-azonosítót** használja.

**Hol találhatok befolyásolt bevételjelentést a csatolt partnerazonosítóhoz?**

A Cloud Product Performance jelentéskészítő szolgáltatás a Partner Centerben a [Saját információk irányítópultján](https://partner.microsoft.com/membership/reports/myinsights) érhető el a partnerek számára. Ott a partner társítási típusaként a partnerrendszergazda hivatkozást kell kiválasztania.

**Miért nem látom az ügyfelemet a jelentésekben?**

A következő lehetséges okok miatt nem jelenik meg az ügyfél a jelentésekben

1. A csatolt felhasználói fiók nem rendelkezik [Azure-beli szerepköralapú hozzáférés-vezérléssel (Azure RBAC-vel)](../../role-based-access-control/overview.md) egyik ügyfél Azure-előfizetéséhez vagy erőforrásához sem.

2. Nincs használatban az Azure-előfizetés, amelyben a felhasználó [Azure-beli szerepköralapú hozzáférés-vezérléssel (Azure RBAC-vel)](../../role-based-access-control/overview.md) rendelkezik.

**Működik-e a partnerazonosító csatolása funkció az Azure Stackkel?**

Igen, partnerazonosítóját csatolhatja az Azure Stackhez.

**Hogyan csatolhatom a partnerazonosítómat, ha a cégem [Azure Lighthouse-t](../../lighthouse/overview.md) használ az ügyfelek erőforrásainak eléréséhez?**

Ahhoz, hogy az Azure Lighthouse-tevékenységek felismerhetők legyenek, hozzá kell rendelnie az MPN-azonosítót legalább egy olyan felhasználói fiókhoz, amely hozzáfér a beérkező előfizetésekhez. Vegye figyelembe, hogy ezt a szolgáltató bérlőjében kell megtennie, nem pedig az egyes ügyfélbérlőkben. Az egyszerűség kedvéért azt javasoljuk, hogy hozzon létre egy olyan szolgáltatásnév-fiókot a bérlőjében, amely az MPN-azonosítójához van társítva, és adjon számára hozzáférést minden olyan ügyfélhez, akit [partneri jóváírásra jogosult, beépített Azure-szerepkörrel](/partner-center/azure-roles-perms-pec) vesz fel. További információért lásd [a partnerazonosító a delegált erőforrásokra gyakorolt hatás nyomon követéséhez történő csatolását](../../lighthouse/how-to/partner-earned-credit.md) ismertető részt.

**Hogyan ismertethetem a partnerrendszergazdai hivatkozást (PAL-t) az ügyfeleimmel?**

A partnerrendszergazdai hivatkozás (PAL) lehetővé teszi, hogy a Microsoft azonosítsa és felismerje azokat a partnereket, amelyek segítenek elérni az ügyfelek üzleti céljait és megvalósítani a felhő adta értékeket. Az ügyfeleknek először partneri hozzáférést kell biztosítaniuk az Azure-erőforrásukhoz. A hozzáférés megadása után a rendszer egy Microsoft Partner Network-azonosítót (MPN-azonosítót) társít a partnerhez. Ez a társítás segít a Microsoftnak megismerni az informatikai szolgáltatók ökoszisztémáját, valamint segítséget nyújt a közös ügyfeleink legjobb támogatásához szükséges eszközök és programok finomhangolásában.

**Milyen adatokat gyűjt a PAL?**

A PAL meglévő hitelesítő adatokkal történő társítása nem küld új ügyféladatokat a Microsoftnak. Egyszerűen a telemetriát biztosítja a Microsoft számára, amelyben a partner aktívan jelen van az ügyfél Azure-környezetében. A Microsoft az ügyfél által a partner számára biztosított fiókengedélyek (Azure-szerepkör) és hatókör (felügyeleti csoport, előfizetés, erőforráscsoport, erőforrás) alapján biztosíthat a partnerszervezet számára befolyást és az Azure által felhasznált bevételt az ügyfélkörnyezetből. 

**Ez hatással van az ügyfél Azure-környezetének biztonságára?**

A PAL társítása csak a partner MPN-azonosítóját adja hozzá a már konfigurált hitelesítő adatokhoz, és nem módosítja az engedélyeket (Azure-szerepkört), valamint nem biztosít az Azure-szolgáltatással kapcsolatos további információkat a partner vagy a Microsoft számára.