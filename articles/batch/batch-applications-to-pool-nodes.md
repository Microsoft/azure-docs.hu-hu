---
title: Alkalmazások és az alkalmazások másolása a készlet csomópontjaira
description: Ismerje meg, hogyan másolhat alkalmazásokat és információkat a készlet csomópontjaira.
ms.topic: how-to
ms.date: 02/18/2021
ms.openlocfilehash: 0109171fd78dc11058daa30bf4604bebc1eeb857
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703647"
---
# <a name="copy-applications-and-data-to-pool-nodes"></a>Alkalmazások és az alkalmazások másolása a készlet csomópontjaira

Azure Batch számos módszert támogat az adatok és alkalmazások számítási csomópontokra történő beolvasására, hogy azok a feladatok számára elérhetők legyenek.

A választott módszer a fájl vagy alkalmazás hatókörének függvényében változhat. Előfordulhat, hogy az adatok és alkalmazások futtatásához a teljes feladatot futtatni kell, ezért minden csomóponton telepítve kell lennie. Egyes fájlokhoz vagy alkalmazásokhoz csak egy adott feladathoz lehet szükség. Előfordulhat, hogy másokat is telepíteni kell a feladathoz, de nem kell minden csomóponton lennie. A Batch eszközöket tartalmaz az egyes forgatókönyvekhez.

## <a name="determine-the-scope-required-of-a-file"></a>A fájlhoz szükséges hatókör meghatározása

Meg kell határoznia egy fájl hatókörét – egy készlethez, feladathoz vagy feladathoz szükséges fájl. A készletre hatókörben lévő fájloknak készlet-alkalmazáscsomag vagy indítási tevékenység használatát kell használniuk. A feladatra hatókörrel rendelkező fájloknak feladat-előkészítési feladatot kell használniuk. A készleten vagy a feladatok szintjén lévő fájlok jó példája az alkalmazások. A feladathoz tartozó fájloknak a feladat-erőforrás fájljait kell használniuk.

## <a name="pool-start-task-resource-files"></a>Készlet indítási tevékenységének erőforrás-fájljai

A készlet minden csomópontján telepítendő alkalmazásokhoz vagy adatokhoz használja a készlet indítási tevékenységének erőforrás-fájljait. A telepítési parancs végrehajtásához használja ezt a metódust vagy egy [alkalmazáscsomag](batch-application-packages.md) vagy az indítási tevékenység erőforrásfájl-gyűjteményét.  

Az alkalmazások áthelyezéséhez vagy telepítéséhez használhatja például a Start Task parancssort. Megadhatja egy Azure Storage-fiókban található fájlok vagy tárolók listáját is. További információ: [# ResourceFile hozzáadása a REST dokumentációban](/rest/api/batchservice/pool/add#resourcefile).

Ha a készleten futó összes feladat egy olyan alkalmazást (. exe) futtat, amelyet először egy. msi fájllal kell telepítenie, akkor az indítási tevékenység **Várakozás a sikerességi** tulajdonság **igaz** értékre kell állítania. További információ: [# StartTask hozzáadása a REST dokumentációban](/rest/api/batchservice/pool/add#starttask).

## <a name="application-package-references"></a>Alkalmazáscsomag-referenciák

A készlet minden csomópontján telepítendő alkalmazásokhoz vagy adathoz az [alkalmazáscsomag](batch-application-packages.md)használatát érdemes használni. Nincs hozzárendelve telepítési parancs egy alkalmazáscsomag számára, de az indítási tevékenységgel bármilyen telepítési parancsot futtathat. Ha az alkalmazás nem igényel telepítést, vagy nagy mennyiségű fájlt tartalmaz, ezt a metódust használhatja.

Az alkalmazáscsomag akkor hasznos, ha nagy számú fájlt használ, mert sok fájl hivatkozását egy kis adattartalomhoz kombinálhatja. Ha több mint 100 különálló erőforrást próbál felvenni egyetlen feladatba, előfordulhat, hogy a Batch szolgáltatás egy adott feladat belső rendszerkorlátain kívül esik. Az alkalmazáscsomag akkor is hasznos, ha ugyanazon alkalmazás számos különböző verziójával rendelkezik, és ezek közül kell választania.

## <a name="job-preparation-task-resource-files"></a>Feladat-előkészítési feladat erőforrás-fájljai

Olyan alkalmazásokhoz vagy adatokhoz, amelyeket telepíteni kell a feladat futtatásához, de nem kell a teljes készletre telepíteni, érdemes a [feladat-előkészítési feladat erőforrás-fájljait](./batch-job-prep-release.md)használni.

Ha például a készlet számos különböző típusú feladattal rendelkezik, és a futtatáshoz csak egy. msi-fájlra van szükség, akkor érdemes a telepítési lépést egy feladat-előkészítési feladatba helyezni.

## <a name="task-resource-files"></a>Feladat-erőforrás fájljai

A feladat-erőforrás fájljai akkor megfelelőek, ha az alkalmazás vagy az adatok csak egy adott feladatra vonatkoznak.

Előfordulhat például, hogy öt tevékenységgel rendelkezik, amelyek mindegyike egy másik fájlt dolgoz fel, majd a kimenetet blob Storage-tárolóba írja. ebben az esetben a bemeneti fájlt meg kell adni a Task Resource Files gyűjteményben, mivel minden egyes feladathoz saját bemeneti fájl tartozik.

## <a name="additional-ways-to-get-data-onto-nodes"></a>További módszerek az információk csomópontokra való beolvasására

Mivel Ön felügyeli Azure Batch csomópontokat, és futtathat egyéni végrehajtható fájlokat, tetszőleges számú egyéni forrásból lekérheti az adatok lekérését. Győződjön meg arról, hogy a Batch-csomópont kapcsolódik a célhoz, és hogy a csomóponton a forráshoz tartozó hitelesítő adatokkal rendelkezik.

Néhány példa arra, hogyan lehet az adatok kötegelt csomópontokra való átvitele:

- Adatok letöltése az SQL-ből
- Adatok letöltése más webszolgáltatásokból/egyéni helyekről
- Hálózati megosztás hozzárendelése

## <a name="azure-storage"></a>Azure Storage tárterület

Ne feledje, hogy a blob Storage a méretezhetőségi célokat tölti le. Az Azure Storage fájlmegosztási skálázhatósági céljai megegyeznek egyetlen blob esetében. A méret hatással lesz a szükséges csomópontok és készletek számára.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók az [alkalmazáscsomag batch](batch-application-packages.md)használatával történő használatáról.
- További információ a [csomópontok és a készletek használatáról](nodes-and-pools.md).
