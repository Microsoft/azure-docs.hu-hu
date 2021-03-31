---
title: Biztonsági másolatok figyelése a Backup Explorerrel
description: Ez a cikk azt ismerteti, hogyan használható a Backup Explorer a tárolók, előfizetések, régiók és bérlők biztonsági mentéseinak valós idejű figyelésére.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: a50b9ee05be48113221f2a12f968540bd3a00b3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88824412"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Biztonsági másolatok figyelése a Backup Explorerrel

Ahogy a szervezetek egyre több gépet készítenek a felhőbe, egyre fontosabbá válik a biztonsági másolatok hatékony monitorozása. Az első lépés a legjobb módszer, ha egy központi helyet használ a működési adatok nagyméretű ingatlanon való megtekintéséhez.

A Backup Explorer egy beépített Azure Monitor-munkafüzet, amely egyetlen, központi helyen biztosítja Azure Backup ügyfeleknek. A Backup Explorer segítségével figyelheti az operatív tevékenységeket az Azure teljes biztonsági mentési területén, amely a bérlőket, a helyszíneket, az előfizetéseket, az erőforráscsoportokat és a tárolókat is felöleli. A Backup Explorer széles körben a következő képességeket biztosítja:

* **Méretezési szempont**: bemutatjuk azon biztonsági mentési elemek, feladatok, riasztások, szabályzatok és erőforrások összesített nézetét, amelyek még nincsenek konfigurálva a biztonsági mentéshez a teljes ingatlanon.
* **Részletezés elemzése**: az egyes feladatokkal, riasztásokkal, házirendekkel és biztonsági másolati elemekkel kapcsolatos részletes információk megjelenítése egy helyen.
* Végrehajtható **felületek**: a probléma azonosítása után zökkenőmentesen megoldható a megfelelő biztonsági mentési elem vagy Azure-erőforrás.

Ezeket a képességeket az Azure Resource Graph és a Azure Monitor munkafüzetek natív integrációja biztosítja.

> [!NOTE]
>
> * A Backup Explorer jelenleg csak Azure-beli virtuális gépek (VM-EK) esetében érhető el.
> * A Backup Explorer egy operatív irányítópult, amely a biztonsági másolatok adatainak megtekintésére szolgál az elmúlt 7 napban (maximum).
> * A Backup Explorer jelenleg nem támogatott az országos felhőkben.
> * Jelenleg a Backup Explorer sablonjának testreszabása nem támogatott.
> * Az Azure Resource Graph-adatszolgáltatásokban nem ajánlott egyéni automatizálást írni.
> * Jelenleg a Backup Explorer lehetővé teszi, hogy a biztonsági mentéseket legfeljebb 1000 előfizetéssel figyelje (a bérlők között).

## <a name="get-started"></a>Bevezetés

A Backup Explorer eléréséhez nyissa meg a Recovery Services-tárolót, és válassza a **Backup Explorer** hivatkozást az **Áttekintés** ablaktáblán.

