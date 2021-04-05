---
title: Az Azure privát kapcsolati szolgáltatás csatlakoztathatósági problémáinak elhárítása
description: Részletes útmutató a privát kapcsolati kapcsolatok diagnosztizálásához
services: private-link
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 45a7a146dd929408b50a0045fe2598726ee05505
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544309"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Az Azure privát kapcsolati szolgáltatás csatlakoztathatósági problémáinak elhárítása

Ez a cikk részletes útmutatást nyújt az Azure Private-kapcsolat beállításához szükséges kapcsolatok ellenőrzéséhez és diagnosztizálásához.

Az Azure Private link használatával a virtuális hálózat egy privát végpontján keresztül elérheti az Azure platform szolgáltatásként nyújtott szolgáltatásokat, például az Azure Storage, a Azure Cosmos DB és a Azure SQL Database, valamint az Azure által üzemeltetett ügyfél-vagy partneri szolgáltatásokat. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinc hálózatán halad át, ami kiküszöböli a nyilvános internetről való kivonást. Létrehozhatja saját privát kapcsolati szolgáltatását is a virtuális hálózatban, és a saját ügyfelei számára elérhetővé teheti azt.

Engedélyezheti, hogy a szolgáltatás a Azure Load Balancer standard szintje mögött fusson a privát kapcsolatok eléréséhez. A szolgáltatás felhasználói létrehozhatnak egy privát végpontot a virtuális hálózaton belül, és leképezhetők a szolgáltatásba, hogy magánjellegű módon férhessenek hozzá.

A privát hivatkozással elérhető csatlakozási forgatókönyvek a következők:

- Azonos régióból származó virtuális hálózat
- Regionálisan egymással összekötött virtuális hálózatok
- Globálisan egyenrangú virtuális hálózatok
- Helyszíni ügyfelek VPN-en vagy Azure ExpressRoute-áramkörökben

## <a name="deployment-troubleshooting"></a>Üzembe helyezés hibaelhárítása

A hibaelhárítási esetekben tekintse át a [hálózati házirendek letiltásával kapcsolatos információkat a privát kapcsolat szolgáltatásban](./disable-private-link-service-network-policy.md) , ahol nem tudja kijelölni a forrás IP-címet az Ön által választott alhálózatból a privát kapcsolati szolgáltatáshoz.

Győződjön meg arról, hogy a **privateLinkServiceNetworkPolicies** beállítás le van tiltva azon alhálózat esetében, amelynek a forrás IP-címét kiválasztja.

## <a name="diagnose-connectivity-problems"></a>Kapcsolódási problémák diagnosztizálása

Ha kapcsolódási problémák merülnek fel a privát kapcsolat beállításakor, tekintse át ezeket a lépéseket, és győződjön meg arról, hogy az összes szokásos konfiguráció a vártnak megfelelően van-e

1. Tekintse át a privát kapcsolat konfigurációját az erőforrás tallózásával.

    a. Nyissa meg a **Private link centert**.

      ![Privát kapcsolati központ](./media/private-link-tsg/private-link-center.png)

    b. A bal oldali panelen válassza a **Private link Services** elemet.

      ![Private link Services](./media/private-link-tsg/private-link-service.png)

    c. Szűrje és válassza ki a diagnosztizálni kívánt privát kapcsolati szolgáltatást.

    d. Tekintse át a magánhálózati végpontok kapcsolatait.
     - Győződjön meg arról, hogy a kapcsolatot kérő privát végpont **jóváhagyott** kapcsolati állapottal van listázva.
     - Ha az állapot **függőben** van, válassza ki és hagyja jóvá.

       ![Magánhálózati végpontok kapcsolatai](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - A név kiválasztásával nyissa meg azt a privát végpontot, amelyhez csatlakozni szeretne. Győződjön meg arról, hogy a kapcsolatok állapota **jóváhagyva** értékre van állítva.

       ![Privát végpontok kapcsolatainak áttekintése](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - Mindkét oldal jóváhagyása után próbálja megismételni a kapcsolatot.

    e. Tekintse át az **aliast** az **Áttekintés** lapon és az **erőforrás-azonosítót** a **Tulajdonságok** lapon.
     - Győződjön meg arról, hogy az **alias** és az **erőforrás-azonosító** információ megegyezik azzal az **ALIASsal** és **erőforrás-azonosítóval** , amelyet a szolgáltatáshoz tartozó privát végpont létrehozásához használ.

       ![Alias adatainak ellenőrzése](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![Erőforrás-azonosító adatainak ellenőrzése](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. Tekintse át a **láthatósági** információkat az **Áttekintés** lapon.
     - Győződjön meg arról, hogy az előfizetése a **láthatósági** hatókör alá esik.

       ![Láthatósági információk ellenőrzése](./media/private-link-tsg/pls-overview-pane-visibility.png)

    : Tekintse **át a terheléselosztó** információit az **Áttekintés** lapon.
     - A Load balancert a terheléselosztó hivatkozásra kattintva érheti el.

       ![Terheléselosztó adatainak ellenőrzése](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - Győződjön meg arról, hogy a terheléselosztó beállításai a saját elvárásainak megfelelően vannak konfigurálva.
       - Tekintse át a előtér **-IP-konfigurációt**.
       - Tekintse át a **háttérbeli készleteket**.
       - Tekintse át a terheléselosztási **szabályokat**.

       ![Terheléselosztó tulajdonságainak ellenőrzése](./media/private-link-tsg/pls-ilb-properties.png)

     - Győződjön meg arról, hogy a terheléselosztó az előző beállításoknak megfelelően működik.
       - Válasszon ki egy virtuális gépet az alhálózaton kívül minden olyan alhálózaton, ahol a terheléselosztó háttér-készlete elérhető.
       - Próbálja meg elérni a terheléselosztó kezelőfelületét az előző virtuális gépről.
       - Ha a kapcsolat terheléselosztási szabályok szerint végzi a háttér-készletet, akkor a terheléselosztó működőképes.
       - A terheléselosztó metrikáját a Azure Monitor segítségével is ellenőrizheti, így ellenőrizheti, hogy az adatok áramlanak-e a terheléselosztó használatával.

1. A [Azure monitor](../azure-monitor/overview.md) használatával megtekintheti, hogy folyik-e az adatforgalom.

    a. A Private link Service-erőforrás lapon válassza a **metrikák** lehetőséget.
     - Válassza ki **a bájtok vagy a** **bájtok** lehetőséget.
     - Ellenőrizze, hogy a magánhálózati kapcsolati szolgáltatáshoz való kapcsolódási kísérlet során az adatforgalom áramlik-e. Körülbelül 10 perc késés várható.

       ![Privát kapcsolati szolgáltatás metrikáinak ellenőrzése](./media/private-link-tsg/pls-metrics.png)

1. Ha a probléma továbbra is megoldatlan, forduljon az [Azure ügyfélszolgálatához](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) , és a kapcsolati probléma továbbra is fennáll.

## <a name="next-steps"></a>Következő lépések

 * [Privát kapcsolati szolgáltatás (CLI) létrehozása](./create-private-link-service-cli.md)
 * [Azure Private Endpoint – hibaelhárítási útmutató](troubleshoot-private-endpoint-connectivity.md)