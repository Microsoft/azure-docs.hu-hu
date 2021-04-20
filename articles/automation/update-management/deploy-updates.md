---
title: Frissítéstelepítések létrehozása a Azure Automation Update Management
description: Ez a cikk a frissítéstelepítések ütemezését és állapotuk áttekintését ismerteti.
services: automation
ms.subservice: update-management
ms.date: 04/19/2021
ms.topic: conceptual
ms.openlocfilehash: e410e5de529bde122fe42d21b593a6fc483dcbc0
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726698"
---
# <a name="how-to-deploy-updates-and-review-results"></a>Frissítések telepítése és az eredmények áttekintése

Ez a cikk azt ismerteti, hogyan ütemezheti a frissítéstelepítést, és hogyan áttekintheti a folyamatot az üzembe helyezés befejezése után. Konfigurálhat egy frissítéstelepítést egy kiválasztott Azure-beli virtuális gépről, a kiválasztott Arc-kompatibilis kiszolgálóról vagy az Automation-fiókból az összes konfigurált gépen és kiszolgálón.

Az egyes forgatókönyvekben a kiválasztott gépet vagy kiszolgálót célzó üzemelő példány, illetve az Automation-fiókból létrehozott üzemelő példányok egy vagy több gépet célozhat meg. Ha azure-beli virtuális gépről vagy Arc-kompatibilis kiszolgálóról ütemez frissítéstelepítést, a lépések ugyanazok, mint az Automation-fiókból való üzembe helyezés, a következő kivételekkel:

* Az operációs rendszer automatikusan ki van választva a gép operációs rendszere alapján
* A frissíteni célgép úgy van beállítva, hogy automatikusan célozza magát
* Az ütemezés konfigurálásakor megadhatja az Update **now**(Frissítés most) értéket, amely egyszer fordul elő, vagy ismétlődő ütemezést használ.

> [!IMPORTANT]
> Frissítéstelepítés létrehozásával elfogadja a vállalat által az operációs rendszerhez elérhető frissítések által biztosított szoftverlicenc-feltételeket (EULA).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>Frissítéstelepítés ütemezése

A frissítéstelepítés ütemezése a **Patch-MicrosoftOMSComputers runbookhoz** csatolt ütemezési erőforrást hoz létre, amely kezeli [a](../shared-resources/schedules.md) frissítéstelepítést a célgépen vagy gépeken. A frissítések telepítéséhez olyan központi telepítést kell ütemeznie, amely követi a kiadási ütemezést és a szolgáltatási időkeretet. Kiválaszthatja a telepítésbe foglalni szükséges frissítési típusokat. Például kritikus vagy biztonsági frissítéseket is tartalmazhat, és kizárhatja az összesítő frissítéseket.

>[!NOTE]
>Ha törli az ütemezett erőforrást a Azure Portal-ból, vagy a PowerShellt használja az üzembe helyezés létrehozása után, a törlés megszakítja az ütemezett frissítéstelepítést, és hibát jelez, amikor megpróbálja újrakonfigurálni az ütemezési erőforrást a portálról. Az ütemezési erőforrást csak a megfelelő üzembe helyezési ütemezés törlésével törölheti.  

Új frissítéstelepítés ütemezéséhez hajtsa végre az alábbi lépéseket. A kiválasztott erőforrástól (azaz Automation-fiók, Arc-kompatibilis kiszolgáló, Azure-beli virtuális gép) függően az alábbi lépések az üzembehelyezés ütemezésének konfigurálásakor az összes kisebb eltéréssel együtt érvényesek.

1. A portálon az üzembe helyezés ütemezése a következőre:

   * Egy vagy több gépen lépjen az **Automation-fiókokhoz,** és válassza ki az Automation-fiókot, Update Management engedélyezve van a listában.
   * Azure-beli virtuális géphez lépjen a **Virtuális gépek lapra,** és válassza ki a virtuális gépet a listából.
   * Arc-kompatibilis kiszolgáló esetén lépjen a **Kiszolgálók** – Azure Arc, és válassza ki a kiszolgálót a listából.

