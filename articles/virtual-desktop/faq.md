---
title: Windows rendszerű virtuális asztali GYIK – Azure
description: Gyakori kérdések és ajánlott eljárások a Windows rendszerű virtuális asztali gépekhez.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8592b679fcfbb860962bf75b882dc1a0543412c0
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613969"
---
# <a name="windows-virtual-desktop-faq"></a>Windows Virtual Desktop – gyakori kérdések

Ez a cikk a gyakori kérdésekre adott válaszokat és a Windows rendszerű virtuális asztalok ajánlott eljárásait ismerteti.

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>Milyen minimális rendszergazdai engedélyek szükségesek az objektumok kezeléséhez?

Ha gazdagép-készleteket és egyéb objektumokat szeretne létrehozni, hozzá kell rendelnie a közreműködő szerepkört azon az előfizetésen vagy erőforráscsoporthoz, amelyen dolgozik.

Ahhoz, hogy a felhasználók vagy felhasználói csoportok számára alkalmazás-csoportokat lehessen közzétenni, hozzá kell rendelnie a felhasználói hozzáférés rendszergazdai szerepkört egy alkalmazás-csoporthoz.

Ha korlátozni szeretné, hogy a rendszergazda csak a felhasználói munkameneteket kezelje, például üzeneteket küldjön a felhasználóknak, kijelentkezzen a felhasználókat, és így tovább, létrehozhat egyéni szerepköröket is. Például:

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>Támogatja a Windows Virtual Desktop a Split Azure Active Directory modelleket?

Amikor egy felhasználó hozzá van rendelve egy alkalmazás-csoporthoz, a szolgáltatás egyszerű Azure-szerepkör-hozzárendelést végez. Ennek eredményeképpen a felhasználó Azure Active Directory (AD) és az alkalmazás csoportjának Azure AD-nek ugyanazon a helyen kell lennie. Az összes szolgáltatási objektumnak, például a gazdagép-készleteknek, az alkalmazás-csoportoknak és a munkaterületeknek is ugyanabban az Azure AD-ben kell lennie, mint a felhasználónak.

A virtuális gépeket egy másik Azure AD-ben is létrehozhatja, ha a Active Directoryt a felhasználó Azure AD-vel szinkronizálja ugyanazon a virtuális hálózaton (VNET).

## <a name="what-are-location-restrictions"></a>Mik a helyek korlátozásai?

Minden szolgáltatási erőforráshoz tartozik egy hely társítva. A címkészlet helye határozza meg, hogy a rendszer hol tárolja a gazdagép metaadatait. Egy alkalmazáscsoport nem létezhet gazdagép-készlet nélkül. Ha alkalmazásokat ad hozzá egy RemoteApp-alkalmazás csoportjához, a Start menü alkalmazásainak meghatározásához egy munkamenet-gazdagépre is szüksége lesz. Bármely alkalmazáscsoport működéséhez szükség van egy kapcsolódó adat-hozzáférésre is a gazdagépen. Annak biztosítása érdekében, hogy a rendszer ne továbbítsa az adatátvitelt több hely között, az alkalmazás csoportjának meg kell egyeznie a gazdagép készletének helyétől.

A munkaterületeknek az alkalmazás csoportjaival megegyező helyen kell lenniük. Amikor a munkaterület frissül, a kapcsolódó alkalmazáscsoport a szolgáltatással együtt frissül. Az alkalmazás-csoportokhoz hasonlóan a szolgáltatáshoz az is szükséges, hogy minden munkaterület társítva legyen ugyanahhoz a helyhez létrehozott alkalmazás-csoportokhoz.

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>Hogyan lehet kibontani egy objektum tulajdonságait a PowerShellben?

PowerShell-parancsmag futtatásakor csak az erőforrás neve és helye jelenik meg.

Például:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

Az összes erőforrás tulajdonságainak megtekintéséhez adja hozzá a vagy a parancsot `format-list` `fl` a parancsmag végéhez.

Például:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

Adott tulajdonságok megjelenítéséhez adja hozzá a megadott tulajdonságokat a vagy a után `format-list` `fl` .

Például:

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>Támogatja a Windows Virtual Desktop a vendég felhasználókat?

