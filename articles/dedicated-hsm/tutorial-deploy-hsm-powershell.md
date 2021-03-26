---
title: Oktatóanyag üzembe helyezése meglévő virtuális hálózatban a PowerShell használatával – Azure dedikált HSM | Microsoft Docs
description: Oktatóanyag, amely bemutatja, hogyan helyezhet üzembe egy dedikált HSM-t egy meglévő virtuális hálózatban a PowerShell használatával
services: dedicated-hsm
documentationcenter: na
author: msmbaldwin
manager: rkarlin
editor: ''
ms.service: key-vault
ms.topic: tutorial
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 5ed5ac90f446f74c54488f6d0cf23adbd63a3e1e
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606878"
---
# <a name="tutorial--deploying-hsms-into-an-existing-virtual-network-using-powershell"></a>Oktatóanyag – a HSM üzembe helyezése meglévő virtuális hálózaton a PowerShell használatával

Az Azure dedikált HSM szolgáltatás egy fizikai eszközt biztosít az ügyfelek kizárólagos használatára, teljes körű felügyeleti felügyelettel és teljes körű felügyelettel. A fizikai hardverek biztosítása miatt a Microsoftnak meg kell határoznia, hogy az eszközök hogyan legyenek kiosztva a kapacitás hatékony kezelése érdekében. Ennek eredményeképpen az Azure-előfizetésen belül a dedikált HSM szolgáltatás általában nem jelenik meg az erőforrás-kiépítés során. A dedikált HSM szolgáltatáshoz hozzáférést igénylő Azure-ügyfeleknek először kapcsolatba kell lépniük a Microsoft-fiók Executive-vel, hogy a dedikált HSM szolgáltatás regisztrációját kérhetik. Csak a folyamat sikeres befejezését követően lehet kiépíteni.
Ez az oktatóanyag egy tipikus kiépítési folyamat megjelenítését célozza, ahol:

- Az ügyfél már rendelkezik egy virtuális hálózattal
- Virtuális géppel rendelkeznek
- A meglévő környezetbe hozzá kell adni a HSM-erőforrásokat.

Egy tipikus, magas rendelkezésre állású, több régióból álló üzembe helyezési architektúra a következőképpen nézhet ki:

![többrégiós üzembe helyezés](media/tutorial-deploy-hsm-powershell/high-availability.png)

Ez az oktatóanyag egy HSM és a szükséges ExpressRoute-átjáróra koncentrál (lásd a fenti 1. alhálózatot) egy meglévő virtuális hálózathoz integrálva (lásd a fenti 1. VNET).  Minden más erőforrás szabványos Azure-erőforrások. Ugyanez az integrációs folyamat a 4. alhálózatban található HSM is használható a fenti 3. VNET.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az Azure dedikált HSM jelenleg nem érhető el a Azure Portalban, ezért a szolgáltatással folytatott minden interakció parancssori vagy a PowerShell használatával történik. Ez az oktatóanyag a PowerShellt fogja használni a Azure Cloud Shellban. Ha még csak most ismerkedik a PowerShell-lel, kövesse az első lépéseket ismertető útmutatót itt: [Azure PowerShell](/powershell/azure/get-started-azureps)az első lépéseket.

Előfeltételek:

- Ön már az Azure dedikált HSM regisztrációs folyamatán keresztül hagyta jóvá a szolgáltatás használatát. Ha nem, akkor a részletekért forduljon a Microsoft-fiók képviselőjéhez. 
- Létrehozott egy erőforráscsoportot ezekhez az erőforrásokhoz, és az ebben az oktatóanyagban üzembe helyezett új eszközök csatlakozni fognak ehhez a csoporthoz.
- Már létrehozta a szükséges virtuális hálózatot, alhálózatot és virtuális gépeket a fenti ábrán látható módon, és most integrálni szeretné a 2 HSM az adott üzembe helyezésbe.

Az alábbi utasítások feltételezik, hogy már elindította a Azure Portal, és megnyitotta a Cloud Shell (a portál jobb felső részén válassza a " \> \_ " lehetőséget).

## <a name="provisioning-a-dedicated-hsm"></a>Dedikált HSM kiépítés

A HSM és az integrálása egy meglévő virtuális hálózatba az ExpressRoute-átjárón keresztül az SSH parancssori eszköz használatával lesz érvényesítve, így biztosítva a HSM-eszköz elérhetőségét és alapszintű rendelkezésre állását minden további konfigurációs tevékenységhez. A következő parancsok egy Resource Manager-sablont használnak a HSM-erőforrások és a társított hálózati erőforrások létrehozásához.

