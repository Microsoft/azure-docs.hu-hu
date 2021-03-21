---
title: HTTP – HTTPS-átirányítás a portálon – Azure Application Gateway
description: Megtudhatja, hogyan hozhat létre olyan Application Gateway-t, amely átirányított forgalmat használ a HTTP-ről a HTTPS-re a Azure Portal használatával.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 67153fa750fee765dcaa1072eec87a2f6169b918
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397280"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>HTTP-alapú Application Gateway létrehozása a HTTPS-átirányítás Azure Portal használatával

A Azure Portal használatával létrehozhat egy [Application Gateway](overview.md) -tanúsítványt a TLS-leállításhoz. Az útválasztási szabályok a HTTP-forgalom átirányítására szolgálnak az Application Gateway HTTPS-portjára. Ebben a példában létrehozunk egy virtuálisgép- [méretezési készletet](../virtual-machine-scale-sets/overview.md) is az Application Gateway két virtuálisgép-példányát tartalmazó, a backend-készlethez.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

* Önaláírt tanúsítvány létrehozása
* Hálózat beállítása
* Alkalmazásátjáró létrehozása a tanúsítvánnyal
* Figyelő és átirányítási szabály hozzáadása
* Virtuálisgép-méretezési csoport létrehozása az alapértelmezett háttérkészlettel

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ehhez az oktatóanyaghoz a Azure PowerShell modul 1.0.0-as vagy újabb verziójára van szükség a tanúsítvány létrehozásához és az IIS telepítéséhez. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Az oktatóanyagban szereplő parancsok futtatásához futtatnia kell a parancsot az `Login-AzAccount` Azure-beli kapcsolatok létrehozásához is.

## <a name="create-a-self-signed-certificate"></a>Önaláírt tanúsítvány létrehozása

Éles használatra a megbízható szolgáltató által aláírt érvényes tanúsítványt kell importálnia. Ebben az oktatóanyagban egy önaláírt tanúsítványt hoz létre a [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) parancsmaggal. Az [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) parancsmagot a visszaadott ujjlenyomattal futtatva egy PFX-fájlt exportálhat a tanúsítványból.

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
6. Kattintson **a virtuális hálózat kiválasztása** elemre, kattintson az **új létrehozása** elemre, majd adja meg a virtuális hálózat következő értékeit:

   - A virtuális hálózat neve *myVNet*.
   - A virtuális hálózat címtere *10.0.0.0/16*.
   - Az alhálózat neve *myAGSubnet*.
   - Az alhálózat címtere *10.0.0.0/24*.

     ![Virtuális hálózat létrehozása](./media/create-url-route-portal/application-gateway-vnet.png)

7. A virtuális hálózat és az alhálózat létrehozásához kattintson az **OK** gombra.
8. Az előtérbeli **IP-konfiguráció** területen győződjön meg arról, hogy az **IP-cím típusa** **nyilvános**, és az **új létrehozása** elem ki van választva. Adja meg a *myAGPublicIPAddress* nevet. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd kattintson az **OK** gombra.
9. A **figyelő konfigurációja** területen válassza a **https** lehetőséget, majd válassza ki **a kívánt fájlt** , és keresse meg a *c:\appgwcert.pfx* fájlt, és válassza a **Megnyitás** lehetőséget.
10. Írja be a *appgwcert* nevet a tanúsítvány neveként és a *Azure123456!* jelszót.
11. Hagyja letiltani a webalkalmazási tűzfalat, majd kattintson **az OK gombra**.
12. Tekintse át a beállításokat az összefoglalás lapon, majd kattintson az **OK** gombra a hálózati erőforrások és az Application Gateway létrehozásához. Az Application Gateway létrehozása több percet is igénybe vehet, várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszra.

### <a name="add-a-subnet"></a>Alhálózat hozzáadása

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd az erőforrások listából válassza a **myVNet** lehetőséget.
2. Válassza az **alhálózatok** lehetőséget, majd kattintson az **alhálózat** elemre.

    ![Alhálózat létrehozása](./media/create-url-route-portal/application-gateway-subnet.png)

