---
title: Azure-Blobok visszaállítása
description: Ismerje meg, hogyan állíthatja vissza az Azure-blobokat (előzetes verzióban).
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 4cbd47ea654115f00095e30f7d5114aec0f2c85a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101745520"
---
# <a name="restore-azure-blobs-in-preview"></a>Azure-Blobok visszaállítása (előzetes verzió)

A tárolási fiókokba tartozó, az operatív biztonsági mentéssel konfigurált Blobok blokkolása a megőrzési időtartamon belül bármely időpontra visszaállítható. Azt is megteheti, hogy visszaállítja a visszaállításokat a Storage-fiókban található összes blobra vagy a Blobok egy részhalmazára.

## <a name="before-you-start"></a>Előkészületek

- A Blobok ugyanarra a Storage-fiókba lesznek visszaállítva. Így a rendszer felülírja azokat a blobokat, amelyek a visszaállítási idő óta módosultak.
- Egy visszaállítási művelet részeként csak a standard általános célú v2-es Storage-fiókban található Blobok állíthatók vissza. A Blobok, a blobok és a prémium blokk Blobok hozzáfűzése nem állítható vissza.
- A visszaállítási feladatok folyamatban vannak, a tárolóban lévő Blobok nem olvashatók és nem írhatók.
- Az aktív bérlettel rendelkező Blobok nem állíthatók vissza. Ha egy aktív bérlettel rendelkező blob szerepel a visszaállítani kívánt Blobok tartományában, a visszaállítási művelet automatikusan meghiúsul. A visszaállítási művelet megkezdése előtt szüntesse meg az aktív címbérletek megadását.
- A pillanatképek létrehozása vagy törlése a visszaállítási művelet részeként nem történt meg. A rendszer csak az alap blobot állítja vissza az előző állapotába.
- Ha töröl egy tárolót a Storage-fiókból a **tároló törlése** művelet meghívásával, a tároló nem állítható vissza visszaállítási művelettel. A teljes tároló törlése helyett törölje az egyes blobokat, ha később szeretné visszaállítani őket. A Microsoft emellett azt is javasolja, hogy az üzemeltetési biztonsági mentés mellett engedélyezze a tárolók törlését a tárolók véletlen törlésével szembeni védelem érdekében.
- Tekintse át a [támogatási mátrixot](blob-backup-support-matrix.md) az összes korlátozás és támogatott forgatókönyv esetében.

## <a name="restore-blobs"></a>Blobok visszaállítása

A biztonsági mentési központban kezdeményezheti a visszaállítást.

1. A Backup Centerben lépjen a **visszaállítás** elemre a felső sávon.

    ![Visszaállítás a Backup Centerben](./media/blob-restore/backup-center-restore.png)

1. A **visszaállítás indítása** lapon válassza az **Azure-Blobok (Azure Storage)** lehetőséget az adatforrás típusaként, és válassza ki a visszaállítani kívánt **biztonsági mentési példányt** . A biztonsági mentési példány itt az a Storage-fiók, amely a visszaállítani kívánt blobokat tartalmazza.

     ![Biztonsági mentési példány kiválasztása](./media/blob-restore/select-backup-instance.png)

1. A **helyreállítási pont kiválasztása** lapon válassza ki azt a dátumot és időpontot, amelynek az adatait vissza szeretné állítani. A csúszka segítségével is kiválaszthatja, hogy melyik időpontban szeretné visszaállítani a szolgáltatást. A dátum melletti információs buborék mutatja azt az érvényességi időtartamot, amelyből vissza lehet állítani az adatokat. A Blobok folyamatos biztonsági mentésének működési biztonsági mentése részletesen szabályozhatja az adatok helyreállításához szükséges pontokat.

    >[!NOTE]
    > Az itt látható idő a helyi idő.

    ![Visszaállítás dátuma és időpontja](./media/blob-restore/date-and-time.png)