![Tároló gyors hivatkozása](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

A hivatkozás kiválasztásával megnyílik a Backup Explorer, amely összesített nézetet biztosít az összes olyan tárolóhoz és előfizetéshez, amelyhez hozzáféréssel rendelkezik. Ha Azure Lighthouse-fiókot használ, megtekintheti az összes olyan bérlőn tárolt adatkapcsolatot, amelyhez hozzáfér. További információkért tekintse meg a cikk végén található "több-bérlős nézetek" című szakaszt.

![Backup Explorer kezdőlapja](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>A Backup Explorer használati esetei

A Backup Explorer több lapot jelenít meg, amelyek mindegyike részletes információkat biztosít egy adott biztonsági mentési összetevőről (például egy biztonsági mentési elemről, feladatról vagy szabályzatról). Ez a szakasz egy rövid áttekintést nyújt az egyes lapokról. A videók minta használati eseteket biztosítanak az egyes biztonsági mentési összetevőkhöz, valamint a rendelkezésre álló vezérlők leírását.

### <a name="the-summary-tab"></a>Az összefoglalás lap

Az **Összefoglalás** lap gyors áttekintést nyújt a Backup Estate általános feltételéről. Megtekintheti például a védett elemek számát, azon elemek számát, amelyekhez nincs engedélyezve a védelem, vagy hogy hány feladat sikeres volt az elmúlt 24 órában.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>A biztonsági mentési elemek lap

Az egyes biztonsági mentési elemeket az előfizetés, a tár és egyéb jellemzők alapján szűrheti és tekintheti meg. A biztonsági másolati elem nevének kiválasztásával megnyithatja az adott elemhez tartozó Azure-ablaktáblát. A táblából például megfigyelheti, hogy az *X* elem utolsó biztonsági mentése meghiúsult. Az *X* lehetőség kiválasztásával megnyithatja az elem **Backup (biztonsági** mentés) paneljét, ahol elindíthat egy igény szerinti biztonsági mentési műveletet.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>A feladatok lap

A **feladatok** lapon megtekintheti az elmúlt 7 napban indított összes feladat részleteit. Itt szűrheti a *feladat művelet*, a *feladat állapota* és a *hibakód* (sikertelen feladatok esetén) szerinti szűrést.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>A riasztások lap

A **riasztások** lapon megtekintheti az elmúlt 7 napban a tárolókban generált összes riasztás részleteit. A riasztások típus szerint szűrhetők (*biztonsági mentési hiba* vagy *visszaállítási hiba*), az aktuális állapot (*aktív* vagy *megoldott*) és a súlyosság (*kritikus*, *Figyelmeztetés* vagy *információ*). Kiválaszthat egy hivatkozást is az Azure-beli virtuális gép ugrásához, és megteheti a szükséges lépéseket.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>A házirendek lap

A **szabályzatok** lapon megtekintheti a biztonsági mentési területen létrehozott biztonsági mentési szabályzatok legfontosabb információit. Megtekintheti az egyes szabályzatokhoz társított elemek számát, valamint a szabályzat által meghatározott megőrzési időtartamot és a biztonsági mentés gyakoriságát is.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>A biztonsági mentés nem engedélyezett lap

A biztonsági mentést engedélyezni kell minden olyan gépen, amely védelmet igényel. A Backup Explorer segítségével a biztonsági mentési rendszergazdák gyorsan azonosíthatják, hogy a szervezet mely számítógépeit még nem védi biztonsági mentés. Ezen információk megtekintéséhez válassza a **biztonsági mentés nem engedélyezett** lapot.

A **biztonsági mentés nem engedélyezett** ablaktáblán a nem védett gépek listáját tartalmazó tábla jelenik meg. A szervezet különböző címkéket rendelhet az üzemi gépekhez és a tesztelési gépekhez, illetve a különböző funkciókat ellátó gépekhez. Mivel a gépek minden egyes osztályának külön biztonsági mentési szabályzatra van szüksége, a címkék alapján történő szűrés segít megtekinteni az egyes alkalmazásokra vonatkozó információkat. Bármely gép nevének kiválasztásával átirányítja a gép **biztonsági mentési** paneljére, ahol kiválaszthatja a megfelelő biztonsági mentési szabályzatot.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exportálás Excelbe

Bármely táblázat vagy diagram tartalmát Excel-táblázatként exportálhatja. A tartalmát a rendszer az-ként exportálja, és a meglévő szűrőket alkalmazza. További táblázatos sorok exportálásához növelheti a lapon megjelenítendő sorok számát az egyes lapok tetején lévő **sorok/lapok** legördülő lista használatával.

## <a name="pin-to-the-dashboard"></a>Rögzítés az irányítópulton

Az egyes táblázatok vagy diagramok tetején található "PIN" ikon kiválasztásával rögzítheti azt a Azure Portal irányítópulton. Az adatok rögzítésével létrehozhat egy testreszabott irányítópultot, amely az Ön számára legfontosabb információk megjelenítésére van szabva.

## <a name="cross-tenant-views"></a>Több-bérlős nézetek

Ha egy Azure Lighthouse-felhasználó delegált hozzáféréssel rendelkezik több bérlői környezetben lévő előfizetésekhez, használhatja az alapértelmezett előfizetési szűrőt. Megjeleníti azokat az előfizetéseket, amelyek adatait meg szeretné jeleníteni, ha a Azure Portal jobb felső sarkában található "szűrő" ikonra kattint. Ha ezt a funkciót használja, a Backup Explorer összesíti a kiválasztott előfizetések összes tárolójának adatait. További információ: [Mi az az Azure Lighthouse?](../lighthouse/overview.md).

## <a name="next-steps"></a>Következő lépések

[Megtudhatja, hogyan használhatja a Azure Monitort a biztonsági mentési adataival kapcsolatos elemzések lekéréséhez](./backup-azure-monitoring-use-azuremonitor.md)
