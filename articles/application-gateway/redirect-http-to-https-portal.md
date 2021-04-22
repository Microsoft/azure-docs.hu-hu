---
title: HTTP–HTTPS átirányítás a portálon – Azure Application Gateway
description: Megtudhatja, hogyan hozhat létre a HTTP-ről HTTPS-re átirányított forgalmat használó Alkalmazásátjárót a Azure Portal.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: e7fb40d95c4659bf353366770da7c903ffa1bd09
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107867222"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>HTTP–HTTPS átirányítást használó Application Gateway létrehozása a Azure Portal

A TLS Azure Portal lejáró tanúsítvánnyal való létrehozásához használhatja a tanúsítványt. [](overview.md) Az útválasztási szabályok a HTTP-forgalom átirányításához használhatók az alkalmazásátjáró HTTPS-portjára. Ebben a példában egy [](../virtual-machine-scale-sets/overview.md) virtuálisgép-méretezési csoport is létre lesz hozva az alkalmazásátjáró háttérkészlete számára, amely két virtuálisgép-példányt tartalmaz.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Önaláírt tanúsítvány létrehozása
* Hálózat beállítása
* Alkalmazásátjáró létrehozása a tanúsítvánnyal
* Figyelési és átirányítási szabály hozzáadása
* Virtuálisgép-méretezési csoport létrehozása az alapértelmezett háttérkészlettel

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyaghoz az Azure PowerShell 1.0.0-s vagy újabb verziójára lesz szükség a tanúsítvány létrehozásához és az IIS telepítéséhez. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Az oktatóanyagban használt parancsok futtatásához a parancsot is futtatnia kell az Azure-ral `Login-AzAccount` való kapcsolat létrehozásához.

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Éles környezetben importálni kell egy megbízható szolgáltató által aláírt érvényes tanúsítványt. Ebben az oktatóanyagban egy önaláírt tanúsítványt hoz létre a [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) parancsmaggal. Az [Export-PfxCertificate](/powershell/module/pki/export-pfxcertificate) parancsmagot a visszaadott ujjlenyomattal futtatva egy PFX-fájlt exportálhat a tanúsítványból.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Ennek nagyjából a következőképpen kell kinéznie:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

A PFX-fájl létrehozása az ujjlenyomattal:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

A létrehozott erőforrások közötti kommunikációhoz virtuális hálózatra van szükség. Ebben a példában két alhálózatot hozunk létre: egyet az alkalmazásátjáróhoz, egy másikat pedig a háttérkiszolgálókhoz. Virtuális hálózatot az alkalmazásátjáróval együtt is létrehozhat.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Az Azure Portal bal felső sarkában kattintson az **Erőforrás létrehozása** gombra.
3. Válassza a **Hálózatkezelés**, majd az **Application Gateway** elemet a Kiemeltek listából.
4. Adja meg a következő értékeket az alkalmazásátjáróhoz:

   - Az alkalmazásátjáró neve *myAppGateway*.
   - Az új erőforráscsoport *myResourceGroupAG*.

     ![Új alkalmazásátjáró létrehozása](./media/create-url-route-portal/application-gateway-create.png)

5. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
6. Kattintson **a Virtuális hálózat kiválasztása elemre,** kattintson az Új **létrehozása** elemre, majd adja meg a következő értékeket a virtuális hálózathoz:

   - A virtuális hálózat neve *myVNet*.
   - A virtuális hálózat címtere *10.0.0.0/16*.
   - Az alhálózat neve *myAGSubnet*.
   - Az alhálózat címtere *10.0.0.0/24*.

     ![Virtuális hálózat létrehozása](./media/create-url-route-portal/application-gateway-vnet.png)

7. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
8. Az **Előtere IP-konfigurációja alatt** győződjön meg arról, hogy **az IP-cím típusa** **Nyilvános,** és az **Új létrehozása** elem van kiválasztva. A névhez adja meg a *myAGPublicIPAddress* nevet. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
9. A **Listener configuration (Figyelő konfigurációja)** alatt válassza a **HTTPS** lehetőséget, majd válassza a Select a file (Fájl kiválasztása) lehetőséget, keresse meg a *c:\appgwcert.pfx* fájlt, és válassza **a** Open (Megnyitás) **lehetőséget.**
10. Írja *be az appgwcert* nevet a tanúsítvány neveként, *és az Azure123456!* jelszót.
11. Hagyja letiltva a webalkalmazási tűzfalat, majd kattintson az **OK gombra.**
12. Tekintse át a beállításokat az összefoglaló lapon, majd kattintson az **OK gombra** a hálózati erőforrások és az alkalmazásátjáró létrehozásához. Az Application Gateway létrehozása több percig is eltarthat, amíg az üzembe helyezés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. Válassza **a Minden erőforrás** elemet a bal oldali menüben, majd válassza ki a **myVNet elemet** az erőforrások listájából.
2. Válassza **az Alhálózatok** lehetőséget, majd kattintson az **Alhálózat elemre.**

    ![Alhálózat létrehozása](./media/create-url-route-portal/application-gateway-subnet.png)

3. Az alhálózat nevének adja a *myBackendSubnet* nevet.
4. A címtartományhoz írja be a *10.0.2.0/24* címet, majd válassza az **OK gombot.**

## <a name="add-a-listener-and-redirection-rule"></a>Figyelési és átirányítási szabály hozzáadása

### <a name="add-the-listener"></a>A listener hozzáadása

Először adja hozzá a *myListener* nevű listenert a 80-as porthoz.

1. Nyissa meg **a myResourceGroupAG** erőforráscsoportot, és válassza a **myAppGateway lehetőséget.**
2. Válassza **a Figyelők,** majd az **+ Alapszintű lehetőséget.**
3. A névnek adja a *MyListener* nevet.
4. Írja *be a httpPort* nevet az új előtereport neveként, és *a 80-as* portot.
5. Győződjön meg arról, hogy a protokoll **BEÁLLÍTÁSA HTTP,** majd kattintson az **OK gombra.**

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Útválasztási szabály hozzáadása átirányítási konfigurációval

1. A **myAppGateway átjárón** válassza a **Szabályok,** majd a **+Kérelem útválasztási szabálya lehetőséget.**
2. A Szabály **neve mezőbe írja be a** *Következőt: Rule2.*
3. Ellenőrizze, **hogy a MyListener** van-e kiválasztva a figyelő számára.
4. Kattintson a **Háttércélok fülre,** és válassza a **Cél típusa lehetőséget** *Átirányításként.*
5. Az **Átirányítás típusaként válassza a** Állandó **lehetőséget.**
6. Az **Átirányítási cél mezőben válassza** a **Figyelés lehetőséget.**
7. Győződjön meg **arról, hogy a Cél listener** beállítása **appGatewayHttpListener.**
8. A Lekérdezési **sztring és** az Include path (Lekérdezési sztring is) és **az Include path (Elérési út is) beállításhoz** válassza az *Igen lehetőséget.*
9. Válassza a **Hozzáadás** lehetőséget.

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy olyan virtuálisgép-méretezési csoportot hoz létre, amely kiszolgálókat biztosít a háttérkészlet számára az alkalmazásátjáróban.

1. A portál bal felső sarkában válassza az **+Erőforrás létrehozása lehetőséget.**
2. Válassza ki **Számítás** (Compute) lehetőséget.
3. A keresőmezőbe írja be a *méretezési készletet, majd* nyomja le az Enter billentyűt.
4. Válassza **a Virtuálisgép-méretezési csoport,** majd a **Létrehozás lehetőséget.**
5. A **Virtuálisgép-méretezésicsoport neve mezőbe** írja be a *myvmss nevet.*
6. Operációsrendszer-lemezkép esetén** ellenőrizze, hogy a **Windows Server 2016 Datacenter van-e** kiválasztva.
7. Az **Erőforráscsoport mezőben** válassza a **myResourceGroupAG lehetőséget.**
8. A **Felhasználónév mezőbe írja be** az *azureuser nevet.*
9. A **Password (Jelszó)** mezőbe írja be a *következőt: Azure123456!* és erősítse meg a jelszót.
10. A **Példányszám mezőben** győződjön meg arról, hogy az érték **2**.
11. A **Példányméret mezőben** válassza a **D2s_v3.**
12. A **Hálózat alatt győződjön meg arról,** hogy a **Terheléselosztási beállítások kiválasztása** a Application Gateway. 
13. Győződjön meg **arról, hogy** az Application Gateway beállítása **myAppGateway.**
14. Győződjön **meg arról, hogy** az Alhálózat beállítása **myBackendSubnet.**
15. Válassza a **Létrehozás** lehetőséget.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>A méretezési készlet társítása a megfelelő háttérkészlethez