### <a name="validating-feature-registration"></a>Szolgáltatás regisztrációjának ellenőrzése

A fentiekben leírtaknak megfelelően a kiépítési tevékenységekhez a dedikált HSM szolgáltatás regisztrálása szükséges az előfizetéséhez. Ennek ellenőrzéséhez futtassa a következő PowerShell-parancsot a Azure Portal Cloud shellben. 

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.HardwareSecurityModules -FeatureName AzureDedicatedHsm
```

A parancsnak "regisztrált" állapotot kell visszaadnia, mielőtt továbblépne.  Ha nincs regisztrálva erre a szolgáltatásra, forduljon a Microsoft-fiók képviselőjéhez.

![előfizetés állapota](media/tutorial-deploy-hsm-powershell/subscription-status.png)

### <a name="creating-hsm-resources"></a>HSM-erőforrások létrehozása

A HSM-eszközök egy ügyfél virtuális hálózatán vannak kiépítve. Ez egy alhálózat követelményét jelenti. A HSM-függőség, amely lehetővé teszi a virtuális hálózat és a fizikai eszköz közötti kommunikációt, egy ExpressRoute-átjáró, és végül egy virtuális gépnek kell hozzáférnie a HSM-eszközhöz a Thales-ügyfélszoftver használatával. Ezeket az erőforrásokat a rendszer egy, a megfelelő paramétert tartalmazó fájlba gyűjtötte a könnyű használat érdekében. A fájlok elérhetőségét közvetlenül a Microsofthoz fordulva érheti el HSMrequest@Microsoft.com .

Miután megtörtént a fájlok használata, szerkesztenie kell a paramétert, hogy beszúrja az erőforrások előnyben részesített nevét. Ez azt jelenti, hogy a sorokat "value": "" értékkel kell szerkeszteni.

- `namingInfix` HSM-erőforrások nevének előtagja
- `ExistingVirtualNetworkName` A HSM használt virtuális hálózat neve
- `DedicatedHsmResourceName1` HSM-erőforrás neve az adatközpont Stamp 1
- `DedicatedHsmResourceName2` HSM-erőforrás neve az adatközpont Stamp 2
- `hsmSubnetRange` Alhálózat IP-címtartomány a HSM
- `ERSubnetRange` Alhálózat IP-címtartomány a VNET-átjáróhoz

Ilyen változások például a következők:

```json
{
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "namingInfix": {
      "value": "MyHSM"
    },
    "ExistingVirtualNetworkName": {
      "value": "MyHSM-vnet"
    },
    "DedicatedHsmResourceName1": {
      "value": "HSM1"
    },
    "DedicatedHsmResourceName2": {
      "value": "HSM2"
    },
    "hsmSubnetRange": {
      "value": "10.0.2.0/24"
    },
    "ERSubnetRange": {
      "value": "10.0.255.0/26"
    },
  }
}
```

A kapcsolódó Resource Manager-sablonfájl 6 erőforrást hoz létre a következő információkkal:

- A megadott VNET lévő HSM alhálózata
- A virtuális hálózati átjáró alhálózata 
- Egy virtuális hálózati átjáró, amely a VNET csatlakoztatja a HSM-eszközökhöz
- Az átjáróhoz tartozó nyilvános IP-cím
- Egy HSM az 1. stampben
- Egy HSM a 2. stampben

A paraméterek beállítása után a fájlokat fel kell tölteni Azure Portal Cloud Shell-fájlmegosztás használatára. A Azure Portal kattintson a " \> \_ " Cloud Shell Symbol jobb felső sarokban, így a képernyő alsó részén egy parancssori környezet jelenik meg. Az ehhez tartozó beállítások a BASH és a PowerShell, és ha még nincs beállítva, válassza a BASH lehetőséget.

A parancs rendszerhéjának feltöltési/letöltési lehetősége van az eszköztáron, és ezt a beállítást kell választania a sablon és a paraméter fájljainak a fájlmegosztás számára való feltöltéséhez:

![fájlmegosztás](media/tutorial-deploy-hsm-powershell/file-share.png)

A fájlok feltöltése után készen áll arra, hogy erőforrásokat hozzon létre.
Az új HSM-erőforrások létrehozása előtt meg kell győződnie néhány előfeltételt jelentő erőforrásról. A számítási, a HSM és az átjáróhoz tartozó alhálózati tartományokkal rendelkező virtuális hálózatnak kell lennie. Az alábbi parancsok példaként szolgálnak a virtuális hálózat létrehozásához.

```powershell
$compute = New-AzVirtualNetworkSubnetConfig `
  -Name compute `
  -AddressPrefix 10.2.0.0/24
