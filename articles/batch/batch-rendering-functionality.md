---
title: Renderelési képességek
description: A standard Azure Batch funkciói a renderelési munkaterhelések és alkalmazások futtatására szolgálnak. A Batch speciális funkciókat tartalmaz a renderelési feladatok támogatásához.
author: mscurrell
ms.author: markscu
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: a2e2cfb71999bd5ab83591448342d4bac1dabdd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496337"
---
# <a name="azure-batch-rendering-capabilities"></a>Renderelési képességek Azure Batch

A standard Azure Batch funkciói a renderelési munkaterhelések és alkalmazások futtatására szolgálnak. A Batch a renderelési munkaterhelések támogatásához is tartalmaz bizonyos funkciókat.

A Batch-fogalmak, például a készletek, a feladatok és a feladatok áttekintését [ebben a cikkben](./batch-service-workflow-features.md)találja.

## <a name="batch-pools-using-custom-vm-images-and-standard-application-licensing"></a>Egyéni virtuálisgép-rendszerképeket és normál alkalmazások licencelését használó batch-készletek

Az egyéb számítási feladatokhoz és az alkalmazások típusaihoz hasonlóan egyéni virtuálisgép-rendszerkép is létrehozható a szükséges renderelési alkalmazásokkal és beépülő modulokkal. Az egyéni virtuálisgép-rendszerkép a [megosztott rendszerkép](../virtual-machines/shared-image-galleries.md) -katalógusba kerül, és [Batch-készletek létrehozásához is használható](batch-sig-images.md).

A feladat parancssori karakterláncának az egyéni virtuálisgép-rendszerkép létrehozásakor használt alkalmazásokra és elérési utakra kell hivatkoznia.

A legtöbb renderelési alkalmazáshoz meg kell adni a licenckiszolgáló által beszerzett licenceket. Ha van meglévő helyszíni licenckiszolgáló, akkor a készletnek és a licenckiszolgálóra is ugyanazon a [virtuális hálózaton](../virtual-network/virtual-networks-overview.md)kell lennie. Az is lehetséges, hogy egy Azure-beli virtuális GÉPEN futtatja a licenckiszolgálót, és a Batch-készlet és a licenckiszolgáló virtuális gépe ugyanazon a virtuális hálózaton található.

## <a name="batch-pools-using-rendering-vm-images"></a>Batch-készletek a virtuálisgép-rendszerképek renderelésével

