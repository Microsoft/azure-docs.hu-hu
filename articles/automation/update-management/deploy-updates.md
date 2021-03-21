---
title: Frissítési központi telepítések létrehozása Azure Automation Update Management
description: Ez a cikk a frissítések központi telepítésének ütemezett és állapotának áttekintését ismerteti.
services: automation
ms.subservice: update-management
ms.date: 03/19/2021
ms.topic: conceptual
ms.openlocfilehash: 6d35d6b49ab72d8aa7b25506011147ab624273fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669678"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Frissítések központi telepítése és eredmények áttekintése

Ez a cikk bemutatja, hogyan ütemezhet egy frissítés központi telepítését, és hogyan tekintheti át a folyamatot az üzembe helyezés befejeződése után. A frissítés központi telepítését egy kiválasztott Azure-beli virtuális gépről, a kijelölt ív-kiszolgálóról vagy az Automation-fiókból az összes konfigurált gépen és kiszolgálón is konfigurálhatja.

Az egyes forgatókönyvek esetében a központi telepítéshez a kiválasztott gép vagy kiszolgáló célokat kell létrehoznia, vagy az Automation-fiókból történő központi telepítés létrehozása esetén egy vagy több gépet is megcélozhat. Ha egy Azure-beli virtuális gépről vagy ív-kompatibilis kiszolgálóról ütemezett frissítést ütemezhet, a lépések ugyanazok, mint az Automation-fiókból való üzembe helyezés, a következő kivételekkel:

* Az operációs rendszer automatikusan ki van választva a gép operációs rendszere alapján
* A frissítendő célszámítógép automatikusan célként van beállítva
* Az ütemterv konfigurálásakor megadhatja, hogy a **Frissítés most** megtörténjen, vagy ismétlődő ütemtervet használ.

> [!IMPORTANT]
> A frissítés központi telepítésének létrehozásával elfogadja az operációs rendszerének frissítését biztosító vállalat által biztosított szoftverlicenc-szerződés (EULA) feltételeit.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítési központi telepítés ütemezése egy olyan [ütemezési](../shared-resources/schedules.md) erőforrást hoz létre, amely a **MicrosoftOMSComputers** runbook van társítva, amely kezeli a frissítés központi telepítését a célszámítógépen vagy a gépeken. A frissítések telepítéséhez be kell ütemeznie egy központi telepítést, amely a kiadási ütemterv és a szolgáltatás ablakát követi. Kiválaszthatja, hogy milyen frissítési típusok szerepeljenek a központi telepítésben. Például megadhatja a kritikus vagy a biztonsági frissítéseket, és kihagyhatja a kumulatív frissítéseket.

>[!NOTE]
>Ha törli az ütemezési erőforrást a Azure Portal vagy a PowerShellt a telepítés létrehozása után, a törlés megszakítja az ütemezett frissítés központi telepítését, és hibaüzenetet jelenít meg, amikor megkísérli újrakonfigurálni az ütemezési erőforrást a portálról. Az ütemezett erőforrást csak a megfelelő központi telepítési ütemterv törlésével törölheti.  

Új frissítés központi telepítésének megadásához hajtsa végre az alábbi lépéseket. A kiválasztott erőforrástól (azaz az Automation-fióktól, az ív-kompatibilis kiszolgálótól, az Azure VM-től) függően az alábbi lépések az összes kisebb eltérésre vonatkoznak a központi telepítési ütemterv konfigurálása során.

1. A portálon:

   * Egy vagy több gép, navigáljon az **Automation-fiókok** elemre, és válassza ki az Automation-fiókját, amelynek Update Management engedélyezve van a listából.
   * Azure-beli virtuális gép esetén navigáljon a **virtuális gépekhez** , és válassza ki a kívánt virtuális gépet a listából.
   * Az ív használatára képes kiszolgálók esetében navigáljon a **kiszolgálók – Azure arc** elemre, és válassza ki a kiszolgálót a listából.

2. A kiválasztott erőforrástól függően navigáljon Update Management:

   * Ha az Automation-fiók lehetőséget választotta, lépjen **a frissítés kezelése** elemre az **Update Management** alatt, majd válassza a **frissítés központi telepítésének** megkezdése lehetőséget.
   * Ha az Azure-beli virtuális gépet választotta, lépjen a **vendég + gazdagép frissítéseire**, majd válassza **a Update Management ugrás** lehetőséget.
   * Ha kiválasztott egy arc-kompatibilis kiszolgálót, lépjen a **Update Managementra**, majd válassza a **frissítés központi telepítésének** megkezdése lehetőséget.

3. Az **új frissítés telepítése** alatt, a **név** mezőben adjon meg egy egyedi nevet a telepítéshez.

4. Válassza ki a frissítés központi telepítéséhez használni kívánt operációs rendszert.

    > [!NOTE]
    > Ez a beállítás nem érhető el, ha Azure-beli virtuális gépet vagy ív-kompatibilis kiszolgálót jelölt ki. Az operációs rendszer automatikusan meg van határozva.

