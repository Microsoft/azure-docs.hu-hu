---
title: Mik azok a Azure Firewall Manager biztonsági partner-szolgáltatók?
description: Tudnivalók a Azure Firewall Manager biztonsági partnereinek szolgáltatói
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: victorh
ms.openlocfilehash: 923c6b685d20ff68788e7d9cfcb45ebaecb535e3
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490108"
---
# <a name="what-are-security-partner-providers"></a>Mik azok a biztonságipartner-szolgáltatók?

A Azure Firewall Managerben található *biztonsági partner szolgáltatók* lehetővé teszik, hogy az ismerős, legjobb, harmadik féltől származó biztonsági szolgáltatásokat (SECaaS) kínálja a felhasználók internet-hozzáférésének védelmére.

A gyors konfigurálással biztonságossá teheti a hubot egy támogatott biztonsági partnerrel, és átirányíthatja és szűrheti az internetes forgalmat a virtuális hálózatok (virtuális hálózatok) vagy az adott régión belüli fiókirodák között. Ezt az automatikus útválasztási felügyelettel teheti meg, a felhasználó által megadott útvonalak (UDR) beállítása és kezelése nélkül.

Az Ön által választott biztonsági partnerrel konfigurált biztonságos hubokat több Azure-régióban is üzembe helyezheti, így a felhasználók bárhonnan és biztonságosan érhetik el a felhasználókat a különböző régiókban. A biztonsági partnernek az internetes/SaaS-alkalmazások forgalmára való használatának, valamint a biztonságos hubokon Azure Firewall a privát forgalomra való felépítése lehetővé teszi, hogy a globálisan elosztott felhasználókhoz és alkalmazásokhoz közeledő Azure-beli biztonsági szegélyt fejlesszen fel.

A támogatott biztonsági partnerek a következők: **Zscaler**, **[ellenőrzési pont](check-point-overview.md)** és **iboss**.

![Biztonságipartner-szolgáltatók](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Főbb forgatókönyvek

A biztonsági partnerek az internetes forgalom szűrésére használhatók a következő helyzetekben:

- Virtual Network (VNet) az internetre

   Az Azure-on futó Felhőbeli számítási feladatokhoz speciális, felhasználó által kompatibilis internetes védelmet használhat.

- Ág – Internet

   Használja ki az Azure-kapcsolatot és a globális disztribúciót, hogy könnyen hozzá lehessen adni a harmadik féltől származó NSaaS-szűrést a fiókirodában az internetes forgatókönyvekhez. Az Azure Virtual WAN használatával felépítheti a globális átviteli hálózatot és a biztonsági szegélyt.

A következő forgatókönyvek támogatottak:
- VNet/ág az interneten keresztül a biztonsági partner szolgáltatója és a másik forgalom (küllő, küllő, ág, ág, hogy küllős) Azure Firewallon keresztül.
- VNet/ág az internethez a biztonsági partner szolgáltatóján keresztül

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Ajánlott eljárások az internetes forgalom szűréséhez a biztonságos virtuális hubokban

Az internetes forgalom jellemzően webes forgalmat tartalmaz. Emellett olyan SaaS-alkalmazásokra irányuló forgalmat is magában foglal, mint például a Microsoft 365 és az Azure-beli nyilvános Pásti-szolgáltatások, például az Azure Storage, az Azure SQL stb. A következő ajánlott eljárásokat ajánljuk a szolgáltatások forgalmának kezelésére:

### <a name="handling-azure-paas-traffic"></a>Az Azure Pásti-forgalom feldolgozása
 
- A védelem Azure Firewall használata, ha a forgalom főleg az Azure Pástiből áll, és az alkalmazások erőforrás-hozzáférése az IP-címek, a teljes tartománynevek, a szolgáltatási címkék vagy a FQDN-címkék használatával szűrhető.

- Használjon harmadik féltől származó partneri megoldást a hubokban, ha a forgalom SaaS-alkalmazás-hozzáférésből áll, vagy ha a felhasználó által használható szűrésre van szüksége (például a virtuális asztali infrastruktúra (VDI) számítási feladataihoz), vagy speciális Internet-szűrési képességekre van szüksége.

![A Azure Firewall Manager összes forgatókönyve](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-microsoft-365-traffic"></a>Microsoft 365 forgalom kezelését

Globálisan elosztott fiókirodai helyzetekben érdemes átirányítani Microsoft 365 forgalmat közvetlenül a fiókirodában, mielőtt elküldené az Azure-beli biztonságos központ fennmaradó internetes forgalmát.

Microsoft 365 esetében a hálózati késés és a teljesítmény kritikus fontosságú a sikeres felhasználói élmény érdekében. Az optimális teljesítmény-és felhasználói élmény érdekében az ügyfeleknek Microsoft 365 közvetlen és helyi Escape-megoldást kell alkalmazniuk, mielőtt fontolóra vennék a többi internetes forgalom Azure-on keresztüli átirányítását.

[Microsoft 365 hálózati kapcsolat alapelveinek](/microsoft-365/enterprise/microsoft-365-network-connectivity-principles) meghívásával a kulcsfontosságú Microsoft 365 hálózati kapcsolatok helyileg irányíthatók a felhasználói ágban vagy mobileszközökön, illetve közvetlenül az interneten keresztül a legközelebbi Microsoft hálózati pontra.

Emellett a Microsoft 365 kapcsolatok titkosítva vannak, és hatékony, védett protokollokat használnak a teljesítménnyel kapcsolatos okokból. Ez a hagyományos hálózati szintű biztonsági megoldásokhoz való csatlakozást nem teszi lehetővé. Ezen okok miatt javasoljuk, hogy az ügyfelek közvetlenül az ágakból küldjenek Microsoft 365 forgalmat az Azure-on keresztüli adatforgalom elküldése előtt. A Microsoft több, az Azure-ral és a Microsoft 365-nal integrált SD-WAN-megoldással rendelkezik, és megkönnyíti az ügyfelek számára Microsoft 365 közvetlen és helyi internetes kitörés engedélyezését. Részletekért lásd: [Mi az az Azure Virtual WAN?](../virtual-wan/virtual-wan-about.md)

## <a name="next-steps"></a>További lépések

[Telepítsen egy biztonsági partneri ajánlatot egy biztonságos hubhoz a Azure Firewall Manager használatával](deploy-trusted-security-partner.md).
