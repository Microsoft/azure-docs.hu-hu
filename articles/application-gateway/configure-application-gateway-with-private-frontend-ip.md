---
title: Belső terheléselosztó (ILB) végpontjának konfigurálása
titleSuffix: Azure Application Gateway
description: Ez a cikk tájékoztatást nyújt arról, hogyan konfigurálható a Application Gateway privát előtérbeli IP-címmel
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: 224cbe1e34e5915a7fa5fc1cf415c35f86c3abe4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711654"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Application Gateway konfigurálása belső terheléselosztó (ILB) végponttal

Az Azure Application Gateway egy internetre irányuló VIP-sel vagy egy belső végponttal konfigurálható, amely nem érhető el az interneten. A belső végpontok magánhálózati IP-címet használnak a előtér számára, amely *belső terheléselosztó (ILB) végpontként* is ismert.

Az átjáró saját előtér-magánhálózati IP-címmel való konfigurálása olyan belső üzletági alkalmazások esetében hasznos, amelyek nem érhetők el az interneten. A többrétegű alkalmazásokban olyan szolgáltatásokhoz és szintekhez is hasznos, amelyek olyan biztonsági határon belül vannak, amely nem érhető el az interneten, hanem:

- továbbra is szükség van a ciklikus multiplexelés terhelésének elosztására
- munkamenetek – stickiy
- vagy Transport Layer Security (TLS) megszakítás (korábbi nevén SSL (SSL)).

Ez a cikk végigvezeti az Application Gateway előtér-magánhálózati IP-címmel történő konfigurálásának lépésein a Azure Portal használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Ahhoz, hogy az Azure kommunikáljon a létrehozott erőforrások között, szüksége van egy virtuális hálózatra. Hozzon létre egy új virtuális hálózatot, vagy használjon egy meglévőt. 

Ebben a példában egy új virtuális hálózatot hozunk létre. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat. Application Gateway példányok külön alhálózatokban jönnek létre. Ebben a példában két alhálózat található: egyet az Application Gateway számára, és egy másikat a háttér-kiszolgálók számára.

