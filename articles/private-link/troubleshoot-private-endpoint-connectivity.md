---
title: Az Azure privát végpont kapcsolati problémáinak hibaelhárítása
description: Részletes útmutató a privát végpontok kapcsolatának diagnosztizálásához
services: private-endpoint
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
ms.openlocfilehash: 2a4f86d9fae7b78a57cf8da7ab42d2d4a4cd7be5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835397"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Az Azure privát végpont kapcsolati problémáinak hibaelhárítása

Ez a cikk részletes útmutatást nyújt az Azure privát végpont kapcsolatbeállításának ellenőrzéséhez és diagnosztizálásához.

Az Azure privát végpont egy olyan hálózati adapter, amely privát és biztonságos módon csatlakoztatja Egy privát kapcsolati szolgáltatáshoz. Ezzel a megoldással biztonságossá teszi az Azure-beli számítási feladatokat azáltal, hogy privát kapcsolatot biztosít az Azure-szolgáltatási erőforrásokhoz a virtuális hálózatról. Ez a megoldás hatékonyan hozza létre ezeket a szolgáltatásokat a virtuális hálózaton.

A privát végpontok esetében a következő kapcsolódási forgatókönyvek érhetők el:

- Azonos régióból származó virtuális hálózat
- Regionálisan társviszonyban lévő virtuális hálózatok
- Globálisan társviszonyban lévő virtuális hálózatok
- Helyszíni ügyfél VPN-en vagy Azure ExpressRoute kapcsolaton keresztül

## <a name="diagnose-connectivity-problems"></a>Kapcsolati problémák diagnosztizálása 

Tekintse át ezeket a lépéseket, és győződjön meg arról, hogy az összes szokásos konfiguráció a várt módon oldja meg a privát végpont beállításával kapcsolatos csatlakozási problémákat.

