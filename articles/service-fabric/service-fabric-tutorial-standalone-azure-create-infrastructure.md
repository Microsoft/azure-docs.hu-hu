---
title: Infrastruktúra létrehozása Azure-beli virtuális gépeken futó fürthöz
description: Ebből az oktatóanyagból megtudhatja, hogyan állíthatja be az Azure-beli virtuálisgép-infrastruktúrát egy Service Fabric-fürt futtatásához.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: e9f62f944fff331bcf2dad1b380161e563614219
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90561840"
---
# <a name="tutorial-create-azure-vm-infrastructure-to-host-a-service-fabric-cluster"></a>Oktatóanyag: Azure VM-infrastruktúra létrehozása Service Fabric-fürt üzemeltetéséhez

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Ebben az oktatóanyag-sorozatban létrehoz egy önálló, Azure-beli virtuális gépeken üzemeltetett fürtöt, és telepít egy alkalmazást.

Ez az oktatóanyag egy sorozat első része. Ebben a cikkben a Service Fabric önálló fürtjének üzemeltetéséhez szükséges Azure-beli virtuálisgép-erőforrásokat állítja elő. A további cikkekben telepíteni fogja az önálló Service Fabric-csomagot, majd egy mintaalkalmazást a fürtön, végül pedig törli a fürtöt.

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * AzureVM-példányok létrehozása
> * A biztonsági csoport módosítása
> * Bejelentkezés az egyik példányba
> * A példány előkészítése a Service Fabrichez

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre.  Ha még nem rendelkezik fiókkal, a létrehozáshoz lépjen a [Azure Portal](https://portal.azure.com) .

## <a name="create-azure-virtual-machine-instances"></a>Azure virtuálisgép-példányok létrehozása

1. Jelentkezzen be a Azure Portalba, és válassza a **virtuális gépek** (nem Virtual Machines (klasszikus)) lehetőséget.

   ![Azure Portal virtuális gép][az-console]

2. Válassza a **Hozzáadás** gombot, amely megnyitja a **virtuális gép létrehozása** űrlapot.

3. Az **alapvető beállítások** lapon válassza ki a kívánt előfizetést és erőforráscsoportot (az új erőforráscsoport használata javasolt).

4. Módosítsa a **rendszerkép** típusát a **Windows Server 2016 Datacenter** értékre. 
 
5. Módosítsa a példány **méretét** a **standard DS2 v2** értékre. Állítsa be a rendszergazda **felhasználónevét** és **jelszavát**, és adja meg, hogy mi is az.

6. Hagyja letiltva a **bejövő portok szabályait** . ezeket a következő szakaszban fogjuk konfigurálni.

7. A **hálózatkezelés** lapon hozzon létre egy új **Virtual Network** , és jegyezze fel a nevét.

8. Ezután állítsa a **NIC hálózati biztonsági csoportot** a **speciális** elemre. Hozzon létre egy új biztonsági csoportot, és adja meg a nevét, és hozza létre a következő szabályokat a TCP-forgalom bármely forrásból való engedélyezéséhez:

   ![A képernyőképen a bejövő TCP-forgalom engedélyezéséhez szükséges szabályok létrehozása látható.][sf-inbound]

   * Port `3389` , RDP és ICMP esetén (alapszintű kapcsolat).
   * Portok `19000-19003` Service Fabric.
   * Portok `19080-19081` Service Fabric.
   * Port `8080` , webböngésző-kérelmek esetén.

   > [!TIP]
   > A Service Fabricben a virtuális gépek összekapcsolásához az infrastruktúrát futtató virtuális gépeknek ugyanazokkal a hitelesítő adatokkal kell rendelkezniük.  Két gyakori módja van a konzisztens hitelesítő adatok elérésének: csatlakoztassa mindet ugyanahhoz a tartományhoz, vagy állítsa be ugyanazt a rendszergazdai jelszót az összes virtuális gépen. Szerencsére az Azure lehetővé teszi, hogy az ugyanazon a **virtuális hálózaton** lévő összes virtuális gép könnyedén kapcsolódjon, így minden példányunk ugyanazon a hálózaton lesz.

9. Adjon hozzá egy másik szabályt. Állítsa a forrást a **szolgáltatás címkére** , és állítsa a forrás szolgáltatás címkéjét **VirtualNetwork** értékre. Service Fabric a következő portok megnyitását igényli a fürtön belüli kommunikációhoz: 135137-139, 445, 20001-20031, 20606-20861.

   ![A képernyőképen a fürt TCP-forgalmának engedélyezéséhez szükséges szabályok létrehozása látható.][vnet-inbound]

10. A többi lehetőség is elfogadható az alapértelmezett állapotukban. Ha szeretné, tekintse át őket, majd indítsa el a virtuális gépet.

## <a name="creating-more-instances-for-your-service-fabric-cluster"></a>További példányok létrehozása a Service Fabric-fürthöz

Indítsa el a két további **Virtual Machines**, és ügyeljen rá, hogy az előző szakaszban leírt beállításokat is megőrizze. Különösen a rendszergazda felhasználónevét és jelszavát kell megtartania. A **Virtual Network** és a **hálózati adapter hálózati biztonsági csoportját** nem szabad újból létrehozni; Válassza ki a legördülő menüből már létrehozott fájlokat. Az egyes példányok üzembe helyezése néhány percet is igénybe vehet.

## <a name="connect-to-your-instances"></a>Kapcsolódás a példányokhoz

1. Válassza ki a **virtuális gép** szakasz egyik példányát.

2. Az **Áttekintés** lapon jegyezze fel a *magánhálózati* IP-címet. Ezután kattintson a **kapcsolat** elemre.

3. Az **RDP** lapon vegye figyelembe, hogy a nyilvános IP-címet és a 3389-es portot használjuk, amelyet kifejezetten korábban nyitottunk meg. Töltse le az RDP-fájlt.
 
4. Nyissa meg az RDP-fájlt, és amikor a rendszer kéri, adja meg a virtuális gép beállításában megadott felhasználónevet és jelszót.

5. Miután kapcsolódott egy példányhoz, ellenőriznie kell, hogy fut-e a távoli beállításjegyzék, engedélyezze az SMB-t, majd nyissa meg a szükséges portokat az SMB és a távoli beállításjegyzék számára.

   Az SMB engedélyezéséhez ez a PowerShell-parancs:

   ```powershell
   netsh advfirewall firewall set rule group="File and Printer Sharing" new enable=Yes
   ```

6. A tűzfalban lévő portok megnyitására ez a PowerShell-parancs szolgál:

   ```powershell
   New-NetFirewallRule -DisplayName "Service Fabric Ports" -Direction Inbound -Action Allow -RemoteAddress LocalSubnet -Protocol TCP -LocalPort 135, 137-139, 445
   ```

7. Ismételje meg ezt a folyamatot a többi példány esetében, és a magánhálózati IP-címeket is megjegyezve.

## <a name="verify-your-settings"></a>A beállítások ellenőrzése

1. Az alapszintű kapcsolat ellenőrzéséhez az RDP használatával csatlakozzon az egyik virtuális géphez.

2. Nyissa meg a **parancssort** a virtuális gépről, majd a ping parancs használatával kapcsolódjon az egyik virtuális gépről egy másikhoz, és cserélje le az alábbi IP-címet a korábban feljegyzett MAGÁNHÁLÓZATI IP-címek egyikére (nem pedig a virtuális gép IP-címére, amelyhez már csatlakozik).

   ```
   ping 172.31.20.163
   ```

   Ha a kimenetben a `Reply from 172.31.20.163: bytes=32 time<1ms TTL=128` szöveg ismétlődik négyszer, akkor a példányok közötti kapcsolat működik.

3. Most ellenőrizze az SMB-megosztás működését a következő paranccsal:

   ```
   net use * \\172.31.20.163\c$
   ```

   A parancs kimenetének a következőnek kell lennie: `Drive Z: is now connected to \\172.31.20.163\c$.`.


   A példányok most már készen állnak a Service Fabricra.

## <a name="next-steps"></a>Következő lépések

A sorozat első részében megtanulta, hogyan indíthat el három Azure-beli virtuálisgép-példányt, és hogyan konfigurálhatja őket a Service Fabric telepítéséhez:

> [!div class="checklist"]
> * Azure-beli virtuálisgép-példányok készletének létrehozása
> * A biztonsági csoport módosítása
> * Bejelentkezés az egyik példányba
> * A példány előkészítése a Service Fabrichez

A sorozat második részében konfigurálni fogja a Service Fabricet a fürtben.

> [!div class="nextstepaction"]
> [A Service Fabric telepítése](service-fabric-tutorial-standalone-create-service-fabric-cluster.md)

<!-- IMAGES -->
[az-console]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/az-console.png
[sf-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/sf-inbound.png
[vnet-inbound]: ./media/service-fabric-tutorial-standalone-azure-create-infrastructure/vnet-inbound.png
