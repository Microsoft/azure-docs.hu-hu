---
title: A Pásti-erőforrásokhoz való hozzáférés korlátozása – oktatóanyag – Azure Portal
description: Ebben az oktatóanyagban megtanulhatja, hogyan korlátozható az Azure-erőforrásokhoz – például az Azure Storage-hoz és az Azure SQL Database-hez – való hálózati hozzáférés virtuális hálózati szolgáltatásvégpontokkal az Azure Portal használatával.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/11/2020
ms.author: kumud
ms.openlocfilehash: cb3a4b6a726ee9163582b15586c65fc750712c63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368293"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Oktatóanyag: PaaS-erőforrásokhoz való hálózati hozzáférés korlátozása virtuális hálózati szolgáltatásvégpontokkal az Azure Portal használatával

Virtuális hálózati szolgáltatásvégpontokkal egy adott virtuális hálózati alhálózatra korlátozható az egyes Azure-szolgáltatási erőforrásokhoz való hálózati hozzáférés. Emellett teljesen le is tiltható az internetes hozzáférés az erőforrásokhoz. A szolgáltatásvégpontok közvetlen csatlakozást biztosítanak a virtuális hálózat és a támogatott Azure-szolgáltatások között, így lehetővé teszik a virtuális hálózat magáncímterének használatát az Azure-szolgáltatások eléréséhez. A szolgáltatásvégpontokon keresztül az Azure-erőforrások felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán marad. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása egyetlen alhálózattal
> * Alhálózat hozzáadása és szolgáltatásvégpont engedélyezése
> * Azure-erőforrás létrehozása és hálózati hozzáférés engedélyezése az erőforráshoz egyetlen alhálózatról
> * Virtuális gép (VM) üzembe helyezése az egyes alhálózatokon
> * Erőforráshoz való alhálózati hozzáférés ellenőrzése
> * Erőforráshoz való alhálózati és internetes hozzáférés letiltásának ellenőrzése