```

```powershell
$delegation = New-AzDelegation `
  -Name "myDelegation" `
  -ServiceName "Microsoft.HardwareSecurityModules/dedicatedHSMs"

```

```powershell
$hsmsubnet = New-AzVirtualNetworkSubnetConfig ` 
  -Name hsmsubnet ` 
  -AddressPrefix 10.2.1.0/24 ` 
  -Delegation $delegation 

```

```powershell

$gwsubnet= New-AzVirtualNetworkSubnetConfig `
  -Name GatewaySubnet `
  -AddressPrefix 10.2.255.0/26

```

```powershell

New-AzVirtualNetwork `
  -Name myHSM-vnet `
  -ResourceGroupName myRG `
  -Location westus `
  -AddressPrefix 10.2.0.0/16 `
  -Subnet $compute, $hsmsubnet, $gwsubnet

```

>[!NOTE]
>A virtuális hálózatra vonatkozó legfontosabb konfiguráció, hogy a HSM-eszköz alhálózatának delegálással kell rendelkeznie a "Microsoft. HardwareSecurityModules/dedicatedHSMs" értékre.  A HSM-kiépítés nélkül nem fog működni.

Ha minden előfeltétel teljesült, futtassa a következő parancsot a Resource Manager-sablon használatához, hogy az egyedi nevekkel (legalább az erőforráscsoport nevével) frissítette az értékeket:

```powershell

New-AzResourceGroupDeployment -ResourceGroupName myRG `
     -TemplateFile .\Deploy-2HSM-toVNET-Template.json `
     -TemplateParameterFile .\Deploy-2HSM-toVNET-Params.json `
     -Name HSMdeploy -Verbose

```

A parancs végrehajtása körülbelül 20 percet vesz igénybe. Az "-Verbose" beállítással biztosítható, hogy az állapot folyamatosan jelenjen meg.

![kiépítési állapot](media/tutorial-deploy-hsm-powershell/progress-status.png)

Ha sikeresen befejeződött, a "provisioningState": "sikeres" üzenet jelenik meg, bejelentkezhet a meglévő virtuális gépre, és az SSH használatával biztosíthatja a HSM-eszköz rendelkezésre állását.

## <a name="verifying-the-deployment"></a>A központi telepítés ellenőrzése

Az eszközök kiépített állapotának ellenőrzéséhez és az eszköz attribútumainak megtekintéséhez futtassa a következő parancsot. Győződjön meg arról, hogy az erőforráscsoport megfelelően van beállítva, és az erőforrás neve pontosan megegyezik a paraméter fájljával.

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG"
$resourceName = "HSM1"  
Get-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName

```

![kiépítés állapota](media/tutorial-deploy-hsm-powershell/progress-status2.png)

Ekkor az [Azure Resource Explorer](https://resources.azure.com/)használatával is megtekintheti az erőforrásokat.   Egyszer a Explorerben bontsa ki az "előfizetések" elemet a bal oldalon, bontsa ki a dedikált HSM-hez tartozó előfizetést, bontsa ki az "erőforráscsoportok" csomópontot, bontsa ki a használt erőforráscsoportot, és végül válassza ki az "erőforrások" elemet.

## <a name="testing-the-deployment"></a>A központi telepítés tesztelése

Az üzembe helyezés tesztelése olyan virtuális géphez való csatlakozás, amely hozzáférhet a HSM-hez, majd közvetlenül a HSM-eszközhöz csatlakozik. Ezek a műveletek megerősítik, hogy a HSM elérhető.
Az SSH-eszköz a virtuális géphez való kapcsolódásra szolgál. A parancs a következőhöz hasonló lesz, de a paraméterben megadott rendszergazdai névvel és DNS-névvel.

`ssh adminuser@hsmlinuxvm.westus.cloudapp.azure.com`

A használandó jelszó a paraméter fájljának egyike.
Miután bejelentkezett a Linux rendszerű virtuális gépre, jelentkezzen be a HSM-be a portálon található privát IP-cím használatával a hsm_vnic erőforráshoz \<prefix> .

```powershell

