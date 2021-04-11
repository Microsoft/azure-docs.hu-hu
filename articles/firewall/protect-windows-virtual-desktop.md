---
title: A Windows rendszerű virtuális asztalok elleni védelem Azure Firewall használata
description: Ismerje meg, hogyan használhatja a Azure Firewall a Windows rendszerű virtuális asztali környezetek elleni védelemhez
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: d5320f44aa5d922cea852ab09e5141fad277e2b0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566026"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Az Azure Firewall használata üzemelő Windows Virtual Desktop-példányok védelmére

A Windows Virtual Desktop egy Azure-on futó asztali és app Virtualization szolgáltatás. Amikor a végfelhasználó egy Windows rendszerű virtuális asztali környezethez csatlakozik, a munkamenetet egy gazdagép futtatja. A címkészlet olyan Azure-beli virtuális gépek gyűjteménye, amelyek munkamenet-gazdagépként regisztrálják a Windows rendszerű virtuális asztalt. Ezek a virtuális gépek a virtuális hálózaton futnak, és a virtuális hálózat biztonsági ellenőrzése alá esnek. A Windows Virtual Desktop szolgáltatás megfelelő működéséhez kimenő internet-hozzáférésre van szükségük, és a végfelhasználók számára is szükség lehet a kimenő internet-hozzáférésre. Azure Firewall segíthet a környezet zárolásában és a kimenő forgalom szűrésében.

[![Windows rendszerű virtuális asztali architektúra ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png)](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

A jelen cikkben található irányelvek alapján további védelmet biztosíthat a Windows rendszerű virtuális asztali címkészlet számára a Azure Firewall használatával.

## <a name="prerequisites"></a>Előfeltételek


 - Telepített Windows rendszerű virtuális asztali környezet és címkészlet.

   További információ: [oktatóanyag: állomáslista létrehozása az Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) - [szel és egy Azure Resource Manager sablonnal rendelkező gazdagép létrehozása](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md).

A Windows rendszerű virtuális asztali környezetekről a [Windows rendszerű virtuális asztali környezet](../virtual-desktop/environment-setup.md)című témakörben olvashat bővebben.

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Gazdagép-készlet kimenő hozzáférése a Windows rendszerű virtuális asztalhoz

A Windows rendszerű virtuális asztali környezethez létrehozott Azure-beli virtuális gépeknek a megfelelő működéshez számos teljes tartománynevet (FQDN) kell elérniük. A Azure Firewall egy Windows rendszerű virtuális asztali FQDN-címkét biztosít a konfiguráció egyszerűsítéséhez. A következő lépésekkel engedélyezheti a kimenő Windowsos virtuális asztali platform forgalmát:

- A Azure Firewall üzembe helyezése és a Windows rendszerű virtuális asztali munkamenetgazda-alhálózati felhasználó által megadott útvonal (UDR) konfigurálása az összes forgalom az Azure Firewall keresztüli átirányításához. Az alapértelmezett útvonal most a tűzfalra mutat.
- Hozzon létre egy alkalmazás-szabálygyűjtemény-gyűjteményt, és adjon hozzá egy szabályt az *WindowsVirtualDesktop* FQDN címke engedélyezéséhez. A forrás IP-címtartomány a gazdagépek virtuális hálózata, a protokoll pedig **https**, a cél pedig **WindowsVirtualDesktop**.

- A Windows rendszerű virtuális asztali készlethez szükséges tárolási és Service Bus-fiókok készlete központi telepítési jellemző, ezért még nincs rögzítve a WindowsVirtualDesktop FQDN-címkében. Ezt a következő módszerek egyikével kezelheti:

   - HTTPS-hozzáférés engedélyezése a gazdaszámítógép-alhálózatról * xt.blob.core.windows.net, * eh.servicebus.windows.net és * xt.table.core.windows.net. Ezek a helyettesítő karakterek teljes tartománynevei lehetővé teszik a szükséges hozzáférést, de kevésbé korlátozóak.
   - A következő log Analytics-lekérdezéssel sorolja fel a szükséges teljes tartományneveket, majd explicit módon engedélyezze őket a tűzfal alkalmazási szabályaiban:
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- Hozzon létre egy hálózati szabálygyűjtemény-gyűjteményt, és adja hozzá a következő szabályokat:

   - DNS engedélyezése – a 53-es TCP-és UDP-portok esetében engedélyezze a magánhálózati IP-cím hozzáadását.
   - KMS engedélyezése – a Windows rendszerű virtuális asztali virtuális gépek forgalmának engedélyezése a Windows aktiválási szolgáltatás 1688-es TCP-portjára. További információ a cél IP-címekről: a [Windows aktiválása sikertelen a kényszerített bújtatási forgatókönyvben](/troubleshoot/azure/virtual-machines/custom-routes-enable-kms-activation#solution).

> [!NOTE]
> Előfordulhat, hogy egyes központi telepítések nem igényelnek DNS-szabályokat, például Azure Active Directory tartományvezérlők továbbítják a DNS-lekérdezéseket Azure DNS a következő helyen: 168.63.129.16.

## <a name="host-pool-outbound-access-to-the-internet"></a>A gazdagép kimenő hozzáférése az internethez

A szervezet igényeitől függően érdemes lehet biztonságos kimenő internet-hozzáférést biztosítani a végfelhasználók számára. Azokban az esetekben, amikor az engedélyezett célhelyek listája megfelelően van definiálva (például [Microsoft 365 hozzáférés](/microsoft-365/enterprise/microsoft-365-ip-web-service)), Azure Firewall alkalmazás-és hálózati szabályok használatával konfigurálhatja a szükséges hozzáférést. Ez a végfelhasználói forgalmat közvetlenül az internetre irányítja a legjobb teljesítmény érdekében.

Ha egy meglévő helyszíni biztonságos webes átjáróval szeretné szűrni a kimenő felhasználói internetes forgalmat, akkor a Windows virtuális asztali gazdagépen futó, explicit módon konfigurált webböngészőket vagy más alkalmazásokat is konfigurálhat. Lásd például a következő témakört: [a Microsoft Edge parancssori kapcsolóinak használata a proxybeállítások konfigurálásához](/deployedge/edge-learnmore-cmdline-options-proxy-settings). Ezek a proxybeállítások csak a végfelhasználói internet-hozzáférést befolyásolják, így a Windows rendszerű virtuális asztali platform kimenő forgalma közvetlenül Azure Firewallon keresztül érhető el.

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

Előfordulhat, hogy a követelményektől függően további tűzfalszabályok konfigurálására van szükség:

- NTP-kiszolgáló elérése

   Alapértelmezés szerint a Windows rendszerű virtuális gépek a 123-as UDP-porton keresztül csatlakoznak a time.windows.com-hez az idő szinkronizálása érdekében. Hozzon létre egy hálózati szabályt a hozzáférés engedélyezéséhez, vagy a környezetben használt időkiszolgálóhoz.


## <a name="next-steps"></a>Következő lépések

- További információ a Windows rendszerű virtuális asztali környezetről: [Mi az a Windows virtuális asztal?](../virtual-desktop/overview.md)