1. Bontsa ki a portál menüt, majd válassza az **erőforrás létrehozása** lehetőséget.
2. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
3. Adja meg a *myAppGateway* nevet az Application Gateway és az új erőforráscsoport *myResourceGroupAG* nevéhez.
4. A régió területen válassza az **USA középső** **régiója** lehetőséget.
5. A **szint** mezőben válassza a **standard** lehetőséget.
6. A **virtuális hálózat konfigurálása** területen válassza az **új létrehozása** lehetőséget, majd adja meg a virtuális hálózat következő értékeit:
   - A virtuális hálózat neve *myVNet*.
   - A virtuális hálózat címtere *10.0.0.0/16*.
   - Az alhálózat neve *myAGSubnet*.
   - Az alhálózat címtere *10.0.0.0/24*.
   - *myBackendSubnet* – a háttér alhálózatának neveként.
   - *10.0.1.0/24* – a háttérbeli alhálózat címterület számára.

    ![Virtuális hálózat létrehozása](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. A virtuális hálózat és az alhálózatok létrehozásához kattintson **az OK gombra** .
7. Válassza a **Next (tovább): frontends** elemet.
8. Az előtérbeli **IP-cím típusa** beállításnál válassza a **magánjellegű** lehetőséget.

   Alapértelmezés szerint ez egy dinamikus IP-cím-hozzárendelés. A konfigurált alhálózat első elérhető címe az előtér-IP-címként van hozzárendelve.
   > [!NOTE]
   > A lefoglalt IP-cím típusa (statikus vagy dinamikus) később nem módosítható.
9. Válassza a Next (tovább) lehetőséget **: háttérrendszer**.
10. Válassza **a háttérbeli készlet hozzáadása** lehetőséget.
11. A **név** mezőbe írja be a következőt: *appGatewayBackendPool*.
12. Ha a **háttér-készletet célok nélkül** kívánja hozzáadni, válassza az **Igen** lehetőséget. Később hozzáadja a célokat.
13. Válassza a **Hozzáadás** lehetőséget.
14. Válassza a **Tovább: konfigurálás** lehetőséget.
15. Az **útválasztási szabályok** területen válassza **az útválasztási szabály hozzáadása** elemet.
16. A **szabály neve** mezőbe írja be a következőt: *Rrule-01*.
17. A **figyelő neve** mezőbe írja be a következőt: *Listener-01*.
18. Előtér **-IP** esetén válassza a **magánjellegű** lehetőséget.
19. Fogadja el a fennmaradó alapértékeket, és válassza a **háttérbeli célok** fület.
20. A **cél típusa** beállításnál válassza a **háttér-készlet** lehetőséget, majd válassza a **appGatewayBackendPool** lehetőséget.
21. **Http-beállítás** esetén válassza az **új hozzáadása** elemet.
22. A **http-beállítás neve** mezőbe írja be a következőt: *http-Setting-01*.
23. A **háttérrendszer protokoll** esetében válassza a **http** lehetőséget.
24. A **backend port** esetében írja be a *80* értéket.
25. Fogadja el a fennmaradó alapértékeket, és válassza a **Hozzáadás** lehetőséget.
26. Az **útválasztási szabály hozzáadása** lapon válassza a **Hozzáadás** lehetőséget.
27. Kattintson a **Tovább gombra: címkék**.
28. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre.
29. Tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás** lehetőséget a hálózati erőforrások és az Application Gateway létrehozásához. Az alkalmazásátjáró létrehozása több percig is eltarthat. Várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.

## <a name="add-backend-pool"></a>Háttér-készlet hozzáadása

A háttér-készlet arra szolgál, hogy a kérelmeket a kérést kiszolgáló háttér-kiszolgálókra irányítsa. A háttérrendszer a hálózati adapterek, a virtuálisgép-méretezési csoportok, a nyilvános IP-címek, a belső IP-címek, a teljes tartománynevek (FQDN) és a több-bérlős háttér-végpontok, például a Azure App Service tagjai lehetnek. Ebben a példában a virtuális gépeket célként használt háttérként használjuk. Használhat meglévő virtuális gépeket, vagy újakat is létrehozhat. Ebben a példában két virtuális gépet hoz létre, amelyeket az Azure háttér-kiszolgálóként használ az Application Gateway számára.

Ehhez tegye a következőket:

1. Hozzon létre két új, háttér-kiszolgálóként használt virtuális gépet, *myVM* és *myVM2*.
2. Telepítse az IIS-t a virtuális gépeken annak ellenőrzéséhez, hogy az Application Gateway sikeresen létrejött-e.
3. Adja hozzá a háttér-kiszolgálókat a háttér-készlethez.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása


1. Válassza az **Erőforrás létrehozása** lehetőséget.
2. Válassza a **számítás** , majd a **virtuális gép** lehetőséget.
4. Adja meg a következő értékeket a virtuális gép számára:
   - Válassza ki előfizetését.
   - Válassza  az myResourceGroupAG lehetőséget.
   - Írja be a *myVM* **nevet a virtuális géphez**.
   - A **rendszerképhez** válassza a **Windows Server 2019 Datacenter** lehetőséget.
   - Írjon be egy érvényes **felhasználónevet**.
   - Írjon be egy érvényes **jelszót**.
1. Fogadja el a fennmaradó alapértékeket, és válassza a **Tovább: lemezek** lehetőséget.
1. Fogadja el az alapértelmezett értékeket, és válassza a **Tovább: hálózatkezelés** lehetőséget.
1. Győződjön meg arról, hogy a virtuális hálózat **myVNet** van kiválasztva, és az alhálózat **myBackendSubnet**.
1. Fogadja el a fennmaradó alapértékeket, és válassza a **Tovább: kezelés** lehetőséget.
1. A rendszerindítási diagnosztika letiltásához válassza a **Letiltás** lehetőséget.
1. Válassza az **Áttekintés + létrehozás** lehetőséget.
1. Tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás** lehetőséget. A virtuális gép létrehozása több percet is igénybe vehet. Várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.

### <a name="install-iis"></a>Az IIS telepítése

1. Nyissa meg a Cloud Shellt, és győződjön meg arról, hogy a **PowerShell**-re van beállítva.
    ![A képernyőképen a PowerShellt használó nyitott Azure Cloud Shell konzolablak látható.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Futtassa a következő parancsot az IIS a virtuális gépen való telepítéséhez:

   ```azurepowershell
   Set-AzVMExtension `
        -ResourceGroupName myResourceGroupAG `
        -ExtensionName IIS `
        -VMName myVM `
        -Publisher Microsoft.Compute `
        -ExtensionType CustomScriptExtension `
        -TypeHandlerVersion 1.4 `
        -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
         -Location CentralUS 

   ```

3. Hozzon létre egy második virtuális gépet, és telepítse az IIS-t az imént befejezett lépésekkel. Használja a myVM2 a virtuális gép nevéhez és a `VMName` -ben `Set-AzVMExtension` .

### <a name="add-backend-servers-to-backend-pool"></a>Háttér-kiszolgálók hozzáadása a háttérbeli készlethez

1. Válassza a **minden erőforrás** lehetőséget, majd válassza a **myAppGateway** lehetőséget.
2. Válassza ki a **háttérbeli készletek** elemet, majd válassza a **appGatewayBackendPool** lehetőséget.
3. A **cél típusa** területen válassza a **virtuális gép**  lehetőséget, és a **cél** területen válassza ki a myVM társított vNIC.
4. MyVM2 hozzáadásához ismételje meg a műveletet.
   ![A háttérbeli készlet ablaktábla szerkesztése a célcsoportok és a célok kiemelésével.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. Válassza a **Mentés lehetőséget.**

## <a name="create-a-client-virtual-machine"></a>Ügyfél virtuális gép létrehozása

Az ügyfél virtuális gépe az Application Gateway backend-készlethez való kapcsolódásra szolgál.

- Hozzon létre egy harmadik virtuális gépet az előző lépések alapján. Használja a myVM3 a virtuális gép nevéhez.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

1. A myAppGateway lapon válassza ki a előtér **IP-konfigurációi** elemet, és jegyezze fel a belső felület magánhálózati IP-címét.
    ![Az előtérbeli IP-konfigurációk panel a Kiemelt magánjellegű típussal.](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Másolja ki a magánhálózati IP-címet, majd illessze be a myVM3 böngésző címsorába az Application Gateway backend-készletének eléréséhez.

## <a name="next-steps"></a>Következő lépések

Ha figyelni szeretné a háttér-készlet állapotát, tekintse meg a [háttér állapotáról és a diagnosztikai naplókat a Application Gateway](application-gateway-diagnostics.md).