A virtuálisgép-méretezési készlet portáljának felhasználói felülete létrehoz egy új háttérkészletet a méretezési készlethez, de azt a meglévő appGatewayBackendPool készlethez szeretné társítani.

1. Nyissa meg **a myResourceGroupAg** erőforráscsoportot.
2. Válassza **a myAppGateway lehetőséget.**
3. Válassza **a Háttérkészletek lehetőséget.**
4. Válassza **ki a myAppGatewaymyvmss et.**
5. Válassza **az Összes cél eltávolítása a háttérkészletből lehetőséget.**
6. Kattintson a **Mentés** gombra.
7. A folyamat befejezése után válassza ki a **myAppGatewaymyvmss** háttérkészletet, válassza a **Törlés,** majd az **OK** lehetőséget a megerősítéshez.
8. Válassza **az appGatewayBackendPool et.**
9. A **Célok területen** válassza a **VMSS lehetőséget.**
10. A **VMSS területen** válassza a **myvmss et.**
11. A **Hálózati adapter konfigurációi alatt válassza** a **myvmssNic lehetőséget.**
12. Kattintson a **Mentés** gombra.

### <a name="upgrade-the-scale-set"></a>A méretezési készlet frissítése

Végül frissítenie kell a méretezési készletet ezekkel a módosításokkal.

1. Válassza ki **a myvmss méretezési** készletet.
2. A **Beállítások** alatt válassza a **Példányok** lehetőséget.
3. Jelölje ki mindkét példányt, majd válassza a Frissítés **lehetőséget.**
4. Válassza az **Igen** lehetőséget a megerősítéshez.
5. Miután ez befejeződött, vissza a **myAppGatewaybe,** és válassza a **Háttérkészletek lehetőséget.** Most azt kell látnia, hogy az **appGatewayBackendPool** két célból áll, a  **myAppGatewaymyvmss pedig** nulla célból áll.
6. Válassza **a myAppGatewaymyvmss** et, majd a **Törlés lehetőséget.**
7. Válassza az **OK** lehetőséget a megerősítéshez.

### <a name="install-iis"></a>Az IIS telepítése

Az IIS méretezési készletre való telepítésének egyszerű módja a PowerShell használata. A portálon kattintson a Cloud Shell ikonra, és győződjön meg arról, hogy a **PowerShell van** kiválasztva.

Illessze be az alábbi kódot a PowerShell ablakába, majd nyomja le az Enter billentyűt.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>A méretezési készlet frissítése

Miután az IIS-sel módosítja a példányokat, ezzel a módosítással ismét frissítenie kell a méretezési készletet.

1. Válassza ki **a myvmss méretezési** készletet.
2. A **Beállítások** alatt válassza a **Példányok** lehetőséget.
3. Jelölje ki mindkét példányt, majd válassza a Frissítés **lehetőséget.**
4. Válassza az **Igen** lehetőséget a megerősítéshez.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazás nyilvános IP-címét az alkalmazásátjáró Áttekintés lapján olvashatja be.

1. Válassza **a myAppGateway lehetőséget.**
2. Az Áttekintés **lapon** jegyezze fel az IP-címet az **Előtere nyilvános IP-címe alatt.**

3. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például: http://52.170.203.149

   ![Biztonsági figyelmeztetés](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Ha önaláírt tanúsítványt használt, a biztonsági figyelmeztetés elfogadásához válassza a **Részletek** lehetőséget, majd válassza a **Továbblépés a weblapra** lehetőséget. Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

   ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Következő lépések

Ismerje meg, [hogyan hozhat létre alkalmazásátjárót belső átirányítással.](redirect-internal-site-powershell.md)