2. A kiválasztott erőforrástól függően a következőre Update Management:

   * Ha az Automation-fiókot választotta, a Frissítéskezelés alatt válassza a Frissítéskezelés **lehetőséget,** majd válassza a **Frissítéstelepítés ütemezése lehetőséget.** 
   * Ha egy Azure-beli virtuális gépet választott, válassza a Vendég + gazdagép frissítése **lehetőséget,** majd válassza az **Ugrás a Update Management.**
   * Ha arc-kompatibilis kiszolgálót választott, a Update Management **válassza** a **Frissítéstelepítés ütemezése lehetőséget.**

3. Az **Új frissítéstelepítés területen** a Név **mezőben** adjon meg egy egyedi nevet az üzemelő példánynak.

4. Válassza ki a frissítéstelepítés cél operációs rendszerét.

    > [!NOTE]
    > Ez a lehetőség nem érhető el, ha Azure-beli virtuális gépet vagy Arc-kompatibilis kiszolgálót választott. A rendszer automatikusan azonosítja az operációs rendszert.

5. A **Régiót frissíthető** csoportok területen definiálhat egy lekérdezést, amely az előfizetést, az erőforráscsoportokat, a helyeket és a címkéket kombinálva hoz létre azure-beli virtuális gépek dinamikus csoportját az üzemelő példányba foglalva. További információ: [Dinamikus csoportok használata a Update Management.](configure-groups.md)

    > [!NOTE]
    > Ez a lehetőség nem érhető el, ha Azure-beli virtuális gépet vagy Arc-kompatibilis kiszolgálót választott. A gép automatikusan az ütemezett telepítést célozza meg.

6. A **Régiót frissíteni kívánt** gépek területen válasszon ki egy  mentett keresést, egy importált csoportot, vagy válassza a Legördülő menü Gépek menüpontját, és válassza ki az egyes gépeket. Ezzel a beállítással láthatja, hogy a Log Analytics-ügynök minden gépre kész-e. A számítógépcsoportok naplókban való létrehozásának különböző Azure Monitor lásd: Számítógépcsoportok a Azure Monitor [naplókban.](../../azure-monitor/logs/computer-groups.md) Ütemezett frissítéstelepítés esetén legfeljebb 1000 gépet foglalhat bele.

    > [!NOTE]
    > Ez a lehetőség nem érhető el, ha Azure-beli virtuális gépet vagy Arc-kompatibilis kiszolgálót választott. A gép automatikusan az ütemezett üzembe helyezést célozza meg.

