---
title: Privát végpont létrehozása biztonságos kapcsolatok létrehozásához
titleSuffix: Azure Cognitive Search
description: Hozzon létre egy magánhálózati végpontot egy virtuális hálózaton egy Azure Cognitive Search szolgáltatás biztonságos kapcsolataihoz.
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 7445ac5d750ac29d3e6ce466a48e82efd1bcde40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545530"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Hozzon létre egy privát végpontot biztonságos kapcsolódáshoz az Azure Cognitive Search

Ebben a cikkben a Azure Portal használatával hozzon létre egy új Azure Cognitive Search Service-példányt, amely nem érhető el az interneten keresztül. Ezután konfiguráljon egy Azure-beli virtuális gépet ugyanabban a virtuális hálózatban, és használja azt a keresési szolgáltatás privát végponton keresztüli eléréséhez.

A privát végpontokat az [Azure privát kapcsolata](../private-link/private-link-overview.md)külön szolgáltatásként biztosíthatja. A költségekkel kapcsolatos további információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/private-link/).

Az ebben a cikkben leírtak szerint létrehozhat egy privát végpontot a Azure Portalban. Azt is megteheti, hogy a 2020-03-13-es, [Azure PowerShell](/powershell/module/az.search)-es vagy az [Azure CLI](/cli/azure/search)-es [verziójának felügyeleti REST API verzióját](/rest/api/searchmanagement/)használja.

