---
title: Gyakori Cloud Service-kezelési feladatok | Microsoft Docs
description: Megtudhatja, hogyan kezelheti Cloud Services a Azure Portalban. Ezek a példák a Azure Portal használják.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: a1b37ed1d15282224cc7de61ec6f8a98a4bbf732
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610501"
---
# <a name="manage-cloud-services-classic-in-the-azure-portal"></a>A Azure Portal Cloud Services (klasszikus) kezelése

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A Azure Portal **Cloud Services** területén a következőket teheti:

* Egy szolgáltatás szerepkörének vagy központi telepítésének frissítése.
* Az előkészített üzembe helyezés előléptetése éles környezetbe.
* Erőforrások összekapcsolása a felhőalapú szolgáltatással, így megtekintheti az erőforrás-függőségeket, és méretezheti az erőforrásokat.
* Felhőalapú szolgáltatás vagy központi telepítés törlése.

A felhőalapú szolgáltatás méretezésével kapcsolatos további információkért lásd: [az automatikus skálázás konfigurálása felhőalapú szolgáltatásokhoz a portálon](cloud-services-how-to-scale-portal.md).

## <a name="update-a-cloud-service-role-or-deployment"></a>Cloud Service-szerepkör vagy-telepítés frissítése
Ha frissítenie kell az alkalmazás kódját a Cloud Service-hez, használja a **frissítés** lehetőséget a Cloud Service panelen. Egyetlen szerepkört vagy az összes szerepkört is frissítheti. A frissítéshez feltöltheti az új szervizcsomagot vagy a szolgáltatás konfigurációs fájlját.

1. A [Azure Portal][Azure portal]válassza ki a frissíteni kívánt felhőalapú szolgáltatást. Ez a lépés megnyitja a Cloud Service-példány panelt.

2. A panelen válassza a **frissítés** lehetőséget.

    ![Frissítés gomb](./media/cloud-services-how-to-manage-portal/update-button.png)

3. Frissítse a központi telepítést egy új csomagfájl-fájllal (. cspkg) és a szolgáltatás konfigurációs fájljával (. cscfg).

    ![UpdateDeployment](./media/cloud-services-how-to-manage-portal/update-blade.png)

4. Szükség esetén a Storage-fiókot és a telepítési címkét is frissítheti.

5. Ha bármelyik szerepkör csak egy szerepkör-példánnyal rendelkezik, jelölje be az **üzembe helyezés akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz** jelölőnégyzetet a frissítés folytatásához.

    Az Azure a Cloud Service frissítése során csak a 99,95%-os rendelkezésre állást garantálhatja, ha az egyes szerepkörökhöz legalább két szerepkör-példány (Virtual machines) tartozik. Az egyik virtuális gép két szerepkör-példánnyal dolgozza fel az ügyfelek kéréseit, miközben a másik frissül.

6. Jelölje be a **központi telepítés indítása** jelölőnégyzetet a frissítés alkalmazásához a csomag feltöltésének befejeződése után.

7. A szolgáltatás frissítésének megkezdéséhez kattintson **az OK gombra** .

## <a name="swap-deployments-to-promote-a-staged-deployment-to-production"></a>Üzembe helyezések cseréje az éles környezetben történő üzembe helyezés elősegítése érdekében
Ha úgy dönt, hogy új kiadást helyez üzembe a Cloud Service-ben, az új kiadást a Cloud Service átmeneti környezetében teszteli és teszteli. A **swap** használatával váltson át a két üzemelő példány által kezelt URL-címekre, és léptesse elő az új kiadást az éles környezetbe.

A központi telepítéseket a **Cloud Services** vagy az irányítópulton cserélheti le.

1. A [Azure Portal][Azure portal]válassza ki a frissíteni kívánt felhőalapú szolgáltatást. Ez a lépés megnyitja a Cloud Service-példány panelt.

2. A panelen válassza a **Csere** lehetőséget.

    ![Cloud Services csere gomb](./media/cloud-services-how-to-manage-portal/swap-button.png)

3. Megnyílik a következő megerősítő üzenet:

    ![Cloud Services swap](./media/cloud-services-how-to-manage-portal/swap-prompt.png)

4. Az üzembe helyezési információk ellenőrzése után kattintson az **OK gombra** az üzemelő példányok kicseréléséhez.

    Az üzemelő példányok cseréje gyorsan történik, mert az egyetlen dolog, ami megváltoztatja az üzemelő példányok virtuális IP-címeit (VIP).

    A számítási költségek mentéséhez törölheti az előkészítési telepítést, miután meggyőződött róla, hogy az éles környezet a várt módon működik-e.

