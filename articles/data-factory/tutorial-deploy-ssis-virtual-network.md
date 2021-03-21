---
title: Oktatóanyag egy Azure-SSIS integrációs modul konfigurálásához egy virtuális hálózathoz való csatlakozáshoz
description: Ismerje meg, hogyan csatlakozhat Azure-SSIS integrációs modulhoz egy Azure-beli virtuális hálózathoz való csatlakozáshoz.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 3dbbce4adc44696fdd12f6ce948b48b34caaed75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100391235"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) konfigurálása virtuális hálózathoz való csatlakozáshoz

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez az oktatóanyag alapvető lépéseket biztosít a Azure Portal használatával egy Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) konfigurálásához a virtuális hálózathoz való csatlakozáshoz.

A lépések a következők:

- Virtuális hálózat konfigurálása.
- Csatlakoztassa a Azure-SSIS IRt egy virtuális hálózathoz Azure Data Factory portálról.

## <a name="prerequisites"></a>Előfeltételek

- **Azure-SSIS integrációs** modul. Ha nem rendelkezik Azure-SSIS integrációs futtatókörnyezettel, a Kezdés előtt hozzon [létre egy Azure-SSIS integrációs modult Azure Data Factory](tutorial-deploy-ssis-packages-azure.md) .

- **Felhasználói engedély**. A Azure-SSIS IR létrehozó felhasználónak legalább az alábbi lehetőségek egyikével kell rendelkeznie a [szerepkör-hozzárendeléssel](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) Azure Data Factory erőforráson:

    - Használja a beépített hálózati közreműködő szerepkört. Ehhez a szerepkörhöz a _Microsoft. Network/ \*_ engedély tartozik, amely a szükségesnél sokkal nagyobb hatókörű.
    - Hozzon létre egy egyéni szerepkört, amely csak a szükséges _Microsoft. Network/virtualNetworks/ \* /JOIN/Action_ engedélyt tartalmazza. Ha a Azure-SSIS IR saját nyilvános IP-címeit is szeretné csatlakoztatni egy Azure Resource Manager virtuális hálózathoz, akkor a szerepkörben a _Microsoft. Network/nyilvános IP/*/JOIN/Action_ engedély is szerepel.

- **Virtuális hálózat**.

    - Ha nem rendelkezik virtuális hálózattal, [hozzon létre egy virtuális hálózatot a Azure Portal használatával](../virtual-network/quick-create-portal.md).

    - Győződjön meg arról, hogy a virtuális hálózat erőforráscsoport létrehozhat és törölhet bizonyos Azure-hálózati erőforrásokat.
    
        A Azure-SSIS IR létre kell hoznia bizonyos hálózati erőforrásokat a virtuális hálózattal azonos erőforráscsoporthoz. Ezek az erőforrások a következők:
        - Egy Azure Load Balancer, amelynek neve: *\<Guid> azurebatch-cloudserviceloadbalancer*
        - Egy hálózati biztonsági csoport, amelynek neve * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Egy Azure-beli nyilvános IP-cím, amelynek neve: azurebatch-cloudservicepublicip
    
        Ezek az erőforrások a Azure-SSIS IR indításakor lesznek létrehozva. Ha a Azure-SSIS IR leáll, a rendszer törli őket. Ha nem szeretné megakadályozni a Azure-SSIS IR leállítását, ne használja újra ezeket a hálózati erőforrásokat a többi erőforrásban.

    - Győződjön meg arról, hogy nincs [erőforrás-zárolás](../azure-resource-manager/management/lock-resources.md) azon az erőforráscsoport/előfizetés esetében, amelyhez a virtuális hálózat tartozik. Ha írásvédett/törlési zárolást konfigurál, a Azure-SSIS IR elindítása és leállítása sikertelen lesz, vagy a rendszer nem válaszol.

    - Győződjön meg arról, hogy nem rendelkezik Azure Policy-hozzárendeléssel, amely megakadályozza, hogy a következő erőforrások jöjjenek létre a virtuális hálózathoz tartozó erőforráscsoport/előfizetés alatt:
        - Microsoft. Network/LoadBalancers
        - Microsoft. Network/NetworkSecurityGroups

- Ebben az oktatóanyagban az alábbi **hálózati konfigurációs** forgatókönyvek nem szerepelnek:
    - Ha saját nyilvános IP-címeit hozza a Azure-SSIS IRhoz.
    - Ha saját tartománynévrendszer-(DNS-) kiszolgálót használ.
    - Ha hálózati biztonsági csoportot (NSG) használ az alhálózaton.
    - Ha az Azure ExpressRoute vagy egy felhasználó által megadott útvonalat (UDR) használ.
    - Ha testreszabott Azure-SSIS IR használ.
    
    További információ: [virtuális hálózati konfiguráció](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Virtuális hálózat konfigurálása

A Azure Portal használatával konfigurálhatja a virtuális hálózatokat, mielőtt megpróbál csatlakoztatni egy Azure-SSIS IR.

1. Indítsa el a Microsoft Edge vagy a Google Chrome böngészőt. Jelenleg csak ezek a böngészők támogatják a Data Factory felhasználói felületet.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza a **További szolgáltatások** lehetőséget. A **virtuális hálózatok** szűrése és kiválasztása.

1. A listában jelölje ki a virtuális hálózatot, és válassza ki azt.

1. A **virtuális hálózat** lapon válassza a **Tulajdonságok** lehetőséget.

1. Válassza a másolás gombot az **erőforrás-azonosítóhoz** , és másolja a virtuális hálózat erőforrás-azonosítóját a vágólapra. Mentse az azonosítót a vágólapról a OneNote vagy egy fájlból.

1. A bal oldali menüben válassza az **alhálózatok** lehetőséget.

    - Győződjön meg arról, hogy a kiválasztott alhálózat rendelkezik-e elegendő szabad hellyel a Azure-SSIS IR használatához. Hagyjon elérhető IP-címeket legalább két alkalommal az IR-csomópont számának. Az Azure néhány IP-címet fenntart az egyes alhálózatokon belül. Ezeket a címeket nem lehet használni. Az alhálózatok első és utolsó IP-címe a protokollok megfelelőségére van fenntartva, és három további címet használ az Azure-szolgáltatásokhoz. További információ: az [IP-címek ezen alhálózatokon belüli használatára vonatkozó korlátozások?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Ne válassza ki a GatewaySubnet egy Azure-SSIS IR telepítéséhez. A virtuális hálózati átjárók számára dedikált.
    - Ne használjon olyan alhálózatot, amelyet kizárólag más Azure-szolgáltatások foglalnak magukban (például SQL Database SQL felügyelt példány, App Service stb.).

1. Ellenőrizze, hogy a Azure Batch szolgáltató regisztrálva van-e a virtuális hálózattal rendelkező Azure-előfizetésben. Vagy regisztrálja a Azure Batch szolgáltatót. Ha már rendelkezik Azure Batch-fiókkal az előfizetésben, az előfizetés regisztrálva lesz a Azure Batchhoz. (Ha a Azure-SSIS IR a Data Factory portálon hozza létre, a Azure Batch szolgáltató automatikusan regisztrálva lesz.)

   1. A Azure Portal bal oldali menüjében válassza az **előfizetések** lehetőséget.

   1. Válassza ki előfizetését.

   1. A bal oldalon válassza az **erőforrás-szolgáltatók** lehetőséget, és győződjön meg arról, hogy **Microsoft.BatCH** egy regisztrált szolgáltató.

   ![A "regisztrált" állapot megerősítése](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Ha nem látja **Microsoft.BatCH** -t a listában, a regisztráláshoz [hozzon létre egy üres Azure batch fiókot](../batch/batch-account-create-portal.md) az előfizetésében. Később is törölheti.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>A Azure-SSIS IR csatlakoztatása egy virtuális hálózathoz

Miután konfigurálta a Azure Resource Manager virtuális hálózatot vagy a klasszikus virtuális hálózatot, csatlakoztathatja a Azure-SSIS IRt a virtuális hálózathoz:

1. Indítsa el a Microsoft Edge vagy a Google Chrome böngészőt. Jelenleg csak ezek a böngészők támogatják a Data Factory felhasználói felületet.

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza az **adatüzemek** lehetőséget. Ha a menüben nem látja az **adatok gyárait** , válassza a **További szolgáltatások** lehetőséget, majd az **intelligencia és elemzés** szakaszban válassza az **adatüzemek** lehetőséget.

   ![Az adatüzemek listája](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Válassza ki az adatgyárat a listában szereplő Azure-SSIS IR. Megjelenik a saját adatelőállítójának kezdőlapja. Válassza ki a **szerző & figyelés** csempét. A Data Factory felhasználói felület külön lapon jelenik meg.

   ![Data factory kezdőlap](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. A Data Factory felhasználói felületen váltson a **Szerkesztés** lapra, válassza a **kapcsolatok** lehetőséget, majd váltson az **Integration Runtimes (integrációs** modulok) lapra.

   !["Integration Runtimes" lap](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Ha a Azure-SSIS IR fut, az **integrációs** modulok listájában, a **műveletek** oszlopban válassza a **Leállítás** gombot a Azure-SSIS IR. A Azure-SSIS IR addig nem szerkeszthető, amíg le nem állítja.

   ![Az IR leállítása](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Az **integrációs** modulok listájának **műveletek** oszlopában válassza ki a Azure-SSIS IR **Szerkesztés** gombját.

   ![Az Integration Runtime szerkesztése](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Az integrációs modul telepítése panelen az **általános beállítások** és az SQL- **Beállítások** szakaszban válassza a **tovább** gombot.

1. A **Speciális beállítások** szakaszban:
   1. Válassza ki a **VNet kiválasztása a Azure-SSIS Integration Runtimehoz való csatlakozáshoz lehetőséget, hogy az ADF bizonyos hálózati erőforrásokat hozzon létre, és igény szerint saját statikus nyilvános IP-címeket** használjon jelölőnégyzetet.

   1. Az **előfizetés** mezőben válassza ki a virtuális hálózattal rendelkező Azure-előfizetést.

   1. A **hely** esetében az integrációs modul ugyanazon helye van kiválasztva.

   1. A **Típus mezőben** válassza ki a virtuális hálózat típusát: klasszikus vagy Azure Resource Manager. Javasoljuk, hogy válasszon ki egy Azure Resource Manager virtuális hálózatot, mert a klasszikus virtuális hálózatok hamarosan elavulttá válnak.

   1. A **VNet neve** mezőben válassza ki a virtuális hálózat nevét. A virtuális hálózati szolgáltatás-végpontokkal vagy az SQL felügyelt példányával, amely a SSISDB üzemeltetésére szolgál, ugyanazt a SQL Database használja. Vagy a helyszíni hálózathoz csatlakoztatva kell lennie. Ellenkező esetben bármely virtuális hálózat lehet a saját statikus nyilvános IP-címeinek használata Azure-SSIS IR számára.

   1. Az **alhálózat neve** mezőben válassza ki a virtuális hálózatához tartozó alhálózat nevét. A virtuális hálózati szolgáltatás-végpontokkal a SSISDB üzemeltetéséhez használt SQL-DATBASE azonosnak kell lennie. Vagy egy másik alhálózatnak kell lennie, amely az SQL felügyelt példányának a SSISDB-t futtató magánhálózati végponttal való futtatására szolgál. Ellenkező esetben bármilyen alhálózat lehet a saját statikus nyilvános IP-címeinek Azure-SSIS IR számára.

   1. Válassza a **VNet érvényesítése** lehetőséget. Ha az ellenőrzés sikeres, válassza a **Folytatás** lehetőséget.

   ![Virtuális hálózat speciális beállításai](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Az **Összefoglalás** szakaszban tekintse át a Azure-SSIS IR összes beállítását. Ezután válassza a **frissítés** lehetőséget.

1. Indítsa el a Azure-SSIS IR a Azure-SSIS IR **műveletek** oszlop **Start** gombjának kiválasztásával. Körülbelül 20 – 30 percet vesz igénybe a virtuális hálózathoz csatlakozó Azure-SSIS IR elindítása.

## <a name="next-steps"></a>Következő lépések

További információ a [Azure-SSIS IR virtuális hálózathoz való csatlakoztatásáról](join-azure-ssis-integration-runtime-virtual-network.md).