> [!NOTE]
> Ha a szolgáltatás végpontja privát, egyes portál-funkciók le vannak tiltva. Megtekintheti és kezelheti a szolgáltatási szint adatait, de az indexet, az indexelő és a készségkészlet adatait biztonsági okokból elrejtheti. A portál alternatívájaként a [vs Code bővítménnyel](https://aka.ms/vscode-search) is használhatja a szolgáltatás különböző összetevőit.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Miért érdemes privát végpontot használni a biztonságos hozzáféréshez?

Az Azure Cognitive Search [magánhálózati végpontok](../private-link/private-endpoint-overview.md) lehetővé teszik, hogy egy virtuális hálózaton lévő ügyfél biztonságosan hozzáférhessen egy keresési indexben lévő adathoz egy [privát hivatkozáson](../private-link/private-link-overview.md)keresztül. A privát végpont egy IP-címet használ a keresési szolgáltatáshoz tartozó [virtuális hálózati címtartomány](../virtual-network/private-ip-addresses.md) alapján. Az ügyfél és a keresési szolgáltatás közötti hálózati forgalom áthalad a virtuális hálózaton és a Microsoft gerinc hálózatán található privát kapcsolaton, ami kiküszöböli a nyilvános internetről való kitettséget. A privát hivatkozást támogató egyéb Pásti-szolgáltatások listájáért tekintse meg a termék dokumentációjának [rendelkezésre állási szakaszát](../private-link/private-link-overview.md#availability) .

A keresési szolgáltatás privát végpontjai a következőket teszik lehetővé:

- A keresési szolgáltatás nyilvános végpontján lévő összes kapcsolat blokkolása.
- Növelje a virtuális hálózat biztonságát azáltal, hogy letiltja a virtuális hálózat kiszűrése.
- Biztonságosan csatlakozhat a keresési szolgáltatáshoz olyan helyszíni hálózatokról, amelyek VPN-vagy [Expressroute](../expressroute/expressroute-locations.md) [-](../vpn-gateway/vpn-gateway-about-vpngateways.md) kapcsolaton keresztül csatlakoznak a virtuális hálózathoz.

## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és alhálózatot a keresési szolgáltatás privát végpontjának eléréséhez használni kívánt virtuális gép üzemeltetéséhez.

1. A Azure Portal Kezdőlap lapon válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **virtuális hálózat** lehetőséget.

1. A **virtuális hálózat létrehozása** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Az előfizetés kiválasztása|
    | Erőforráscsoport | Válassza az **új létrehozása** elemet, írja be a *myResourceGroup*, majd kattintson **az OK gombra** . |
    | Name | *MyVirtualNetwork* megadása |
    | Region | Válassza ki a kívánt régiót |
    |||

1. Hagyja meg az alapértelmezett értékeket a többi beállításnál. Kattintson a **felülvizsgálat + létrehozás** , majd a **Létrehozás** elemre.

## <a name="create-a-search-service-with-a-private-endpoint"></a>Keresési szolgáltatás létrehozása privát végponttal

Ebben a szakaszban egy új Azure Cognitive Search szolgáltatást fog létrehozni egy privát végponttal. 

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **webes**  >  **Azure-Cognitive Search** lehetőséget.

1. Az **új Search Service – alapismeretek** területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az előző szakaszban hozta létre.|
    | **PÉLDÁNY RÉSZLETEI** |  |
    | URL-cím | Adjon meg egy egyedi nevet. |
    | Hely | Válassza ki a kívánt régiót. |
    | Tarifacsomag | Válassza az **árképzés módosítása** lehetőséget, és válassza ki a kívánt szolgáltatási szintet. (Az **ingyenes** szintet nem támogatja. **Alap** vagy magasabb értéknek kell lennie.) |
    |||
  
1. Válassza a **Next (tovább): skála** lehetőséget.

1. Hagyja meg az alapértelmezett értéket, és válassza a **Tovább: hálózatkezelés** lehetőséget.

1. Az **új Search Service-hálózatkezelés** területen válassza a **privát** lehetőséget a **végponti kapcsolathoz (adatkapcsolat)**.

1. Az **új Search Service-hálózatkezelés** területen válassza a **+ Hozzáadás** lehetőséget a **privát végpont** alatt. 

1. A **privát végpont létrehozása** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az előző szakaszban hozta létre.|
    | Hely | Válassza az **USA nyugati** régiója lehetőséget.|
    | Name | Adja meg a *myPrivateEndpoint* nevet.  |
    | Célzott alerőforrás | Hagyja meg az alapértelmezett **searchService**. |
    | **HÁLÓZATKEZELÉS** |  |
    | Virtuális hálózat  | Válassza ki a *MyVirtualNetwork* az erőforráscsoport *myResourceGroup*. |
    | Alhálózat | Válassza a *mySubnet* lehetőséget. |
    | **PRIVÁT DNS-INTEGRÁCIÓ** |  |
    | Integrálás saját DNS-zónával  | Hagyja meg az alapértelmezett **Igen értéket**. |
    | Privát DNS-zóna  | Hagyja meg az alapértelmezett * * (új) privatelink.search.windows.net * * értéket. |
    |||

1. Válassza az **OK** lehetőséget. 

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját. 

1. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget. 

1. Miután befejeződött az új szolgáltatás üzembe helyezése, keresse meg az imént létrehozott erőforrást.

1. A bal oldali tartalom menüben válassza a **kulcsok** lehetőséget.

1. Másolja az **elsődleges rendszergazdai kulcsot** később, amikor csatlakozik a szolgáltatáshoz.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** lehetőséget.

1. A **virtuális gép létrehozása – alapismeretek** területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **PROJEKT RÉSZLETEI** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az előző szakaszban hozta létre.  |
    | **PÉLDÁNY RÉSZLETEI** |  |
    | Virtuális gép neve | Adja meg a *myVm*. |
    | Region | Válassza az **USA nyugati** régiója vagy bármely Ön által használt régiót. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Kép | Válassza a **Windows Server 2019 Datacenter** lehetőséget. |
    | Méret | Hagyja meg az alapértelmezett **standard DS1 v2** értéket. |
    | **RENDSZERGAZDAFIÓK** |  |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **BEJÖVŐPORT-SZABÁLYOK** |  |
    | Nyilvános bejövő portok | Hagyja meg az alapértelmezett **engedélyezett portok engedélyezése beállítást**. |
    | Válassza ki a bejövő portokat | Hagyja meg az alapértelmezett **RDP-t (3389)**. |
    | **MEGTAKARÍTÁSI LEHETŐSÉG** |  |
    | Már van Windows-licence? | Hagyja meg az alapértelmezett **nem** értéket. |
    |||

1. Válassza a **Tovább: lemezek** lehetőséget.

1. A **Virtuális gép létrehozása – Lemezek** lehetőségnél hagyja meg az alapértelmezett értékeket, és válassza a **Tovább: Hálózatkezelés** lehetőséget.

1. A **virtuálisgép-hálózat létrehozása** területen válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Hagyja meg az alapértelmezett **MyVirtualNetwork**.  |
    | Címtér | Hagyja meg az alapértelmezett **10.1.0.0/24** értéket.|
    | Alhálózat | Hagyja meg az alapértelmezett **mySubnet (10.1.0.0/24)**.|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése** lehetőséget. |
    | Válassza ki a bejövő portokat | Válassza a **http** és az **RDP** lehetőséget.|
    ||

   > [!NOTE]
   > Az IPv4-címek [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) formátumban is megadhatók. Ne felejtse el elkerülni a magánhálózati hálózat számára fenntartott IP-címtartományt az [RFC 1918](https://tools.ietf.org/html/rfc1918)-ben leírtak szerint:
   >
   > - `10.0.0.0 - 10.255.255.255  (10/8 prefix)`
   > - `172.16.0.0 - 172.31.255.255  (172.16/12 prefix)`
   > - `192.168.0.0 - 192.168.255.255 (192.168/16 prefix)`

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját.

1. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget. 

## <a name="connect-to-the-vm"></a>Kapcsolódás a virtuális géphez

Töltse le, majd kapcsolódjon a virtuális gép *myVm* a következőképpen:

1. A portál keresési sávjába írja be a *myVm* szöveget.

1. Kattintson a **Csatlakozás** gombra. A **Kapcsolódás** gombra kattintva megnyílik a **virtuális géphez való kapcsolódás** .

1. Válassza az **RDP-fájl letöltése** lehetőséget. Az Azure létrehoz egy RDP protokoll (*. rdp*) fájlt, és letölti a számítógépre.

1. Nyissa meg a letöltött. rdp fájlt.

    1. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.

    1. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

        > [!NOTE]
        > Előfordulhat, hogy a   >  virtuális gép létrehozásakor megadott hitelesítő adatok megadásához több választási lehetőséget kell választania **egy másik fiók használatával**.

1. Válassza az **OK** lehetőséget.

1. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha a tanúsítvány figyelmeztetést kap, válassza az **Igen** vagy a **Folytatás** lehetőséget.

1. Ha megjelenik a virtuális gép asztala, csökkentse a helyi asztalra való visszatérést.  

## <a name="test-connections"></a>Kapcsolatok tesztelése

Ebben a szakaszban ellenőrzi, hogy a magánhálózati hozzáférés a keresési szolgáltatáshoz történik-e, és hogy a privát végpont használatával csatlakozik-e.

Ha a keresési szolgáltatás végpontja privát, egyes portál-funkciók le vannak tiltva. Megtekintheti és kezelheti a szolgáltatási szint beállításait, de a portál hozzáférése az indexhez és a szolgáltatás különböző egyéb összetevőihez, például az index, az indexelő és a készségkészlet-definíciók biztonsági okokból korlátozottak.

1. A *myVM* távoli asztal nyissa meg a PowerShellt.

1. Adja meg az "nslookup [Search szolgáltatás neve]. Search. Windows. net" kifejezést.

    Egy ehhez hasonló üzenet jelenik meg:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. A virtuális gépről kapcsolódjon a keresési szolgáltatáshoz, és hozzon létre egy indexet. [Ezt a](search-get-started-rest.md) rövid útmutatót követve létrehozhat egy új keresési indexet a szolgáltatásban a REST API használatával. A webes API-tesztelési eszköz kéréseinek beállítása megköveteli a keresési szolgáltatás végpontját (https://[Search szolgáltatás neve]. Search. Windows. net) és az admin API-kulcsot, amelyet az előző lépésben másolt.

1. A virtuális gép gyors üzembe helyezésének befejezésével megerősíti, hogy a szolgáltatás teljesen működőképes.

1. A távoli asztali kapcsolat bezárásával *myVM*. 

1. Annak ellenőrzéséhez, hogy a szolgáltatás nem érhető el nyilvános végponton, nyissa meg a Poster szolgáltatást a helyi munkaállomáson, és próbálja meg a gyors útmutató első több feladatát. Ha hibaüzenet jelenik meg arról, hogy a távoli kiszolgáló nem létezik, sikeresen konfigurált egy magánhálózati végpontot a keresési szolgáltatáshoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha végzett a privát végpont, a Search szolgáltatás és a virtuális gép használatával, törölje az erőforráscsoportot és a benne lévő összes erőforrást:
1. Adja meg a  *myResourceGroup* a   portál tetején található **keresőmezőbe** , és válassza a  *myResourceGroup* lehetőséget   a keresési eredmények közül. 
1. Válassza az **Erőforráscsoport törlése** elemet. 
1. Írja be a  *myResourceGroup*   **nevet az erőforráscsoport neveként** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben létrehozott egy virtuális GÉPET egy virtuális hálózaton és egy keresési szolgáltatást egy privát végponttal. Az internetről csatlakozik a virtuális géphez, és biztonságosan kommunikál a keresési szolgáltatással a privát hivatkozás használatával. További információ a privát végpontról: [Mi az az Azure Private Endpoint?](../private-link/private-endpoint-overview.md).