Igény szerint az oktatóanyagot az [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) vagy az [Azure PowerShell](tutorial-restrict-network-access-to-resources-powershell.md) használatával is elvégezheti.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. Válassza a **hálózatkezelés**, majd a **virtuális hálózatok** lehetőséget.
3. Kattintson a **+ Hozzáadás** gombra, és adja meg a következő adatokat: 

   |Beállítás|Érték|
   |----|----|
   |Előfizetés| Az előfizetés kiválasztása|
   |Erőforráscsoport | Válassza az **Új létrehozása** elemet, és adja meg a *myResourceGroup* nevet.|
   |Name| *MyVirtualNetwork* megadása |
   |Region| Válassza ki az USA **keleti** régióját |

   ![Írja be a virtuális hálózat alapvető adatait](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

4. Kattintson a **Tovább gombra: IP-címek >**
   
   |Beállítás|Érték|
   |----|----|
   |IPv4Address terület| Kilépés alapértelmezettként |
   |Alhálózat neve| Kattintson az **alapértelmezett** elemre, és módosítsa a nevet a "default" értékről a "Public" névre.|
   |Alhálózat címtartomány| Kilépés alapértelmezettként|

5. Kattintson a **Tovább gombra: biztonsági >** 
   
   |Beállítás|Érték|
   |----|----|   
   |BastionHost| Letiltás|
   |DDoS Protection| Letiltás|
   |Firewall| Letiltás|

6. Ha elkészült, kattintson **a felülvizsgálat és létrehozás** gombra.
7. Ha az ellenőrzés sikeres, kattintson a **Létrehozás** gombra.
8. Várjon, amíg az üzembe helyezés befejeződik, kattintson az **erőforrás keresése** vagy a következő szakaszra. 

## <a name="enable-a-service-endpoint"></a>Szolgáltatásvégpont engedélyezése

A szolgáltatásvégpontok szolgáltatásonként és alhálózatonként engedélyezhetők. Alhálózat létrehozása és szolgáltatás végpontjának engedélyezése az alhálózathoz:

1. Ha még nem a virtuális hálózati erőforrás oldalon található, az újonnan létrehozott hálózatot a portál tetején található **erőforrások, szolgáltatások és dokumentumok keresése** mezőbe keresheti meg, majd a listából válassza ki a *myVirtualNetwork* lehetőséget.
2. A **Beállítások** menüben (balra) válassza az **alhálózatok** lehetőséget, majd válassza a **+ alhálózat** lehetőséget, ahogy az a következő képen látható:

    ![Alhálózat hozzáadása](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. Az **Alhálózat hozzáadása** alatt válassza ki vagy adja meg a következő adatokat, és kattintson az **OK** gombra:

    |Beállítás|Érték|
    |----|----|
    |Név| Személyes |
    |Címtartomány| Kilépés alapértelmezettként|
    |Szolgáltatásvégpontok| Válassza a **Microsoft. Storage elemet.**|
    |Szolgáltatásvégpont-szabályzatok | Az alapértelmezett érték kihagyása 0 |

> [!CAUTION]
> Mielőtt engedélyezne egy szolgáltatásvégpontot egy erőforrásokkal rendelkező meglévő alhálózaton, tekintse meg az [alhálózat beállításainak módosítását](virtual-network-manage-subnet.md#change-subnet-settings) ismertető szakaszt.

4. Kattintson a **Save (Mentés**) elemre, majd a jobb oldalon lévő alhálózat ablak bezárásához. Az újonnan létrehozott alhálózatnak meg kell jelennie a listán.

## <a name="restrict-network-access-for-a-subnet"></a>Alhálózat hálózati hozzáférésének korlátozása

Alapértelmezés szerint az alhálózatban lévő összes virtuálisgép-példány képes kommunikálni az összes erőforrással. Egy hálózati biztonsági csoport létrehozásával és az alhálózathoz való társításával korlátozhatja a kommunikációt az alhálózaton lévő összes erőforrásra vonatkozóan.

1. Az Azure Portal bal felső sarkában válassza az **Összes szolgáltatás** elemet.
2. Válassza a **hálózatkezelés**, majd a **hálózati biztonsági csoportok**(vagy a keresés) lehetőséget.
3. A **hálózati biztonsági csoportok** lapon kattintson a **+ Hozzáadás** elemre.
4. Adja meg a következő információkat 

    |Beállítás|Érték|
    |----|----|
    |Előfizetés| Az előfizetés kiválasztása|
    |Erőforráscsoport | Válassza ki a *myResourceGroup* a listából|
    |Name| **MyNsgPrivate** megadása |
    |Hely| Válassza ki az **USA keleti** régióját |

5. Kattintson a **felülvizsgálat + létrehozás** lehetőségre, majd az érvényesítés ellenőrzése után kattintson a **Létrehozás** gombra.
6. A hálózati biztonsági csoport létrehozása után kattintson az **erőforrás** keresése elemre, vagy keresse meg a *myNsgPrivate*.
7. A bal oldali **Beállítások** területen válassza a **kimenő biztonsági szabályok** elemet.
8. Válassza a **+ Hozzáadás** lehetőséget.
9. Hozzon létre egy szabályt, amely engedélyezi a kimenő kommunikációt az Azure Storage szolgáltatás felé. Adja meg vagy válassza ki a következő adatokat, majd válassza a **Hozzáadás** lehetőséget:

    |Beállítás|Érték|
    |----|----|
    |Forrás| Válassza a **VirtualNetwork** lehetőséget. |
    |Forrásporttartományok| * |
    |Cél | Válassza a **Service Tag** lehetőséget.|
    |Cél szolgáltatáscímkéje | Válassza a **Storage** lehetőséget.|
    |Célporttartományok| Az alapértelmezett érték *8080* |
    |Protokoll|Bármely|
    |Művelet|Engedélyezés|
    |Prioritás|100|
    |Név|Átnevezés az **Allow-Storage-all**|

10. Hozzon létre még egy kimenő biztonsági szabályt, amely letiltja az internetes kommunikációt. Ez a szabály felülírja az összes hálózati biztonsági csoportra érvényes alapértelmezett szabályt, amely engedélyezi a kimenő internetes kommunikációt. Hajtsa végre a 6-9-as lépéseket a fentiek alapján a következő értékek használatával:

    |Beállítás|Érték|
    |----|----|
    |Forrás| Válassza a **VirtualNetwork** lehetőséget. |
    |Forrásporttartományok| * |
    |Cél | Válassza a **Service Tag** lehetőséget.|
    |Cél szolgáltatáscímkéje| Válassza az **Internet** lehetőséget.|
    |Célporttartományok| * |
    |Protokoll|Bármely|
    |Művelet|**Alapértelmezett érték módosítása *Megtagadás* esetén** |
    |Prioritás|110|
    |Name|Váltás *Megtagadás esetén – Internet-mind*|

11. Hozzon létre egy *bejövő biztonsági szabályt* , amely lehetővé teszi a RDP protokoll (RDP) forgalmat bárhonnan az alhálózatra. Ez a szabály felülbírálja azon alapértelmezett biztonsági szabályokat, amelyek elutasítanak minden bejövő forgalmat az internetről. A távoli asztali kapcsolatok engedélyezettek az alhálózathoz, hogy a kapcsolat egy későbbi lépésben tesztelhető legyen. 
12. A **Beállítások** területen válassza a **bejövő biztonsági szabályok** elemet.
13. Válassza a **+ Hozzáadás** lehetőséget, és használja a következő értékeket:

    |Beállítás|Érték|
    |----|----|
    |Forrás| Bármely |
    |Forrásporttartományok| * |
    |Cél | Válassza a **VirtualNetwork** lehetőséget.|
    |Célporttartományok| Váltás *3389* -re |
    |Protokoll|Bármely|
    |Művelet|Engedélyezés|
    |Prioritás|120|
    |Name|Módosítás az *Engedélyezés-RDP-all* értékre|

   >[!WARNING] 
   > Az 3389-es RDP-port elérhető az interneten. Ez csak teszteléshez ajánlott. *Éles környezetekben* ajánlott VPN-t vagy magánhálózati kapcsolatokat használni.

1.  A **Beállítások** területen válassza az **Alhálózatok** lehetőséget.
2.  Kattintson a **+ Társítás** gombra.
3.  A **virtuális hálózat** területen válassza a **myVirtualNetwork** lehetőséget.
4.  Az **alhálózat** területen válassza a **magánjellegű** lehetőséget, majd kattintson **az OK gombra**.

## <a name="restrict-network-access-to-a-resource"></a>Erőforráshoz való hálózati hozzáférés korlátozása

Az Azure-szolgáltatásokon keresztül létrehozott erőforrásokhoz való hálózati hozzáférés korlátozásához szükséges lépések a szolgáltatásbeli végpontok esetében eltérőek lesznek. Az egyes szolgáltatásokhoz szükséges lépéseket az adott szolgáltatások dokumentációja tartalmazza. Az oktatóanyag a továbbiakban egy Azure Storage-tárfiók hálózati hozzáférésének korlátozásához szükséges lépéseket ismerteti példaként.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

1. Válassza az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** lehetőséget.
2. A keresőmezőbe írja be a "Storage-fiók" kifejezést, majd válassza ki a legördülő menüből.
3. Kattintson a **+ Hozzáadás** gombra.
4. Adja meg a következő információkat:

    |Beállítás|Érték|
    |----|----|
    |Előfizetés| Az előfizetés kiválasztása|
    |Erőforráscsoport| *MyResourceGroup* kiválasztása|
    |Storage-fiók neve| Olyan nevet adjon meg, amely az összes Azure-helyen egyedi, 3–24 karakter hosszú, és csak számokat és kisbetűket tartalmaz.|
    |Hely| Válassza ki az USA **keleti** régióját |
    |Teljesítmény|Standard|
    |Fiók altípusa| StorageV2 (általános célú v2)|
    |Replikáció| Helyileg redundáns tárolás (LRS)|

5. Válassza a **Létrehozás + áttekintés** lehetőséget, és az érvényesítési ellenőrzések átadása után kattintson a **Létrehozás** gombra. 

>[!NOTE] 
> Az üzembe helyezés eltarthat néhány percig.

6. A Storage-fiók létrehozása után kattintson az **erőforrás keresése** elemre.

### <a name="create-a-file-share-in-the-storage-account"></a>Fájlmegosztás létrehozása a tárfiókban

1. Nyissa meg a Storage-fiók áttekintése lapot.
2. Válassza ki a **fájlmegosztás** alkalmazás ikonját, majd kattintson a **+ fájlmegosztás** elemre.

    |Beállítás|Érték|
    |----|----|
    |Név| saját fájl – megosztás|
    |Kvóta| "Maximális érték" |

   ![Tárfiók](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Kattintson a **Létrehozás** lehetőségre.
4. A fájlmegosztást az Azure-ablakban kell megjeleníteni, ha nem kattint a **frissítés** gombra.

### <a name="restrict-network-access-to-a-subnet"></a>Alhálózathoz való hálózati hozzáférés korlátozása

Alapértelmezés szerint a tárfiókok bármilyen hálózatban található ügyféltől érkező hálózati kapcsolatot elfogadnak, beleértve az internetet is. A hálózati hozzáférést korlátozhatja az internetről, és minden más alhálózatot az összes virtuális hálózatban (kivéve a *myVirtualNetwork* virtuális hálózatában lévő *magánhálózati* alhálózatot). Alhálózat hálózati hozzáférésének korlátozása:

1. Az (egyedi névvel ellátott) Storage-fiók **beállításai** területen válassza a **hálózatkezelés** lehetőséget.
2. Válassza a **Kijelölt hálózatok** lehetőséget.
3. Válassza a **+ meglévő virtuális hálózat hozzáadása** elemet.
4. A **Hálózatok hozzáadása** területen válassza ki a következő értékeket, majd kattintson a **Hozzáadás** gombra:

    |Beállítás|Érték|
    |----|----|
    |Előfizetés| Az előfizetés kiválasztása|
    |Virtuális hálózatok| **myVirtualNetwork**|
    |Alhálózatok| **Személyes**|

    ![Képernyőfelvétel: a hálózatok hozzáadása panel, ahol megadhatja a megadott értékeket.](./media/tutorial-restrict-network-access-to-resources/virtual-networks.png)

5. Kattintson a **Hozzáadás** elemre, majd kattintson a **Mentés** ikonra a módosítások mentéséhez.
6. A Storage-fiók **beállításai** területen válassza a **hozzáférési kulcsok** elemet a következő képen látható módon:

      ![A képernyőképen a beállítások közül kiválasztott hozzáférési kulcsok láthatók, ahol megszerezheti a kulcsot.](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

7. Kattintson a **kulcsok megjelenítése** lehetőségre, és jegyezze fel a **kulcs** értékeit, mivel a fájlmegosztást egy későbbi lépésben manuálisan kell megadnia, amikor egy virtuális gépen lévő meghajtóbetűjelhez rendeli a key1.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Tárfiókhoz való hálózati hozzáférés teszteléséhez helyezzen üzembe egy virtuális gépet minden alhálózaton.

### <a name="create-the-first-virtual-machine"></a>Az első virtuális gép létrehozása

1. A "Keresés az erőforrások között. . ." sávban keresse meg a **virtuális gépeket**.
2. Válassza a **+ > virtuális gép hozzáadása** elemet. 
3. Adja meg a következő adatokat:

   |Beállítás|Érték|
   |----|----|
   |Előfizetés| Az előfizetés kiválasztása|
   |Erőforráscsoport| Válassza a * * myResourceGroup, amely korábban lett létrehozva.|
   |Virtuális gép neve| *MyVmPublic* megadása|
   |Region | (US) Az USA keleti régiója
   |Rendelkezésre állási beállítások| A rendelkezésre állási zóna|
   |A rendelkezésre állási zóna | 1 |
   |Kép | Windows Server 2019 Datacenter – Gen1 |
   |Méret | Válassza ki a használni kívánt virtuálisgép-példány méretét |
   |Felhasználónév|Adjon meg egy tetszőleges felhasználónevet.|
   |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Nyilvános bejövő portok | Kijelölt portok engedélyezése |
   |Válassza ki a bejövő portokat | Hagyja meg az alapértelmezett értéket RDP értékre *(3389)* |

   ![Virtuális hálózat kiválasztása](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
  
4. Válassza a **hálózatkezelés** fület, majd válassza a **myVirtualNetwork** lehetőséget. 
5. Válassza ki a *nyilvános* alhálózatot.
6. A **NIC hálózati biztonsági csoport** területen válassza a **speciális** lehetőséget. A portál automatikusan létrehoz egy hálózati biztonsági csoportot, amely engedélyezi a 3389-es portot. Egy későbbi lépésben meg kell nyitnia ezt a portot a virtuális géphez való csatlakozáshoz. 

   ![Virtuális gép alapvető információinak megadása](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)

7. Válassza a **felülvizsgálat és létrehozás**, majd **a létrehozás lehetőséget, és várja** meg, amíg a telepítés befejeződik.
8. Kattintson az **erőforrás** megnyitása lehetőségre, vagy nyissa meg a **Home > Virtual Machines** lapot, és válassza ki az imént létrehozott *myVmPublic*, amelyet el kell indítani.

### <a name="create-the-second-virtual-machine"></a>A második virtuális gép létrehozása

1. Hajtsa végre ismét a 1-8 lépést, de a 3. lépésben adja meg a virtuális gép *myVmPrivate* , és állítsa be a **nyilvános bejövő portot** a "None" értékre. 
2. Az 4-5. lépésben válassza ki a **privát** alhálózatot.

>[!NOTE]
> A **hálózati adapter hálózati biztonsági csoportjának** és a **nyilvános bejövő portok** beállításainak tükröznie kell az alább látható képet, beleértve a kék megerősítő ablakot, amely a következőt tartalmazza: "az összes nyilvános internetes forgalom alapértelmezés szerint le lesz tiltva".

   ![Privát virtuális gép létrehozása](./media/tutorial-restrict-network-access-to-resources/create-private-virtual-machine.png)

3. Válassza a **felülvizsgálat és létrehozás**, majd **a létrehozás lehetőséget, és várja** meg, amíg a telepítés befejeződik. 

>[!WARNING]
> A telepítés befejeződéséig ne folytassa a következő lépéssel.

4. Várjon a megerősítő ablakra, majd kattintson az **erőforrás keresése** elemre.

   ![Privát virtuális gép megerősítési ablakának létrehozása](./media/tutorial-restrict-network-access-to-resources/create-vm-confirmation-window.png)

## <a name="confirm-access-to-storage-account"></a>Tárfiókhoz való hozzáférés ellenőrzése

1. Miután létrehozta a *myVmPrivate* virtuális gépet, kattintson az **erőforrás keresése** elemre. 
2. A virtuális géphez való kapcsolódáshoz válassza a **kapcsolódás > RDP**-t.
3. A **csatlakozási** gomb kiválasztása után létrejön egy RDP protokoll (. rdp) fájl. Az **RDP-fájl letöltése** gombra kattintva letöltheti a számítógépre.  
4. Nyissa meg a letöltött RDP-fájlt. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót. Előfordulhat, hogy a virtuális gép létrehozásakor megadott hitelesítő adatok megadásához **másik fiókot** **kell választania**. Az e-mail mezőben adja meg a korábban megadott "rendszergazdai fiók: username" hitelesítő adatokat. 
5. Válassza az **OK** lehetőséget.
6. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Ha figyelmeztetést kap, kattintson az **Igen** vagy a **Folytatás** gombra a csatlakozás folytatásához. A virtuális gép indítását a következőképpen kell megjelennie:

   ![Futó privát virtuális gép megjelenítése](./media/tutorial-restrict-network-access-to-resources/virtual-machine-running.png)

7. A virtuális gép ablakban nyisson meg egy PowerShell CLI-példányt.
8. Az alábbi szkript használatával képezze le az Azure-fájlmegosztást a Z meghajtón a PowerShell használatával. Az alábbi parancsok futtatása előtt cserélje le a (z) és a (z) függvényt a `<storage-account-key>` `<storage-account-name>` megadott értékekre, és a "d-t a [Storage-fiók létrehozásakor](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   A PowerShell a következő példához hasonló kimenetet ad vissza:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   Az Azure-fájlmegosztás sikeresen le lett képezve a Z meghajtóra.

9.   Zárja be a *myVmPrivate* virtuális gépre irányuló távoli asztali munkamenetet.

## <a name="confirm-access-is-denied-to-storage-account"></a>Tárfiókhoz való hozzáférés letiltásának ellenőrzése

1. Írja be a *myVmPublic* kifejezést a portál tetején található **Erőforrások, szolgáltatások és dokumentumok keresése** mezőbe.
2. Amikor a **myVmPublic** megjelenik a keresési eredmények között, válassza ki.
3. A *myVmPublic* virtuális gép [Storage-fiókjához való hozzáférésének](#confirm-access-to-storage-account) ellenőrzéséhez hajtsa végre a fenti 1-8. lépést.

   Kis idő elteltével a rendszer a következő hibaüzenetet adja vissza: `New-PSDrive : Access is denied`. A hozzáférést a rendszer megtagadja, mert a *myVmPublic* virtuális gép a *Nyilvános* alhálózaton van üzembe helyezve. A *Nyilvános* alhálózat nem rendelkezik az Azure Storage-hoz engedélyezett szolgáltatásvégponttal. A tárfiók kizárólag a *Magánjellegű* alhálózatról engedélyezi a hozzáférést, a *Nyilvános* alhálózatról nem.

4. Zárja be a távoli asztali munkamenetet a *myVmPublic* virtuális géppel.
5. Lépjen vissza a Azure Portal a korábban létrehozott, egyedileg elnevezett Storage-fiókra.
6. A file Service (Fájlszolgáltatások) területen válassza a **fájlmegosztás**, a *saját fájl-megosztás*, korábban létrehozva lehetőséget.
7. A következő hibaüzenetnek kell megjelennie:

   ![A hozzáférés megtagadva hiba](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)
   
>[!NOTE] 
> A hozzáférést a rendszer megtagadja, mert a számítógép nem a *MyVirtualNetwork* virtuális hálózat *Magánjellegű* alhálózatában található.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezett egy szolgáltatásvégpontot egy virtuális hálózat alhálózatához. Megtudta, hogy a több Azure-szolgáltatásból üzembe helyezett erőforrások számára szolgáltatásvégpontokat engedélyezhet. Létrehozott egy Azure Storage-fiókot, és egy adott virtuális hálózati alhálózat erőforrásaira korlátozta a tárfiók felé irányuló hálózati hozzáférést. További információkat a szolgáltatásvégpontokról a [szolgáltatásvégpontok áttekintését](virtual-network-service-endpoints-overview.md) és az [alhálózatok kezelését](virtual-network-manage-subnet.md) ismertető cikkekben olvashat.

Ha több virtuális hálózat található a fiókjában, érdemes lehet összekapcsolni két virtuális hálózatot, hogy az egyes virtuális hálózatokban található erőforrások kommunikálhassanak egymással. Annak megismeréséhez, hogyan kapcsolhatók össze virtuális hálózatok, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Virtuális hálózatok csatlakoztatása](./tutorial-connect-virtual-networks-portal.md)
