---
title: Frissítés alapszintű belsőről standard belsőre – Azure Load Balancer
description: Ez a cikk bemutatja, hogyan frissítheti az Azure belső Load Balancereit alapszintű SKU-ról standard SKU-ra.
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/07/2020
ms.author: irenehua
ms.openlocfilehash: 1b7bdbdb9e1d642f2ef4a715d4993e4f449ccd0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050697"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Azure belső Load Balancer frissítése – nincs szükség kimenő kapcsolatok megtételére
Az [Azure standard Load Balancer](load-balancer-overview.md) számos funkciót és magas rendelkezésre állást kínál a zónák redundancia révén. További információ az Load Balancer SKU-ról: [összehasonlító táblázat](./skus.md#skus).

Ez a cikk egy PowerShell-szkriptet vezet be, amely egy standard Load Balancert hoz létre ugyanazzal a konfigurációval, mint az alapszintű Load Balancer, valamint az alapszintű Load Balancerról standard Load Balancerre történő áttelepítést

## <a name="upgrade-overview"></a>Frissítés áttekintése

Olyan Azure PowerShell-parancsfájl érhető el, amely a következő műveleteket végzi el:

* Létrehoz egy szabványos belső SKU Load Balancer a megadott helyen. Vegye figyelembe, hogy a szabványos belső Load Balancer nem biztosít [kimenő kapcsolatokat](./load-balancer-outbound-connections.md) .
* Zökkenőmentesen másolja az alapszintű SKU Load Balancer konfigurációit az újonnan létrehozott standard Load Balancerra.
* A magánhálózati IP-címek zökkenőmentes mozgatása az alapszintű Load Balancer az újonnan létrehozott standard Load Balancerig.
* Zökkenőmentesen helyezheti át a virtuális gépeket az alapszintű Load Balancer háttér-készletéből a standard Load Balancer háttér-készletéből.

### <a name="caveatslimitations"></a>Caveats\Limitations

* A parancsfájl csak a belső Load Balancer frissítését támogatja, ha nincs szükség kimenő kapcsolatok használatára. Ha egyes virtuális gépekhez [Kimenő kapcsolatok](./load-balancer-outbound-connections.md) szükségesek, tekintse meg ezt az [oldalt](upgrade-InternalBasic-To-PublicStandard.md) . 
* Az alapszintű Load Balancernak ugyanabban az erőforráscsoporthoz kell esnie, mint a háttérbeli virtuális gépeket és a hálózati adaptereket.
* Ha a standard Load Balancer egy másik régióban lett létrehozva, akkor a régi régióban meglévő virtuális gépeket nem lehet az újonnan létrehozott standard Load Balancerhoz rendelni. A korlátozás megkerüléséhez hozzon létre egy új virtuális gépet az új régióban.
* Ha a Load Balancer nem rendelkezik előtér-IP-konfigurációval vagy háttér-készlettel, valószínűleg a parancsfájl futtatásakor hiba lépett fel. Győződjön meg arról, hogy nem üresek.

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>IP-kiosztási módszer módosítása statikusra a előtér-IP-konfigurációhoz (hagyja figyelmen kívül ezt a lépést, ha már statikus)

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd válassza ki az alapszintű Load Balancer az erőforrások listából.

2. A **Beállítások** területen válassza ki a **frontend IP-konfiguráció** elemet, majd válassza ki az első előtér-IP-konfigurációt. 

3. A **hozzárendelés** beállításnál válassza a **statikus** lehetőséget.

4. Ismételje meg a 3. lépést az alapszintű Load Balancer összes előtérbeli IP-konfigurációjában.


## <a name="download-the-script"></a>A parancsfájl letöltése

Töltse le az áttelepítési parancsfájlt a  [PowerShell-Galéria](https://www.powershellgallery.com/packages/AzureILBUpgrade/5.0).
## <a name="use-the-script"></a>A szkript használata

A helyi PowerShell-környezet beállításaitól és beállításaitól függően két lehetőség közül választhat:

* Ha nem rendelkezik az Azure az modulok telepítésével, vagy ne feledje eltávolítani az Azure-t, akkor a legjobb lehetőség a `Install-Script` szkript futtatására szolgáló lehetőség használata.
* Ha meg kell őriznie az Azure az modulokat, a legjobb megoldás, ha letölti a szkriptet, és közvetlenül futtatja.

Annak megállapításához, hogy telepítve van-e az Azure az modulok, futtassa a parancsot `Get-InstalledModule -Name az` . Ha a telepített modulok nem láthatók, akkor használhatja a `Install-Script` metódust.

### <a name="install-using-the-install-script-method"></a>Telepítés a Install-Script metódus használatával

Ha ezt a beállítást szeretné használni, az Azure az modulokat nem kell telepítenie a számítógépre. Ha telepítve vannak, a következő parancs hibát jelez. Távolítsa el az Azure az modulokat, vagy használja a másik lehetőséget a szkript manuális letöltéséhez és futtatásához.
  
Futtassa a szkriptet a következő paranccsal:

`Install-Script -Name AzureILBUpgrade`

Ez a parancs telepíti a szükséges az modulokat is.  

### <a name="install-using-the-script-directly"></a>Telepítés közvetlenül a szkript használatával

Ha van néhány Azure az modulok telepítve, és nem távolítható el (vagy nem szeretné eltávolítani őket), manuálisan letöltheti a szkriptet a parancsfájl letöltése hivatkozás **manuális Letöltés** lapján. A parancsfájl nyers nupkg-fájlként van letöltve. Ha a parancsfájlt erről a nupkg-fájlról szeretné telepíteni, olvassa el a [manuális csomagok letöltése](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)című témakört.

A szkript futtatása:

1. Az Azure-hoz való `Connect-AzAccount` kapcsolódáshoz használható.

1. `Import-Module Az`A használatával importálhatja az az modulokat.

1. Vizsgálja meg a szükséges paramétereket:

   * **rgName: [string]: kötelező** – ez a meglévő alapszintű Load Balancer és az új standard Load Balancerhoz tartozó erőforráscsoport. A karakterlánc értékének megkereséséhez navigáljon Azure Portal, válassza ki az alapszintű Load Balancer forrását, és kattintson a terheléselosztó **áttekintésére** . Az erőforráscsoport ezen az oldalon található.
   * **oldLBName: [string]: kötelező** – ez a meglévő alapszintű, frissíteni kívánt alapbalancer neve. 
   * **newlocation: [string]: kötelező** – ez az a hely, ahol a standard Load Balancer létre lesz hozva. Javasoljuk, hogy a kiválasztott alapszintű Load Balancer ugyanazon helyét örökli a standard Load Balancer a többi meglévő erőforrással való jobb együttműködés érdekében.
   * **newLBName: [string]: kötelező** – ez a létrehozandó standard Load Balancer neve.
1. Futtassa a szkriptet a megfelelő paraméterek használatával. A befejezéshez öt – hét percet is igénybe vehet.

    **Példa**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg&quot; -oldLBName &quot;LBForInternal&quot; -newlocation &quot;centralus&quot; -newLbName &quot;LBForUpgrade"
   ```

## <a name="common-questions"></a>Gyakori kérdések

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Vannak korlátozások a Azure PowerShell szkripttel a konfiguráció a v1-ről a v2-re való áttelepíteni?

Igen. Lásd a [figyelmeztetéseket és korlátozásokat](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>A Azure PowerShell szkript az alapszintű Load Balancerról az újonnan létrehozott standard Load Balancerra is átváltja a forgalmat?

Igen, áttelepíti a forgalmat. Ha a forgalmat személyesen szeretné áttelepíteni, használja [ezt a parancsfájlt](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) , amely nem helyezi át a virtuális gépeket.

## <a name="next-steps"></a>Következő lépések

[Tudnivalók a standard Load Balancer](load-balancer-overview.md)