3. Az alhálózat neveként írja be a *myBackendSubnet* nevet.
4. Írja be a *10.0.2.0/24* értéket a címtartomány mezőbe, majd kattintson **az OK gombra**.

## <a name="add-a-listener-and-redirection-rule"></a>Figyelő és átirányítási szabály hozzáadása

### <a name="add-the-listener"></a>A figyelő hozzáadása

Először adja hozzá a *myListener* nevű figyelőt a 80-es porthoz.

1. Nyissa meg a **myResourceGroupAG** erőforráscsoportot, és válassza a **myAppGateway** lehetőséget.
2. Válassza a **figyelők** lehetőséget, majd válassza az **+ alapszintű** lehetőséget.
3. A név mezőbe írja be a *MyListener* nevet.
4. Írja be a *httpPort* nevet az új előtér-port neveként és a *80* -es portot a porthoz.
5. Győződjön meg arról, hogy a protokoll a **http** értékre van állítva, majd kattintson **az OK gombra**.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>Útválasztási szabály hozzáadása átirányítási konfigurációval

1. A **myAppGateway** területen válassza a **szabályok** , majd a **+ kérelem útválasztási szabály** lehetőséget.
2. A **szabály neve** mezőbe írja be a következőt: *Rule2*.
3. Győződjön meg arról, hogy a figyelő **MyListener** van kiválasztva.
4. Kattintson a **háttérbeli célok** lapra, és válassza a **cél típusa** *átirányítás* lehetőséget.
5. Az **átirányítás típusa** beállításnál válassza az **állandó** lehetőséget.
6. **Átirányítási cél** esetén válassza a **figyelő** lehetőséget.
7. Győződjön meg arról, hogy a **cél figyelő** **appGatewayHttpListener** van beállítva.
8. A **belefoglalási lekérdezési karakterlánc** és az **elérési út** beállításnál válassza az *Igen* lehetőséget.
9. Válassza a **Hozzáadás** lehetőséget.

## <a name="create-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport létrehozása

Ebben a példában egy olyan virtuálisgép-méretezési csoportot hoz létre, amely kiszolgálókat biztosít a háttérkészlet számára az alkalmazásátjáróban.

1. A portál bal felső sarkában válassza az **+ erőforrás létrehozása** lehetőséget.
2. Válassza ki **Számítás** (Compute) lehetőséget.
3. A keresőmezőbe írja be a *méretezési csoport* kifejezést, majd nyomja le az ENTER billentyűt.
4. Válassza ki a **virtuálisgép-méretezési csoport** elemet, majd kattintson a **Létrehozás** gombra.
5. A **virtuálisgép-méretezési csoport neve** mezőbe írja be a következőt: *myvmss*.
6. Operációs rendszer lemezképe esetén * * ellenőrizze, hogy a **Windows Server 2016 Datacenter** van-e kiválasztva.
7. Az **erőforráscsoport** területen válassza a **myResourceGroupAG** lehetőséget.
8. A **Felhasználónév** mezőbe írja be a következőt: *azureuser*.
9. A **jelszó** mezőbe írja be a következőt: *Azure123456!* és erősítse meg a jelszót.
10. A **Példányszám** mezőben ellenőrizze, hogy az érték **2**-e.
11. A **példány mérete** beállításnál válassza a **D2s_v3** lehetőséget.
12. A **hálózatkezelés** területen győződjön meg arról, hogy a terheléselosztási **beállítások** beállítás értéke **Application Gateway**.
13. Győződjön meg arról, hogy az **Application Gateway** **myAppGateway** van beállítva.
14. Győződjön meg arról, hogy az **alhálózat** **myBackendSubnet** értékre van beállítva.
15. Válassza a **Létrehozás** lehetőséget.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>A méretezési csoport hozzárendelése a megfelelő háttér-készlethez