A Windows virtuális asztal nem támogatja az Azure AD vendég felhasználói fiókokat. Tegyük fel például, hogy a vendég felhasználói csoportnak van Microsoft 365 E3 felhasználónkénti, Windows E3 felhasználónkénti vagy VDA-licence a saját vállalatában, de a vendég felhasználói egy másik cég Azure AD-beli felhasználói. A másik vállalat felügyeli a vendég felhasználói objektumait az Azure AD-ben és Active Directory például a helyi fiókokban.

Harmadik fél javára nem használhatja a saját licenceit. Emellett a Windows virtuális asztal jelenleg nem támogatja a Microsoft-fiókot (MSA).

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>Miért nem látom az ügyfél IP-címét a WVDConnections táblában?

Jelenleg nem megbízható módon gyűjthetjük a webes ügyfél IP-címeit, ezért nem vesszük fel ezt az értéket a táblázatban.

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Hogyan kezeli a Windows Virtual Desktop a biztonsági mentéseket?

Az Azure-ban több lehetőség áll rendelkezésre a biztonsági mentés kezelésére. Használhatja az Azure Backup, a Site Recovery és a pillanatképeket.

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>Támogatja a Windows Virtual Desktop a harmadik féltől származó együttműködési alkalmazásokat?

A Windows rendszerű virtuális asztal jelenleg a Teams szolgáltatásra van optimalizálva. A Microsoft jelenleg nem támogatja a harmadik féltől származó együttműködési alkalmazásokat, például a nagyítást. A harmadik féltől származó szervezetek feladata, hogy kompatibilitási irányelveket adjanak ügyfeleiknek. A Windows virtuális asztal nem támogatja a Skype vállalati verzió használatát is.

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>Válthatok a készletezett és a személyes gazdagépek között?

Miután létrehozta a gazdagépet, a típusa nem módosítható. Áthelyezheti azonban azokat a virtuális gépeket, amelyeket egy gazdagép-készletbe regisztrál egy másik típusú gazdagépre.

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>Mi a legnagyobb profil FSLogix képes kezelni?

A FSLogix korlátozásai vagy kvótái a felhasználói profil VHD (X) fájlok tárolására használt tárolási hálótól függenek.

A következő táblázat példát mutat be arra, hogy a FSLogix-profiloknak milyen erőforrásokra van szükségük az egyes felhasználók támogatásához. A követelmények a felhasználótól, az alkalmazástól és az egyes profilokon végzett tevékenységtől függően eltérőek lehetnek.

| Erőforrás | Követelmény |
|---|---|
| Állandó állapot IOPS | 10 |
| Bejelentkezés/kijelentkezés IOPS | 50 |

Az ebben a táblázatban szereplő példa egyetlen felhasználó, de felhasználható a környezetében lévő felhasználók teljes számára vonatkozó követelmények becslésére is. A bejelentkezés és a kijelentkezés során például 1 000 IOPS kell lennie 100 felhasználóhoz, és körülbelül 5 000 IOPS.

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>Van-e méretezési korlát a Azure Portalban létrehozott gazdagép-készletekhez?

Ezek a tényezők befolyásolhatják a gazdagép-készletek méretezési korlátját:

- Az Azure-sablon 800 objektumra van korlátozva. További információ: Azure- [előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits). Az egyes virtuális gépek körülbelül hat objektumot is létrehoznak, így a sablon minden egyes futtatásakor létre lehet hozni körülbelül 132 virtuális gépet.

- Az egyes régiókban és előfizetésekben létrehozható magok száma korlátozott. Ha például Nagyvállalati Szerződés-előfizetéssel rendelkezik, 350 magot hozhat létre. A 350-et a virtuális gépek alapértelmezett számával vagy a saját alapvető korláttal kell elosztania, hogy meghatározza, hány virtuális gépet hozhat létre a sablon minden egyes futtatásakor. További információ: [Virtual Machines Limits-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager).

- A virtuális gép előtagjának neve és a virtuális gépek száma kevesebb, mint 15 karakter. További információ: [Az Azure-erőforrások elnevezési szabályai és korlátozásai](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute).

## <a name="can-i-manage-windows-virtual-desktop-environments-with-azure-lighthouse"></a>Kezelhetem a Windows rendszerű virtuális asztali környezeteket az Azure Lighthouse használatával?

Az Azure Lighthouse nem támogatja teljes mértékben a Windows rendszerű virtuális asztali környezetek kezelését. Mivel a világítótorony jelenleg nem támogatja az Azure AD-bérlői felhasználók felügyeletét, a világítótorony ügyfeleinek továbbra is be kell jelentkezniük az Azure AD-be, amelyet az ügyfelek a felhasználók kezeléséhez használnak.

