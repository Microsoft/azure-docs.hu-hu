---
title: Az Azure Backup díjszabása
description: Ismerje meg, hogyan végezhet költségbecslést az Azure Backup díjszabásával kapcsolatban.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 03ec0076d3089562ddaace6db413fb3f1ba949a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88654531"
---
# <a name="azure-backup-pricing"></a>Az Azure Backup díjszabása

Az Azure Backup díjszabásával kapcsolatban lásd az [Azure Backup díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/backup/).

## <a name="download-detailed-estimates-for-azure-backup-pricing"></a>Részletes becslések letöltése az Azure Backup díjszabása alapján

Ha költségvetési vagy árösszevetési célokból szeretne becslést készíteni, töltse le a részletes [Azure Backup díjszabásbecslőt](https://aka.ms/AzureBackupCostEstimates).  

### <a name="what-does-the-estimator-contain"></a>Mit tartalmaz a becslő?

Az Azure Backup költségbecslő lapja lehetővé teszi az összes olyan számítási feladat becslését, amelyekről biztonsági másolatot szeretne készíteni az Azure Backup használatával. Ezek közé a számítási feladatok közé tartoznak a következők:

- Azure-beli virtuális gépek
- Helyszíni kiszolgálók
- SQL az Azure-beli virtuális gépeken
- SAP HANA az Azure-beli virtuális gépeken
- Azure-fájlmegosztások

## <a name="estimate-costs-for-backing-up-azure-vms-or-on-premises-servers"></a>Az Azure-beli virtuális gépek vagy helyszíni kiszolgálók biztonsági mentésének költségbecslése

Az Azure-beli virtuális gépek vagy helyszíni kiszolgálók Azure Backuppal végzett biztonsági mentési költségeinek megbecsléséhez a következő paraméterekre lesz szüksége:

- Azon virtuális gépek vagy helyszíni kiszolgálók mérete, amelyekről biztonsági másolatot szeretne készíteni
  - Adja meg azon lemezek vagy kiszolgálók kihasznált területének méretét, amelyekről biztonsági másolatot kíván készíteni

- Az ilyen méretű kiszolgálók száma

- Mi az adatváltozás várt mértéke ezeken a kiszolgálókon?<br>
  Az adatváltozás azt jelenti, hogy milyen mennyiségű adatmódosítás várható. Ha például egy virtuális gépen 200 GB adatról szeretne biztonsági másolatot készíteni, és ebből 10 GB változik minden nap, akkor a napi változás 5%.

  - A nagyobb változási arány azt jelenti, hogy több adatról kell biztonsági másolatot készíteni.

  - Válassza az **Alacsony** vagy a **Közepes** értéket a fájlkiszolgálókhoz, és a **Magas** értéket, ha adatbázisokat futtat.

  - Ha ismeri a **változás százalékát**, használhatja a **saját adatváltozási százalék megadására** szolgáló lehetőséget.

- Válassza ki a biztonsági mentési szabályzatot

  - Várhatóan mennyi ideig lesznek megőrizve a „napi” biztonsági másolatok? (napokban)

  - Várhatóan mennyi ideig lesznek megőrizve a „heti” biztonsági másolatok? (hetekben)

  - Várhatóan mennyi ideig lesznek megőrizve a „havi” biztonsági másolatok? (hónapokban)

  - Várhatóan mennyi ideig lesznek megőrizve az „évi” biztonsági másolatok? (években)

  - Várhatóan mennyi ideig lesznek megőrizve az „azonnali visszaállítási pillanatképek”? (1-5 nap)

    - Ezzel a lehetőséggel akár egy hét nappal korábbi állapotról is gyorsan végezhet visszaállítást, lemezeken tárolt pillanatképekről.

- **Választható** – Szelektív lemezes biztonsági mentés

  - Ha a **Szelektív lemezes biztonsági mentést** használja az Azure-beli virtuális gépek biztonsági mentéséhez, válassza a **Lemez kizárása** lehetőséget, és adja meg a biztonsági mentésből kizárt lemezek méretének százalékos arányát. Ha például egy virtuális gép három lemezhez csatlakozik, ahol mindegyik lemezen 200 GB van használatban, és kettőt ki szeretne zárni a biztonsági mentésből, írja be a 66,7% értéket.

- **Választható** – Biztonsági mentési tár redundanciája

  - Ez azon tárfiók redundanciáját jelöli, amelyre a biztonsági mentés adatai kerülnek. A legmagasabb rendelkezésre álláshoz a **GRS** használatát javasoljuk. Ez gondoskodik róla, hogy a biztonsági mentés adatainak másolata egy másik régióban legyen tárolva, így több megfelelőségi szabvány követelményeinek is megfelelhet. Módosítsa a redundanciát **LRS** értékre, ha vállalati szintű biztonsági mentést nem igénylő fejlesztési vagy tesztelési környezetekről készít biztonsági másolatot. Válassza a lapon az **RAGRS** lehetőséget, ha tisztában szeretne lenni a költségekkel, amikor engedélyezett a [régiók közötti visszaállítás](backup-azure-arm-restore-vms.md#cross-region-restore) a biztonsági másolatokhoz.

- **Választható** – Regionális díjszabás módosítása vagy kedvezményes díjszabás érvényesítése

  - Ha egy másik régióra vagy kedvezményes díjakra vonatkozó becsléseket szeretne megtekinteni, válassza az **Igen** lehetőséget a **másik régióra vonatkozó becslések kipróbálásának** lehetőségénél, és adja meg azokat a díjszabásokat, amelyekkel a becsléseket futtatni szeretné.

## <a name="estimate-costs-for-backing-up-sql-servers-in-azure-vms"></a>Azure-beli virtuális gépeken futó SQL-kiszolgálók biztonsági mentésével kapcsolatos költségek becslése

Az Azure-beli virtuális gépeken futó SQL-kiszolgálók Azure Backuppal végzett biztonsági mentési költségeinek megbecsléséhez a következő paraméterekre lesz szüksége:

- Azon SQL-kiszolgálók mérete, amelyekről biztonsági másolatot szeretne készíteni

- Az ilyen méretű SQL-kiszolgálók száma

- Mi az SQL-kiszolgálók biztonsági mentési adatainak várt tömörítési aránya?

  - A legtöbb Azure Backup-ügyfél azt tapasztalja, hogy a biztonsági mentési adatok tömörítése 80%-os az SQL-kiszolgáló méretéhez képest, amikor az SQL-tömörítés **engedélyezve van**.

  - Ha más tömörítési arányt szeretne, írja be a számot ebbe a mezőbe.

- Mi a naplók biztonsági másolatának várt mérete?

  - A % a napi mentett naplóméretet jelzi az SQL-kiszolgáló méretének százalékában megadva.

- Mi a várt napi adatváltozás ezeken a kiszolgálókon?

  - Általában az adatbázisokon „magas” az adatváltozás mértéke.

  - Ha ismeri a **változás százalékát**, használhatja a **saját adatváltozási százalék megadására** szolgáló lehetőséget.

- Válassza ki a biztonsági mentési szabályzatot

  - Biztonsági mentés típusa

    - A leghatékonyabb választható szabályzat a **Napi különbözeti**, heti/havi/éves teljes biztonsági mentésekkel. Az Azure Backup egyetlen kattintással visszaállítást tud végezni a különbözeti mentésekből.

    - Napi/heti/havi/éves teljes biztonsági mentést megkövetelő szabályzatot is választhat. Ez a lehetőség kissé több tárhelyet foglal, mint az első.

  - Várhatóan mennyi ideig lesznek megőrizve a „naplók” biztonsági másolatai? (napokban) [7-35]

  - Várhatóan mennyi ideig lesznek megőrizve a „napi” biztonsági másolatok? (napokban)

  - Várhatóan mennyi ideig lesznek megőrizve a „heti” biztonsági másolatok? (hetekben)

  - Várhatóan mennyi ideig lesznek megőrizve a „havi” biztonsági másolatok? (hónapokban)

  - Várhatóan mennyi ideig lesznek megőrizve az „évi” biztonsági másolatok? (években)

- **Választható** – Biztonsági mentési tár redundanciája

  - Ez azon tárfiók redundanciáját jelöli, amelyre a biztonsági mentés adatai kerülnek. A legmagasabb rendelkezésre álláshoz a **GRS** használatát javasoljuk. Ez gondoskodik róla, hogy a biztonsági mentés adatainak másolata egy másik régióban legyen tárolva, így több megfelelőségi szabvány követelményeinek is megfelelhet. Módosítsa a redundanciát **LRS** értékre, ha vállalati szintű biztonsági mentést nem igénylő fejlesztési vagy tesztelési környezetekről készít biztonsági másolatot.

- **Választható** – Regionális díjszabás módosítása vagy kedvezményes díjszabás érvényesítése

  - Ha egy másik régióra vagy kedvezményes díjakra vonatkozó becsléseket szeretne megtekinteni, válassza az **Igen** lehetőséget a **másik régióra vonatkozó becslések kipróbálásának** lehetőségénél, és adja meg azokat a díjszabásokat, amelyekkel a becsléseket futtatni szeretné.

## <a name="estimate-costs-for-backing-up-sap-hana-servers-in-azure-vms"></a>Azure-beli virtuális gépeken futó SAP HANA-kiszolgálók biztonsági mentésével kapcsolatos költségek becslése

Az Azure-beli virtuális gépeken futó SAP HANA-kiszolgálók Azure Backuppal végzett biztonsági mentési költségeinek megbecsléséhez a következő paraméterekre lesz szüksége:

- Azon SAP HANA-adatbázisok teljes mérete, amelyekről biztonsági másolatot szeretne készíteni. Ez a SAP HANA által jelentett adatbázisok összesített teljes biztonsági mentési mérete.
- Az ilyen méretű SAP HANA-kiszolgálók száma
- Mi a naplók biztonsági másolatának várt mérete?
  
  - A % az átlagos napi mentett naplóméretet jelzi azon SAP HANA-adatbázisok teljes méretének százalékaként megadva, amelyekről biztonsági másolatot készít a SAP HANA-kiszolgálón.
- Mi a várt napi adatváltozás ezeken a kiszolgálókon?
  - A % az átlagos napi adatváltozási méretet jelzi azon SAP HANA-adatbázisok teljes méretének százalékaként megadva, amelyekről biztonsági másolatot készít a SAP HANA-kiszolgálón.
  - Általában az adatbázisokon „magas” az adatváltozás mértéke.
  - Ha ismeri a **változás százalékát**, használhatja a **saját adatváltozási százalék megadására** szolgáló lehetőséget.
- Válassza ki a biztonsági mentési szabályzatot
  - Biztonsági mentés típusa
    - A leghatékonyabb választható szabályzat a **Napi különbözeti**, **heti/havi/éves** teljes biztonsági mentésekkel. Az Azure Backup egyetlen kattintással visszaállítást tud végezni a különbözeti mentésekből.
    - **Napi/heti/havi/éves** teljes biztonsági mentést megkövetelő szabályzatot is választhat. Ez a lehetőség kissé több tárhelyet foglal, mint az első.
  - Várhatóan mennyi ideig lesznek megőrizve a „naplók” biztonsági másolatai? (napokban) [7-35]
  - Várhatóan mennyi ideig lesznek megőrizve a „napi” biztonsági másolatok? (napokban)
  - Várhatóan mennyi ideig lesznek megőrizve a „heti” biztonsági másolatok? (hetekben)
  - Várhatóan mennyi ideig lesznek megőrizve a „havi” biztonsági másolatok? (hónapokban)
  - Várhatóan mennyi ideig lesznek megőrizve az „évi” biztonsági másolatok? (években)
- **Választható** – Biztonsági mentési tár redundanciája
  
  - Ez azon tárfiók redundanciáját jelöli, amelyre a biztonsági mentés adatai kerülnek. A legmagasabb rendelkezésre álláshoz a **GRS** használatát javasoljuk. Ez gondoskodik róla, hogy a biztonsági mentés adatainak másolata egy másik régióban legyen tárolva, így több megfelelőségi szabvány követelményeinek is megfelelhet. Módosítsa a redundanciát **LRS** értékre, ha vállalati szintű biztonsági mentést nem igénylő fejlesztési vagy tesztelési környezetekről készít biztonsági másolatot.
- **Választható** – Regionális díjszabás módosítása vagy kedvezményes díjszabás érvényesítése
  
  - Ha egy másik régióra vagy kedvezményes díjakra vonatkozó becsléseket szeretne megtekinteni, válassza az **Igen** lehetőséget a **másik régióra vonatkozó becslések kipróbálásának** lehetőségénél, és adja meg azokat a díjszabásokat, amelyekkel a becsléseket futtatni szeretné.
  
## <a name="estimate-costs-for-backing-up-azure-file-shares"></a>Az Azure-fájlmegosztások biztonsági mentésének becsült költségei

Az Azure-fájlmegosztások Azure Backup által nyújtott [pillanatkép-alapú biztonsági mentési megoldással](azure-file-share-backup-overview.md) végzett mentési költségeinek megbecsléséhez a következő paraméterekre lesz szüksége:

- Azon fájlmegosztások mérete (**GB-ban**), amelyekről biztonsági másolatot szeretne készíteni.

- Ha több tárfiók között megoszló fájlmegosztásokról szeretne biztonsági másolatot készíteni, adja meg a fenti méretű fájlmegosztásokat futtató tárfiókok számát.

- Azon fájlmegosztások adatváltozásának várt mennyisége, amelyekről biztonsági másolatot szeretne készíteni. <br>Az adatváltozás azt jelenti, hogy milyen mennyiségű adatmódosítás várható, és közvetlen hatással van a pillanatkép tárolóméretére. Ha például egy fájlmegosztáson 200 GB adatról szeretne biztonsági másolatot készíteni, és ebből 10 GB változik minden nap, akkor a napi változás 5%.
  - A magasabb mértékű adatváltozás azt jelenti, hogy a fájlmegosztás tartalmában minden nap nagy mennyiségű adat változik, így a (csak az adatváltozásokat rögzítő) növekményes pillanatkép mérete is nagyobb lesz.
  - A fájlmegosztás jellemzői és használati adatai alapján válassza az Alacsony (1%), Közepes (3%) vagy Magas (5%) értéket.
  - Ha ismeri a fájlmegosztás pontos **változási százalékát**, kiválaszthatja a **saját adatváltozási százalék megadására** szolgáló lehetőséget a legördülő listából. Határozza meg a napi, heti, havi és éves adatváltozási értékeket (%-ban).

- A tárfiók típusa (standard vagy prémium) és a biztonsági mentési fájlmegosztást tartalmazó tárfiók tárhely-redundanciájának beállítása. <br>Az Azure-fájlmegosztások jelenlegi biztonsági mentési megoldásában a pillanatképeket ugyanaz a tárfiók tárolja, mint a biztonsági mentési fájlmegosztást. Így a pillanatképekkel társított tárolási költség az Azure Files számláiban van feltüntetve, és a biztonsági mentési fájlmegosztást és pillanatképeket tartalmazó tárfiók fióktípusának és redundancia-beállításának megfelelő pillanatkép-díjszabáson alapul.

- Különböző biztonsági másolatok megőrzése
  - Várhatóan mennyi ideig lesznek megőrizve a „napi” biztonsági másolatok? (napokban)
  - Várhatóan mennyi ideig lesznek megőrizve a „heti” biztonsági másolatok? (hetekben)
  - Várhatóan mennyi ideig lesznek megőrizve a „havi” biztonsági másolatok? (hónapokban)
  - Várhatóan mennyi ideig lesznek megőrizve az „évi” biztonsági másolatok? (években)

  Az egyes kategóriák maximális támogatott megőrzési értékeit [az Azure-fájlmegosztás támogatási mátrixában](azure-file-share-support-matrix.md#retention-limits) találja.

- **Választható** – Regionális díjszabás módosítása vagy kedvezményes díjszabás érvényesítése.
  - A becslőben a pillanatkép-tárolás GB-onkénti költségeihez és a védett példány költségeihez beállított alapértelmezett értékek az USA keleti régiójára vonatkoznak. Ha egy másik régióra vagy kedvezményes díjakra vonatkozó becsléseket szeretne megtekinteni, válassza az **Igen** lehetőséget a **másik régióra vonatkozó becslések kipróbálásának** lehetőségénél, és adja meg azokat a díjszabásokat, amelyekkel a becsléseket futtatni szeretné.

## <a name="next-steps"></a>További lépések

[Mi az az Azure App Service?](backup-overview.md)