7. A Frissítési **besorolások régióval** megadhatja a termékek [frissítési besorolását.](view-update-assessments.md#work-with-update-classifications) Minden termék esetén törölje az összes támogatott frissítési besorolás kijelölését, de azokat, amelyek bele vannak foglalva a frissítéstelepítésbe.

   :::image type="content" source="./media/deploy-updates/update-classifications-example.png" alt-text="Adott frissítési besorolások kiválasztását bemutató példa.":::

    Ha a telepítés célja, hogy csak bizonyos frissítéseket alkalmaz, akkor a következő lépésben leírtak szerint az összes előre kiválasztott frissítésbesorolás kijelölését meg kell semmisülni a Frissítések be- **és** kizárása lehetőség konfigurálásakor. Ez biztosítja, hogy csak  az ebben a telepítésben megadott frissítések telepítése legyen telepítve a célgépeken.

   >[!NOTE]
   > A frissítések frissítésbesorolással történő telepítése nem működik a CentOS RTM-verzióiban. A CentOS frissítésének megfelelő telepítéséhez válassza ki az összes besorolást, és győződjön meg arról, hogy a frissítések alkalmazva vannak. A CentOS-on jelenleg nincs támogatott módszer a natív besorolási adatok rendelkezésre állásának engedélyezésére. A frissítési besorolásokkal kapcsolatos további [információkért lásd a következőt:](overview.md#update-classifications).

8. A Frissítések **felvétele/kizárása régió** használatával hozzáadhat vagy kizárhat kiválasztott frissítéseket a telepítésből. A **Beletűnés/kizárás** lapon adja meg a Tudásbáziscikk windowsos frissítésekbe foglalni vagy kizárni szükséges azonosítószámait. A támogatott Linux-disztribúciókhoz meg kell adnia a csomag nevét.

   :::image type="content" source="./media/deploy-updates/include-specific-updates-example.png" alt-text="Adott frissítéseket bemutató példa.":::

   > [!IMPORTANT]
   > Ne feledje, hogy a kizárások felülbírálják a belefoglalásokat. Ha például a kizárási szabályt definiálja, a Update Management a telepítésből kizárja az összes `*` javítást vagy csomagot. A kizárt javítások továbbra is hiányzóként mutatjak a gépeket. Linux rendszerű gépek esetén, ha olyan függő csomaggal rendelkezik, amely ki van zárva, a Update Management nem telepíti a fő csomagot.

   > [!NOTE]
   > Nem adhatja meg azokat a frissítéseket, amelyek a frissítéstelepítésbe bele vannak foglalva.

   Az alábbi példaforgatókönyvek segítségével megértheti, hogyan használható egyszerre a belefoglalás/kizárás és a frissítési besorolás a frissítéstelepítések során:

   * Ha csak a frissítések egy adott listáját szeretné telepíteni, ne válasszon frissítési besorolást, és ne adja meg az Include beállítással alkalmazandó frissítések **listáját.** 

   * Ha csak biztonsági és kritikus frissítéseket, valamint egy vagy több választható  illesztőprogram-frissítést szeretne telepíteni, a Frissítésbesorolások alatt válassza a Biztonság és kritikus **lehetőséget.**  Ezután az Include (Be **include)** lehetőséghez adja meg az illesztőprogram frissítéseit.

   * Ha csak biztonsági és kritikus frissítéseket szeretne telepíteni, de át szeretne hagyni egy vagy  több  Python-frissítést, hogy elkerülje az örökölt alkalmazás feltörését, válassza a Biztonság és kritikus lehetőséget a Frissítési besorolások **alatt.** Ezután az **Exclude (Kizárás) lehetőséghez** adja hozzá a kihagyni kívánt Python-csomagokat.

9. Válassza **az Ütemezési beállítások lehetőséget.** Az alapértelmezett kezdési időpont az aktuális időpontnál 30 perccel későbbi időpont. Bármilyen időpontra beállítható a pillanatnyi időt követő 10. perc után.

    > [!NOTE]
    > Ez a beállítás más, ha arc-kompatibilis kiszolgálót választott. Választhatja az **Update now (Frissítés most) lehetőséget,** vagy a start time 20 minutes into the future (20 perces kezdési időpont) lehetőséget.

10. Az **Ismétlődés beállítás** használatával megadhatja, hogy az üzembe helyezés egyszer történik-e meg, vagy ismétlődő ütemezést használ, majd kattintson az **OK gombra.**

11. A **Pre-scripts + Post-scripts** (Szkriptek előtti és utólagos szkriptek) régióban válassza ki az üzembe helyezés előtt és után futtatni kívánt szkripteket. További tudnivalókért lásd: Az előszk szkriptek és az [utólagos szkriptek kezelése.](pre-post-scripts.md)

12. A Karbantartási **időszak (perc)** mezőben adhatja meg a frissítések telepítéséhez szükséges időt. Karbantartási időszak megadásakor vegye figyelembe a következő részleteket:

    * A karbantartási időszak a telepített frissítéseket szabályozhatja.
    * Update Management nem áll le az új frissítések telepítése, ha egy karbantartási időszak közeledik.
    * Update Management nem szünteti meg a folyamatban lévő frissítéseket a karbantartási időszak túllépése esetén. A rendszer nem kísérel meg további telepíteni kívánt frissítéseket. Ha ez folyamatosan történik, újra kell értékelnie a karbantartási időszak időtartamát.
    * Ha a karbantartási időszak túl van lépve Windows rendszeren, annak gyakran az az oka, hogy a szervizcsomag-frissítés telepítése hosszú ideig tart.

    > [!NOTE]
    > Az Ubuntu karbantartási időszakán kívül alkalmazott frissítések elkerülése érdekében konfigurálja újra a csomagot az `Unattended-Upgrade` automatikus frissítések letiltásához. A csomag konfigurálásról az Ubuntu Server útmutatójának Automatikus frissítések [témakörében található információ.](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)

13. Az **Újraindítási beállítások mezőben** adhatja meg, hogyan kezelje az újraindításokat az üzembe helyezés során. A következő lehetőségek érhetők el: 
    * Szükség esetén indítsa újra az újraindítást (alapértelmezés)
    * Mindig induljon újra
    * Soha ne induljon újra
    * Csak újraindítás; ez a beállítás nem telepíti a frissítéseket

    > [!NOTE]
    > Az újraindítás kezeléséhez használt beállításkulcsok alatt felsorolt  beállításkulcsok újraindítási eseményt okozhatnak, ha az Újraindítási beállítások Never restart (Soha ne induljon [](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) **újra) beállításra vannak beállítva.**

14. Ha végzett az üzembe helyezés ütemezésének konfigurálásával, válassza a **Létrehozás lehetőséget.**

    ![A frissítés ütemezési beállításai ablaktábla](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > Ha befejezte egy kijelölt Arc-kompatibilis kiszolgáló üzembe helyezési ütemezésének konfigurálását, válassza a **Felülvizsgálat + létrehozás lehetőséget.**

15. Ekkor visszalép az állapot-irányítópultra. Válassza **az Üzembe helyezési ütemezések** lehetőséget a létrehozott üzembe helyezési ütemezések megjelenítése érdekében. A listán legfeljebb 500 ütemezés szerepel. Ha 500-asnál több ütemezéssel és a teljes listát szeretné áttekinteni, tekintse meg a Szoftverfrissítési konfigurációk [–](/rest/api/automation/softwareupdateconfigurations/list) Lista REST API metódust. Adja meg az API 2019-06-01-es vagy újabb verzióját.

## <a name="schedule-an-update-deployment-programmatically"></a>Frissítéstelepítés programozott ütemezése

Ha többet szeretne megtudni arról, hogyan hozhat létre frissítéstelepítést a REST API a [Szoftverfrissítési konfigurációk – Létrehozás cikkből.](/rest/api/automation/softwareupdateconfigurations/create)

Egy minta runbook használatával heti frissítéstelepítést hozhat létre. További információ erről a runbookról: Create a weekly update deployment for one or [more VMs in a resource group (Heti](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group)frissítéstelepítés létrehozása egy vagy több virtuális géphez egy erőforráscsoportban).

## <a name="check-deployment-status"></a>Az üzembe helyezés állapotának ellenőrzése

Az ütemezett telepítés elindulás után az Update Management (Frissítéskezelés) lap **History** (Előzmények) lapján láthatja **az állapotát.** Ha a telepítés fut, az állapota **Folyamatban** lesz. Ha az üzembe helyezés sikeresen befejeződik, az állapot Sikeres **állapotúra változik.** Ha a telepítésben egy vagy több frissítés meghiúsul, az állapot **Sikertelen** lesz.

## <a name="view-results-of-a-completed-update-deployment"></a>Befejezett frissítéstelepítés eredményeinek megtekintése

Ha az üzembe helyezés befejeződött, kiválaszthatja az eredményeket.

[![Frissítéstelepítési állapot irányítópultja egy adott üzemelő példányhoz](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

A **Frissítési eredmények alatt** egy összegzés a frissítések teljes számát és a cél virtuális gépekre vonatkozó telepítési eredményeket biztosítja. A jobb oldalon található táblázat a frissítések részletes lebontását és az egyes frissítések telepítési eredményeit jeleníti meg.

Az elérhető értékek a következőek:

* **Nem történt kísérlet** – A frissítés nem lett telepítve, mert a megadott karbantartási időszak alapján nem volt elég idő.
* **Nincs kiválasztva** – A frissítés nem lett kiválasztva az üzembe helyezéshez.
* **Sikeres –** A frissítés sikeres volt.
* **Sikertelen –** A frissítés sikertelen volt.

Válassza **a Minden napló** lehetőséget az üzemelő példány által létrehozott összes naplóbejegyzés megtekintéséhez.

Válassza **a Kimenet** lehetőséget annak a runbooknak a feladatstreamjeként, amely a frissítéstelepítés kezeléséért felelős a cél virtuális gépeken.

A telepítés közben felmerülő hibák részletes információinak megtekintéséhez válassza a **Hibák** elemet.

## <a name="next-steps"></a>Következő lépések

* A frissítéstelepítési eredményekről értesítő riasztások létrehozásáról lásd: Riasztások létrehozása a [Update Management.](configure-alerts.md)
* Az általános hibaelhárítási Update Management lásd: [Hibaelhárítás Update Management problémák.](../troubleshoot/update-management.md)
