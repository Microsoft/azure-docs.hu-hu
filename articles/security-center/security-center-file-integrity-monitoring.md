---
title: A fájlok integritásának figyelése Azure Security Centerban | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja a fájlok integritásának figyelését (FIM) Azure Security Center az útmutató segítségével.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: b858903b87dc6c4cc1f7c40338c09bf5b8204b3d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176535"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>A fájlok integritásának figyelése Azure Security Center
Ismerje meg, hogyan konfigurálhatja a fájlok integritásának figyelését (FIM) Azure Security Center az útmutató segítségével.


## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|[Az Azure Defender kiszolgálót igényli a kiszolgálókhoz](defender-for-servers-introduction.md).<br>A FIM feltölti az adatokat a Log Analytics munkaterületre. Az adatforgalmi díjak a feltöltött adatok mennyiségétől függően érvényesek. További információkért tekintse meg a [log Analytics díjszabását](https://azure.microsoft.com/pricing/details/log-analytics/) .|
|Szükséges szerepkörök és engedélyek:|A **munkaterület tulajdonosa** engedélyezheti vagy letilthatja a FIM-t (További információ: [log Analytics Azure-szerepkörök](/services-hub/health/azure-roles#azure-roles)).<br>Az **olvasó** megtekintheti az eredményeket.|
|Felhők|![Igen ](./media/icons/yes-icon.png) kereskedelmi felhők<br>![Igen ](./media/icons/yes-icon.png) nemzeti/szuverén (US gov, Kína gov, other gov)<br>Csak olyan régiókban támogatott, ahol a Azure Automation Change Tracking megoldás elérhető.<br>![Igen, az ](./media/icons/yes-icon.png) [Azure arc](https://docs.microsoft.com/azure/azure-arc/servers/overview) használatára képes eszközök.<br>Lásd: [a csatolt log Analytics munkaterület támogatott régiói](../automation/how-to/region-mappings.md).<br>[További információ a Change Tracking](../automation/change-tracking/overview.md)szolgáltatásról.|
|||

## <a name="what-is-fim-in-security-center"></a>Mi az a FIM in Security Center?
A fájlok integritásának figyelése (FIM), más néven változások figyelése, megvizsgálja az operációs rendszer fájljait, a Windows-nyilvántartásokat, az alkalmazás szoftvereit, a Linux rendszerfájlokat és egyebeket a támadásra utaló változásokhoz. 

Security Center azt javasolja, hogy az entitások a FIM használatával figyeljenek, és saját FIM-szabályzatokat vagy entitásokat is megadhat a figyeléshez. A FIM riasztást küld a gyanús tevékenységekről, például:

- Fájl-és beállításjegyzék-kulcs létrehozása vagy eltávolítása
- Fájl módosításai (a fájlméret változásai, a hozzáférés-vezérlési listán és a tartalom kivonata)
- Beállításjegyzék-módosítások (méret módosítása, hozzáférés-vezérlési listában, típus és tartalom)

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A FIM-vel figyelt javasolt entitások listájának áttekintése
> * Saját, egyéni FIM-szabályok definiálása
> * A figyelt entitások változásainak naplózása
> * Használjon helyettesítő karaktereket a címtárak közötti nyomkövetés leegyszerűsítéséhez


## <a name="how-does-fim-work"></a>Hogyan működik a FIM?

Az elemek aktuális állapotának és az előző vizsgálat állapotának összehasonlításával a FIM riasztást küld, ha gyanús módosítások történnek.

A FIM az Azure Change Tracking megoldást használja a környezet változásainak nyomon követésére és azonosítására. Ha engedélyezve van a fájl integritásának figyelése, akkor egy **megoldás** típusú **change Tracking** erőforrással rendelkezik. Az adatgyűjtés gyakoriságának részleteiért lásd: [change Tracking adatgyűjtési részletek](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection).

> [!NOTE]
> Ha eltávolítja a **change Tracking** -erőforrást, a Security Center a fájl sértetlenségének figyelése funkciót is letiltja.

## <a name="which-files-should-i-monitor"></a>Mely fájlokat kell figyelni?
A figyelni kívánt fájlok kiválasztásakor vegye figyelembe, hogy mely fájlok kritikus fontosságúak a rendszer és az alkalmazások számára. Figyelje azokat a fájlokat, amelyeket nem várható a tervezés nélkül módosítani. Ha az alkalmazások vagy az operációs rendszer által gyakran módosított fájlokat (például a naplófájlokat és a szövegfájlokat) választja, nagy mennyiségű zajt hoz létre, ami megnehezíti a támadás azonosítását.

A Security Center az alábbi, az ismert támadási minták alapján figyelt elemek listáját tartalmazza.

|Linux-fájlok|Windows-fájlok|Windows-beállításkulcsok (HKLM = HKEY_LOCAL_MACHINE)|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*. conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini \Boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows CurrentVersion|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|Hklmsoftwaremicrosoftwindowscurrentversionexplorershell\\\\ mappák|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User rendszerhéj-mappák|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin helyekre|C:\Program Files\Microsoft biztonsági Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0 \ CryptSIPDllRemoveSignedDataMsg \{ 603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini \Boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows CurrentVersion|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell mappák|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User rendszerhéj-mappák|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>A fájlok integritásának figyelésének engedélyezése 

A FIM csak a Azure Portal Security Center lapjairól érhető el. A FIM használata jelenleg nem REST API.

1. Az **Azure Defender** irányítópultjának **speciális védelmi** területén válassza a **fájl integritásának figyelése** elemet.

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="FIM indítása" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    Megnyílik a **fájl integritásának figyelése** konfiguráció oldal.

    Az egyes munkaterületek esetében az alábbi információk szerepelnek:

    - A múlt héten bekövetkezett módosítások teljes száma (egy kötőjelet láthat, ha a FIM nincs engedélyezve a munkaterületen)
    - A munkaterületnek jelentett számítógépek és virtuális gépek teljes száma
    - A munkaterület földrajzi helye
    - Az Azure-előfizetés, amelyre a munkaterület tartozik

1. Ezen a lapon a következőket használhatja:

    - Az egyes munkaterületek állapotának és beállításainak elérése és megtekintése

    - ![A Frissítési terv ikonja ][4] frissíti a munkaterületet az Azure Defender használatára. Ez az ikon azt jelzi, hogy a munkaterületet vagy előfizetést nem az Azure Defender védi. A FIM-funkciók használatához az előfizetését az Azure Defendernek kell védenie. További információ: [Azure Security Center Free vs Azure Defender engedélyezve](security-center-pricing.md).

    - ![Engedélyezés ikon][3] Engedélyezze a FIM-t a munkaterület alá tartozó összes gépen, és konfigurálja a FIM beállításait. Ez az ikon azt jelzi, hogy a FIM nincs engedélyezve a munkaterületen.

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="A FIM engedélyezése egy adott munkaterülethez":::


    > [!TIP]
    > Ha nincs engedélyezve vagy frissítés gomb, és a szóköz üres, az azt jelenti, hogy a FIM már engedélyezve van a munkaterületen.


1. Válassza az **Engedélyezés** lehetőséget. A munkaterület részletei, beleértve a munkaterületen található Windows és Linux rendszerű gépek számát is.

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="FIM-munkaterület részletei lap":::

   A Windows és a Linux ajánlott beállításai is szerepelnek a felsorolásban.  Bontsa ki a **Windows-fájlok**, a **beállításjegyzék** és a **Linux-fájlok** elemet a javasolt elemek teljes listájának megtekintéséhez.

1. Törölje a jelölőnégyzeteket azokhoz az ajánlott entitásokhoz, amelyeket nem szeretne a FIM által figyelni.

1. A FIM engedélyezéséhez válassza a **Fájlszűrő-figyelés alkalmazása** lehetőséget.

> [!NOTE]
> Bármikor módosíthatja a beállításokat. További információért lásd: a [figyelt entitások szerkesztése](#edit-monitored-entities) .



## <a name="audit-monitored-workspaces"></a>Figyelt munkaterületek naplózása 

A **fájl integritás-figyelési** irányítópultja megjeleníti azokat a munkaterületeket, amelyeken engedélyezve van a FIM. A FIM-irányítópult megnyílik, miután engedélyezte a FIM használatát egy munkaterületen, vagy ha kiválaszt egy munkaterületet a **fájl integritásának figyelése** ablakban, amelyen már engedélyezve van a FIM.

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="A FIM-irányítópult és a hozzá tartozó különféle információs panelek":::

A munkaterület FIM-irányítópultja a következő adatokat jeleníti meg:

- A munkaterülethez csatlakozó számítógépek teljes száma
- A kiválasztott időszakban történt módosítások teljes száma
- A változás típusának részletezése (fájlok, beállításjegyzék)
- A módosítási kategória részletezése (módosított, hozzáadott, eltávolított)

Az irányítópult tetején található **szűrő** lehetőségre kattintva módosíthatja azt az időtartamot, ameddig a módosítások megjelennek.

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="A FIM-irányítópult időintervallum-szűrője":::

A **kiszolgálók** lap felsorolja a munkaterületnek jelentett gépeket. Az irányítópultok az egyes gépeknél a következőket sorolja fel:

- A kiválasztott időszakban történt összes módosítás
- A módosítások vagy a beállításjegyzék változásainak teljes változásainak részletezése

A **naplóbeli keresés** akkor nyílik meg, amikor beírja a számítógép nevét a keresőmezőbe, vagy kiválasztja a számítógépek lapon felsorolt gépet. a naplóbeli keresés megjeleníti a számítógép kiválasztott időszakában végrehajtott összes módosítást. További információért bontsa ki a változást.

![Naplók keresése][8]

A **módosítások** lap (lásd alább) megjeleníti a munkaterület összes változását a kiválasztott időszakban. Minden módosított entitás esetében az irányítópult a következőket sorolja fel:

- A számítógép, amelyen a módosítás történt
- Változás típusa (beállításjegyzék vagy fájl)
- Változás kategóriája (módosított, hozzáadott, eltávolított)
- A módosítás dátuma és időpontja

![A munkaterület módosításai][9]

A **change details** (módosítás részletei) elemre kattintva megadhatja a keresés mezőben megjelenő változást, vagy kijelölhet egy entitást a **módosítások** lapon.

![Változás részletei][10]

## <a name="edit-monitored-entities"></a>Figyelt entitások szerkesztése

1. Térjen vissza a **fájl integritás-figyelési irányítópultra** , és válassza a **Beállítások** lehetőséget.

   ![Beállítások][11]

   A **munkaterület-konfiguráció** három lapot jelenít meg: **Windows beállításjegyzék**, **Windows-fájlok** és Linux- **fájlok**. Minden lap felsorolja az adott kategóriában szerkeszthető entitásokat. Az egyes felsorolt entitások esetében Security Center azonosítja, hogy a FIM engedélyezve van-e (true) vagy nincs engedélyezve (false).  Az entitás szerkesztése lehetővé teszi a FIM engedélyezését vagy letiltását.

   ![Munkaterület konfigurálása][12]

2. Válasszon ki egy Identity Protectiont. Ebben a példában egy elemet választottunk a Windows beállításjegyzékben. Megnyílik **a Change Tracking szerkesztése** .

   ![Szerkesztés vagy módosítás nyomon követése][13]

**A Change Tracking szerkesztése területen a** következőket teheti:

- A fájl integritás-figyelésének engedélyezése (true) vagy Disable (false)
- Adja meg vagy módosítsa az entitás nevét
- Adja meg vagy módosítsa az értéket vagy az elérési utat
- Törölje az entitást, vesse el a módosítást, vagy mentse a módosítást.

## <a name="add-a-new-entity-to-monitor"></a>Új entitás hozzáadása a figyeléshez
1. Térjen vissza a **fájl integritás-figyelési irányítópultra** , és válassza a felül található **Beállítások** lehetőséget. Megnyílik a **munkaterület konfigurációja** .
2. A **munkaterület konfigurálása** területen válassza ki a hozzáadni kívánt entitás típusának lapfülét: Windows beállításjegyzék, Windows-fájlok vagy Linux-fájlok. Ebben a példában a Linux- **fájlokat** választottuk.

   ![Új elem hozzáadása a figyeléshez][14]

3. Válassza a **Hozzáadás** lehetőséget. **A hozzáadás Change Tracking** megnyílik.

   ![Adja meg a kért adatokat][15]

4. A **Hozzáadás** lapon írja be a kért információkat, majd válassza a **Mentés** lehetőséget.

## <a name="disable-monitored-entities"></a>Figyelt entitások letiltása
1. Térjen vissza a **fájl integritásának figyelése** irányítópultra.
2. Válasszon ki egy munkaterületet, ahol a FIM jelenleg engedélyezve van. Ha nincs megadva az engedélyezés gomb vagy a Frissítési terv gomb, egy munkaterület engedélyezve van a FIM-hez.

   ![Válassza ki azt a munkaterületet, ahol a FIM engedélyezve van][16]

3. A fájl integritásának figyelése területen válassza a **Beállítások** lehetőséget.

   ![Beállítások kiválasztása][17]

4. A **munkaterület konfigurálása** területen válassza ki azt a csoportot, ahol az **engedélyezve** beállítás értéke TRUE (igaz).

   ![Munkaterület konfigurációja][18]

5. A **szerkesztés alatt Change Tracking** az ablak értéke false (hamis) értékre **van** állítva.

   ![Beállítás engedélyezve hamis értékre][19]

6. Kattintson a **Mentés** gombra.

## <a name="folder-and-path-monitoring-using-wildcards"></a>Mappa és elérési út figyelése helyettesítő karakterek használatával

Használjon helyettesítő karaktereket a címtárak közötti nyomkövetés egyszerűsítéséhez. A következő szabályok akkor érvényesek, ha a mappák figyelését helyettesítő karakterek használatával konfigurálja:
-   Több fájl nyomon követéséhez helyettesítő karakterek szükségesek.
-   Helyettesítő karakterek csak az elérési út utolsó szegmensében használhatók, például C:\folder\file vagy/etc/*. conf
-   Ha egy környezeti változó érvénytelen elérési utat tartalmaz, az érvényesítés sikeres lesz, de az elérési út sikertelen lesz a leltár futtatásakor.
-   Az elérési út beállításakor kerülje el az általános elérési utakat, például a c: \* . * értéket, ami túl sok mappát fog áthaladni.

## <a name="disable-fim"></a>FIM letiltása
A FIM letiltható. A FIM az Azure Change Tracking megoldást használja a környezet változásainak nyomon követésére és azonosítására. A FIM letiltásával eltávolítja a Change Tracking megoldást a kiválasztott munkaterületről.

1. A FIM letiltásához térjen vissza a **fájl integritásának figyelése** irányítópultra.
2. Jelöljön ki egy munkaterületet.
3. A **fájl integritásának figyelése** területen válassza a **Letiltás** lehetőséget.

   ![FIM letiltása][20]

4. A letiltáshoz válassza az **Eltávolítás** lehetőséget.

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan használhatja a fájlok integritásának figyelését (FIM) a Security Centerban. Ha többet szeretne megtudni a Security Centerről, tekintse meg a következő lapokat:

* [Biztonsági házirendek beállítása](tutorial-security-policy.md) – megtudhatja, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és-erőforráscsoportok számára.
* [Biztonsági javaslatok kezelése](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Azure biztonsági blog](/archive/blogs/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-file-integrity-monitoring/security-center-dashboard.png
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png
[5]: ./media/security-center-file-integrity-monitoring/enable-fim.png
[7]: ./media/security-center-file-integrity-monitoring/filter.png
[8]: ./media/security-center-file-integrity-monitoring/log-search.png
[9]: ./media/security-center-file-integrity-monitoring/changes-tab.png
[10]: ./media/security-center-file-integrity-monitoring/change-details.png
[11]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings.png
[12]: ./media/security-center-file-integrity-monitoring/workspace-config.png
[13]: ./media/security-center-file-integrity-monitoring/edit.png
[14]: ./media/security-center-file-integrity-monitoring/add.png
[15]: ./media/security-center-file-integrity-monitoring/add-item.png
[16]: ./media/security-center-file-integrity-monitoring/fim-dashboard-disable.png
[17]: ./media/security-center-file-integrity-monitoring/fim-dashboard-settings-disabled.png
[18]: ./media/security-center-file-integrity-monitoring/workspace-config-disable.png
[19]: ./media/security-center-file-integrity-monitoring/edit-disable.png
[20]: ./media/security-center-file-integrity-monitoring/disable-fim.png
