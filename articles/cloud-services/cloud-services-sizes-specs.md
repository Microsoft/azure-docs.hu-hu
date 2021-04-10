---
title: Virtuális gépek méretei az Azure Cloud Serviceshez (klasszikus) | Microsoft Docs
description: Felsorolja az Azure Cloud Service webes és feldolgozói szerepköreinek különböző virtuálisgép-méreteit (és azonosítóit).
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: a31c9c4747ccc72f0552ebe2f8daf85ef5fba8a3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015301"
---
# <a name="sizes-for-cloud-services-classic"></a>Cloud Services (klasszikus) Méretek

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

Ez a témakör a Cloud Service-példányok (webes szerepkörök és feldolgozói szerepkörök) elérhető méreteit és beállításait ismerteti. Emellett üzembe helyezési megfontolásokat is biztosít, hogy az erőforrások használatának megtervezésekor vegye figyelembe a használatát. Minden mérethez tartozik egy azonosító, amelyet a [szolgáltatás definíciós fájljába](cloud-services-model-and-package.md#csdef)helyez. Az egyes méretek árai a [Cloud Services díjszabási](https://azure.microsoft.com/pricing/details/cloud-services/) oldalon érhetők el.

> [!NOTE]
> A kapcsolódó Azure-korlátok megtekintéséhez tekintse meg az [Azure-előfizetés és a szolgáltatás korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md) című témakört.
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>A webes és feldolgozói szerepkör példányainak mérete
Az Azure-ban több standard méret közül választhat. A méretek némelyikével kapcsolatos megfontolások a következők:

* A D-sorozat virtuális gépei nagyobb számítási teljesítményt és ideiglenes lemezteljesítményt igénylő alkalmazások futtatására lettek kialakítva. A D-sorozat virtuális gépei gyorsabb processzorokat, nagyobb magonkénti memóriaarányt, valamint az ideiglenes lemezteljesítményhez SSD meghajtókat kínálnak. Részletekért lásd az Azure blogon megjelent bejelentést [a D-sorozat új virtuális gépméreteit](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) ismertető bejegyzésben.
* A Dv3 sorozat, az Dv2 sorozat, az eredeti D sorozatnak megfelelő, hatékonyabb PROCESSZORokkal rendelkezik. A Dv2-sorozathoz használt processzor mintegy 35%-kal gyorsabb, mint a D-sorozathoz használt processzorok. A legújabb generációs 2,4 GHz-es Intel Xeon® E5-2673 v3 (Haswell) processzoron alapul, és a teljesítménye az Intel Turbo Boost Technology 2.0 alkalmazásával akár 3,1 GHz-re is növelhető. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.
* A G-sorozat virtuális gépei kínálják a legtöbb memóriát, és az Intel Xeon E5 V3 család processzorait tartalmazó gazdagépeken futnak.
* Az A sorozatú virtuális gépek különböző hardveres típusokra és processzorokra telepíthetők. A méret szabályozása a hardver alapján történik, hogy konzisztens processzor-teljesítményt nyújtson a futó példány számára, függetlenül a telepített hardvertől. Az adott méretet futtató fizikai hardver meghatározásához kérdezze le a virtuális hardvert a virtuális gépen belülről.
* Az A0 méret esetében a fizikai hardvernek az ideálisnál több előfizetést kell kezelnie. Ennek az egy méretnek az esetében a többi felhasználói üzemelő példány befolyásolhatja a futó számítási feladat teljesítményét. A relatív teljesítmény várható alapértéke az alábbiak szerint alakul, hozzávetőleg 15 százalékos varianciával.

A virtuális gépek mérete befolyásolja az árképzést. A méret emellett hatással van a virtuális gép feldolgozási teljesítményére, valamint a memória- és tárhelykapacitására is. A tárolási díjak számítása ettől külön történik a tárfiókban használt oldalak mennyisége alapján. Részletekért lásd: [Cloud Services díjszabás](https://azure.microsoft.com/pricing/details/cloud-services/) és az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/).

Az alábbiak segíthetnek a megfelelő méret kiválasztásában:

* Az A8–A11- és a H-sorozat méretei más néven *nagy számítási igényű példányokként* ismertek. Az ezeket a méreteket futtató hardver a nagy számítási és hálózatigényű alkalmazások futtatására lett kialakítva és optimalizálva, ide értve a nagy teljesítményű feldolgozási (HPC) fürtalkalmazásokat, a modellezést és a szimulációkat. Az A8–A11-sorozat Intel Xeon E5-2670 @ 2,6 GHz-es, a H-sorozat pedig Intel Xeon E5-2667 v3 @ 3,2 GHz-es processzorokat használ. A méretek használatával kapcsolatos részletes információkért és szempontokért lásd: [nagy teljesítményű számítási](../virtual-machines/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)virtuálisgép-méretek.
* A Dv3 sorozat, a Dv2 sorozat, a D sorozat, a G sorozat, ideális olyan alkalmazások számára, amelyek gyorsabb processzorokat, jobb helyi lemez-teljesítményt vagy nagyobb memóriát igényelnek. Nagyon hatékony kombinációt kínálnak számos nagyvállalati szintű alkalmazáshoz.
* Az Azure-központokban lévő némelyik fizikai gazdagép nem képes futtatni a nagyobb virtuálisgép-méreteket, például az A5–A11 méreteket. Ennek eredményeképpen előfordulhat, hogy a hibaüzenet **nem tudta konfigurálni a (z) {Machine Name} virtuális gépet** , vagy **nem tudta létrehozni a (z) {Machine Name} virtuális** gépet a meglévő virtuális gép új méretre való átméretezése során. hozzon létre egy új virtuális gépet egy, a 2013. április 16. előtt létrehozott virtuális hálózaton. új virtuális gép hozzáadása egy meglévő felhőalapú szolgáltatáshoz. Lásd a következő [hibát: "nem sikerült konfigurálni a virtuális gépet"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) a támogatási fórumon az egyes telepítési forgatókönyvekre vonatkozó megkerülő megoldásokhoz.
* Az előfizetés is korlátozhatja az egyes családokban üzembe helyezhető magok mennyiségét. A kvóták növelésével kapcsolatban vegye fel a kapcsolatot az Azure ügyfélszolgálatával.

## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások
Létrehozta az Azure számítási egység (ACU) koncepcióját, amely lehetővé teszi a számítási (CPU-) teljesítmény összehasonlítását az Azure-beli SKU-ban, valamint annak azonosítását, hogy melyik SKU a legnagyobb valószínűséggel kielégíti a teljesítménybeli igényeket.  Az ACU jelenlegi standard alapjaként a Kisméretű (Standard_A1) virtuális gép 100-as értéket képvisel, és a többi termékváltozat értéke ehhez képest jelöli, hogy mennyivel gyorsabban futtatja az adott termékváltozat a standard teljesítménytesztet.

> [!IMPORTANT]
> Az ACU csupán iránymutatóként szolgál. Az egyes számítási terhelések eredményei ettől eltérhetnek.
>
>

<br>

| Termékváltozat-család | ACU/mag |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Kis ExtraLarge](#a-series) |100 |
| [A5 – 7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [Egy v2](#av2-series) |100 |
| [D](#d-series) |160 |
| [D v2](#dv2-series) |210 - 250* |
| [D v3](#dv3-series) |160 – 190 * |
| [E v3](#ev3-series) |160 – 190 * |
| [G](#g-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

A * jelzésű ACU-értékek Intel® Turbo technológia használatával növelik a processzor órajelét, és ezáltal a teljesítményt. A növekedés mértéke a virtuális gép méretétől és terhelésétől, valamint az ugyanazon a gazdagépen futó számítási feladatoktól függően eltérő lehet.

## <a name="size-tables"></a>Mérettáblázatok
Az alábbi táblázatokban a méretek és azok kapacitásai láthatóak.

* A tárolókapacitás mértékegysége GiB (gibibájt = 1024^3 bájt). A gigabájtban (1000^3 bájt) és a gibibájtban (1024^3 bájt) mért meghajtók összehasonlításakor tartsa észben, hogy a GiB-ban kifejezett kapacitások kisebbnek tűnhetnek. Például: 1023 GiB = 1098,4 GB
* A lemezteljesítmény másodpercenkénti bemeneti/kimeneti műveletek (IOPS) mennyiségeként van kifejezve, valamint MBps-ben, ahol 1 MBps = 10^6 bájt/másodperc.
* Az adatlemezek gyorsítótárazott és gyorsítótárazás nélküli módban üzemelhetnek. Gyorsítótárazott adatlemezüzem esetében a gazdagép gyorsítótáras üzemmódja **ReadOnly** (Csak olvasás) vagy **ReadWrite** (Írás és olvasás) beállításra van konfigurálva. Gyorsítótárazás nélküli adatlemezüzem esetében a gazdagép gyorsítótáras üzemmódja **None** (Nincs) beállításra van konfigurálva.
* A maximális hálózati sávszélesség a virtuálisgép-típusonként kiosztott és hozzárendelt maximális összesített sávszélesség. A maximális sávszélesség iránymutatást nyújthat a megfelelő VM-típus kiválasztása során, hogy biztosítható legyen a megfelelő hálózati kapacitás rendelkezésre állása. Az alacsony, közepes, magas és nagyon magas érték közötti váltáskor az átviteli sebesség ennek megfelelően növekszik. A tényleges hálózati teljesítmény számos tényezőtől függ, többek közt a hálózat és az alkalmazás terhelésétől, valamint az alkalmazás hálózati beállításaitól.

## <a name="a-series"></a>A-sorozat
| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tárolás: GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmall      | 1         | 0.768        | 20                   | 1/alacsony |
| Kicsi           | 1         | 1,75         | 225                  | 1/közepes |
| Közepes          | 2         | 3.5          | 490                  | 1/közepes |
| Nagy           | 4         | 7            | 1000                 | 2/magas |
| ExtraLarge      | 8         | 14           | 2040                 | 4/magas |
| A5              | 2         | 14           | 490                  | 1/közepes |
| A6              | 4         | 28           | 1000                 | 2/magas |
| A7              | 8         | 56           | 2040                 | 4/magas |

## <a name="a-series---compute-intensive-instances"></a>A-sorozat – nagy számítási igényű példányok
A méretek használatával kapcsolatos információkért és szempontokért lásd: [nagy teljesítményű számítási VM-méretek](../virtual-machines/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tárolás: GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8             |8          | 56           | 1817                 | 2/magas |
| A9             |16         | 112          | 1817                 | 4/nagyon magas |
| A10             |8          | 56           | 1817                 | 2/magas |
| A11             |16         | 112          | 1817                 | 4/nagyon magas |

\*RDMA-kompatibilis

## <a name="av2-series"></a>Av2-sorozat

| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tároló (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1/közepes                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2/közepes                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4/magas                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8/magas                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2/közepes                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4/magas                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8/magas                     |


## <a name="d-series"></a>D-sorozat
| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tároló (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1         | 3.5          | 50                   | 1/közepes |
| Standard_D2     | 2         | 7            | 100                  | 2/magas |
| Standard_D3     | 4         | 14           | 200                  | 4/magas |
| Standard_D4     | 8         | 28           | 400                  | 8/magas |
| Standard_D11    | 2         | 14           | 100                  | 2/magas |
| Standard_D12    | 4         | 28           | 200                  | 4/magas |
| Standard_D13    | 8         | 56           | 400                  | 8/magas |
| Standard_D14    | 16        | 112          | 800                  | 8/nagyon magas |

## <a name="dv2-series"></a>Dv2-sorozat
| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tároló (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3.5          | 50                   | 1/közepes |
| Standard_D2_v2  | 2         | 7            | 100                  | 2/magas |
| Standard_D3_v2  | 4         | 14           | 200                  | 4/magas |
| Standard_D4_v2  | 8         | 28           | 400                  | 8/magas |
| Standard_D5_v2  | 16        | 56           | 800                  | 8/rendkívül magas |
| Standard_D11_v2 | 2         | 14           | 100                  | 2/magas |
| Standard_D12_v2 | 4         | 28           | 200                  | 4/magas |
| Standard_D13_v2 | 8         | 56           | 400                  | 8/magas |
| Standard_D14_v2 | 16        | 112          | 800                  | 8/rendkívül magas |
| Standard_D15_v2 | 20        | 140          | 1,000                | 8/rendkívül magas |

## <a name="dv3-series"></a>Dv3-sorozat

| Méret            | Processzormagok | Memória: GiB   | Ideiglenes tároló (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_D2_v3  | 2         | 8             | 50                   | 2/közepes |
| Standard_D4_v3  | 4         | 16            | 100                  | 2/magas |
| Standard_D8_v3  | 8         | 32            | 200                  | 4/magas |
| Standard_D16_v3 | 16        | 64            | 400                  | 8/rendkívül magas |
| Standard_D32_v3 | 32        | 128           | 800                  | 8/rendkívül magas |
| Standard_D48_v3 | 48        | 192           | 1200                 | 8/rendkívül magas |
| Standard_D64_v3 | 64        | 256           | 1600                 | 8/rendkívül magas |

## <a name="ev3-series"></a>Ev3-sorozat

| Méret            | Processzormagok | Memória: GiB   | Ideiglenes tároló (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_E2_v3  | 2         | 16            | 50                   | 2/közepes |
| Standard_E4_v3  | 4         | 32            | 100                  | 2/magas |
| Standard_E8_v3  | 8         | 64            | 200                  | 4/magas |
| Standard_E16_v3 | 16        | 128           | 400                  | 8/rendkívül magas |
| Standard_E32_v3 | 32        | 256           | 800                  | 8/rendkívül magas |
| Standard_E48_v3 | 48        | 384           | 1200                 | 8/rendkívül magas |
| Standard_E64_v3 | 64        | 432           | 1600                 | 8/rendkívül magas |


## <a name="g-series"></a>G-sorozat
| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tároló (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1/magas |
| Standard_G2     | 4         | 56           | 768                  |2/magas |
| Standard_G3     | 8         | 112          | 1,536                |4/nagyon magas |
| Standard_G4     | 16        | 224          | 3,072                |8/rendkívül magas |
| Standard_G5     | 32        | 448          | 6,144                |8/rendkívül magas |

## <a name="h-series"></a>H-sorozat
Az Azure H-sorozat virtuális gépei a nagy teljesítményű feldolgozásra képes virtuális gépek következő generációja, amelyek olyan magas szintű számítási igényekre lettek kialakítva, mint a molekuláris modellezés vagy a folyadékdinamikai számítások. Ez a 8 és 16 Magos virtuális gép az Intel Haswell E5-2667 v3 processzor-technológiára épül, amely DDR4 memóriával és helyi SSD-alapú tárolással rendelkezik.

A jelentős CPU-teljesítmény mellett a H-sorozat különféle lehetőségeket kínál a kis késésű RDMA-hálózatkezeléshez az FDR InfiniBand használatával, valamint számos memóriakonfigurációt is a memóriaigényes számítási követelmények támogatására.

| Méret            | Processzormagok | Memória: GiB  | Ideiglenes tároló (SSD): GiB       | Hálózati adapterek max. száma/hálózati sávszélesség |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8/magas |
| Standard_H16    | 16        | 112          | 2000                 | 8/nagyon magas |
| Standard_H8m    | 8         | 112          | 1000                 | 8/magas |
| Standard_H16m   | 16        | 224          | 2000                 | 8/nagyon magas |
| Standard_H16r*  | 16        | 112          | 2000                 | 8/nagyon magas |
| Standard_H16mr* | 16        | 224          | 2000                 | 8/nagyon magas |

\*RDMA-kompatibilis

## <a name="configure-sizes-for-cloud-services"></a>Méretek konfigurálása Cloud Serviceshoz
A szerepkör-példány virtuálisgép-méretét a [szolgáltatás definíciós fájlja](cloud-services-model-and-package.md#csdef)által leírt szolgáltatási modell részeként adhatja meg. A szerepkör mérete határozza meg a CPU-magok számát, a memória kapacitását, valamint a futó példányhoz lefoglalt helyi fájlrendszeri méretet. Válassza ki a szerepkör méretét az alkalmazás erőforrás-követelménye alapján.

Íme egy példa arra, hogy a szerepkör mérete Standard_D2 egy webes szerepkör-példányhoz:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Meglévő szerepkör méretének módosítása

A számítási feladatok változásai vagy az új virtuálisgép-méretek elérhetővé válásának jellegéből adódóan érdemes lehet módosítani a szerepkör méretét. Ehhez módosítania kell a virtuálisgép-méretet a szolgáltatás definíciós fájljában (ahogy fent látható), újra kell csomagolnia a felhőalapú szolgáltatást, és telepítenie kell.

>[!TIP]
> Érdemes lehet különböző virtuálisgép-méreteket használni a szerepkörhöz különböző környezetekben (például test vs termelés). Ennek egyik módja, ha több szolgáltatás-definíciós (. csdef) fájlt hoz létre a projektben, majd a CSPack eszköz használatával környezetekben különböző felhőalapú szolgáltatási csomagokat hoz létre az automatizált buildek során. Ha többet szeretne megtudni a Cloud Services-csomagok elemeiről és azok létrehozásáról, tekintse meg [a mi a Cloud Services-modell, és hogyan csomagolja ki?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Méretek listájának lekérése
A PowerShell vagy a REST API használatával lekérheti a méretek listáját. A [REST API dokumentálva van.](/previous-versions/azure/reference/dn469422(v=azure.100)) A következő kód egy PowerShell-parancs, amely felsorolja Cloud Services összes rendelkezésre álló méretét. 

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize, RoleSizeLabel
```

## <a name="next-steps"></a>Következő lépések
* Ismerje meg [az Azure-előfizetések és -szolgáltatások korlátozásait, kvótáit és megkötéseit](../azure-resource-manager/management/azure-subscription-service-limits.md).
* További [Tudnivalók a nagy teljesítményű számítási virtuálisgép-méretekről](../virtual-machines/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) a HPC-munkaterhelések esetében.