1. Tekintse át a privát végpont konfigurációját az erőforrás tallózással.

    a. Ugrás a **Private Link-központ.**

      ![Private Link-központ](./media/private-endpoint-tsg/private-link-center.png)

    b. A bal oldali panelen válassza a **Privát végpontok lehetőséget.**
    
      ![Privát végpontok](./media/private-endpoint-tsg/private-endpoints.png)

    c. Szűrje és válassza ki a diagnosztizálni kívánt privát végpontot.

    d. Tekintse át a virtuális hálózat és a DNS adatait.
     - Ellenőrizze, hogy a kapcsolat állapota **Jóváhagyva.**
     - Győződjön meg arról, hogy a virtuális gép csatlakozik a privát végpontokat tartalmazó virtuális hálózathoz.
     - Ellenőrizze, hogy az FQDN-adatok (másolat) és a magánhálózati IP-cím hozzá vannak-e rendelve.
    
       ![Virtuális hálózat és DNS-konfiguráció](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. A [Azure Monitor](../azure-monitor/overview.md) meg, hogy az adatok áramlnak-e.

    a. A privát végpont erőforrásán válassza a Monitor **lehetőséget.**
     - Válassza **a Bájtok be vagy** a **Bájtok ki lehetőséget.** 
     - Nézze meg, hogy áramlik-e az adatok, amikor megpróbál csatlakozni a privát végponthoz. Körülbelül 10 perces késés várható.
    
       ![Privát végpont telemetriának ellenőrzése](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Használja **az Azure-beli** virtuálisgép-kapcsolat hibaelhárítási Network Watcher.

    a. Válassza ki az ügyféloldali virtuális gépet.

    b. Válassza **a Kapcsolat hibaelhárítása** lehetőséget, majd válassza a **Kimenő kapcsolatok** lapot.
    
      ![Network Watcher – Kimenő kapcsolatok tesztelése](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Válassza **a Network Watcher a részletes kapcsolatkövetéshez lehetőséget.**
    
      ![Network Watcher – Kapcsolat hibaelhárítása](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Válassza **a Test by FQDN (Tesztelés teljes tartománynként) lehetőséget.**
     - Illessze be az FQDN-t a privát végpont erőforrásból.
     - Adjon meg egy portot. Általában használja a 443-ast az Azure Storage-hoz vagy a Azure Cosmos DB 1336-ot az SQL-hez.

    e. Válassza **a Tesztelés** lehetőséget, és ellenőrizze a teszteredményeket.
    
      ![Network Watcher – Teszteredmények](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. A teszteredmények DNS-feloldásának ugyanazt a magánhálózati IP-címet kell hozzárendelnie a privát végponthoz.

    a. Ha a DNS-beállítások helytelenek, kövesse az alábbi lépéseket:
     - Ha privát zónát használ: 
       - Győződjön meg arról, hogy az ügyfélszámítógép virtuális hálózata társítva van a privát zónával.
       - Ellenőrizze, hogy létezik-e a privát DNS-zóna rekordja. Ha még nem létezik, hozza létre.
     - Ha egyéni DNS-t használ:
       - Tekintse át az egyéni DNS-beállításokat, és ellenőrizze, hogy a DNS-konfiguráció helyes-e.
       Útmutatásért lásd: [Privát végpontok áttekintése: DNS-konfiguráció.](./private-endpoint-overview.md#dns-configuration)

    b. Ha a kapcsolat hálózati biztonsági csoportok (NSG-k) vagy felhasználó által megadott útvonalak miatt sikertelen:
     - Tekintse át a kimenő NSG-szabályokat, és hozza létre a megfelelő kimenő szabályokat a forgalom engedélyezése érdekében.
    
       ![Kimenő NSG-szabályok](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. A forrás virtuális gépnek a privát végpont IP-címének következő ugrás útvonalát InterfaceEndpoints útvonalként kell látnia a hálózati adapter hatályos útvonalai között. 

    a. Ha nem látja a privát végpont útvonalát a forrás virtuális gépen, ellenőrizze, hogy 
     - A forrás virtuális gép és a privát végpont ugyanannak a virtuális hálózatnak a tagja. Ha igen, akkor fel kell vennie a kapcsolatot az ügyfélszolgálattal. 
     - A forrás virtuális gép és a privát végpont különböző virtuális hálózatok részei, majd ellenőrizze a virtuális hálózatok közötti IP-kapcsolatot. Ha ip-kapcsolat van, de továbbra sem látja az útvonalat, lépjen kapcsolatba az ügyfélszolgálattal. 

1. Ha a kapcsolat ellenőrzött eredményeket kapott, a kapcsolódási probléma más aspektusokkal, például titkos okkal, jogkivonatokkal és jelszavakkal kapcsolatos lehet az alkalmazásrétegben.
   - Ebben az esetben tekintse át a privát végponthoz társított privát kapcsolati erőforrás konfigurációját. További információért tekintse meg a Azure Private Link [hibaelhárítási útmutatóját.](troubleshoot-private-link-connectivity.md)
   
1. Mindig jó, ha a támogatási jegy emelése előtt leszűkíti a látókört. 

    a. Ha a forrás helyszíni, és az Azure-beli privát végponthoz csatlakozik, problémákat okozhat, akkor próbáljon meg csatlakozni 
      - Egy másik helyszíni virtuális gépre, és ellenőrizze, hogy rendelkezik-e IP-kapcsolattal a Virtual Network helyszíni virtuális gépről. 
      - A virtuális gépről a Virtual Network a privát végpontra.
      
    b. Ha a forrás Azure- és a privát végpont eltérő Virtual Network, próbáljon meg csatlakozni 
      - Másik forrásból származó privát végpontra. Ezzel elkülönítheti a virtuális géppel kapcsolatos problémákat. 
      - Bármely olyan virtuális gépre, amely a privát végpont azonos Virtual Network részét képezi.  

1. Ha a [probléma Azure ügyfélszolgálata](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) megoldatlan, és továbbra is fennáll a kapcsolódási probléma, lépjen kapcsolatba a kapcsolati csapattal.

## <a name="next-steps"></a>Következő lépések

 * [Privát végpont létrehozása a frissített alhálózaton (Azure Portal)](./create-private-endpoint-portal.md)
 * [Azure Private Link hibaelhárítási útmutató](troubleshoot-private-link-connectivity.md)
