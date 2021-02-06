---
title: Tesztkörnyezet beállítása a ArcMap\ArcGIS Desktophoz a Azure Lab Services | Microsoft Docs
description: Megtudhatja, hogyan állíthat be labort az osztályok számára a ArcGIS használatával.
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: dbe4191b64773b71bc1ae04842d824fbfead8e55
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628152"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>Labor beállítása a ArcMap\ArcGIS Desktophoz

A [ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) egy földrajzi információs rendszer (GIS) típusa.  A ArcGIS segítségével térképeket make\analyze, és a [környezeti rendszerekkel](https://www.esri.com/en-us/home) foglalkozó Kutatóintézet (ESRI) által biztosított földrajzi információkkal dolgozhat.  Bár a ArcGIS Desktop számos alkalmazást tartalmaz, ez a cikk bemutatja, hogyan állíthatja be a laborokat a ArcMap használatához.  A [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) a 2D-térképek készítésére, szerkesztésére és elemzésére szolgál.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja

Ha meg szeretné kezdeni a tesztkörnyezet beállítását a ArcMap használatához, szüksége lesz egy Azure-előfizetésre és egy labor-fiókra.  Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services.  Az új Labor-fiókok létrehozásával kapcsolatos további információkért lásd: [labor-fiók beállítása](tutorial-setup-lab-account.md).  Használhat meglévő labor-fiókot is.

### <a name="lab-account-settings"></a>Tesztkörnyezet-Fiókbeállítások

Engedélyezze a labor-fiókok beállításait az alábbi táblázatban leírtak szerint.  Az Azure Marketplace-lemezképek engedélyezésével kapcsolatos további információkért lásd: [Az Azure Marketplace-lemezképek elérhetővé tétele a labor-készítők](https://docs.microsoft.com/azure/lab-services/specify-marketplace-images)számára.

| Tesztkörnyezet-fiók beállítása | Utasítások |
| ------------------- | ------------ |
|Marketplace-beli rendszerkép| Engedélyezze a Windows 10 Pro vagy a Windows 10 Pro N rendszerképet a labor-fiókjában való használatra.|

### <a name="licensing-server"></a>Licencelési kiszolgáló

A ArcGIS Desktop által kínált licencelési típus [egyidejű használati licenc](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm).  Ehhez telepítenie kell a ArcGIS License Managert a licenckiszolgálóra.  A License Manager nyomon követi a szoftver azon példányainak számát, amelyek egyszerre futtathatók.  A License Manager kiszolgálón való beállításával kapcsolatos további információkért tekintse meg a [License Manager útmutatóját](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm).

A licenckiszolgáló általában a helyszíni hálózatban található, vagy egy Azure-beli virtuális hálózaton belül fut.  A licenckiszolgáló beállítása után meg kell adnia [a virtuális hálózatot](https://docs.microsoft.com/azure/lab-services/how-to-connect-peer-virtual-network) a [labor-fiókjával](https://docs.microsoft.com/azure/lab-services/tutorial-setup-lab-account).  A tesztkörnyezet létrehozása előtt el kell végeznie a hálózati társítást, hogy a labor virtuális gépei hozzáférhessenek a licenckiszolgálóhoz, és fordítva.

További információ: [Licenckiszolgáló beállítása megosztott erőforrásként](how-to-create-a-lab-with-shared-resource.md).

### <a name="lab-settings"></a>Tesztkörnyezet beállításai

A ArcGIS Desktop használatára javasolt virtuális gép (VM) mérete az alkalmazásoktól, a bővítménytől, valamint a tanulók által használt adott verzióktól függ.  A virtuális gép mérete a tanulók által várhatóan végrehajtandó munkaterheléstől is függ.  A virtuális gép méretének azonosításához tekintse meg a [ArcGIS asztali rendszerkövetelményeit](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm) ismertető témakört.  Ha azonosította a lehetséges virtuálisgép-méretet, javasoljuk, hogy tesztelje a tanulók munkaterheléseit a megfelelő teljesítmény biztosítása érdekében.

Ebben a cikkben azt javasoljuk, hogy a [ **közepes** méretű virtuálisgép-méretet](administrator-guide.md#vm-sizing) a ArcMap verziójának [10.7.1](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm)használja, feltéve, hogy nincs más ArcGIS asztali bővítmény használva.  Azonban az osztály igényeitől függően **nagy** vagy akár **Small\Medium GPU (vizualizáció)** virtuálisgép-méretre is szükség lehet.  Például a ArcGIS Desktopban található [térbeli elemző bővítmény](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm) támogatja a GPU-t a jobb teljesítmény érdekében, de nem szükséges GPU-t használni.

| Tesztkörnyezet-beállítás | Érték és leírás |
| ------------ | ------------------ |
|Virtuális gép mérete| **Közepes méretű**.  Legmegfelelőbb a kapcsolódó adatbázisokhoz, a memóriában történő gyorsítótárazáshoz és az elemzésekhez.|  

### <a name="template-machine"></a>Sablon számítógép

A jelen szakaszban ismertetett lépések bemutatják, hogyan állíthatja be a sablon virtuális gépet:

1.  Indítsa el a sablon virtuális gépet, és csatlakozzon a géphez RDP használatával.

2.  Töltse le és telepítse a ArcGIS asztali összetevőket az ESRI-től származó utasítások használatával.  Ezek a lépések többek között a licenckezelő hozzárendelésének egyidejű használatára való engedélyezése: 
    - [A ArcGIS Desktop telepítésének és konfigurálásának bemutatása](https://desktop.arcgis.com/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  Külső biztonságimásolat-tároló beállítása diákoknak.  A diákok közvetlenül a hozzárendelt virtuális gépre menthetik a fájlokat, mivel az általuk végrehajtott módosítások a munkamenetek között lesznek mentve.  Azt javasoljuk azonban, hogy a tanulók a saját virtuális gépén kívüli tárhelyre biztonsági másolatot készítenek a munkájuk alól a következő okok miatt:
    - Annak lehetővé tétele, hogy a tanulók az osztály és a labor vége után hozzáférjenek a munkához.  
    - Ha a tanuló rossz állapotba állítja a virtuális gépet, és a rendszerképét [alaphelyzetbe kell állítani](how-to-set-virtual-machine-passwords.md#reset-vms).

    A ArcGIS minden tanulónak a következő fájlokról kell biztonsági másolatot készítenie az egyes munkamunkamenetek végén:

    - mxd-fájl, amely egy projekt elrendezési információit tárolja.
    - Fájl geodatabases, amely a ArcGIS által előállított összes adattárolást tárolja.
    - Bármely más, a tanuló által használható, például raszteres fájlok, alakzatfájlok, GeoTIFF stb.

    Javasoljuk, hogy a OneDrive használatát a biztonsági mentési tárterülethez.  A OneDrive a sablon virtuális gépen való beállításához kövesse a [OneDrive telepítése és konfigurálása](how-to-prepare-windows-template.md#install-and-configure-onedrive)című cikkben ismertetett lépéseket. 

4.  Végül [tegye közzé](how-to-create-manage-template.md#publish-the-template-vm) a sablon virtuális gépet a tanuló virtuális gép létrehozásához.

### <a name="auto-shutdown-and-disconnect-settings"></a>Automatikus leállítás és a beállítások leválasztása

A labor [automatikus leállítási és leválasztási beállításai](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) segítenek meggyőződni arról, hogy a tanuló virtuális gépe leáll, amikor nincs használatban.  Ezeket a beállításokat a tanulók által elvégezhető munkaterhelések típusai alapján kell beállítani, hogy a virtuális gép ne álljon le a munkájuk közepén.  Például a **felhasználók leválasztása, ha a virtuális gépek üresjárati** beállítással leválasztják a tanulót az RDP-munkamenetből, miután az egér vagy a billentyűzet bemenete nem észlelhető a megadott időtartamra.  Ennek a beállításnak elegendő időt kell biztosítania a munkaterhelések számára, ha a tanuló nem aktívan használja az egeret vagy a billentyűzetet, például hosszú lekérdezéseket futtathat, vagy a renderelésre vár.

A ArcGIS esetében a következő értékeket javasoljuk a beállításokhoz:
- Felhasználók leválasztása, ha a virtuális gépek üresjáratban vannak
    - a rendszer 30 perccel az inaktív állapot után észlelt
- Virtuális gépek leállítása a felhasználók leválasztásakor
    - 15 perccel a felhasználó leválasztása után

## <a name="cost"></a>Költségek

Az osztályra vonatkozó lehetséges költségbecslés. Ez a becslés nem tartalmazza a licenckiszolgáló futtatásának költségeit. 25 tanulós osztályt fogunk használni. 20 órányi ütemezett idő van. Emellett minden tanuló 10 órás kvótát kap a házi feladat vagy az ütemezett osztályon kívüli hozzárendelések számára. A kiválasztott virtuálisgép-méret **közepes**, ami 42 labor egység.

25 tanuló \* (20 ütemezett óra + 10 kvóta óra) \* 42 labor egység * 0,01 USD/óra = 315,00 USD

>[!IMPORTANT]
> A költségbecslés csak példaként szolgál.  A díjszabással kapcsolatos aktuális információk: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Következő lépések

A következő lépések közösek a laborok beállításához.

- [Sablon létrehozása és kezelése](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users)