### <a name="common-questions-about-swapping-deployments"></a>Gyakori kérdések az üzemelő példányok cseréjével kapcsolatban

**Mik az üzembe helyezések cseréjének előfeltételei?**

A sikeres üzembe helyezési felcserélés két fő előfeltétele:

- Ha statikus IP-címet szeretne használni az üzemi tárolóhelyhez, le kell foglalni egyet az előkészítési tárolóhelyhez is. Ellenkező esetben a swap sikertelen lesz.

- A felcserélés végrehajtása előtt a szerepkörök összes példányának futnia kell. A példányok állapotát a Azure Portal **Áttekintés** paneljén tekintheti meg. Azt is megteheti, hogy a [Get-AzureRole](/powershell/module/servicemanagement/azure.service/get-azurerole) parancsot használja a Windows PowerShellben.

Vegye figyelembe, hogy a vendég operációs rendszer frissítései és a szolgáltatás-javító műveletek miatt a telepítési swap-feladatok sikertelenek lehetnek. További információ: a [Cloud Service üzembe helyezési problémáinak elhárítása](cloud-services-troubleshoot-deployment-problems.md).

**Felmerül a swap-állásidő az alkalmazásom esetében? Hogyan kezelhető?**

Az előző szakaszban leírtaknak megfelelően a rendszer általában gyors üzembe helyezést végez, mert csak az Azure Load Balancer konfigurációjának változása. Bizonyos esetekben 10 vagy több másodpercet is igénybe vehet, és átmeneti csatlakoztatási hibákat eredményezhet. Az ügyfelekre gyakorolt hatás korlátozásához érdemes lehet megvalósítani az [ügyfél újrapróbálkozási logikáját](/azure/architecture/best-practices/transient-faults).

## <a name="delete-deployments-and-a-cloud-service"></a>Központi telepítések és felhőalapú szolgáltatások törlése
A felhőalapú szolgáltatások törléséhez minden meglévő telepítést törölnie kell.

A számítási költségek mentéséhez törölheti az előkészítési telepítést, miután meggyőződött róla, hogy az éles környezet a várt módon működik-e. A leállított, telepített szerepkör-példányok számítási költségeiért számítunk fel díjat.

A központi telepítés vagy a felhőalapú szolgáltatás törléséhez kövesse az alábbi eljárást.

1. A [Azure Portal][Azure portal]válassza ki a törölni kívánt felhőalapú szolgáltatást. Ez a lépés megnyitja a Cloud Service-példány panelt.

2. A panelen válassza a **Törlés** lehetőséget.

    ![Cloud Services törlés gomb](./media/cloud-services-how-to-manage-portal/delete-button.png)

3. A teljes felhőalapú szolgáltatás törléséhez jelölje be a **Cloud Service és a központi telepítések** jelölőnégyzetet. Vagy kiválaszthatja az **éles üzembe helyezést** vagy az **átmeneti üzembe helyezés** jelölőnégyzetet is.

    ![Cloud Services törlés](./media/cloud-services-how-to-manage-portal/delete-blade.png)

4. Válassza a **Törlés** elemet a lap alján.

5. A Cloud Service törléséhez válassza a **Cloud Service törlése** lehetőséget. Ezután a megerősítő üzenetben válassza az **Igen** lehetőséget.

> [!NOTE]
> Ha a felhőalapú szolgáltatás törlődik, és a részletes figyelés konfigurálva van, akkor manuálisan kell törölnie az adatait a Storage-fiókból. További információ a metrikák táblázatának helyéről: Bevezetés a [Cloud Service monitorozásba](cloud-services-how-to-monitor.md).


## <a name="find-more-information-about-failed-deployments"></a>További információ a sikertelen üzembe helyezésekről
Az **Áttekintés** panel tetején egy állapotsor található. Amikor kiválasztja a sávot, megnyílik egy új panel, és megjeleníti a hiba adatait. Ha a központi telepítés nem tartalmaz hibákat, az információ panel üres.

![Cloud Services áttekintése](./media/cloud-services-how-to-manage-portal/status-info.png)



[Azure portal]: https://portal.azure.com

## <a name="next-steps"></a>Következő lépések
* [A felhőalapú szolgáltatás általános konfigurációja](cloud-services-how-to-configure-portal.md).
* Ismerje meg, hogyan [helyezhet üzembe egy felhőalapú szolgáltatást](cloud-services-how-to-create-deploy-portal.md).
* Konfigurálja az [Egyéni tartománynevet](cloud-services-custom-domain-name-portal.md).
* Konfigurálja a [TLS/SSL-tanúsítványokat](cloud-services-configure-ssl-certificate-portal.md).