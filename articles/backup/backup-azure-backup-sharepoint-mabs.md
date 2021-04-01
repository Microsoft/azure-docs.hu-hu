---
title: SharePoint-farm biztonsági mentése az Azure-ba a MABS használatával
description: A SharePoint-adatai biztonsági mentését és visszaállítását Azure Backup Server használatával végezheti el. Ez a cikk ismerteti a SharePoint-farm konfigurálásához szükséges információkat, hogy a kívánt adatok az Azure-ban is tárolhatók legyenek. A védett SharePoint-adatok a lemezről vagy az Azure-ból is visszaállíthatók.
ms.topic: conceptual
ms.date: 04/26/2020
ms.openlocfilehash: 837aabf739431eebaa6406770620329fe6345eb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89375397"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-mabs"></a>SharePoint-farm biztonsági mentése az Azure-ba a MABS használatával

A SharePoint-farmok biztonsági mentését úgy végezheti el, hogy a Microsoft Azure Backup Server (MABS) használatával ugyanúgy Microsoft Azure a biztonsági mentést, mint az egyéb adatforrások. Azure Backup rugalmasságot biztosít a biztonsági mentési ütemezésben napi, heti, havi vagy éves biztonsági mentési pontok létrehozásához, és adatmegőrzési házirend-beállításokat biztosít a különböző biztonsági mentési pontokhoz. A MABS lehetővé teszi a helyi lemezek másolását a gyors helyreállítási idejű célkitűzésekhez (RTO), valamint a másolatok Azure-ba történő tárolását gazdaságos, hosszú távú adatmegőrzésre.

A SharePoint Azure-ba való biztonsági mentése a MABS hasonló folyamat a SharePoint DPM (Data Protection Manager) helyi biztonsági mentéséhez. Az Azure-ra vonatkozó különös megfontolásokat ebben a cikkben fogjuk feltüntetni.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>A SharePoint által támogatott verziók és kapcsolódó védelmi forgatókönyvek

A támogatott SharePoint-verziók és a MABS szükséges verziók listáját lásd: [a MABS védelmi mátrixa](./backup-mabs-protection-matrix.md)

## <a name="before-you-start"></a>Előkészületek

A SharePoint-farmok Azure-ba történő biztonsági mentése előtt néhány dolgot meg kell erősítenie.

### <a name="whats-not-supported"></a>Nem támogatott műveletek

* A SharePoint-farmok védelmét biztosító MABS nem védik a keresési indexeket vagy az Application Service-adatbázisokat. Az adatbázisok védelmét külön kell konfigurálnia.

* A MABS nem biztosít biztonsági másolatot a kibővíthető fájlkiszolgáló-(SOFS-) megosztásokon tárolt SharePoint SQL Server-adatbázisokról.

### <a name="prerequisites"></a>Előfeltételek

