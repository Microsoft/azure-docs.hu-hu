---
title: Azure Recovery Services-tárolók és-kiszolgálók kezelése
description: Ebből a cikkből megtudhatja, hogyan figyelheti és kezelheti a Recovery Services-tárolókat a Recovery Services-tároló áttekintő irányítópultjának használatával.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 74351d781287d863db8be0fc7d20517e0479106c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89002130"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Recovery Services-tárolók figyelése és kezelése

Ez a cikk azt ismerteti, hogyan használható a Recovery Services-tároló **áttekintése** irányítópult a Recovery Services-tárolók monitorozásához és kezeléséhez. Amikor megnyit egy Recovery Services tárolót a listából, megnyílik a kiválasztott tárolóhoz tartozó **áttekintő** irányítópult. Az irányítópult a tár különböző részleteit tartalmazza. A következő *csempék* láthatók: a kritikus és figyelmeztető riasztások állapota, a folyamatban lévő és a sikertelen biztonsági mentési feladatok, valamint a helyileg redundáns tárolás (LRS) és a Geo-redundáns tárolás (GRS) mennyisége. Ha Azure-beli virtuális gépekről készít biztonsági mentést a [ **tárolóba, a biztonsági mentés előzetes ellenőrzési állapota** csempe megjeleníti a kritikus vagy figyelmeztető elemeket](#backup-pre-check-status). Az alábbi képen a **contoso-Vault** **Áttekintés** irányítópultja látható. A **biztonsági mentési elemek** csempén látható, hogy a tárolóban kilenc elem van regisztrálva.

![Recovery Services tároló irányítópultja](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

A cikk előfeltételei: Azure-előfizetések, Recovery Services-tárolók, és hogy van legalább egy biztonsági mentési elem konfigurálva a tárolóhoz.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Recovery Services-tároló megnyitása

A riasztások figyeléséhez vagy Recovery Services-tároló felügyeleti adatainak megtekintéséhez nyissa meg a tárolót.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-előfizetésével.

2. A portálon válassza a **Minden szolgáltatás** lehetőséget.

   ![Recovery Services-tárolók listájának megnyitása 1. lépés](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. A **minden szolgáltatás** párbeszédpanelen írja be a következőt: **Recovery Services**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Amikor megjelenik a **Recovery Services** -tárolók lehetőség, válassza ki azt az előfizetésben található Recovery Services-tárolók listájának megnyitásához.

    ![Hozzon létre Recovery Services Vault 1. lépését](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. A tárolók listájából válassza ki a tárolót az **áttekintő** irányítópult megnyitásához.

    ![Recovery Services tároló irányítópultja](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    Az áttekintési irányítópult csempéket használ a riasztások és a biztonsági mentési feladatok adatok biztosításához.

## <a name="monitor-backup-jobs-and-alerts"></a>Biztonsági mentési feladatok és riasztások figyelése

A Recovery Services-tároló **áttekintése** irányítópult csempéket biztosít a figyeléshez és a használathoz. A figyelés szakasz csempéi kritikus és figyelmeztető riasztásokat, valamint folyamatban és sikertelen feladatokat jelenítenek meg. Válasszon ki egy adott riasztást vagy feladatot a biztonsági mentési riasztások vagy a biztonsági mentési feladatok menü megnyitásához, amely az adott feladat vagy riasztás számára szűrve van.

![Biztonsági mentési irányítópult feladatai](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

A figyelés szakasz az előre definiált **biztonságimásolat-riasztások** és a **biztonsági mentési feladatok** lekérdezésének eredményeit jeleníti meg. A monitorozási csempék naprakész információkat biztosítanak az alábbiakról:

* Kritikus és figyelmeztető riasztások a biztonsági mentési feladatokhoz (az elmúlt 24 órában)
* Azure-beli virtuális gépek előzetes ellenőrzési állapota. Az előzetes ellenőrzési állapottal kapcsolatos részletes információkért lásd: [biztonsági mentés előzetes ellenőrzési állapota](#backup-pre-check-status).
* A folyamatban lévő biztonsági mentési feladatok és a sikertelen feladatok (az elmúlt 24 órában).

A használati csempék a következőket biztosítják:

* A tárolóhoz konfigurált biztonságimásolat-elemek száma.
* A tár által felhasznált Azure Storage (LRS és GRS elválasztva).

A társított menü megnyitásához válassza ki a csempéket (kivéve a biztonsági mentési tárolót). A fenti képen a biztonsági mentési riasztások csempe három kritikus riasztást jelenít meg. Ha kiválasztja a kritikus riasztások sort a biztonsági mentési riasztások csempén, megnyílik a kritikus riasztások számára szűrt biztonsági mentési riasztások.

![A biztonsági mentési riasztások menü a kritikus riasztások esetében szűrve](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

A biztonsági mentési riasztások menüt a fenti képen a szűri: állapot aktív, súlyosság kritikus, és az idő az előző 24 óra.

### <a name="backup-pre-check-status"></a>Biztonsági mentés előzetes ellenőrzési állapota

A biztonsági mentés előzetes ellenőrzése ellenőrzi a virtuális gépek konfigurációját olyan problémák esetén, amelyek hátrányosan befolyásolhatják a biztonsági mentéseket. Összesítik ezeket az adatokat, így közvetlenül az Recovery Services-tároló irányítópultján tekinthetők meg, és a megfelelő fájl-konzisztens vagy alkalmazás-konzisztens biztonsági másolatok biztosítására vonatkozó ajánlásokat is biztosítanak. Nem igényelnek infrastruktúrát, és nem igényelnek további költségeket.  

A biztonsági mentés előzetes ellenőrzése az Azure-beli virtuális gépek ütemezett biztonsági mentési műveleteinek részeként fut. A következő állapotok egyikével zárulnak:

* **Átadva**: ez az állapot azt jelzi, hogy a virtuális gép konfigurációjának sikeres biztonsági mentéseket kell eredményeznie, és nem kell végrehajtania javítási műveletet.
* **Figyelmeztetés**: ez az állapot azt jelzi, hogy a virtuális gép konfigurációja egy vagy több olyan hibát jelez *, amely biztonsági* mentési hibákhoz vezethet. *Ajánlott* lépéseket biztosít a sikeres biztonsági mentések biztosításához. Ha például nincs telepítve a legújabb virtuálisgép-ügynök, a biztonsági mentések időnként sikertelenek lehetnek. Ez a helyzet figyelmeztetési állapotot ad.
* **Kritikus**: ez az állapot azt jelzi, hogy a virtuális gép konfigurációja egy vagy több kritikus problémát jelez *, amely a* biztonsági mentési hibákat eredményezi, és a sikeres biztonsági mentések biztosításához *szükséges* lépéseket tesz lehetővé. Például egy virtuális gép NSG-szabályainak frissítése által okozott hálózati hiba miatt a biztonsági mentések sikertelenek lesznek, mivel megakadályozza, hogy a virtuális gép kommunikáljon a Azure Backup szolgáltatással. Ez a helyzet kritikus állapotot biztosít.

Az alábbi lépésekkel megkezdheti az Recovery Services-tárolóban található virtuális gépek biztonsági mentésének előzetes ellenőrzései által jelentett problémák megoldását.

* Válassza a **biztonsági mentés előzetes ellenőrzési állapota (Azure-beli virtuális gépek)** csempét az Recovery Services-tároló irányítópultján.
* Válasszon ki egy olyan virtuális gépet, amelynek biztonsági mentésének előzetes ellenőrzési állapota **kritikus** vagy **Figyelmeztetés**. Ez a művelet megnyitja a **virtuális gép részleteit** tartalmazó ablaktáblát.
* Kattintson a panel tetején látható értesítésre a konfigurációs probléma leírásának és a javító lépéseknek a megjelenítéséhez.

## <a name="manage-backup-alerts"></a>Biztonsági mentési riasztások kezelése

A biztonsági mentési riasztások menü megnyitásához a Recovery Services tároló menüjében válassza a **biztonsági mentési riasztások** lehetőséget.

![Biztonsági mentési riasztások](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

A biztonsági mentési riasztások jelentés felsorolja a tár riasztásait.

![Biztonsági mentési riasztások jelentés](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>Riasztások

A biztonsági mentési riasztások listája a kiválasztott adatokat jeleníti meg a szűrt riasztásokhoz. A biztonsági mentési riasztások menüben szűrheti a kritikus vagy figyelmeztető riasztásokat.

| Riasztási szint | Riasztásokat létrehozó események |
| ----------- | ----------- |
| Kritikus | Kritikus riasztások jelennek meg, ha a biztonsági mentési feladatok sikertelenek, a helyreállítási feladatok meghiúsulnak, és a védelem leállítása a kiszolgálón, de megtartja az adatok védelmét.|
| Figyelmeztetés | Figyelmeztető riasztásokat kap, ha: a biztonsági mentési feladatok figyelmeztetésekkel fejeződött be. Ha például a rendszer sérülési problémák miatt nem készít biztonsági mentést a 100-nél kevesebb fájlról, vagy ha nagyobb, mint 1 000 000 fájl, akkor a biztonsági mentés sikeresen megtörtént. |
| Tájékoztató | Jelenleg nincsenek használatban tájékoztató riasztások. |

### <a name="viewing-alert-details"></a>A riasztások részleteinek megtekintése

A biztonsági mentési riasztások jelentés az egyes riasztások nyolc részletét követi nyomon. A jelentés részleteinek szerkesztéséhez használja az **Oszlopok kiválasztása** gombot.

![Biztonsági mentési riasztások kiválasztása oszlopok gomb](./media/backup-azure-manage-windows-server/backup-alerts.png)

Alapértelmezés szerint az összes adat, a **legutóbbi előfordulási idő** kivételével, megjelenik a jelentésben.

* Riasztás
* Biztonsági mentési elem
* Védett kiszolgáló
* Súlyosság
* Időtartam
* Létrehozás ideje
* Állapot
* Legutóbbi előfordulás időpontja

### <a name="change-the-details-in-alerts-report"></a>A riasztások jelentés adatainak módosítása

1. A jelentés adatainak módosításához a **biztonsági mentési riasztások** menüben válassza az **Oszlopok kiválasztása** lehetőséget.

   ![Válassza az Oszlopok kiválasztása lehetőséget.](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   Megnyílik az **Oszlopok kiválasztása** menü.

2. Az **Oszlopok kiválasztása** menüben válassza ki a jelentésben megjeleníteni kívánt adatokat.

    ![Oszlopok kiválasztása menü](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. A módosítások mentéséhez és az Oszlopok kiválasztása menü bezárásához kattintson a **kész** gombra.

   Ha módosítja a módosításokat, de nem szeretné megtartani a módosításokat, válassza az **Alaphelyzetbe állítás** lehetőséget a kiválasztott utolsó mentett konfiguráció visszaadásához.

### <a name="change-the-filter-in-alerts-report"></a>A szűrő módosítása a riasztások jelentésében

A **szűrő** menü használatával módosíthatja a riasztások súlyosságát, állapotát, kezdési idejét és befejezési idejét.

> [!NOTE]
> A biztonsági mentési riasztások szűrő szerkesztése nem módosítja a kritikus vagy figyelmeztető riasztásokat a tároló áttekintő irányítópultján.
>  

1. A biztonsági mentési riasztások szűrő módosításához a biztonsági mentési riasztások menüben válassza a **szűrő** elemet.

   ![Szűrő menü kiválasztása](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   Megjelenik a szűrő menü.

   ![Riasztási menü szűrése](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. Szerkessze a súlyosságot, az állapotot, a kezdési időt vagy a befejezési időpontot, majd kattintson a **kész** gombra a módosítások mentéséhez.

## <a name="configuring-notifications-for-alerts"></a>Értesítések konfigurálása riasztásokhoz

Értesítéseket állíthat be az e-mailek létrehozásához, ha figyelmeztetési vagy kritikus riasztás történik. Minden órában elküldheti az e-mail-riasztásokat, vagy megadhat egy adott riasztást.

   ![Riasztások szűrése](./media/backup-azure-manage-windows-server/configure-notification.png)

Alapértelmezés szerint az e-mail-értesítések **be vannak kapcsolva**. Az e-mail-értesítések leállításához válassza a **ki** lehetőséget.

Az **értesítési** vezérlőn válassza a **riasztást** , ha nem szeretné, hogy a csoportosítás vagy ne legyen sok olyan elem, amely riasztásokat tud előállítani. Minden riasztás egy értesítést (az alapértelmezett beállítást), a megoldási e-mailt pedig azonnal elküldi.

Ha az **óránkénti kivonatoló** lehetőséget választja, a címzettek elküldenek egy e-mailt, amely ismerteti az elmúlt órában létrehozott megoldatlan riasztásokat. A rendszer az óra végén elküld egy feloldási e-mailt.

Válassza ki az e-mailek létrehozásához használt riasztás súlyosságát (kritikus vagy figyelmeztetés). Jelenleg nincsenek tájékoztató riasztások.

## <a name="manage-backup-items"></a>Biztonsági másolati elemek kezelése

A Recovery Services-tároló számos biztonsági mentési adattípust tárol. [További](backup-overview.md#what-can-i-back-up) információ a biztonsági mentésről. A különböző kiszolgálók, számítógépek, adatbázisok és munkaterhelések kezeléséhez válassza a **biztonsági másolati elemek** csempét a tár tartalmának megtekintéséhez.

![Biztonsági mentési elemek csempe](./media/backup-azure-manage-windows-server/backup-items.png)

Megnyílik a biztonságimásolat-kezelési típus szerint rendezett biztonsági mentési elemek listája.

![Biztonsági másolati elemek listája](./media/backup-azure-manage-windows-server/list-backup-items.png)

A védett példányok egy adott típusának megismeréséhez válassza ki az elemet a biztonságimásolat-kezelés típusa oszlopban. A fenti képen például két Azure-beli virtuális gép védett ebben a tárban. Az **Azure-beli virtuális gép** lehetőség kiválasztásával megnyílik a tárolóban található védett virtuális gépek listája.

![A védett virtuális gépek listája](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

A virtuális gépek listája hasznos adatokkal rendelkezik: a társított erőforráscsoporthoz, a korábbi [biztonsági mentés előzetes ellenőrzési](#backup-pre-check-status), utolsó biztonsági mentési állapota és a legutóbbi visszaállítási pont dátuma. A három pont, az utolsó oszlopban a gyakori feladatok elindítására szolgáló menüt nyitja meg. Az oszlopokban megadott hasznos információk eltérnek az egyes biztonsági mentési típusoknál.

![Gyakori feladatokhoz tartozó három pont menü megnyitása](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Biztonsági mentési feladatok kezelése

A tároló irányítópultján a **biztonsági mentési feladatok** csempén látható, hogy hány feladat van folyamatban, vagy az elmúlt 24 órában sikertelen volt. A csempe betekintést nyújt a biztonsági mentési feladatok menübe.

![Feladatok csempe](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

Ha meg szeretné tekinteni a feladatok további részleteit, válassza a **folyamatban** vagy a **nem sikerült** megnyitni a biztonsági mentési feladatok menüt az adott állapothoz.

### <a name="backup-jobs-menu"></a>Biztonsági mentési feladatok menü

A **biztonsági mentési feladatok** menü megjeleníti az elem típusát, a műveletet, az állapotot, a kezdési időt és az időtartamot.  

A biztonsági mentési feladatok menü megnyitásához a tároló főmenüjében válassza a **biztonsági mentési feladatok** elemet.

![Biztonsági mentési feladatok kiválasztása](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

Megnyílik a biztonsági mentési feladatok listája.

![Biztonsági mentési feladatok listája](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

A biztonsági mentési feladatok menü az elmúlt 24 órában megjeleníti az összes művelet állapotát az összes biztonsági mentési típusnál. **Szűrő** használatával módosítsa a szűrőket. A szűrőket a következő részben ismertetjük.

A szűrők módosítása:

1. A tároló biztonsági mentési feladatok menüjében válassza a **szűrő** elemet.

   ![Biztonsági mentési feladatok szűrésének kiválasztása](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    Megnyílik a szűrő menü.

   ![Megnyílik a szűrő menü a biztonsági mentési feladatokhoz](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. Válassza ki a szűrő beállításait, és válassza a **kész** lehetőséget. A szűrt lista az új beállítások alapján frissül.

#### <a name="item-type"></a>Elemtípus

Az elemtípus a védett példány biztonságimásolat-kezelési típusa. Négy típus létezik; Tekintse meg a következő listát. Megtekintheti az összes elemtípust, vagy egy elemtípust is. Két vagy három elemtípust nem lehet kijelölni. A rendelkezésre álló elemek típusai a következők:

* Minden elemtípus
* Azure virtuális gép
* Fájlok és mappák
* Azure Storage
* Azure-beli számítási feladatok

#### <a name="operation"></a>Művelet

Megtekintheti az egyik műveletet vagy az összes műveletet. Két vagy három műveletet nem választhat ki. Az elérhető műveletek a következők:

* Minden művelet
* Regisztráció
* Biztonsági mentés konfigurálása
* Backup
* Visszaállítás
* Biztonsági mentés letiltása
* Biztonsági mentési adatok törlése

#### <a name="status"></a>Állapot

Az összes állapotot vagy egyet megtekintheti. Két vagy három állapotot nem lehet kijelölni. Az elérhető állapotok a következők:

* Minden állapot
* Befejeződött
* Folyamatban
* Sikertelen
* Megszakítva
* Figyelmeztetésekkel fejeződött be

#### <a name="start-time"></a>Kezdési idő

A lekérdezés elkezdésének napja és időpontja. Az alapértelmezett érték egy 24 órás időszak.

#### <a name="end-time"></a>Befejezési idő

A lekérdezés befejezésének napját és időpontját.

### <a name="export-jobs"></a>Feladatok exportálása

A feladatok **exportálásával** hozzon létre egy táblázatot, amely tartalmazza az összes feladat menüpontot. A számolótáblának van egy lapja, amely tartalmazza az összes feladat összegzését, valamint az egyes feladatokhoz tartozó egyes lapokat.

A feladatok adatainak számolótáblába való exportálásához válassza a **feladatok exportálása** lehetőséget. A szolgáltatás létrehoz egy táblázatot a tár és a dátum nevével, de megváltoztathatja a nevet.

## <a name="monitor-backup-usage"></a>Biztonsági másolatok használatának figyelése

Az irányítópulton található Backup Storage (biztonsági mentés) csempén az Azure-ban felhasznált tárterület látható. A tárolási használatot a következőhöz biztosítjuk:

* A tárolóhoz társított Felhőbeli LRS
* A tárolóhoz társított Felhőbeli GRS

## <a name="troubleshooting-monitoring-issues"></a>Figyelési problémák elhárítása

**Probléma:** A Azure Backup ügynöktől származó feladatok és/vagy riasztások nem jelennek meg a portálon.

**Hibaelhárítási lépések:** A folyamat ```OBRecoveryServicesManagementAgent``` elküldi a feladatot és a riasztási adatokat a Azure Backup szolgáltatásnak. Alkalmanként ez a folyamat beragadhat vagy leállíthatja.

1. Annak ellenőrzéséhez, hogy a folyamat nem fut, nyissa meg a **Feladatkezelő eszközt**, és ellenőrizze, hogy fut-e ```OBRecoveryServicesManagementAgent``` .

2. Ha a folyamat nem fut, nyissa meg a **Vezérlőpultot**, és tallózással keresse meg a szolgáltatások listáját. Indítsa el vagy indítsa újra **Microsoft Azure Recovery Services felügyeleti ügynököt**.

    További információkért keresse fel a naplókat a következő címen:<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` Például:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>Következő lépések

* [Windows Server-vagy Windows-ügyfél visszaállítása az Azure-ból](backup-azure-restore-windows-server.md)
* További információ a Azure Backupről: [Azure Backup áttekintése](./backup-overview.md)
