---
title: 'Adatimportálás: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan tölthetők be adatok egy gépi tanulási folyamatba a meglévő felhőalapú adatszolgáltatásokból a Azure Machine Learning adatimportálási moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/13/2020
ms.openlocfilehash: 69d27c102ca059974da87224e44f0ad7aa103fff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "94592634"
---
# <a name="import-data-module"></a>Adatmodul importálása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal az adatok betölthetők egy gépi tanulási folyamatba a meglévő felhőalapú adatszolgáltatásokból. 

> [!Note]
> A modul által biztosított összes funkció az **adattár** és az **adatkészletek** segítségével végezhető el a munkaterület üzemelő példányának kezdőlapján. Javasoljuk, hogy az adattárat és az **adatkészletet** **használja, amely** olyan további funkciókat is tartalmaz, mint az adatfigyelés További információk: [az adathozzáférés](../how-to-access-data.md) és [az adatkészletek regisztrálása](../how-to-create-register-datasets.md) című cikk.
> Az adatkészlet regisztrálása után megkeresheti **azt az adatkészletek**  ->  **saját adatkészletek** kategóriájában a Designer felületen. Ez a modul Studio (klasszikus) felhasználók számára van fenntartva ismerős élmény érdekében. 
>

Az **adatimportálási** modul támogatja az adatok olvasását az alábbi forrásokból:

- URL-cím HTTP-n keresztül
- Azure Cloud Storage adattárolók [**használatával)**](../how-to-access-data.md)
    - Azure Blob-tároló
    - Azure-fájlmegosztás
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL Database
    - Azure PostgreSQL    

A felhőalapú tárolás használata előtt először regisztrálnia kell egy adattárat a Azure Machine Learning-munkaterületen. További információ: [az adatok elérése](../how-to-access-data.md). 

A kívánt adatok definiálása és a forráshoz való kapcsolódás után az **[adatok importálása](./import-data.md)** az egyes oszlopok adattípusát következteti ki a benne foglalt értékek alapján, és betölti az adatait a tervezői folyamatba. Az **importálási adat** kimenete egy olyan adatkészlet, amely bármely tervezői folyamattal használható.

Ha a forrásadatok megváltoznak, frissítheti az adatkészletet, és új adatokat adhat hozzá az [importálási adatokat](./import-data.md)újrafuttatva.

> [!WARNING]
> Ha a munkaterület virtuális hálózatban található, akkor konfigurálnia kell az adattárolókat a tervező adatvizualizációs funkcióinak használatához. További információ az adattárolók és adatkészletek virtuális hálózatban való használatáról: [Azure Machine learning Studio használata Azure-beli virtuális hálózaton](../how-to-enable-studio-virtual-network.md).


## <a name="how-to-configure-import-data"></a>Az importálási adatgyűjtés konfigurálása

1. Adja hozzá az **adatimportálási** modult a folyamathoz. Ezt a modult a tervező **adatbevitel és kimenet** kategóriájában találja.

1. Válassza ki a modult a jobb oldali ablaktábla megnyitásához.

1. Válassza **az adatforrás lehetőséget, majd** válassza ki az adatforrás típusát. Ez lehet HTTP vagy adattár.

    Ha az adattár lehetőséget választja, akkor kiválaszthatja azokat a meglévő adattárolókat, amelyek már regisztrálva vannak a Azure Machine Learning munkaterületen, vagy létrehozhat egy új adattárat. Ezután adja meg az adattárba importálandó adatelérési utat. Az elérési **út tallózására kattintva könnyedén** böngészhet az elérési úton ![ .](media/module/import-data-path.png)

    > [!NOTE]
    > Az **adatimportálási** modul csak **táblázatos** adatokat szolgál.
    > Ha egyszerre több táblázatos adatfájlt szeretne importálni, akkor a következő feltételeket kell megadnia, ellenkező esetben hibák történnek:
    > 1. Ha a mappában lévő összes adatfájlt bele szeretné foglalni, meg kell adnia `folder_name/**` az **elérési utat**.
    > 2. Az összes adatfájlnak Unicode-8 kódolással kell rendelkeznie.
    > 3. Az összes adatfájlnak azonos számú oszloppal és oszlopnevek kell szerepelnie.
    > 4. Több adatfájl importálásának eredménye a több fájlból származó összes sor összefűzése sorrendben.

1. Válassza ki az előnézeti sémát a felvenni kívánt oszlopok szűréséhez. A speciális beállításokat, például a határolójeleket is megadhatja a beállítások elemzésekor.

    ![Importálás – adatelőnézet](media/module/import-data.png)

1. A **kimenet újragenerált** jelölőnégyzete határozza meg, hogy végrehajtja-e a modult a kimenet újralétrehozásához a futási időben. 

    Alapértelmezés szerint nincs kiválasztva, ami azt jelenti, hogy ha a modult korábban ugyanazokkal a paraméterekkel hajtották végre, a rendszer a legutóbbi futtatásból a futási idő csökkentése érdekében újra fogja használni a kimenetet. 

    Ha be van jelölve, a rendszer ismét végrehajtja a modult a kimenet újralétrehozásához. Ezért válassza ezt a lehetőséget, ha a tárterületen alapuló adatokat frissíti a rendszer, így a legfrissebb adatokat is beolvashatja.


1. A folyamat elküldése.

    Amikor az adatok importálása betölti az adatok betöltését a tervezőbe, az egyes oszlopok adattípusát az általa tartalmazott értékek alapján, számszerű vagy kategorikusan következteti ki.

    Ha van fejléc, a fejléc a kimeneti adatkészlet oszlopainak elnevezésére szolgál.

    Ha az adatsorokban nem találhatók oszlopfejlécek, az új oszlopnevek a col1, a col2,... formátum használatával jönnek létre. , Coln *.

## <a name="results"></a>Results (Eredmények)

Ha az importálás befejeződött, kattintson a jobb gombbal a kimeneti adatkészletre, és válassza a **Megjelenítés** lehetőséget, hogy megtekintse az adatokat az importálás sikeres volt-e.

Ha újra szeretné menteni az adatokat, ahelyett, hogy az új adatkészletet importálja a folyamat minden egyes futtatásakor, válassza az **adatkészlet regisztrálása** ikont a modul jobb oldali paneljének **kimenetek és naplók** lapján. Válassza ki az adatkészlet nevét. A mentett adatkészlet megőrzi az adatokat a mentéskor, az adatkészlet nem frissül a folyamat újrafuttatásakor, még akkor is, ha megváltozik a folyamat adatkészlete. Ez hasznos lehet az adatpillanatképek készítésekor.

Az információk importálása után szükség lehet néhány további modellezési és elemzési előkészületre:

- Használja a [metaadatok szerkesztése](./edit-metadata.md) lehetőséget az oszlopnevek módosításához, egy oszlop más adattípusként való kezeléséhez, illetve annak jelzéséhez, hogy egyes oszlopok címkék vagy szolgáltatások.

- Az [adatkészletben az Oszlopok kiválasztása](./select-columns-in-dataset.md) lehetőséggel kiválaszthatja a modellezéshez átalakítani vagy használni kívánt oszlopok részhalmazát. Az átalakított vagy eltávolított oszlopok könnyedén csatlakoztathatók az eredeti adatkészlethez az [Oszlopok hozzáadása](./add-columns.md) modullal.  

- A [Partition és a Sample](./partition-and-sample.md) használatával ossza fel az adatkészletet, végezzen mintavételezést, vagy töltse le az első n sort.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
