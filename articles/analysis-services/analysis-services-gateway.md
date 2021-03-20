---
title: Helyszíni adatátjáró a Azure Analysis Serviceshoz | Microsoft Docs
description: A helyszíni átjáróra akkor van szükség, ha az Azure-beli Analysis Services-kiszolgáló a helyszíni adatforrásokhoz csatlakozik.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 6ca96f76287482a445d8a9a1cdc441333b36efbd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97739603"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Csatlakozás helyszíni adatforrásokhoz helyszíni adatátjáróval

A helyszíni adatátjáró biztonságos adatátvitelt biztosít a helyszíni adatforrások és a felhőben lévő Azure Analysis Services-kiszolgálók között. Az azonos régióban található több Azure Analysis Services kiszolgáló használata mellett az átjáró legújabb verziója a Azure Logic Apps, a Power BI, a Power apps és a Power automatizáló szolgáltatásokkal is együttműködik. Míg a telepített átjáró ugyanaz, mint az összes szolgáltatás, Azure Analysis Services és Logic Apps néhány további lépéssel.

Az itt megadott információk az Azure Analysis Services a helyszíni adatátjáróval való együttműködésére vonatkoznak. Ha többet szeretne megtudni az átjáróról, és hogyan működik együtt más szolgáltatásokkal, tekintse meg a [Mi az a helyszíni adatátjáró?](/data-integration/gateway/service-gateway-onprem)című témakört.

Azure Analysis Services esetén a telepítő az átjáróval való első alkalommal egy négy részből álló folyamat:

- A **telepítő letöltése és futtatása** – ezzel a lépéssel telepítheti az átjárószolgáltatás szolgáltatást a szervezeten belüli számítógépen. A [bérlő](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Azure ad-fiókjával is bejelentkezhet az Azure-ba. Az Azure B2B-(vendég-) fiókok nem támogatottak.

- **Az átjáró regisztrálása** – ebben a lépésben meg kell adnia egy nevet és egy helyreállítási kulcsot az átjáróhoz, és ki kell választania egy régiót, és regisztrálnia kell az átjárót az átjáró Cloud Service-ben. Az átjáró-erőforrás bármely régióban regisztrálható, de azt javasoljuk, hogy a Analysis Services-kiszolgálókkal megegyező régióban legyen elérhető. 

- **Átjáró-erőforrás létrehozása az Azure** -ban – ebben a lépésben létrehoz egy átjáró-erőforrást az Azure-ban.

- **Az átjáró erőforrásának csatlakoztatása a kiszolgálókhoz** – ha átjáró-erőforrást használ, megkezdheti a kiszolgálók csatlakoztatását. Több kiszolgálót és más erőforrást is összekapcsolhat, ha azok ugyanabban a régióban találhatók.

## <a name="installing"></a>Telepítés

Azure Analysis Services környezet telepítésekor fontos, hogy kövesse a helyszíni [adatátjáró telepítése és konfigurálása a Azure Analysis Services számára](analysis-services-gateway-install.md)című témakörben ismertetett lépéseket. Ez a cikk Azure Analysis Servicesra vonatkozik. További lépéseket is tartalmaz, amelyek szükségesek egy helyszíni adatátjáró-erőforrás beállításához az Azure-ban, és a Azure Analysis Services-kiszolgáló összekapcsolása az erőforrással.

## <a name="connecting-to-a-gateway-resource-in-a-different-subscription"></a>Csatlakozás átjáró-erőforráshoz egy másik előfizetésben

Javasoljuk, hogy az Azure Gateway-erőforrást ugyanabban az előfizetésben hozza létre, mint a kiszolgálót. A kiszolgálókat azonban úgy is beállíthatja, hogy egy másik előfizetésben lévő átjáró-erőforráshoz kapcsolódjanak. Egy másik előfizetésben lévő átjáró-erőforráshoz való csatlakozás nem támogatott a meglévő kiszolgálóbeállítások konfigurálásakor vagy új kiszolgáló létrehozásakor a portálon, de a PowerShell használatával konfigurálható. További információ: [az átjáró erőforrásának összekötése a kiszolgálóhoz](analysis-services-gateway-install.md#connect-gateway-resource-to-server).

## <a name="ports-and-communication-settings"></a>Portok és kommunikációs beállítások

Az átjáró kimenő kapcsolatot hoz létre az Azure Service Bus felé. A következő kimenő portokon kommunikál: TCP 443 (alapméretezett), 5671, 5672, 9350–9354.  Az átjáró nem igényel bejövő portokat.

Előfordulhat, hogy az adatterületének IP-címeit is meg kell adnia a tűzfalon. [A Microsoft Azure Datacenter IP-címlistáját innen töltheti le](https://www.microsoft.com/download/details.aspx?id=56519). A listát hetente frissítjük. Az Azure Datacenter listájában szereplő IP-címek a CIDR-jelölésrendszer használatával vannak megadva. További információ: osztály nélküli [Inter-Domain útválasztás](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

A következő az átjáró által használt teljes tartománynevek.

| Tartománynevek | Kimenő portok | Description |
| --- | --- | --- |
| *.powerbi.com |80 |A telepítő letöltéséhez használt HTTP-cím. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queueing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |A Service Bus Relay figyelői a TCP-n (443-as portot igényel a hozzáférés-vezérlési token beszerzéséhez) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |80 |Az internetkapcsolat tesztelésére használható, ha az átjáró nem érhető el a Power BI szolgáltatás számára. |
| *.microsoftonline-p.com |443 |Hitelesítésre használható a konfigurációtól függően. |
| dc.services.visualstudio.com    |443 |A AppInsights használja a telemetria gyűjtésére. |

## <a name="next-steps"></a>Következő lépések 

A következő cikkek tartalmazzák a helyszíni adatátjáró általános tartalmát, amely az átjáró által támogatott összes szolgáltatásra vonatkozik:

* [Helyszíni adatátjáró – GYIK](/data-integration/gateway/service-gateway-onprem-faq)   
* [A helyszíni adatátjáró alkalmazás használata](/data-integration/gateway/service-gateway-app)   
* [Bérlőszintű felügyelet](/data-integration/gateway/service-gateway-tenant-level-admin)
* [Proxybeállítások konfigurálása](/data-integration/gateway/service-gateway-proxy)   
* [Kommunikációs beállítások módosítása](/data-integration/gateway/service-gateway-communication)   
* [Naplófájlok konfigurálása](/data-integration/gateway/service-gateway-log-files)   
* [Hibaelhárítás](/data-integration/gateway/service-gateway-tshoot)
* [Átjáró teljesítményének monitorozása és optimalizálása](/data-integration/gateway/service-gateway-performance)
