---
title: Kerberos kulcsszolgáltató proxy Windows rendszerű virtuális asztal beállítása – Azure
description: Windows rendszerű virtuális asztali címkészlet beállítása Kerberos kulcsszolgáltató proxy használatára.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: bcf28fbc0d2f4ec9eeac5bcb8f0b2c9b65a62b6b
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775031"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Kerberos kulcsszolgáltató proxy konfigurálása (előzetes verzió)

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Biztonság – tudatos ügyfelek, például pénzügyi vagy kormányzati szervezetek, gyakran intelligens kártyák használatával jelentkeznek be. Az intelligens kártyák a többtényezős hitelesítés (MFA) megkövetelésével biztonságosabbá teszik a központi telepítéseket. A Windows rendszerű virtuális asztali munkamenet RDP-része esetében azonban az intelligens kártyákhoz közvetlen kapcsolat szükséges, vagy a "vonal", amely egy Active Directory (AD) tartományvezérlővel rendelkezik a Kerberos-hitelesítéshez. A közvetlen kapcsolat nélkül a felhasználók nem tudnak automatikusan bejelentkezni a szervezet hálózatára távoli kapcsolatokból. A Windows rendszerű virtuális asztali környezetben üzemelő felhasználók a KDC proxy szolgáltatás használatával felügyelhetik a hitelesítési forgalmat, és távolról is bejelentkezhetnek. A KDC proxy lehetővé teszi a hitelesítést a Windows rendszerű virtuális asztali munkamenetek RDP protokoll számára, így a felhasználó biztonságosan bejelentkezhet. Ez sokkal egyszerűbben működik, és lehetővé teszi, hogy bizonyos vész-helyreállítási forgatókönyvek zökkenőmentesen fussanak.

A KDC-proxy beállítása azonban általában magában foglalja a Windows Server átjáró szerepkör hozzárendelését a Windows Server 2016-es vagy újabb verziójában. Hogyan használhat egy Távoli asztali szolgáltatások szerepkört a Windows rendszerű virtuális asztalra való bejelentkezéshez? Ha válaszolni szeretne, vessünk egy gyors pillantást az összetevőkre.

A Windows rendszerű virtuális asztali szolgáltatásnak két összetevője van, amelyeket hitelesíteni kell:

- A Windows rendszerű virtuális asztali ügyfél hírcsatornája, amely a felhasználók számára hozzáférést biztosít az elérhető asztali számítógépekhez vagy alkalmazásokhoz. Ez a hitelesítési folyamat Azure Active Directory történik, ami azt jelenti, hogy ez az összetevő nem a cikk középpontjában áll.
- A felhasználó által a rendelkezésre álló erőforrások egyikét kiválasztó RDP-munkamenet. Ez az összetevő Kerberos-hitelesítést használ, és KDC-proxyt igényel a távoli felhasználók számára.

Ez a cikk bemutatja, hogyan konfigurálhatja a hírcsatornát a Windows rendszerű virtuális asztali ügyfélen a Azure Portalban. Ha szeretné megismerni a RD-átjáró szerepkör konfigurálásának módját, tekintse meg [a RD-átjáró szerepkör üzembe helyezése](/windows-server/remote/rd-gateway-role)című témakört.

## <a name="requirements"></a>Követelmények

Ha egy Windows rendszerű virtuális asztali munkamenetgazda-proxyt szeretne konfigurálni, a következőkre lesz szüksége:

- Hozzáférés a Azure Portalhoz és egy Azure-beli rendszergazdai fiókhoz.
- A távoli ügyfélszámítógépeken a Windows 10 vagy a Windows 7 rendszernek kell futnia, és telepítve kell lennie a [Windows asztali ügyfelének](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) .
- A számítógépen már telepítve kell lennie egy KDC-proxynak. Ennek elvégzéséhez tekintse meg [a Windows rendszerű virtuális asztalok Rd-átjáró szerepkörének beállítása](rd-gateway-role.md)című témakört.
- A gép operációs rendszerének Windows Server 2016 vagy újabb verziójúnak kell lennie.

A követelmények teljesítése után készen áll a kezdésre.

## <a name="how-to-configure-the-kdc-proxy"></a>A KDC-proxy konfigurálása

A KDC-proxy konfigurálása:

1. Jelentkezzen be rendszergazdaként a Azure Portalba.

2. Nyissa meg a Windows rendszerű virtuális asztali lapot.

3. Válassza ki azt az alkalmazáskészletet, amely számára engedélyezni szeretné a KDC-proxyt, majd válassza az **RDP-tulajdonságok** lehetőséget.

    > [!div class="mx-imgBorder"]
    > ![A Azure Portal lap képernyőképe, amely egy felhasználót jelöl ki a gazdagépeken, majd a példaként szolgáló címkészlet nevét, majd az RDP-tulajdonságokat.](media/rdp-properties.png)

4. Válassza a **speciális** fület, majd adjon meg egy értéket a következő formátumban szóközök nélkül:

    
    > kdcproxyname: s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![A speciális lapot kijelölő képernyőkép, amely a 4. lépésben leírtak szerint megadott értéket tartalmazza.](media/advanced-tab-selected.png)

5. Kattintson a **Mentés** gombra.

6. A kiválasztott gazdagépnek most meg kell kezdenie az RDP-kapcsolati fájlok kiírását, amelyek tartalmazzák a 4. lépésben megadott kdcproxyname értéket.

## <a name="next-steps"></a>Következő lépések

A KDC-proxy Távoli asztali szolgáltatások oldalának és a RD-átjáró szerepkör hozzárendelésének megismeréséhez tekintse meg [a RD-átjáró szerepkör üzembe helyezése](/windows-server/remote/rd-gateway-role)című témakört.

Ha szeretné átméretezni a KDC-proxykiszolgálók méretezését, Ismerje meg, hogyan állíthatja be a magas rendelkezésre állást a KDC-proxy számára [a magas rendelkezésre állás biztosítása érdekében a távoli asztali webes és átjáró webes](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)felületén.