(Get-AzResource -ResourceGroupName myRG -Name HSMdeploy -ExpandProperties).Properties.networkProfile.networkInterfaces.privateIpAddress

```
Ha rendelkezik az IP-címmel, futtassa a következő parancsot:

`ssh tenantadmin@<ip address of HSM>`

Ha a művelet sikeres, a rendszer jelszót kér. Az alapértelmezett jelszó jelszó. A HSM megkéri, hogy változtassa meg a jelszavát, hogy erős jelszót állítson be, és használja a szervezete által előnyben részesített mechanizmust a jelszó tárolásához és a veszteség elkerüléséhez.  

>[!IMPORTANT]
>ha elveszíti ezt a jelszót, a HSM alaphelyzetbe kell állítani, és ez a kulcs elvesztését jelenti.

Ha SSH-val csatlakozik a HSM-eszközhöz, futtassa a következő parancsot a HSM működésének biztosításához.

`hsm show`

A kimenetnek az alábbi képen láthatóhoz hasonlóan kell kinéznie:

![Képernyőkép, amely a HSM show parancs kimenetét jeleníti meg.](media/tutorial-deploy-hsm-powershell/output.png)

Ezen a ponton az összes erőforrást lefoglalta egy nagy rendelkezésre állású, két HSM üzembe helyezési és érvényesített hozzáférés és működési állapot számára. Minden további konfiguráció vagy tesztelés több munkát is magában foglal a HSM-eszközzel. Ehhez kövesse a Thales Luna 7 HSM felügyeleti útmutató 7. fejezetének utasításait a HSM inicializálásához és a partíciók létrehozásához. Az összes dokumentáció és szoftver közvetlenül a Thales érhető el, ha a [Thales ügyfélszolgálati portálon](https://supportportal.thalesgroup.com/csm) regisztrálva van, és rendelkezik ügyfél-azonosítóval. Töltse le az ügyfélszoftver 7,2-es verzióját az összes szükséges összetevő lekéréséhez.

## <a name="delete-or-clean-up-resources"></a>Erőforrások törlése vagy eltávolítása

Ha csak a HSM-eszközzel fejeződött be, akkor azt erőforrásként is törölheti, és visszaküldheti az ingyenes készletbe. Ha ezt a problémát tapasztalja, az eszközön található bizalmas vásárlói adatok is megtalálhatók. A legjobb módszer a "zeroize", hogy az eszköz a HSM-rendszergazda jelszavának rossz 3 alkalommal való beszerzését kéri (Megjegyzés: ez nem a berendezés rendszergazdája, hanem a tényleges HSM-rendszergazda). A kulcsfontosságú anyagok védelme érdekében az eszköz nem törölhető Azure-erőforrásként, amíg a nulla állapotba nem kerül.

> [!NOTE]
> Ha bármilyen Thales-eszköz konfigurációval kapcsolatos problémát tapasztal, vegye fel a kapcsolatot a [Thales ügyfélszolgálatával](https://supportportal.thalesgroup.com/csm).

Ha el szeretné távolítani a HSM-erőforrást az Azure-ban, a következő parancs használatával cserélje le a "$" változókat az egyedi paraméterekre:

```powershell

$subid = (Get-AzContext).Subscription.Id
$resourceGroupName = "myRG" 
$resourceName = "HSMdeploy"  
Remove-AzResource -Resourceid /subscriptions/$subId/resourceGroups/$resourceGroupName/providers/Microsoft.HardwareSecurityModules/dedicatedHSMs/$resourceName 

```

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag lépéseinek elvégzése után a dedikált HSM-erőforrások kiépítve és elérhetővé válnak a virtuális hálózaton. Most már olyan helyzetben van, hogy ezt az üzembe helyezést az előnyben részesített üzembe helyezési architektúrához szükséges további erőforrásokkal kiegészítse. Az üzembe helyezés megtervezésével kapcsolatos további információkért tekintse meg a fogalmakat ismertető dokumentumokat. Az elsődleges régió két HSM rendelkező kialakítás az állvány szintjén kezeli a rendelkezésre állást, a másodlagos régióban pedig két HSM ajánlott. Az oktatóanyagban használt sablonfájl egyszerűen használható két HSM-telepítés alapjaként, de a hozzájuk tartozó paramétereket módosítani kell, hogy megfeleljenek a követelményeinek.

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Figyelés](monitoring.md)
* [Támogatási lehetőségek](supportability.md)
