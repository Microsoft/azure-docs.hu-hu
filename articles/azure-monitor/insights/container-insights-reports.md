---
title: Azure Monitor-jelentések tárolók számára
description: A Azure Monitor által a tárolók számára gyűjtött adatok elemzéséhez rendelkezésre álló jelentéseket ismerteti.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 94709cf4a8b579447c11f91f9bc6863ab4b4fe08
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582318"
---
# <a name="reports-in-azure-monitor-for-containers"></a>Azure Monitor-jelentések tárolók számára
Az Azure Monitor for containers szolgáltatásban lévő jelentések ajánlott, beépített Azure- [munkafüzetek](../visualize/workbooks-overview.md). Ez a cikk az elérhető különböző jelentéseket és azok elérését ismerteti.

## <a name="viewing-reports"></a>Jelentések megtekintése
A Azure Portal **Azure monitor** menüjében válassza a **tárolók** lehetőséget. A **figyelés** szakaszban válassza ki az adatokat, **válasszon ki egy** adott fürtöt, majd válassza a **jelentések (előzetes verzió)** lapot. 

[![Jelentések lap](media/container-insights-reports/reports-page.png)](media/container-insights-reports/reports-page.png#lightbox)

## <a name="create-a-custom-workbook"></a>Egyéni munkafüzet létrehozása
Ha ezen munkafüzetek bármelyikén alapuló egyéni munkafüzetet szeretne létrehozni, válassza a **munkafüzetek megtekintése** legördülő menüt, majd a legördülő lista alján **lépjen** a következőre: A munkafüzetekkel és a munkafüzet-sablonokkal kapcsolatos további információkért lásd: [Azure monitor munkafüzetek](../visualize/workbooks-overview.md) .

[![AK-gyűjtemény](media/container-insights-reports/aks-gallery.png)](media/container-insights-reports/aks-gallery.png#lightbox)

## <a name="node-workbooks"></a>Csomópont-munkafüzetek

- **Lemezterület**: az interaktív lemezhasználat diagramjai a tárolóban a csomóponton a következő perspektívák szerint bemutatott minden lemez esetében:

    - Az összes lemez százalékos kihasználtsága.
    - Szabad lemezterület az összes lemez számára.
    - Egy rács, amely megjeleníti az egyes csomópontok lemezeit, a felhasznált terület százalékos arányát, a felhasznált terület százalékos arányát, a szabad lemezterületet (GiB) és a szabad lemezterület (GiB) trendjét. Ha a táblázatban egy sor van kiválasztva, a rendszer a felhasznált terület és a szabad lemezterület (GiB) százalékos arányát jeleníti meg a sor alatt.

- **Lemez i/o**: az interaktív lemezhasználat-diagramok a tárolóban a csomóponton a következő perspektívák szerint bemutatott lemezek esetében:

    - Lemez I/O összegzése az összes lemezről olvasási sebesség (bájt/s), bájt/mp írás, olvasás és írás bájt/mp tendenciák.
    - Nyolc teljesítményű diagramon a fő teljesítménymutatók láthatók a lemez I/O-szűk keresztmetszetek mérése és azonosítása érdekében.

- **GPU**: interaktív GPU-használati diagramok minden GPU-kompatibilis Kubernetes-fürtcsomóponton.

## <a name="resource-monitoring-workbooks"></a>Erőforrás-figyelési munkafüzetek

- **Központi telepítések**: az üzemelő példányok állapota & vízszintes Pod automéretező (hPa), beleértve az egyéni hPa-t is. 
  
- **Munkaterhelés részletei**: interaktív diagramok, amelyek a névtér számítási feladatainak statisztikáit mutatják be. Több fület is tartalmaz:

  - A CPU-és memóriahasználat áttekintése a POD használatával.
  - POD/Container állapot, amely a POD újraindítási trendjét, a tároló újraindítási trendjét, valamint a hüvelyek tároló állapotát mutatja.
  - A vezérlőhöz tartozó események összegzését bemutató Kubernetes.

- **Kubelet**: két rácsot tartalmaz, amelyek a fő csomópont működési statisztikáit mutatják:

    - A Node Grid áttekintés a teljes művelet, a teljes hibák és a sikeres műveletek összesítésével összegzi az egyes csomópontok százalékát és trendjét.
    - Az áttekintés a műveleti típus szerint összefoglalja a teljes művelet, az összes hiba és a sikeres műveletek százalékos és trend szerinti műveleteit.
## <a name="billing-workbooks"></a>Számlázási munkafüzetek

- **Adathasználat**: megkönnyíti az adatok forrásának megjelenítését anélkül, hogy saját könyvtárat kellene létrehoznia a dokumentációban osztozó lekérdezésekből. Ebben a munkafüzetben vannak olyan diagramok, amelyekkel megtekintheti a számlázható adatait az alábbi szempontok szerint:

  - A GB-ban betöltött számlázható összes adat a megoldás szerint
  - Tároló-naplók által betöltött számlázható adatmennyiség (alkalmazás-naplók)
  - A számlázható tároló a Kubernetes-névtér által betöltött adatot naplózza
  - Számlázható tároló naplózza a fürt neve szerint elkülönítve tárolt adatmennyiségeket
  - Logsource-bejegyzés által betöltött számlázható tároló naplózási adata
  - Diagnosztikai főcsomóponti naplók által betöltött számlázható diagnosztikai adatok

## <a name="networking-workbooks"></a>Hálózati munkafüzetek

- **NPM-konfiguráció**: a hálózati házirend-kezelőn (NPM) keresztül konfigurált hálózati konfigurációk figyelése.

  - Az általános konfiguráció bonyolultságával kapcsolatos összefoglaló információk.
  - A szabályzat, a szabály és a készlet az idő múlásával lehetővé teszi, hogy betekintést kapjon a három és a konfiguráció hibakereséséhez szükséges idő dimenzióval.
  - A bejegyzések száma az összes IPSets és az egyes IPSet.
  - Az összetevőknek a hálózati konfigurációhoz való hozzáadására szolgáló csomópontok legrosszabb és átlagos teljesítménye.

- **Hálózat**: az egyes csomópontok hálózati adapteréhez tartozó interaktív hálózati kihasználtsági diagramok, a rács pedig a fő teljesítménymutatókat mutatja be a hálózati adapterek teljesítményének méréséhez.



## <a name="next-steps"></a>Következő lépések

- A Azure Monitor munkafüzetekkel kapcsolatos részletekért tekintse meg [Azure monitor munkafüzeteket](../visualize/workbooks-overview.md) .