A folytatás előtt győződjön meg arról, hogy teljesítette az [Microsoft Azure Backup használatának előfeltételeit](backup-azure-dpm-introduction.md#prerequisites-and-limitations) a munkaterhelések elleni védelemhez. Az előfeltételek egyes feladatai közé tartozik a Backup-tároló létrehozása, a tár hitelesítő adatainak letöltése, Azure Backup ügynök telepítése és a Azure Backup Server regisztrálása a tárolóban.

További előfeltételek és korlátozások:

* Alapértelmezés szerint a SharePoint védelme során a rendszer az összes tartalom-adatbázist (és a SharePoint_Config és az SharePoint_AdminContent * adatbázist) is védi. Ha például olyan egyéni beállításokat is szeretne alkalmazni, mint a keresési indexek, a sablonok, az alkalmazásszolgáltatás-adatbázisok vagy a felhasználói profil szolgáltatás, ezeket a védelem szempontjából külön-külön konfigurálnia kell. Feltétlenül engedélyeznie kell az összes ilyen típusú funkció és testreszabási fájlokat tartalmazó mappa védelmét.

* A SharePoint-adatbázisok SQL Server-adatforrásként nem védhetők meg. Egy farm biztonsági másolatából különálló adatbázisok is helyreállíthatók.

* Ne feledje, hogy a MABS **helyi rendszerként** fut, és a SQL Server adatbázisok biztonsági mentéséhez rendszergazdai jogosultságokkal kell RENDELKEZNIE az SQL Serverhez tartozó fiókban. Az SQL Server, amelyről biztonsági másolatot szeretne készíteni, állítsa be az NT AUTHORITY\SYSTEM-t a **sysadmin (rendszergazda**) értékre.

* A farm minden 10 000 000 eleméhez legalább 2 GB szabad területnek kell lennie azon a köteten, ahol a MABS mappa található. Ez a terület a katalógus előállításához szükséges. Annak lehetővé tételéhez, hogy a MABS használatával hajtsa végre az elemek (webhelycsoportok, webhelyek, listák, dokumentumtárak, mappák, egyes dokumentumok és listaelemek) adott helyreállítását, a katalógus létrehozása létrehoz egy listát az egyes tartalom-adatbázisokban található URL-címekről. Az URL-címek listáját a helyreállítható elem ablaktáblán tekintheti meg a MABS felügyeleti konzol helyreállítási feladat területén.

* Ha a SharePoint-farmban olyan SQL Server-adatbázisok vannak, amelyek SQL Server aliasokkal vannak konfigurálva, telepítse a SQL Server ügyfél-összetevőket a MABS által védett előtér-webkiszolgálón.

* Az alkalmazástároló elemeinek védelme a SharePoint 2013 szoftverrel nem támogatott.

* A MABS nem támogatja a távoli FILESTREAM védelmét. A védelem csak akkor támogatott, ha a FILESTREAM az adatbázis része.

## <a name="configure-backup"></a>Biztonsági mentés konfigurálása

A SharePoint-farm biztonsági mentéséhez konfigurálja a SharePoint védelmét a ConfigureSharePoint.exe használatával, majd hozzon létre egy védelmi csoportot a MABS-ben.

1. **Futtassa a ConfigureSharePoint.exe fájlt** – Ez az eszköz konfigurálja a SharePoint VSS-író szolgáltatást \(WSS\), és ez biztosítja a védelmi ügynöknek a SharePoint-farm számára a szükséges hitelesítő adatokat. A védelmi ügynök üzembe helyezésekor a ConfigureSharePoint.exe fájl az `<MABS Installation Path\>\bin` előtér- \- webkiszolgáló mappában található.  Ha több WFE-kiszolgálóval rendelkezik, akkor csak az egyikre kell telepítenie. A parancsokat a következő sorrendben futtassa le:

    * A WFE-kiszolgálón a parancssorban navigáljon és futtassa a következőt `\<MABS installation location\>\\bin\\` `ConfigureSharePoint \[\-EnableSharePointProtection\] \[\-EnableSPSearchProtection\] \[\-ResolveAllSQLAliases\] \[\-SetTempPath <path>\]` :

        * A **EnableSharePointProtection** engedélyezi a SharePoint-farm védelmét, engedélyezi a VSS-író használatát, és regisztrálja a DCOM-alkalmazás WssCmdletsWrapper identitását úgy, hogy olyan felhasználóként fusson, amelynek a hitelesítő adatai ezzel a kapcsolóval vannak megadva. Ennek a fióknak farm egyik rendszergazdájának, valamint helyi rendszergazdának kell lennie az előtér\-webkiszolgálón.

        * Az **EnableSPSearchProtection** Engedélyezi a WSS 3.0 SP-keresés védelmét az előtér\-webkiszolgálón a HKLM\\Software\\Microsoft\\ Microsoft Data Protection Manager\\Agent\\2.0\\ alatt található SharePointSearchEnumerationEnabled beállításkulcs használatával, és a WssCmdletsWrapper DCOM-alkalmazás identitását úgy regisztrálja, hogy a parancssori kapcsolóval megadott hitelesítési adatokkal rendelkező felhasználóként fusson. Ennek a fióknak farm egyik rendszergazdájának, valamint helyi rendszergazdának kell lennie az előtér\-webkiszolgálón.

        * A **ResolveAllSQLAliases** a SharePoint VSS-író által jelentett összes aliast megjeleníti, és feloldja azokat a megfelelő SQL Serveren. Emellett a feloldott példányneveiket is megjeleníti. Tükrözött kiszolgálók esetén megjeleníti a tükrözött kiszolgálót is. Az összes olyan aliasnevet jelenti, amely nem oldható fel egy SQL Server.

        * A **SetTempPath** a megadott útvonalra állítja be a TEMP és TMP környezeti változót. Az elemszintű helyreállítás meghiúsul, ha egy nagyméretű webhelycsoportot, helyet, listát vagy tételt helyreállít, és nincs elég hely a farm rendszergazdai ideiglenes mappájához. Ezzel a parancssori kapcsolóval módosíthatja az ideiglenes fájlok mappaútvonalát egy olyan kötet útvonalára, amelyen elegendő hely áll rendelkezésre a helyreállítandó helygyűjtemény vagy hely tárolásához.

    * Adja meg a farm rendszergazdai hitelesítő adatait. Ennek a fióknak a helyi rendszergazdák csoportja tagjának kell lennie a WFE-kiszolgálón. Ha a farm rendszergazdája nem helyi rendszergazda, a következő engedélyeket kell megadnia a WFE-kiszolgálón:

        * Adja meg a **WSS_Admin_WPG** csoport teljes hozzáférését a MABS mappához ( `%Program Files%\Data Protection Manager\DPM\` ).

        * Adjon olvasási hozzáférést a **WSS_Admin_WPG** csoportnak a MABS beállításkulcs ( `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager` ) számára.

        ConfigureSharePoint.exe futtatása után újra kell futtatnia, ha módosul a SharePoint-farm rendszergazdai hitelesítő adatai.

1. Védelmi csoport létrehozásához kattintson a **védelmi**  >  **műveletek**  >  **védelmi csoport létrehozása** elemre az **új védelmi csoport létrehozása** varázsló megnyitásához a MABS-konzolon.

1. A **védelmi csoport típusának kiválasztása** lapon válassza a **kiszolgálók** elemet.

1. A **csoporttagok kiválasztása** területen bontsa ki azt a kiszolgálót, amely a WFE szerepkört tárolja. Ha egynél több WFE-kiszolgáló található, válassza ki a ConfigureSharePoint.exe telepített példányát.

    Ha kibontja a SharePoint Server MABS lekérdezi a VSS-t, hogy megtudja, milyen MABS tud védelemmel ellátni.  Ha a SharePoint-adatbázis távoli, a MABS csatlakozik hozzá. Ha a SharePoint-adatforrások nem jelennek meg, ellenőrizze, hogy a VSS-író fut-e a SharePoint-kiszolgálón és a távoli SQL Serveron, és győződjön meg arról, hogy a MABS-ügynök telepítve van a SharePoint-kiszolgálón és a távoli SQL Server is. Továbbá győződjön meg arról, hogy a SharePoint-adatbázisok nem védettek máshol SQL Server-adatbázisokként.

1. Az **adatvédelmi módszer kiválasztása** lapon adja meg, hogyan szeretné kezelni a rövid és a hosszú \- távú biztonsági mentést. A rövid \- távú biztonsági mentés mindig lemezre történik, és a lemezről az Azure-felhőbe történő biztonsági mentés lehetősége \( rövid vagy hosszú \- távú Azure Backup \) .

1. A **rövid \- távú célok kiválasztása** lapon adja meg, hogyan szeretné biztonsági mentést készíteni a rövid \- távú lemezes tárolásra.   A **megőrzési** időtartam beállításnál megadhatja, hogy mennyi ideig szeretné megőrizni a lemezen tárolt adatok körét. A **szinkronizálás gyakorisága** beállításnál megadhatja, milyen gyakran szeretne növekményes biztonsági másolatot készíteni lemezre. Ha nem szeretné beállítani a biztonsági mentés időközét, akkor közvetlenül egy helyreállítási pont előtt is megtekintheti, hogy a MABS expressz teljes biztonsági mentést futtasson közvetlenül az egyes helyreállítási pontok ütemezése előtt.

1. A lemez lefoglalásának áttekintése lapon tekintse át a védelmi csoport számára lefoglalt tárterület lemezterületét.

    A **teljes adatméret** a biztonsági mentéshez használni kívánt adatmennyiség, a **MABS** pedig a lemezterület, amelyet a MABS a védelmi csoport számára ajánl. A MABS a beállítások alapján kiválasztja az ideális biztonsági mentési kötetet. Lehetőség van azonban arra, hogy a **Disk allocation details** (Lemezfoglalás részletei) panelen módosítsa a biztonsági mentési kötetek választási lehetőségeit. A munkaterhelésekhez válassza ki a legördülő menüből az előnyben részesített tárhelyet. A módosítások megváltoztatják az **Összes tárhely** és a **Szabad tárterület** értékeit az **Elérhető lemezterület** ablaktáblán. A kiépített terület az a tárolási MABS, amely a kötethez való hozzáadást javasolja, hogy zökkenőmentesen folytassa a biztonsági mentéseket a jövőben.

1. A **replika-létrehozási módszer** kiválasztása lapon válassza ki, hogyan szeretné kezelni a kezdeti teljes adatreplikációt.  Ha a hálózaton keresztüli replikálást választja, javasoljuk, hogy válasszon ki egy off-Peak időpontot. Nagyobb adatmennyiségeknél vagy nem optimális hálózati állapotok esetén érdemes lehet az adatokat kapcsolat nélküli módban, cserélhető adathordozó segítségével replikálni.

1. A **Konzisztencia-ellenőrzési beállítások** lépésben válassza ki, hogyan szeretné automatizálni a konzisztencia-ellenőrzéseket. Beállíthatja hogy a rendszer ütemezés szerint futtasson ellenőrzést, vagy csak akkor, amikor a replikaadatok inkonzisztenssé válnak. Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, bármikor lefuttathat egy manuális ellenőrzést, ha a jobb gombbal a MABS-konzol **védelem** területén található védelmi csoportra kattint, és kiválasztja a **konzisztencia-ellenőrzés végrehajtása** elemet.

1. Ha azt választotta, hogy a felhőbe készít biztonsági másolatot az Azure Backup szolgáltatással, akkor ellenőrizze, hogy **Az online védelem adatainak megadása** oldalon ki vannak-e jelölve azok a munkaterhelések, amelyekről biztonsági másolatot szeretne készíteni az Azure-ba.

1. Az **online biztonsági mentés ütemezésének megadása** területen határozza meg, hogy milyen gyakran történjen növekményes biztonsági mentés az Azure-ba. Napi, heti, havi és éves rendszerességet is beállíthat, és megadhatja a futtatás kívánt dátumát és időpontját is. Naponta legfeljebb kétszer történhet biztonsági mentés. Minden alkalommal, amikor egy biztonsági mentés fut, egy adathelyreállítási pont jön létre az Azure-ban a MABS lemezen tárolt biztonsági másolati adatok másolatával.

1. Az **online megőrzési szabály meghatározása lapon** megadhatja, hogy a rendszer hogyan őrizze meg a napi/heti/havi/éves biztonsági másolatokből létrehozott helyreállítási pontokat az Azure-ban.

1. Az **online replikáció kiválasztása** lapon adja meg, hogyan történjen az adatkezdeti teljes replikálás. Replikálhat a hálózaton keresztül, vagy offline biztonsági mentést (offline áttöltést) is végezhet. Az offline biztonsági mentés az Azure Import szolgáltatással működik. [További információk](./backup-azure-backup-import-export.md).

1. Az **Összefoglalás** lapon tekintse át a beállításokat. Miután kiválasztotta a **csoport létrehozása** lehetőséget, az adatműveletek kezdeti replikálása történik. Amikor befejeződik, a védelmi csoport állapota **OK** lesz az **állapot** lapon. Ezután a védelmicsoport-beállításoknak megfelelően megtörténik a biztonsági mentés.

## <a name="monitoring"></a>Figyelés

A védelmi csoport létrehozása után a rendszer elindítja a kezdeti replikálást, és a MABS megkezdi a SharePoint-adatbázis biztonsági mentését és szinkronizálását. A MABS figyeli a kezdeti szinkronizálást és az azt követő biztonsági mentéseket.  A SharePoint-adatok figyelése több módon is lehetséges:

* Az alapértelmezett MABS-figyelés használatával értesítéseket állíthat be a proaktív figyeléshez a riasztások közzétételével és az értesítések konfigurálásával. E-mail értesítéseket küldhet a kritikus, figyelmeztető vagy tájékoztató riasztásokról, valamint a példányosított helyreállítások állapotáról.

* Operations Manager használata esetén központilag tehet közzé riasztásokat.

### <a name="set-up-monitoring-notifications"></a>Figyelési értesítések beállítása

1. A MABS felügyeleti konzol válassza a **figyelés**  >  **műveleti**  >  **Beállítások lehetőséget**.

2. Válassza az **SMTP-kiszolgáló** lehetőséget, írja be a kiszolgáló nevét, a portot és az e-mail-címet, amelyről az értesítéseket küldi a rendszer. A címnek érvényesnek kell lennie.

3. A **hitelesített SMTP-kiszolgáló** területen írjon be egy felhasználónevet és egy jelszót. A felhasználónévnek és a jelszónak az előző lépésben megadott "feladó" címről származó személy tartományi fiókjának kell lennie. Ellenkező esetben az értesítés kézbesítése sikertelen lesz.

4. Az SMTP-kiszolgáló beállításainak teszteléséhez válassza a **Tesztüzenet küldése** lehetőséget, írja be azt az e-mail címet, AHOVÁ a MABS elküldeni a tesztüzenet, majd kattintson **az OK gombra**. Válassza a **Beállítások**  >  **értesítések** lehetőséget, és válassza ki, hogy milyen típusú riasztásokat szeretne kapni a címzetteknek. A **címzettek** mezőbe írja be az egyes címzettek e-mail címét, akiknek a MABS szeretné elküldeni az értesítések másolatait.

### <a name="publish-operations-manager-alerts"></a>Riasztások közzététele az Operations Managerben

1. A MABS felügyeleti konzol válassza a **figyelés**  >  **műveleti**  >  **Beállítások**  >  **riasztások közzététele**  >  **aktív riasztások közzététele** lehetőséget.

2. Miután engedélyezte a **riasztások közzétételét**, minden olyan meglévő MABS-riasztást, amely felhasználói beavatkozást igényelhet, közzé lesz téve a **MABS riasztások** eseménynaplójában. Ezután a MABS-kiszolgálóra telepített Operations Manager ügynök közzéteszi ezeket a riasztásokat a Operations Manager, és folytatja a konzol frissítését új riasztások létrehozásakor.

## <a name="restore-a-sharepoint-item-from-disk-by-using-mabs"></a>SharePoint-elem visszaállítása a lemezről a MABS használatával

A következő példában a SharePoint- *elem helyreállítása* véletlenül törölve lett, és vissza kell állítani.
![MABS SharePoint-Protection4](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection5.png)

1. Nyissa meg a **MABS felügyeleti konzol**. A MABS által védett SharePoint-farmok a **védelem** lapon jelennek meg.

    ![MABS SharePoint-Protection3](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection4.png)
2. Az elem helyreállításának megkezdéséhez válassza a **helyreállítás** lapot.

    ![MABS SharePoint-Protection5](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection6.png)
3. A SharePointban a *SharePoint-elem* helyreállítása a helyreállítási pont tartományán belüli helyettesítő karakteres keresés használatával végezhető el.

    ![MABS SharePoint-Protection6](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection7.png)
4. Válassza ki a megfelelő helyreállítási pontot a keresési eredmények közül, kattintson a jobb gombbal az elemre, majd válassza a **helyreállítás** lehetőséget.
5. A helyreállításhoz különböző helyreállítási pontokat is megkereshet, és kiválaszthatja a helyreállítani kívánt adatbázist vagy elemet. Válassza a **dátum > a helyreállítási idő** lehetőséget, majd válassza ki a megfelelő **adatbázist > SharePoint-farm > helyreállítási pont > elem**.

    ![MABS SharePoint-Protection7](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection8.png)
6. Kattintson a jobb gombbal az elemre, majd **válassza a helyreállítás elemet** a **helyreállítási varázsló** megnyitásához. Kattintson a **Tovább** gombra.

    ![Visszaállítási kijelölés áttekintése](./media/backup-azure-backup-sharepoint/review-recovery-selection.png)
7. Válassza ki a végrehajtani kívánt helyreállítás típusát, majd kattintson a **tovább** gombra.

    ![Helyreállítási típus](./media/backup-azure-backup-sharepoint/select-recovery-type.png)

   > [!NOTE]
   > A példában az **eredeti helyre történő helyreállítás** a következő helyre visszaállítja az elemet az eredeti SharePoint-webhelyre.
   >
   >
8. Válassza ki a használni kívánt **helyreállítási folyamatot** .

   * Ha a SharePoint-farm nem módosult, és megegyezik a visszaállítani kívánt helyreállítási ponttal, válassza a helyreállítás a **helyreállítási Farm használata nélkül** lehetőséget.
   * Ha a SharePoint-farm megváltozott a helyreállítási pont létrehozása óta, válassza **a helyreállítás a helyreállítási Farm használatával** lehetőséget.

     ![Helyreállítási folyamat](./media/backup-azure-backup-sharepoint/recovery-process.png)
9. Adja meg az átmeneti SQL Server példány helyét az adatbázis ideiglenes helyreállításához, és adjon meg egy átmeneti fájlmegosztást a MABS és a SharePoint rendszert futtató kiszolgálón az elem helyreállításához.

    ![Előkészítési Location1](./media/backup-azure-backup-sharepoint/staging-location1.png)

    A MABS csatolja a SharePoint-elemeket tároló tartalom-adatbázist az ideiglenes SQL Server példányhoz. A tartalom-adatbázisból helyreállítja az elemet, és áthelyezi az átmeneti fájl helyére a MABS. Az átmeneti helyen található helyreállított elem most exportálva lesz a SharePoint-farm átmeneti helyére.

    ![Előkészítési Location2](./media/backup-azure-backup-sharepoint/staging-location2.png)
10. Válassza a **helyreállítási beállítások megadása lehetőséget**, majd alkalmazza a biztonsági beállításokat a SharePoint-farmra, vagy alkalmazza a helyreállítási pont biztonsági beállításait. Kattintson a **Tovább** gombra.

    ![Helyreállítási beállítások](./media/backup-azure-backup-sharepoint/recovery-options.png)

    > [!NOTE]
    > Megadhatja a hálózati sávszélesség használatának szabályozását. Ez a művelet a termelési idő alatt a termelési kiszolgálóra is hatással van.
    >
    >
11. Tekintse át az összegző információkat, majd kattintson a **helyreállítás** elemre a fájl helyreállításának megkezdéséhez.

    ![Helyreállítási összefoglalás](./media/backup-azure-backup-sharepoint/recovery-summary.png)
12. Most válassza a **figyelés** fület a **MABS felügyeleti konzol** a helyreállítás **állapotának** megtekintéséhez.

    ![Helyreállítási állapot](./media/backup-azure-backup-sharepoint/recovery-monitoring.png)

    > [!NOTE]
    > A fájl most vissza lett állítva. Frissítheti a SharePoint-webhelyet a visszaállított fájl vizsgálatához.
    >
    >

## <a name="restore-a-sharepoint-database-from-azure-by-using-mabs"></a>SharePoint-adatbázis visszaállítása az Azure-ból a MABS használatával

1. SharePoint tartalom-adatbázis helyreállításához tallózzon a különböző helyreállítási pontok között (ahogy az előzőekben látható), és válassza ki a visszaállítani kívánt helyreállítási pontot.

    ![MABS SharePoint-Protection8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. A SharePoint-katalógus elérhető adatainak megjelenítéséhez kattintson duplán a SharePoint helyreállítási pontra.

   > [!NOTE]
   > Mivel a SharePoint-farm védelme hosszú távú adatmegőrzést biztosít az Azure-ban, a MABS-kiszolgálón nem érhető el katalógus-információ (metaadatok). Ennek eredményeképpen, amikor egy időponthoz tartozó SharePoint tartalmi adatbázist helyre kell állítani, újra kell katalogizálni a SharePoint-farmot.
   >
   >
3. Válassza az **újrakatalogizálás** lehetőséget.

    ![MABS SharePoint-Protection10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Megnyílik a **felhő-újrakatalogizálás** állapota ablak.

    ![MABS SharePoint-Protection11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    A katalogizálás befejezése után az állapot *sikeresre* változik. Válassza a **Bezárás** gombot.

    ![MABS SharePoint-Protection12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. A tartalom-adatbázis struktúrájának lekéréséhez válassza ki a MABS- **helyreállítás** lapon megjelenő SharePoint-objektumot. Kattintson a jobb gombbal az elemre, majd válassza a **helyreállítás** lehetőséget.

    ![MABS SharePoint-Protection13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. Ezen a ponton kövesse a cikkben korábban ismertetett helyreállítási lépéseket a SharePoint tartalmi adatbázis lemezről történő helyreállításához.

## <a name="switching-the-front-end-web-server"></a>A Front-End webkiszolgáló váltása

Ha több előtér-webkiszolgálóval rendelkezik, és át szeretné váltani a MABS által a farm védeleméhez használt kiszolgálót, kövesse az alábbi utasításokat:

Az alábbi eljárás egy kiszolgálófarm példáját használja két előtér-webkiszolgálóval, a *Kiszolgáló1* és a *Kiszolgáló2*. A MABS a *Kiszolgáló1* -t használja a farm védeleméhez. Módosítsa a MABS által a *Kiszolgáló2* -hez használt előtér-webkiszolgálót, hogy el tudja távolítani a *Kiszolgáló1* -t a farmról.

> [!NOTE]
> Ha a MABS által a farm biztosítására használt előtér-webkiszolgáló nem érhető el, az alábbi eljárással módosíthatja az előtér-webkiszolgálót a 4. lépéstől kezdve.

### <a name="to-change-the-front-end-web-server-that-mabs-uses-to-protect-the-farm"></a>A MABS által a farm védeleméhez használt előtér-webkiszolgáló módosítása

1. Állítsa le a SharePoint VSS-író szolgáltatást a *Kiszolgáló1* -kiszolgálón a következő parancs parancssorból történő futtatásával:

    ```CMD
    stsadm -o unregisterwsswriter
    ```

1. A *Kiszolgáló1*-ben nyissa meg a Beállításszerkesztőt, és navigáljon a következő kulcshoz:

   **HKLM\System\CCS\Services\VSS\VssAccessControl**

1. A VssAccessControl alkulcsban felsorolt összes érték ellenőrzését. Ha bármelyik bejegyzés 0 értékű, és egy másik VSS-író fut a társított fiók hitelesítő adatai alatt, módosítsa az értéket 1-re.

1. Telepítsen egy védelmi ügynököt a *Kiszolgáló2*.

   > [!WARNING]
   > Csak akkor válthat webes előtér-kiszolgálókat, ha mindkét kiszolgáló ugyanazon a tartományon van.

1. A *Kiszolgáló2*-on a parancssorban módosítsa a könyvtárat, `_MABS installation location_\bin\` és futtassa a **ConfigureSharepoint**. További információ a ConfigureSharePoint: a [biztonsági mentés konfigurálása](#configure-backup).

1. Válassza ki azt a védelmi csoportot, amelyhez a kiszolgálófarm tartozik, majd válassza a **védelmi csoport módosítása** lehetőséget.

1. A csoport módosítása varázslóban a **csoporttagok kiválasztása** lapon bontsa ki a *Kiszolgáló2* elemet, és válassza ki a kiszolgálófarmot, majd fejezze be a varázslót.

   A konzisztencia-ellenőrzés elindul.

1. Ha végrehajtotta a 6. lépést, most eltávolíthatja a kötetet a védelmi csoportból.

## <a name="next-steps"></a>Következő lépések

Lásd az [Exchange Server biztonsági mentése](backup-azure-exchange-mabs.md) című cikket.
Tekintse meg a [SQL Server biztonsági mentése](backup-azure-sql-mabs.md) című cikket.
