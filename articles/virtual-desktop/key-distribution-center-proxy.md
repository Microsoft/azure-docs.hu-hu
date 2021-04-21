---
title: Kerberos-proxy kulcsszolgáltató beállítása Windows Virtual Desktop – Azure
description: Gazdagépkészlet beállítása Windows Virtual Desktop Kerberos-proxy kulcsszolgáltató használatára.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 9dce264b7f2c88aed11f5b82a61f83cbac6c9697
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785108"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Kerberos-kulcsszolgáltató konfigurálása (előzetes verzió)

> [!IMPORTANT]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előzetes verzió szolgáltatói szerződés nélkül érhető el, és nem javasoljuk, hogy éles számítási feladatokhoz használja. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A biztonsággal kapcsolatos ügyfelek, például pénzügyi vagy kormányzati szervezetek, gyakran intelligens kártyákkal jelentkeznek be. Az intelligens kártyák a többtényezős hitelesítés (MFA) megkövetelve biztonságossá teszik az üzembe helyezést. A Windows Virtual Desktop-munkamenet RDP-részéhez azonban közvetlen kapcsolatra vagy "rálátásra" van szükség egy Active Directory- (AD-) tartományvezérlővel Kerberos-hitelesítéshez. E közvetlen kapcsolat nélkül a felhasználók nem tudnak automatikusan bejelentkezni a szervezet hálózatára távoli kapcsolatokról. A központi telepítési Windows Virtual Desktop KDC proxyszolgáltatással proxyzhatja ezt a hitelesítési forgalmat, és távolról bejelentkezhet. A KDC proxy lehetővé teszi a RDP protokoll munkamenet Windows Virtual Desktop hitelesítését, így a felhasználó biztonságosan bejelentkezik. Ez megkönnyíti az otthonról való munkát, és lehetővé teszi bizonyos vészhelyreállítási forgatókönyvek zökkenőmentesebb futtatását.

A KDC-proxy beállítása azonban általában magában foglalja a Windows Server Átjáró szerepkör hozzárendelését a Windows Server 2016-os vagy újabb verzióban. Hogyan használhat egy Távoli asztali szolgáltatások szerepkört a Windows Virtual Desktop? Ennek megválaszolásán vessünk egy pillantást az összetevőkre.

A Windows Virtual Desktop két összetevőt kell hitelesíteni:

- A Windows Virtual Desktop-ügyfélben található hírcsatorna, amely felsorolja a felhasználók számára az elérhető asztalokat vagy alkalmazásokat, amelyekhez hozzáféréssel rendelkezik. Ez a hitelesítési folyamat a Azure Active Directory történik, ami azt jelenti, hogy nem ez az összetevő a cikk középpontjában.
- Az RDP-munkamenet, amely azt jelenti, hogy a felhasználó kiválasztja az egyik rendelkezésre álló erőforrást. Ez az összetevő Kerberos-hitelesítést használ, és KDC-proxyt igényel a távoli felhasználók számára.

Ez a cikk bemutatja, hogyan konfigurálhatja a hírcsatornát a Windows Virtual Desktop ügyfélben a Azure Portal. Ha szeretné megtudni, hogyan konfigurálhatja a RD-átjáró szerepkört, tekintse meg [a következőt: Deploy the RD-átjáró role](/azure/virtual-desktop/rd-gateway-role).

## <a name="requirements"></a>Követelmények

Ha egy Windows Virtual Desktop-munkamenetgazdát KDC-proxyval konfigurál, a következőkre lesz szüksége:

- Hozzáférés az Azure Portal azure rendszergazdai fiókhoz.
- A távoli ügyfélszámítógépek csak windowsos Windows 10 Windows 7 rendszert futtatnak, és telepítve kell lennie [a Windows asztali ügyfélnek.](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) A webes ügyfél jelenleg nem támogatott.
- A számítógépen már telepítve kell lennie egy KDC-proxynak. Ennek mikéntjért lásd: RD-átjáró szerepkör [beállítása a Windows Virtual Desktop.](rd-gateway-role.md)
- A gép operációs rendszerének Windows Server 2016 vagy újabb rendszernek kell lennie.

Ha már biztosan megfelel ezeknek a követelményeknek, készen áll az első lépésekre.

## <a name="how-to-configure-the-kdc-proxy"></a>A KDC-proxy konfigurálása

A KDC-proxy konfigurálása:

1. Jelentkezzen be a Azure Portal rendszergazdaként.

2. Ugrás a Windows Virtual Desktop lapra.

3. Válassza ki azt a gazdagépkészletet, amely számára engedélyezni szeretné a KDC-proxyt, majd válassza az **RDP-tulajdonságok lehetőséget.**

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Azure Portal lapról, amely a gazdagépkészletek kiválasztását, a példa gazdagépkészlet nevét, majd az RDP-tulajdonságokat mutatja.](media/rdp-properties.png)

4. Válassza a **Speciális lapot,** majd adjon meg egy értéket szóközök nélkül a következő formátumban:

    
    > kdcproxyname:s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a kijelölt Speciális lapról, a 4. lépésben leírt értékkel.](media/advanced-tab-selected.png)

5. Kattintson a **Mentés** gombra.

6. A kiválasztott gazdagépkészletnek most olyan RDP-kapcsolatfájlokat kell kiosztaniuk, amelyek tartalmazzák a 4. lépésben megadott kdcproxyname értéket.

## <a name="next-steps"></a>Következő lépések

A KDC-proxy Távoli asztali szolgáltatások kezeléséhez és a szerepkör hozzárendeléséhez RD-átjáró következő cikkből megtudhatja, hogyan helyezheti üzembe a RD-átjáró [szerepkört.](rd-gateway-role.md)

Ha érdekli a KDC proxykiszolgálók skálázása, a Magas rendelkezésre állás hozzáadása [](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)a távoli asztali web- és átjáró webes előtérhezcímű cikkből megtudhatja, hogyan állíthatja be a magas rendelkezésre állást a KDC-proxyhoz.