5. A **csoportok frissítése** a régióban területen adjon meg egy olyan lekérdezést, amely kombinálja az előfizetést, az erőforráscsoportot, a helyszíneket és a címkéket, hogy az üzembe helyezéshez felvenni kívánt Azure-beli virtuális gépek dinamikus csoportját hozza létre. További információ: [dinamikus csoportok használata a Update Management használatával](configure-groups.md).

    > [!NOTE]
    > Ez a beállítás nem érhető el, ha Azure-beli virtuális gépet vagy ív-kompatibilis kiszolgálót jelölt ki. A gép automatikusan az ütemezett telepítéshez van rendelve.

6. A **frissítendő gépek** területen válasszon ki egy mentett keresést, egy importált csoportot, vagy válasszon ki **gépeket** a legördülő menüből, és válassza az egyes gépek lehetőséget. Ezzel a beállítással megtekintheti az egyes gépek Log Analytics ügynökének készültségét. A számítógépcsoportok Azure Monitor-naplókban való létrehozásának különböző módszereiről további információt a következő témakörben talál: [számítógépcsoportok Azure monitor-naplókban](../../azure-monitor/logs/computer-groups.md). Az ütemezett frissítések telepítése legfeljebb 1000 gépet tartalmazhat.

    > [!NOTE]
    > Ez a beállítás nem érhető el, ha Azure-beli virtuális gépet vagy ív-kompatibilis kiszolgálót jelölt ki. A gép automatikusan az ütemezett telepítéshez van rendelve.