> [!IMPORTANT]
> A renderelési virtuálisgép-lemezképek és a használati licencek [elavultak, és az 2024. február 29-én](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/)megszűnik. A Batch for rendering használatához [Egyéni virtuálisgép-rendszerképet és normál alkalmazás-licencelést kell használni.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

### <a name="rendering-application-installation"></a>Alkalmazás telepítésének megjelenítése

Ha csak az előre telepített alkalmazásokat kell használni, az Azure piactéren megjelenített virtuálisgép-rendszerkép is megadható a készlet konfigurációjában.

Egy Windows-rendszerkép és egy CentOS-rendszerkép is rendelkezésre áll.  Az [Azure Marketplace](https://azuremarketplace.microsoft.com)-en a virtuálisgép-lemezképek a "batch rendering" kifejezésre kereshetnek.

Példa a készlet konfigurálására: az [Azure CLI renderelési oktatóanyaga](./tutorial-rendering-cli.md).  A Azure Portal és a Batch Explorer GUI-eszközöket biztosítanak egy renderelési virtuálisgép-rendszerkép kiválasztásához a készlet létrehozásakor.  Ha batch API-t használ, adja meg a következő tulajdonságértékeket a [ImageReference](/rest/api/batchservice/pool/add#imagereference) a készlet létrehozásakor:

| Publisher | Ajánlat | SKU | Verzió |
|---------|---------|---------|--------|
| kötegelt | renderelés – centos73 | renderelési | legújabb |
| kötegelt | renderelés – windows2016 | renderelési | legújabb |

Egyéb beállítások is elérhetők, ha további alkalmazásokra van szükség a készletben lévő virtuális gépeken:

* Egy egyéni rendszerkép a megosztott rendszerkép-katalógusból:
  * így pontosan azokkal az alkalmazásokkal és verziókkal konfigurálhatja a virtuális gépet, amelyekre szüksége van. További információt [a készlet létrehozása a megosztott rendszerkép](batch-sig-images.md)-katalógusban című témakörben talál. Az Autodesk és a Chaos csoport módosította az Arnold és a V-Ray-t, hogy érvényesítse a Azure Batch licencelési szolgáltatását. Győződjön meg arról, hogy ezen alkalmazások verziói rendelkeznek ezzel a támogatással, ellenkező esetben a használaton kívüli licencelés nem fog működni. A Maya vagy a 3ds Max jelenlegi verzióiban nem szükséges a licenckiszolgáló a fej (batch/parancssori mód) futtatásakor. Ha nem tudja, hogyan folytassa ezt a lehetőséget, forduljon az Azure ügyfélszolgálatához.
* [Alkalmazáscsomag](./batch-application-packages.md):
  * Csomagolja az alkalmazás fájljait egy vagy több ZIP-fájl használatával, töltse fel a Azure Portalon keresztül, és a csomag a készlet konfigurációjában legyen megadva. A készletben lévő virtuális gépek létrehozásakor a rendszer letölti a ZIP-fájlokat, és Kinyeri a fájlokat.
* Erőforrások fájljai:
  * Az alkalmazásfájlok az Azure Blob Storage-ba lesznek feltöltve, és a [készlet indítási tevékenységében](/rest/api/batchservice/pool/add#starttask)a fájlokra mutató hivatkozások is megadhatók. A készletben lévő virtuális gépek létrehozásakor az erőforrás-fájlok minden virtuális gépre letöltődnek.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Használaton kívüli licencelés az előre telepített alkalmazásokhoz

A használni kívánt alkalmazásokat és a licencelési díjat meg kell adni a készlet konfigurációjában.

* `applicationLicenses` [Készlet létrehozásakor](/rest/api/batchservice/pool/add#request-body)a tulajdonság megadása.  A következő értékek megadhatók a karakterláncok tömbben: "Ray", "Arnold", "3dsmax", "Maya".
* Egy vagy több alkalmazás megadásakor a rendszer hozzáadja az alkalmazások költségeit a virtuális gépekhez.  Az alkalmazások árai a [Azure batch díjszabási oldalon](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)találhatók.

> [!NOTE]
> Ha ehelyett egy licenckiszolgálóra csatlakozik a renderelési alkalmazások használatához, ne határozza meg a `applicationLicenses` tulajdonságot.

Az Azure Portal vagy Batch Explorer használatával kiválaszthatja az alkalmazásokat, és megjelenítheti az alkalmazások árát.

Ha egy alkalmazás használatára történt kísérlet, de az alkalmazás nem lett megadva a `applicationLicenses` készlet konfigurációjának tulajdonságában, vagy nem éri el a licenckiszolgálót, akkor az alkalmazás végrehajtása licencelési hiba és nem nulla értékű kilépési kóddal meghiúsul.

### <a name="environment-variables-for-pre-installed-applications"></a>Az előre telepített alkalmazások környezeti változói

Ahhoz, hogy létre tudja hozni a parancssort a renderelési feladatokhoz, meg kell adni a renderelési alkalmazás végrehajtható fájljainak telepítési helyét.  A rendszerkörnyezet változói az Azure Marketplace virtuálisgép-rendszerképein lettek létrehozva, és nem kell tényleges elérési utakat megadni.  Ezek a környezeti változók az egyes feladatokhoz létrehozott [szabványos batch környezeti változók](./batch-compute-node-environment-variables.md) mellett vannak.

|Alkalmazás|Alkalmazás végrehajtható fájlja|Környezeti változó|
|---------|---------|---------|
|Autodesk 3ds Max 2021|3dsmaxcmdio.exe|3DSMAX_2021_EXEC|
|Autodesk Maya 2020|render.exe|MAYA_2020_EXEC|
|Chaos Group V-Ray önálló|vray.exe|VRAY_4 VRAY_4.10.03_EXEC|
|Arnold 2020 parancssor|kick.exe|ARNOLD_2020_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

## <a name="azure-vm-families"></a>Azure VM-családok

Más számítási feladatokhoz hasonlóan a renderelési alkalmazások rendszerkövetelményei eltérőek, és a teljesítményre vonatkozó követelmények a feladatok és a projektek esetében eltérőek.  Számos virtuálisgép-család elérhető az Azure-ban a követelményektől függően – a legalacsonyabb költség, a legjobb ár/teljesítmény, a legjobb teljesítmény és így tovább.
Egyes renderelési alkalmazások, például Arnold, CPU-alapúak; mások, például a V-Ray és a Blender ciklusok a processzorokat és/vagy a GPU-t is használhatják.
A rendelkezésre álló virtuálisgép-családok és a virtuálisgép-méretek leírását [lásd: VM-típusok és-méretek](../virtual-machines/sizes.md).

## <a name="low-priority-vms"></a>Alacsony prioritású virtuális gépek

Más számítási feladatokhoz hasonlóan az alacsony prioritású virtuális gépek is használhatók a rendereléshez a Batch-készletekben.  Az alacsony prioritású virtuális gépek ugyanúgy teljesítik a normál dedikált virtuális gépeket, mint a felesleges Azure-kapacitást, és nagy kedvezményekhez is elérhetők.  Az alacsony prioritású virtuális gépek használatának kompromisszuma az, hogy ezek a virtuális gépek nem érhetők el, vagy bármikor előzik, a rendelkezésre álló kapacitástól függően. Emiatt az alacsony prioritású virtuális gépek nem lesznek megfelelőek az összes renderelési feladathoz. Ha például a képek több órát is igénybe vesznek, akkor valószínű, hogy a képek megjelenítését megszakították, és a virtuális gépek előzik miatt újraindulnak.

Az alacsony prioritású virtuális gépek jellemzőivel és a Batch használatával történő konfigurálásának különböző módjaival kapcsolatos további információkért lásd: [alacsony prioritású virtuális gépek használata a Batch](./batch-low-pri-vms.md)szolgáltatással.

## <a name="jobs-and-tasks"></a>Feladatok és tevékenységek

A feladatokhoz és a feladatokhoz nem szükségesek megjelenítésre vonatkozó támogatás.  A fő konfigurációs elem a feladat parancssora, amelynek a szükséges alkalmazásra kell hivatkoznia.
Az Azure Marketplace virtuálisgép-rendszerképeinek használatakor az ajánlott eljárás az, hogy a környezeti változók használatával adja meg az elérési utat és az alkalmazás végrehajtható fájlját.

## <a name="next-steps"></a>Következő lépések

A Batch rendering példái között próbálja ki a két oktatóanyagot:

* [Megjelenítés az Azure CLI használatával](./tutorial-rendering-cli.md)
* [Renderelés a Batch Explorer használatával](./tutorial-rendering-batchexplorer-blender.md)