A virtuálisgép-méretezési csoport portál felhasználói felülete létrehoz egy új háttér-készletet a méretezési csoport számára, de hozzá kívánja rendelni a meglévő appGatewayBackendPool.

1. Nyissa meg a **myResourceGroupAg** erőforráscsoportot.
2. Válassza a **myAppGateway** lehetőséget.
3. Válassza ki a **háttérbeli készletek** elemet.
4. Válassza a **myAppGatewaymyvmss** lehetőséget.
5. Válassza **a minden cél eltávolítása a háttér-készletből** lehetőséget.
6. Kattintson a **Mentés** gombra.
7. A folyamat befejezése után válassza ki a **myAppGatewaymyvmss** háttér-készletet, válassza a **Törlés** lehetőséget, majd kattintson az **OK gombra** a megerősítéshez.
8. Válassza a **appGatewayBackendPool** lehetőséget.
9. A **célok** területen válassza a **VMSS** lehetőséget.
10. A **VMSS** területen válassza a **myvmss** elemet.
11. A **hálózati adapter konfigurációja** területen válassza a **myvmssNic** lehetőséget.
12. Kattintson a **Mentés** gombra.

### <a name="upgrade-the-scale-set"></a>A méretezési csoport frissítése

Végül frissítenie kell a méretezési csoport ezeket a módosításokat.

1. Válassza ki a **myvmss** méretezési csoportját.
2. A **Beállítások** alatt válassza a **Példányok** lehetőséget.
3. Jelölje ki mindkét példányt, majd válassza a **frissítés** lehetőséget.
4. Válassza az **Igen** lehetőséget a megerősítéshez.
5. Ezt követően térjen vissza a **myAppGateway** , és válassza a **háttérbeli készletek** elemet. Ekkor látnia kell, hogy a **appGatewayBackendPool** két célponttal rendelkezik, és a  **myAppGatewaymyvmss** nulla célponttal rendelkezik.
6. Válassza a **myAppGatewaymyvmss** lehetőséget, majd válassza a **Törlés** lehetőséget.
7. Válassza az **OK** lehetőséget a megerősítéshez.

### <a name="install-iis"></a>Az IIS telepítése

Az IIS a méretezési csoportba való telepítésének egyszerű módja a PowerShell használata. A portálon kattintson a Cloud Shell ikonra, és ellenőrizze, hogy a **PowerShell** van-e kiválasztva.

Illessze be a következő kódot a PowerShell-ablakba, majd nyomja le az ENTER billentyűt.

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

### <a name="upgrade-the-scale-set"></a>A méretezési csoport frissítése

Miután módosította a példányokat az IIS-sel, újra kell frissítenie a méretezési készletet ezzel a módosítással.

1. Válassza ki a **myvmss** méretezési csoportját.
2. A **Beállítások** alatt válassza a **Példányok** lehetőséget.
3. Jelölje ki mindkét példányt, majd válassza a **frissítés** lehetőséget.
4. Válassza az **Igen** lehetőséget a megerősítéshez.

## <a name="test-the-application-gateway"></a>Az alkalmazásátjáró tesztelése

Az alkalmazás nyilvános IP-címét az Application Gateway – áttekintés oldalon érheti el.

1. Válassza a **myAppGateway** lehetőséget.
2. Az **Áttekintés** lapon jegyezze fel az IP-címet az előtér **nyilvános IP-címe** területen.

3. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Például: http://52.170.203.149

   ![Biztonsági figyelmeztetés](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. Ha önaláírt tanúsítványt használt, a biztonsági figyelmeztetés elfogadásához válassza a **Részletek** lehetőséget, majd válassza a **Továbblépés a weblapra** lehetőséget. Ekkor a biztonságos IIS-webhely a következő példához hasonlóan jelenik meg:

   ![Az alap URL-cím tesztelése az alkalmazásátjáróban](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre egy belső átirányítással rendelkező Application Gateway-átjárót](redirect-internal-site-powershell.md).