7. A **frissítési** besorolások régió használatával adhatja meg a termékek [frissítési besorolását](view-update-assessments.md#work-with-update-classifications) . Minden termék esetében törölje az összes támogatott frissítési besorolást, de azokat is, amelyeket bele szeretne foglalni a frissítés telepítésére.

    Ha a központi telepítés célja csak a frissítések kiválasztása, akkor a következő lépésben leírtak szerint ki kell választania a frissítések **belefoglalása/kizárása** beállítás konfigurálásakor az összes előre kiválasztott frissítési besorolást. Ez biztosítja, hogy csak azok a frissítések legyenek telepítve a célszámítógépen, amelyeket az adott központi telepítésbe való *felvételhez* megadott.

8. A **frissítések belefoglalása/kizárása** régió használatával vegye fel vagy zárja ki a kiválasztott frissítéseket a központi telepítésből. A **Belefoglalás/kizárás** lapon adja meg a Windows-frissítések belefoglalásához vagy kizárásához szükséges TUDÁSBÁZISCIKK-azonosítók számát. A támogatott Linux-disztribúciók esetében adja meg a csomag nevét.

   > [!IMPORTANT]
   > Ne feledje, hogy a kizárások felülbírálják a belefoglalásokat. Ha például meghatároz egy kizárási szabályt `*` , Update Management kizárja az összes javítást vagy csomagot a telepítésből. A kizárt javítások továbbra is hiányzóként jelennek meg a gépeken. Linux rendszerű gépek esetén, ha olyan csomagot tartalmaz, amely egy kizárt függő csomaggal rendelkezik, Update Management nem telepíti a fő csomagot.

   > [!NOTE]
   > Nem adhat meg olyan frissítéseket, amelyek felváltották a frissítés központi telepítésbe való felvételét.

9. Válassza az **időzítési beállítások** lehetőséget. Az alapértelmezett kezdési időpont az aktuális időpontnál 30 perccel későbbi időpont. Bármilyen időpontra beállítható a pillanatnyi időt követő 10. perc után.

    > [!NOTE]
    > Ez a beállítás eltérő, ha egy arc-kompatibilis kiszolgálót jelölt ki. A **Frissítés most** lehetőségre kattintva 20 percet is igénybe vehet a jövőben.

10. Az **ismétlődéssel** megadhatja, hogy a telepítés egyszer történjen-e, vagy ismétlődő ütemezéset használ, majd kattintson **az OK gombra**.

11. A **parancsfájlok előtti és utáni parancsfájlok** területen válassza ki az üzembe helyezés előtt és után futtatandó parancsfájlokat. További információ: a [parancsfájlok előtti és utáni parancsfájlok kezelése](pre-post-scripts.md).

12. A frissítések telepítésének időtartamát a **karbantartási időszak (perc)** mező segítségével adhatja meg. Karbantartási időszak megadásakor vegye figyelembe a következő részleteket:

    * A karbantartási időszakok azt szabályozzák, hogy a rendszer hány frissítést telepítsen.
    * A Update Management nem állítja le az új frissítések telepítését, ha a karbantartási időszak végére közeledik.
    * A Update Management nem szakítja meg a folyamatban lévő frissítéseket, ha a karbantartási időszak túllépve. A rendszer nem próbálkozik a telepítendő többi frissítéssel. Ha ez folyamatosan történik, újra kell értékelnie a karbantartási időszak időtartamát.
    * Ha a karbantartási időszak túllépi a Windowst, gyakran előfordul, hogy a szervizcsomag frissítése hosszú időt vesz igénybe.

    > [!NOTE]
    > Ha el szeretné kerülni, hogy a frissítések ne legyenek alkalmazva az Ubuntu karbantartási időszakán kívül, konfigurálja újra a `Unattended-Upgrade` csomagot az automatikus frissítések letiltásához. A csomag konfigurálásával kapcsolatos további információkért tekintse meg az [Ubuntu Server útmutatójának automatikus frissítések témakörét](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

13. Az újraindítási **Beállítások** mező segítségével megadhatja, hogyan kezelje az újraindítást az üzembe helyezés során. A következő lehetőségek érhetők el: 
    * Újraindítás szükség esetén (alapértelmezett)
    * Mindig induljon újra
    * Soha ne induljon újra
    * Csak újraindítás; Ez a beállítás nem telepíti a frissítéseket

    > [!NOTE]
    > Az újraindítás [kezeléséhez használt beállításkulcsok](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) alatt felsorolt beállításkulcsok újraindítási eseményt okozhatnak, ha az újraindítási **Beállítások** úgy vannak beállítva, hogy **Soha ne induljon újra**.

14. Amikor befejezte a központi telepítési ütemterv konfigurálását, válassza a **Létrehozás** lehetőséget.

    ![A frissítés ütemezési beállításai ablaktábla](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Amikor befejezte a központi telepítési ütemterv konfigurálását egy kiválasztott ív-kompatibilis kiszolgálón, válassza a **felülvizsgálat + létrehozás** lehetőséget.

15. Ekkor visszalép az állapot-irányítópultra. Válassza a **központi telepítési ütemtervek** lehetőséget a létrehozott központi telepítési ütemterv megjelenítéséhez. Legfeljebb 500 ütemterv szerepel a felsorolásban. Ha több mint 500 ütemterve van, és szeretné áttekinteni a teljes listát, tekintse meg a [szoftverfrissítési konfigurációk – lista](/rest/api/automation/softwareupdateconfigurations/list) REST API metódust. Az API 2019-06-01-es vagy újabb verzióját kell megadni.

## <a name="schedule-an-update-deployment-programmatically"></a>Frissítési üzembe helyezés programozott módon történő beütemezett

Ha meg szeretné tudni, hogyan hozhat létre frissítési központi telepítést a REST API, tekintse meg a [szoftverfrissítési konfigurációk – létrehozás](/rest/api/automation/softwareupdateconfigurations/create)című témakört.

A heti frissítések központi telepítésének létrehozásához minta-runbook is használhat. További információ erről a runbook: [heti frissítési telepítés létrehozása egy vagy több virtuális géphez egy erőforráscsoport esetében](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group).

## <a name="check-deployment-status"></a>Központi telepítés állapotának keresése

Az ütemezett telepítés megkezdése után az állapotot az **Update Management**( **Előzmények** ) lapon tekintheti meg. Ha a telepítés fut, az állapota **Folyamatban** lesz. Az üzembe helyezés sikeres befejeződése után az állapot **sikeresre** változik. Ha az üzemelő példány egy vagy több frissítésével hibát észlel, az állapot **sikertelen** lesz.

## <a name="view-results-of-a-completed-update-deployment"></a>Befejezett frissítés telepítésének eredményeinek megtekintése

Az üzembe helyezés befejezésekor kiválaszthatja, hogy az eredmények megjelenjenek.

[![Központi telepítési állapot irányítópultjának frissítése egy adott központi telepítéshez](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

A **frissítés eredményei** között az összefoglalás a frissítések és a telepítési eredmények teljes számát adja meg a cél virtuális gépeken. A jobb oldali táblázat a frissítések részletes részletezését és a telepítés eredményét jeleníti meg.

Az elérhető értékek a következők:

* **Nem lett megkísérelve** – a frissítés nem lett telepítve, mert a karbantartási időszak meghatározott időtartama alapján nem volt elegendő idő a rendelkezésére.
* **Nincs kiválasztva** – a frissítés nem lett kijelölve telepítésre.
* **Sikeres** – a frissítés sikeres volt.
* **Sikertelen** – a frissítés sikertelen volt.

A **minden napló** lehetőség kiválasztásával megtekintheti az üzemelő példány által létrehozott összes naplóbejegyzést.

Válassza a **kimenet** lehetőséget, hogy megtekintse a runbook a cél virtuális gépeken történő telepítésének kezeléséért felelős folyamat adatfolyamát.

A telepítés közben felmerülő hibák részletes információinak megtekintéséhez válassza a **Hibák** elemet.

## <a name="next-steps"></a>Következő lépések

* Ha meg szeretné tudni, hogyan hozhat létre riasztásokat a telepítési eredmények frissítésével kapcsolatban, tekintse meg [a riasztások létrehozása a Update Managementhoz](configure-alerts.md)című témakört.
* Az általános Update Management hibák elhárításával kapcsolatban lásd: [Update Management problémák elhárítása](../troubleshoot/update-management.md).