1. A **visszaállítási paraméterek** lapon adja meg, hogy szeretné-e visszaállítani a Storage-fiókban lévő összes blobot, a megadott tárolókat vagy a Blobok egy részhalmazát az előtag-egyeztetés használatával. Előtag egyezésének használatakor legfeljebb 10 tartományt adhat meg az előtagok vagy a filepaths. Az előtag-egyezés használatáról [itt talál](#use-prefix-match-for-restoring-blobs)további információt.

    ![Visszaállítási paraméterek](./media/blob-restore/restore-parameters.png)

    Válasszon egyet a következő lehetőségek közül:

    - **A Storage-fiókban lévő összes blob visszaállítása**: ezzel a beállítással visszaállíthatja a Storage-fiókban lévő összes blokk blobot a kijelölt időpontra való visszalépéssel. A nagy mennyiségű adatmennyiséget vagy a magas adatváltozást tanúsító Storage-fiókok esetében a visszaállítás hosszabb időt vehet igénybe.

    - **Kiválasztott tárolók tallózása és helyreállítása**: ezzel a beállítással megkeresheti és kiválaszthatja a legfeljebb 10 tárolót a visszaállításhoz. A Storage-fiókban található tárolók megtekintéséhez megfelelő engedélyekkel kell rendelkeznie, különben előfordulhat, hogy nem fogja tudni megtekinteni a Storage-fiók tartalmát.

    - **Válassza ki a blobokat az előtag-egyezés használatával történő visszaállításhoz**: Ez a beállítás lehetővé teszi a Blobok egy részhalmazának visszaállítását egy előtag-egyezés használatával. Akár 10 lexicographical-tartományt is megadhat egyetlen tárolóban vagy több tárolóban, hogy a blobokat egy adott időpontban visszaadja az előző állapotába. A következő szempontokat érdemes szem előtt tartani:

        - A perjel (/) használatával megszabhatja a tároló nevét a blob-előtagból
        - A megadott tartomány kezdete, de a megadott tartomány kizárólagos.

    További információ az előtagok a blob-tartományok visszaállítására való használatáról: [ebben a szakaszban](#use-prefix-match-for-restoring-blobs).

1. Ha befejezte a visszaállítani kívánt Blobok megadását, folytassa a **felülvizsgálat + visszaállítás** lapon, majd a visszaállítás indításához válassza a **visszaállítás** lehetőséget.

1. **Visszaállítás nyomon követése**: a **biztonsági mentési feladatok** nézet használatával nyomon követheti a visszaállított adatok részleteit és állapotát. Ehhez navigáljon a biztonsági mentési **központ**  >  **biztonsági mentési** feladataihoz. Az állapot a visszaállítás végrehajtása közben jelenik **meg** .

    ![Biztonsági mentési feladatok lap](./media/blob-restore/backup-jobs.png)

    Ha a visszaállítási művelet sikeresen befejeződött, az állapot **Befejezettre** változik. Miután a visszaállítás sikeresen befejeződik, újra elolvashatja és megírhatja a blobokat a Storage-fiókban.

## <a name="additional-topics"></a>További témakörök

### <a name="use-prefix-match-for-restoring-blobs"></a>A Blobok visszaállításához használja az előtag egyezését

Tekintse meg a következő példát:

![Visszaállítás előtag-egyeztetéssel](./media/blob-restore/prefix-match.png)

A rendszerképben látható visszaállítási művelet a következő műveleteket hajtja végre:

- Visszaállítja a *container1* teljes tartalmát.
- Visszaállítja a blobokat a lexicographical tartomány *blob1* a container2 *blob5* keresztül . Ez a tartomány a blobokat olyan nevekkel állítja vissza, mint például a *blob1*, a *blob11*, a *blob100*, a *blob2* és így tovább. Mivel a tartomány vége kizárólagos, visszaállítja a blobokat, amelyek nevei a *blob4*-vel kezdődnek, de nem állítja vissza azokat a blobokat, amelyek nevei a *blob5*-vel kezdődnek.
- Visszaállítja az összes blobot a *container3* és a *container4*. Mivel a tartomány vége kizárólagos, ez a tartomány nem állítja vissza a *container5*.

## <a name="next-steps"></a>Következő lépések

- [Azure-Blobok operatív biztonsági mentésének áttekintése (előzetes verzió)](blob-backup-overview.md)
