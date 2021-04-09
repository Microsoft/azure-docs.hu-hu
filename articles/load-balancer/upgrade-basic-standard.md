---
title: Az alapszintű nyilvános verzióról a standard Public-Azure Load Balancer
description: Ez a cikk bemutatja, hogyan frissítheti az Azure Public Load Balancert alapszintű SKU-ról standard SKU-ra
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: ef018e58f8336220b96eba568c94efc40a0fb0c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612830"
---
# <a name="upgrade-azure-public-load-balancer"></a>Azure nyilvános Load Balancer frissítése
Az [Azure standard Load Balancer](load-balancer-overview.md) számos funkciót és magas rendelkezésre állást kínál a zónák redundancia révén. További információ az Load Balancer SKU-ról: [összehasonlító táblázat](./skus.md#skus).

A frissítés két szakaszból áll:

1. Módosítsa az IP-kiosztási módszert Dinamikusról statikusra.
2. Futtassa a PowerShell-parancsfájlt a frissítés és a forgalom áttelepítésének befejezéséhez.

## <a name="upgrade-overview"></a>Frissítés áttekintése

Olyan Azure PowerShell-parancsfájl érhető el, amely a következő műveleteket végzi el:

* Egy szabványos SKU-Load Balancer hoz létre az alapszintű standard Load Balancer azonos erőforráscsoporthoz megadott helyen.
* Frissíti a nyilvános IP-címet az alapszintű SKU-ból a standard SKU-ra.
* Zökkenőmentesen másolja az alapszintű SKU Load Balancer konfigurációit az újonnan létrehozott standard Load Balancerra.
* Egy alapértelmezett kimenő szabályt hoz létre, amely lehetővé teszi a kimenő kapcsolat használatát.

### <a name="caveatslimitations"></a>Caveats\Limitations

* A parancsfájl csak a nyilvános Load Balancer frissítését támogatja. A belső alapszintű Load Balancer frissítéséhez tekintse meg [ezt az oldalt](./upgrade-basicinternal-standard.md) .
* A nyilvános IP-cím kiosztási módszerét a parancsfájl futtatása előtt "Static" értékre kell módosítani. 
* Ha a Load Balancer nem rendelkezik előtér-IP-konfigurációval vagy háttér-készlettel, valószínűleg a parancsfájl futtatásakor hiba lépett fel. Ügyeljen arra, hogy ne legyenek üresek.

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>Nyilvános IP-cím kiosztási módszerének módosítása statikusra

* * * A javasolt lépések:

    1. A rövid útmutatóban szereplő feladatok végrehajtásához jelentkezzen be a [Azure Portalba](https://portal.azure.com).
 
    1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza ki az alapszintű **Load Balancerhoz társított alapszintű nyilvános IP-címet** az erőforrás-listából.
   
    1. A **Beállítások** területen válassza a **konfigurációk** lehetőséget.
   
    1. A **hozzárendelés** területen válassza a **statikus** lehetőséget.
    1. Kattintson a **Mentés** gombra.
    >[!NOTE]
    >A nyilvános IP-címmel rendelkező virtuális gépek esetében először a szabványos IP-címeket kell létrehoznia, ahol az IP-cím nem garantált. A virtuális gépeket az alapszintű IP-címekről társíthatja, és társíthatja őket az újonnan létrehozott szabványos IP-címekkel. Ezt követően az utasításokat követve hozzáadhat virtuális gépeket standard Load Balancer háttérbeli készletéből. 

* **Új virtuális gépek létrehozása az újonnan létrehozott Standard nyilvános Load Balancer háttér-készletekhez való hozzáadásához**.
    * További információ a virtuális gép létrehozásával és a standard Load Balancerhoz való hozzárendelésével kapcsolatban [itt](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines)található.


## <a name="download-the-script"></a>A parancsfájl letöltése

Töltse le az áttelepítési parancsfájlt a  [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0).
## <a name="use-the-script"></a>A szkript használata

A helyi PowerShell-környezet beállításaitól és beállításaitól függően két lehetőség közül választhat:

* Ha nem rendelkezik az Azure az modulok telepítésével, vagy ne feledje eltávolítani az Azure-t, akkor a legjobb lehetőség a `Install-Script` szkript futtatására szolgáló lehetőség használata.
* Ha meg kell őriznie az Azure az modulokat, a legjobb megoldás, ha letölti a szkriptet, és közvetlenül futtatja.

Annak megállapításához, hogy telepítve van-e az Azure az modulok, futtassa a parancsot `Get-InstalledModule -Name az` . Ha a telepített modulok nem láthatók, akkor használhatja a `Install-Script` metódust.

### <a name="install-using-the-install-script-method"></a>Telepítés a Install-Script metódus használatával

Ha ezt a beállítást szeretné használni, az Azure az modulokat nem kell telepítenie a számítógépre. Ha telepítve vannak, a következő parancs hibát jelez. Távolítsa el az Azure az modulokat, vagy használja a másik lehetőséget a szkript manuális letöltéséhez és futtatásához.
  
Futtassa a szkriptet a következő paranccsal:

`Install-Script -Name AzurePublicLBUpgrade`

Ez a parancs telepíti a szükséges az modulokat is.  

### <a name="install-using-the-script-directly"></a>Telepítés közvetlenül a szkript használatával

Ha van néhány Azure az modulok telepítve, és nem távolítható el (vagy nem szeretné eltávolítani őket), manuálisan letöltheti a szkriptet a parancsfájl letöltése hivatkozás **manuális Letöltés** lapján. A parancsfájl nyers nupkg-fájlként van letöltve. Ha a parancsfájlt erről a nupkg-fájlról szeretné telepíteni, olvassa el a [manuális csomagok letöltése](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)című témakört.

A szkript futtatása:

1. Az Azure-hoz való `Connect-AzAccount` kapcsolódáshoz használható.

1. `Import-Module Az`A használatával importálhatja az az modulokat.

1. Vizsgálja meg a szükséges paramétereket:

   * **oldRgName: [string]: kötelező** – ez a meglévő alapszintű Load Balancerhez tartozó erőforráscsoport, amelyet frissíteni kíván. A karakterlánc értékének megkereséséhez navigáljon Azure Portal, válassza ki az alapszintű Load Balancer forrását, és kattintson a terheléselosztó **áttekintésére** . Az erőforráscsoport ezen az oldalon található.
   * **oldLBName: [string]: kötelező** – ez a meglévő alapszintű, frissíteni kívánt alapbalancer neve. 
   * **newLBName: [string]: kötelező** – ez a létrehozandó standard Load Balancer neve.
1. Futtassa a szkriptet a megfelelő paraméterek használatával. A befejezéshez öt – hét percet is igénybe vehet.

    **Példa**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg&quot; -oldLBName &quot;LBForPublic&quot; -newLbName &quot;LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Kimenő szabály létrehozása a kimenő kapcsolatok számára

Kövesse az [utasításokat](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration) egy kimenő szabály létrehozásához, hogy
* Adja meg a kimenő NAT-t a semmiből.
* A meglévő kimenő NAT működésének skálázása és finomhangolása.

## <a name="common-questions"></a>Gyakori kérdések

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Vannak korlátozások a Azure PowerShell szkripttel a konfiguráció a v1-ről a v2-re való áttelepíteni?

Igen. Lásd a [figyelmeztetéseket és korlátozásokat](#caveatslimitations).

### <a name="how-long-does-the-upgrade-take"></a>Mennyi ideig tart a frissítés?

Általában körülbelül 5-10 percet vesz igénybe, amíg a szkript befejeződik, és a Load Balancer konfigurációjának összetettsége miatt hosszabb időt vehet igénybe. Ezért szükség esetén tartsa szem előtt az állásidőt, és tervezze meg a feladatátvételt.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>A Azure PowerShell szkript az alapszintű Load Balancerról az újonnan létrehozott standard Load Balancerra is átváltja a forgalmat?

Igen. A Azure PowerShell parancsfájl nem csak a nyilvános IP-címet frissíti, a konfigurációt alapszintről standard Load Balancerre másolja, de a virtuális gépet az újonnan létrehozott Standard nyilvános Load Balancer is telepíti. 

## <a name="next-steps"></a>Következő lépések

[Tudnivalók a standard Load Balancer](load-balancer-overview.md)
