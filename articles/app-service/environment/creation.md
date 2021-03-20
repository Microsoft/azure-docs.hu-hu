---
title: App Service Environment létrehozása
description: Útmutató App Service Environment létrehozásához.
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 021c85037ea105ff7d8ff642e3a9f28ed3ebe991
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594106"
---
# <a name="create-an-app-service-environment"></a>App Service Environment létrehozása

> [!NOTE]
> Ez a cikk a App Service Environment v3 (előzetes verzió)
> 

A [app Service Environment (][Intro] bevezetési) a app Service egy olyan bérlői példánya, amely befecskendez az Azure Virtual Networkba (VNet).  A ASEv3 csak a saját vnet lévő privát címeken teszi lehetővé az alkalmazások megjelenítését. Ha az előzetes verzióban létrejön egy ASEv3, a rendszer hozzáadja ezeket az erőforrásokat az előfizetéséhez.

- App Service-környezet
- Privát végpont

Egy szolgáltató üzembe helyezéséhez két alhálózatot kell használni.  Egy alhálózat fogja tárolni a privát végpontot.  Ezt az alhálózatot más dolgokhoz, például virtuális gépekhez is használhatja.  A másik alhálózat a kifelé irányuló kimenő hívások esetében használatos.  Ezt az alhálózatot nem lehet más, mint a központhoz nem. 

## <a name="before-you-create-your-ase"></a>A bekészítés előtt

A kisegítő lehetőség létrehozása után a következő nem módosítható:

- Hely
- Előfizetés
- Erőforráscsoport
- Azure Virtual Network (VNet) használatban
- Használt alhálózatok
- Alhálózat mérete
- A Bea bemutató neve

A kimenő alhálózatnak elég nagynak kell lennie ahhoz, hogy a legnagyobb méretet a teljes körűen méretezhető legyen. Válasszon ki egy elég nagy alhálózatot a maximális skálázási igények támogatásához, mivel a létrehozás után nem módosítható. Az ajánlott méret a/24 256 címmel.

A kiegészítő szolgáltatás létrehozása után hozzáadhat alkalmazásokat. Ha a ASEv3 nem rendelkezik App Service-csomaggal, akkor díjat számítunk fel, mintha egy I1v2 App Service-csomag egy példánya volt a szolgáltatóban.  

A ASEv3 csak a kiválasztott régiókban érhető el. További régiók lesznek hozzáadva, mivel az előzetes verzió a GA felé halad. 

## <a name="creating-an-ase-in-the-portal"></a>A bekészítés létrehozása a portálon

1. ASEv3 létrehozásához keresse meg a piactéren a **app Service Environment (előzetes verzió)** lehetőséget.  
2. Alapismeretek: válassza ki az előfizetést, válassza ki vagy hozza létre az erőforráscsoportot, majd adja meg a beadás nevét.  A betekintő neve a bevezetés tartományának utótagját is felhasználja.  Ha a szolgáltatás neve *contoso* , akkor a tartomány utótagja *contoso.appserviceenvironment.net* lesz.  Ez a név automatikusan be lesz állítva a vnet által használt Azure DNS privát zónában. 

    ![App Service Environment alapok létrehozása lap](./media/creation/creation-basics.png)

3. Üzemeltetés: válassza az operációs rendszer beállításai lehetőséget, majd a gazda csoport központi telepítését. Az operációs rendszer beállításai azt az operációs rendszert jelzik, amelyet kezdetben az alkalmazásaihoz fog használni ebben a központban. A másik operációs rendszer alkalmazásai a beadás után is hozzáadhatók. A gazdagépek központi telepítése a dedikált hardver kiválasztására szolgál. A ASEv3 lehetőség kiválasztásával engedélyezheti a dedikált hardveren. A teljes dedikált gazdagépre vonatkozó díjat a bevezetéses létrehozás után számítjuk fel, és a App Service csomag példányai esetében kedvezményes árat kell fizetni. 

    ![App Service Environment-üzemeltető kiválasztása](./media/creation/creation-hosting.png)

4. Hálózatkezelés: válassza ki vagy hozza létre a Virtual Network, válassza ki vagy hozza létre a bejövő alhálózatot, válassza ki vagy hozza létre a kimenő alhálózatot. A kimenő forgalomhoz használt összes alhálózatnak üresnek kell lennie, és delegálni kell a Microsoft. Web/hostingEnvironments. Ha a portálon hozza létre az alhálózatot, a rendszer automatikusan delegálja az alhálózatot.

    ![App Service Environment hálózatkezelési beállítások](./media/creation/creation-networking.png)

5. Áttekintés és létrehozás: Ellenőrizze, hogy helyes-e a konfiguráció, és válassza a létrehozás lehetőséget. A bevezetés körülbelül egy órát vesz igénybe. 

    ![App Service Environment áttekintése és létrehozása](./media/creation/creation-review.png)

A központú környezet létrehozása után kiválaszthatja azt az alkalmazások létrehozásakor. Ha többet szeretne megtudni az alkalmazások új kiegészítő [szolgáltatásban][UsingASE] való létrehozásáról, olvassa el a app Service Environment

## <a name="os-preference"></a>Operációs rendszer preferencia
A Rendszerfelügyeleti webszolgáltatásokban Windows-alkalmazásokat, linuxos alkalmazásokat vagy mindkettőt használhat. A ASEv2-ben a létrehozás során kiválasztott kezdeti preferencia hozzáadja a magas rendelkezésre állású infrastruktúrát az adott operációs rendszerhez a központilag történő létrehozás során. A másik operációs rendszer alkalmazásaihoz csak a szokásos módon végezze el az alkalmazások hozzáadását, és válassza ki a kívánt operációs rendszert. A ASEv3-ben ez nem befolyásolja a háttérbeli viselkedés appreciatively.  

## <a name="dedicated-hosts"></a>Dedikált gazdagépek
A bevezetést általában a több-bérlős hypervisoron kiépített virtuális gépeken helyezik üzembe. Ha dedikált rendszereken kell üzembe helyeznie, beleértve a hardvert, akkor kiépítheti a bevezetést dedikált gazdagépekre. A kezdeti ASEv3 előzetes verziójában a dedikált gazdagépek egy párral érkeznek. Minden dedikált gazdagép külön rendelkezésre állási zónában található, ha a régió engedélyezi azt. A dedikált gazdagép-alapú központú központi telepítések díjszabása eltérő a normálnél. A dedikált gazdagépért díjat számítunk fel, majd az egyes App Servicei csomagok esetében egy másik díjat számítunk fel.  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