A CSP-előfizetések nem használhatók a Windows rendszerű virtuális asztali szolgáltatással. További információ: [Integration sandbox Account](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account).

Végül, ha engedélyezte az erőforrás-szolgáltatót a CSP tulajdonosi fiókjából, a CSP-ügyfél fiókjai nem tudják módosítani az erőforrás-szolgáltatót.

## <a name="how-often-should-i-turn-my-vms-on-to-prevent-registration-issues"></a>Milyen gyakran kell bekapcsolni a virtuális gépeket a regisztrációs problémák megelőzésére?

Miután regisztrált egy virtuális gépet a Windows Virtual Desktop szolgáltatásban található gazdagép-készletbe, az ügynök rendszeresen frissíti a virtuális gép tokenjét, amikor a virtuális gép aktív. A regisztrációs jogkivonat tanúsítványa 90 napig érvényes. A 90 napos korlát miatt javasoljuk, hogy a virtuális gépeket minden 90 nap alatt indítsa el. A virtuális gép ezen időtartamon belüli bekapcsolásával megakadályozhatja a regisztrációs token lejáratát vagy érvénytelenné válását. Ha 90 nap elteltével kezdte meg a virtuális gépet, és regisztrációs problémákat tapasztal, kövesse a [Windows rendszerű virtuális asztali ügynök hibaelhárítási útmutatójának](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) utasításait a virtuális gép a gazdagépről való eltávolításához, telepítse újra az ügynököt, és regisztrálja újra a készletbe.

## <a name="can-i-set-availability-options-when-creating-host-pools"></a>Megadhatok rendelkezésre állási beállításokat a gazdagépek létrehozásakor?

Igen. A Windows rendszerű virtuális asztali gazdagépek lehetőséget biztosítanak a rendelkezésre állási csoport vagy a rendelkezésre állási zónák kiválasztására a virtuális gépek létrehozásakor. Ezek a rendelkezésre állási lehetőségek ugyanazok, mint az Azure számítási felhasználása. Ha kijelöl egy zónát a virtuális géphez, amelyet a gazdagépen hoz létre, a beállítás automatikusan az adott zónában létrehozott összes virtuális gépre érvényes lesz. Ha inkább több zónában szeretné terjeszteni a gazdagép-készletet, akkor a [virtuális gépek hozzáadása a Azure Portalhoz](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal) című témakör útmutatását követve manuálisan ki kell választania egy új zónát minden létrehozott új virtuális géphez.

## <a name="which-availability-option-is-best-for-me"></a>Melyik rendelkezésre állási lehetőség a legjobb számomra?

A virtuális gépekhez használni kívánt rendelkezésre állási beállítás a rendszerkép helyétől és a felügyelt lemez mezőitől függ. A következő táblázat ismerteti az egyes beállításokhoz tartozó kapcsolatokat, amelyek segítenek kideríteni, hogy melyik lehetőség a legmegfelelőbb a telepítéshez. 

| Rendelkezésre állási beállítás | Rendszerkép helye | Felügyelt lemez lehetőség gombjának használata (választógomb) |
|---|---|---|
| Nincs | Katalógus | Alapértelmezés szerint letiltva az "igen" értékkel |
| Nincs | Blob Storage | Engedélyezve a "nem" beállítással alapértelmezettként |
| A rendelkezésre állási zóna | Katalógus (blob Storage-beállítás letiltva) | Alapértelmezés szerint letiltva az "igen" értékkel |
| Rendelkezésre állási csoport felügyelt SKU-val (felügyelt lemez) | Katalógus | Alapértelmezés szerint letiltva az "igen" értékkel |
| Rendelkezésre állási csoport felügyelt SKU-val (felügyelt lemez) | Blob Storage | Engedélyezve a "nem" beállítással alapértelmezettként |
| Rendelkezésre állási csoport felügyelt SKU-val (felügyelt lemez) | BLOB Storage (a katalógus lehetőség le van tiltva) | Alapértelmezés szerint letiltva a "nem" értékkel |
| Rendelkezésre állási csoport (újonnan létrehozott felhasználó) | Katalógus | Alapértelmezés szerint letiltva az "igen" értékkel |
| Rendelkezésre állási csoport (újonnan létrehozott felhasználó) | Blob Storage | Engedélyezve a "nem" beállítással alapértelmezettként |
