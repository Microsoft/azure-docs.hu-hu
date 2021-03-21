---
title: Rendszerállapot és operációs rendszer nélküli helyreállítás védelme
description: A Azure Backup Server használatával biztonsági mentést készíthet a rendszerállapotról, és operációs rendszer nélküli helyreállítást (BMR) biztosíthat.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: c5096158ca0e76ca03577347d8dd3e1419a33ca0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021622"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Rendszerállapot biztonsági mentése és visszaállítása operációs rendszer nélküli számítógépre Azure Backup Server

Azure Backup Server biztonsági mentést készít a rendszerállapotról, és operációs rendszer nélküli helyreállítást (BMR) biztosít.

* **Rendszerállapot biztonsági mentése**: biztonsági másolatot készít az operációs rendszer fájljairól. Ez a biztonsági másolat lehetővé teszi a helyreállítást a számítógép indításakor, de a rendszerfájlok és a beállításjegyzék elvesznek. A rendszerállapot biztonsági mentése a következő elemeket tartalmazza:
  * Tartományi tag: rendszerindító fájlok, COM+-osztályok regisztrációs adatbázisa, beállításjegyzék
  * Tartományvezérlő: Windows Server Active Directory (NTDS), rendszerindító fájlok, COM+-osztályok regisztrációs adatbázisa, beállításjegyzék, rendszerkötet (SYSVOL)
  * A fürtszolgáltatást futtató számítógép: a fürt kiszolgálói metaadatai
  * Tanúsítványszolgáltatásokat futtató számítógép: tanúsítvány-és adatkezelési szolgáltatások
* Operációs rendszer **nélküli biztonsági mentés**: biztonsági másolatot készít az operációsrendszer-fájlokról és a kritikus köteteken lévő összes információról, kivéve a felhasználói adatmennyiséget. Definíció szerint a BMR biztonsági mentése a rendszerállapot biztonsági mentését is tartalmazza. Védelmet biztosít, ha a számítógép nem indul el, és mindent helyre kell állítani.

Az alábbi táblázat összefoglalja, hogy a biztonsági mentést és helyreállítást hogyan végezheti el. További információ a rendszerállapot-és a BMR által védett alkalmazás-verziókról: [Mi a Azure Backup Server biztonsági mentés?](backup-mabs-protection-matrix.md).

|Backup|Probléma|Helyreállítás Azure Backup Server biztonsági másolatból|Rendszerállapot biztonsági mentése esetén helyreállítható|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Fájladatok**<br /><br />Az adatok rendszeres biztonsági mentése<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett fájladatok|Y|N|N|
|**Fájladatok**<br /><br />A fájlinformációk Azure Backup Server biztonsági mentése<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|Y|Y|
|**Fájladatok**<br /><br />A fájlinformációk Azure Backup Server biztonsági mentése<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatkötetek érintetlenek)|N|N|Y|
|**Fájladatok**<br /><br />A fájlinformációk Azure Backup Server biztonsági mentése<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatkötetek elvesztek)|Y|N|Y<br /><br />BMR, majd a biztonsági másolatban tárolt fájlok rendszeres helyreállítása|
|**SharePoint-adatszolgáltatások**<br /><br />Azure Backup Server a farmon tárolt adatbiztonsági másolatok<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett hely, lista, listaelem, dokumentum|Y|N|N|
|**SharePoint-adatszolgáltatások**<br /><br />Azure Backup Server a farmon tárolt adatbiztonsági másolatok<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|Y|Y|
|**SharePoint-adatszolgáltatások**<br /><br />Azure Backup Server a farmon tárolt adatbiztonsági másolatok<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Vészhelyreállítás|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />A Hyper-V-gazdagép vagy-vendég Azure Backup Server biztonsági mentése<br /><br />A gazdagép teljes biztonsági mentése (BMR)/rendszerállapotának biztonsági mentése|Elveszett virtuális gép|Y|N|N|
|Hyper-V<br /><br />A Hyper-V-gazdagép vagy-vendég Azure Backup Server biztonsági mentése<br /><br />A gazdagép teljes biztonsági mentése (BMR)/rendszerállapotának biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|Y|Y|
|Hyper-V<br /><br />A Hyper-V-gazdagép vagy-vendég Azure Backup Server biztonsági mentése<br /><br />A gazdagép teljes biztonsági mentése (BMR)/rendszerállapotának biztonsági mentése|Elvesztett Hyper-V-gazdagép (a virtuális gépek nem sérültek)|N|N|Y|
|Hyper-V<br /><br />A Hyper-V-gazdagép vagy-vendég Azure Backup Server biztonsági mentése<br /><br />A gazdagép teljes biztonsági mentése (BMR)/rendszerállapotának biztonsági mentése|Elvesztett Hyper-V-gazdagép (a virtuális gépek elvesztek)|N|N|Y<br /><br />BMR, amelyet a rendszeres Azure Backup Server helyreállítás követ|
|SQL Server/Exchange<br /><br />Azure Backup Server alkalmazás biztonsági mentése<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett alkalmazásadatok|Y|N|N|
|SQL Server/Exchange<br /><br />Azure Backup Server alkalmazás biztonsági mentése<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett vagy sérült operációs rendszer|N|Y|Y|
|SQL Server/Exchange<br /><br />Azure Backup Server alkalmazás biztonsági mentése<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatbázis és a tranzakciós naplófájlok nem sérültek)|N|N|Y|
|SQL Server/Exchange<br /><br />Azure Backup Server alkalmazás biztonsági mentése<br /><br />Teljes biztonsági mentés (BMR)/rendszerállapot biztonsági mentése|Elveszett kiszolgáló (az adatbázis és a tranzakciós naplófájlok elvesztek)|N|N|Y<br /><br />BMR helyreállítás, amelyet a rendszeres Azure Backup Server helyreállítás követ|

## <a name="how-system-state-backup-works"></a>A rendszerállapot biztonsági mentésének működése

Rendszerállapot biztonsági mentésének futtatásakor a Backup kiszolgáló a Windows Server biztonsági másolatával kommunikál a kiszolgáló rendszerállapotának biztonsági másolatának kéréséhez. Alapértelmezés szerint a Backup Server és a Windows Server biztonsági másolat a legnagyobb rendelkezésre álló szabad területtel rendelkező meghajtót használja. A meghajtóval kapcsolatos információkat a rendszer a *PSDataSourceConfig.xml* fájlba menti.

Testreszabhatja azt a meghajtót, amelyet a Backup Server használ a rendszerállapot biztonsági mentéséhez:

1. A védett kiszolgálón lépjen a *C:\Program Files\Microsoft adatvédelem Manager\MABS\Datasources*.
1. Nyissa meg a *PSDataSourceConfig.xml* fájlt szerkesztésre.
1. Módosítsa a meghajtó betűjeléhez tartozó \<FilesToProtect\> értéket.
1. Mentse és zárja be a fájlt.

Ha a védelmi csoport úgy van beállítva, hogy megvédje a számítógép rendszerállapotát, akkor futtasson egy konzisztencia-ellenőrzést. Ha riasztás jön létre, válassza a **védelmi csoport módosítása** lehetőséget a riasztásban, majd hajtsa végre a lapokat a varázslóban. Ezután futtasson egy újabb konzisztencia-ellenőrzést.

Ha a védelmi kiszolgáló egy fürtben található, akkor lehet, hogy a legnagyobb szabad területtel rendelkező meghajtóként van kiválasztva a fürt meghajtója. Ha a meghajtó tulajdonosa egy másik csomópontra vált, és a rendszerállapot biztonsági mentése fut, akkor a meghajtó nem érhető el, és a biztonsági mentés sikertelen lesz. Ebben az esetben módosítsa *PSDataSourceConfig.xml* úgy, hogy helyi meghajtóra mutasson.

Ezután Windows Server biztonsági másolat létrehoz egy *WindowsImageBackup* nevű mappát a visszaállítási mappa gyökerében. Ahogy Windows Server biztonsági másolat létrehozza a biztonsági másolatot, a rendszer az összes adatterületet ebbe a mappába helyezi. A biztonsági mentés befejezésekor a rendszer átviszi a fájlt a biztonsági mentési kiszolgáló számítógépére. Tekintse meg az alábbi információkat:

* Ez a mappa és annak tartalma nem törlődik a biztonsági mentés vagy az átvitel befejeződése után. Ennek a legjobb módja az, hogy a tárhely a következő biztonsági mentés befejezésének idejére van fenntartva.
* A mappa minden biztonsági mentéshez létrejön. Az idő és a dátum bélyegzője a rendszerállapot utolsó biztonsági mentésének időpontját tükrözi.

## <a name="how-bmr-backup-works"></a>A BMR biztonsági mentése működése

A BMR (beleértve a rendszerállapot biztonsági mentését is) a biztonsági mentési feladatot a rendszer közvetlenül a biztonságimásolat-kiszolgáló számítógép egyik megosztására menti. A rendszer nem menti a védett kiszolgáló egyik mappájába.

A Backup Server meghívja a Windows Server biztonsági másolat, és megosztja az adott BMR biztonsági másolatának replikájának kötetét. Ebben az esetben nem szükséges Windows Server biztonsági másolat a legnagyobb szabad területtel rendelkező meghajtó használatára. Ehelyett a feladatokhoz létrehozott megosztást használja.

A biztonsági mentés befejezésekor a rendszer átviszi a fájlt a biztonsági mentési kiszolgáló számítógépére. A naplók tárolása a *C:\Windows\Logs\WindowsServerBackup* történik.

## <a name="prerequisites-and-limitations"></a>Előfeltételek és korlátozások

* A BMR nem támogatott a Windows Server 2003 rendszerű számítógépeken, illetve az ügyfél operációs rendszerét futtató számítógépeken.

* A BMR és a rendszerállapot védelme ugyanazon számítógép esetében nem lehetséges különböző védelmi csoportokban.

* A biztonsági mentési kiszolgáló számítógépe nem tudja megóvni magát a BMR.

* A szalag rövid távú védelme (lemezről szalagra vagy D2T) nem támogatott a BMR. A szalagos hosszú távú tárolás (lemezről szalagra vagy D2D2T) támogatott.

* A BMR-védelem esetében Windows Server biztonsági másolat a védett számítógépre kell telepíteni.

* A BMR-védelem esetében a rendszerállapot-védelemtől eltérően a biztonsági mentési kiszolgálónak nincs lemezterületre vonatkozó követelménye a védett számítógépen. Windows Server biztonsági másolat közvetlenül továbbítja a biztonsági mentéseket a biztonságimásolat-kiszolgáló számítógépére. A biztonságimásolat-áthelyezési feladat nem jelenik meg a biztonsági mentési kiszolgáló **feladatok** nézetében.

* A Backup Server 30 GB helyet foglal le a BMR lévő replika köteten. A tárterület kiosztása a védelmi csoport módosítása varázsló **lemez kiosztása** lapján módosítható. Vagy használhatja a Get-DatasourceDiskAllocation és a Set-DatasourceDiskAllocation PowerShell-parancsmagokat is. A helyreállítási pont kötetén a BMR-védelemnek körülbelül 6 GB-nak kell lennie öt nap megőrzéséhez.
  * A replika kötetének mérete nem csökkenthető 15 GB-nál kevesebbre.
  * A Backup Server nem számítja ki a BMR-adatforrás méretét. Minden kiszolgáló esetében 30 GB-ot feltételez. Módosítsa az értéket a környezetében várható BMR biztonsági mentések méretétől függően. Nagyjából kiszámíthatja a BMR biztonsági mentésének méretét a felhasznált lemezterület összegeként az összes kritikus köteten. Kritikus kötetek = rendszerindító kötet + rendszerkötet + kötet, amely a rendszerállapot-adatmennyiséget (például Active Directory) üzemelteti.

* Ha a rendszerállapot-védelemről BMR-védelemre vált, a BMR-védelem kevesebb helyet igényel a *helyreállítási pont kötetén*. A köteten található felesleges terület azonban nem lett visszaállítva. A védelmi csoport módosítása varázsló **lemez kiosztása** lapján manuálisan is csökkentheti a kötet méretét. Vagy használhatja a Get-DatasourceDiskAllocation és a Set-DatasourceDiskAllocation PowerShell-parancsmagokat is.

    Ha a rendszerállapot-védelemről BMR-védelemre vált, a BMR-védelem több helyet igényel a *replika kötetén*. A kötet automatikusan ki van bővítve. Ha módosítani szeretné az alapértelmezett terület foglalásait, használja a Modify-DiskAllocation PowerShell-parancsmagot.

* Ha a BMR-védelemről rendszerállapot-védelemre vált, több helyre lesz szüksége a helyreállítási pont kötetén. Előfordulhat, hogy a biztonsági mentési kiszolgáló megpróbálja automatikusan megnövelni a kötetet. Ha a tárolóhelyen nincs elég hely, hiba történik.

    Ha a BMR-védelemről rendszerállapot-védelemre vált, szüksége lesz a védett számítógép területére. A tárterületre azért van szükség, mert a rendszerállapot-védelem először a helyi számítógépre írja a replikát, majd átviszi a replikát a biztonsági mentési kiszolgáló számítógépére.

## <a name="before-you-begin"></a>Előkészületek

1. **Azure Backup Server üzembe helyezése**. Ellenőrizze, hogy a biztonsági mentési kiszolgáló megfelelően van-e telepítve. További információkért lásd:
    * [A Azure Backup Server rendszerkövetelményei](/system-center/dpm/install-dpm#setup-prerequisites)
    * [A Backup Server védelmi mátrixa](backup-mabs-protection-matrix.md)

1. **Állítsa be a tárolót**. A biztonsági mentési adatok lemezen, szalagon és a felhőben is tárolhatók az Azure-ban. További információ: az [adattároló előkészítése](/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Állítsa be a védelmi ügynököt**. Telepítse a védelmi ügynököt arra a számítógépre, amelyre biztonsági másolatot szeretne készíteni. További információ: [a DPM védelmi ügynök telepítése](/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Rendszerállapot biztonsági mentése és teljes biztonsági mentés

Rendszerállapot biztonsági mentése és operációs rendszer nélküli számítógép:

1. A Create új védelmi csoport varázsló megnyitásához a biztonsági mentési kiszolgáló felügyeleti konzol válassza a **védelmi**  >  **műveletek**  >  **védelmi csoport létrehozása** lehetőséget.

1. A **védelmi csoport típusának kiválasztása** lapon válassza a **kiszolgálók** lehetőséget, majd kattintson a **tovább** gombra.

1. A **csoporttagok kiválasztása** lapon bontsa ki a számítógépet, majd válassza a **BMR** vagy a **rendszerállapot** lehetőséget.

    Ne feledje, hogy a BMR és a rendszerállapotot nem lehet a különböző csoportokban lévő ugyanazon a számítógépen is védelemmel ellátni. Emellett a BMR kijelölésekor a rendszer automatikusan engedélyezi a rendszerállapotot. További információ: [védelmi csoportok telepítése](/system-center/dpm/create-dpm-protection-groups).

1. Az **adatvédelmi módszer kiválasztása** lapon válassza ki, hogyan szeretné kezelni a rövid távú biztonsági mentést és a hosszú távú biztonsági mentést.

    A rövid távú biztonsági mentés mindig lemezre történik, és a lemezről az Azure-ba történő biztonsági mentés lehetőségét Azure Backup (rövid távú vagy hosszú távú) használatával. A hosszú távú biztonsági mentés alternatívájaként a hosszú távú biztonsági mentést egy különálló szalagos eszközre vagy a Backup Serverhez csatlakoztatott szalagos tárba kell beállítani.

1. A **Short-Term céljainak kiválasztása** lapon válassza ki, hogyan kell biztonsági mentést készíteni a lemez rövid távú tárolására:
    * A **megőrzési** időtartam beállításnál válassza ki, hogy mennyi ideig szeretné megőrizni a lemezen lévő adatok megtartását.
    * A **szinkronizálás gyakorisága** beállításnál válassza ki, hogy milyen gyakran fusson a növekményes biztonsági mentés lemezre. Ha nem szeretné beállítani a biztonsági mentés időközét, akkor **közvetlenül egy helyreállítási pont előtt** választhatja ki. A Backup Server expressz teljes biztonsági mentést futtat az egyes helyreállítási pontok ütemezése előtt.

1. Ha szalagon szeretné tárolni az adatok hosszú távú tárolását, akkor a **Long-Term céljainak megadása** lapon válassza ki, hogy mennyi ideig tartsa meg a szalagos adatok (1 – 99 év).
    1. A **biztonsági mentés gyakorisága** beállításnál válassza ki, hogy milyen gyakran szeretné futtatni a szalagos biztonsági mentést. A gyakoriság a kiválasztott megőrzési tartományon alapul:
        * Ha a megőrzési időtartam 1 – 99 év, akkor naponta, hetente, kéthetente, havonta, negyedévente, félévente vagy évente készíthet biztonsági mentést.
        * Ha a megőrzési tartomány 1 – 11 hónap, akkor naponta, hetente, kéthetente vagy havonta készíthet biztonsági mentést.
        * Ha a megőrzési tartomány 1 – 4 hét, a napi vagy heti biztonsági mentést végezheti el.

    1. A **szalag és könyvtár részleteinek kiválasztása** lapon válassza ki a használni kívánt szalagot és tárat. Adja meg azt is, hogy az adattömörítés és a titkosítás titkosított legyen-e.

1. A **lemez kiosztásának áttekintése** lapon tekintse át a védelmi csoport számára elérhető tárterület lemezterületét.

    * Az adatmennyiség **teljes mérete** a biztonsági mentésre használni kívánt adatmennyiség.
    * **Azure Backup Server kiépíthető** lemezterület az a terület, amelyet a biztonsági mentési kiszolgáló a védelmi csoport számára javasol. A Backup Server ezeket a beállításokat használja az ideális biztonsági mentési kötet kiválasztásához. A kötetek biztonsági mentésének lehetőségeit a **lemez foglalásának részletei** között módosíthatja.
    * A munkaterhelések esetében a legördülő menüben válassza ki az előnyben részesített tárolót. A módosítások megváltoztatják az **Összes tárhely** és a **Szabad tárterület** értékeit az **Elérhető lemezterület** ablaktáblán. A kiépített terület az a tárterület, amelyet a Backup Server javasol, hogy a kötethez hozzáadja a zökkenőmentes biztonsági mentéseket.

1. A **replika-létrehozási módszer kiválasztása** lapon válassza ki, hogyan szeretné kezelni a kezdeti teljes adatreplikációt.

    Ha úgy dönt, hogy replikálja a hálózaton keresztül, javasoljuk, hogy válasszon ki egy off-Peak időpontot. Nagy mennyiségű vagy az optimálisnál kisebb hálózati feltételek esetén érdemes lehet a cserélhető adathordozón keresztül replikálni az adatkapcsolatot.

1. A **konzisztencia-ellenőrzési beállítások kiválasztása** lapon válassza ki a konzisztencia-ellenőrzések automatizálásának módját.

    Dönthet úgy, hogy csak akkor futtatja az ellenőrzését, ha a replika adatai inkonzisztensek, vagy egy adott időpontban. Ha nem szeretne automatikus konzisztencia-ellenőrzést beállítani, bármikor lefuttathatja a manuális ellenőrzést. Manuális ellenőrzés futtatásához a biztonsági mentési felügyeleti konzol kiszolgáló **védelme** területén kattintson a jobb gombbal a védelmi csoportra, majd válassza a **konzisztencia-ellenőrzés végrehajtása** lehetőséget.

1. Ha úgy döntött, hogy a Azure Backup használatával készít biztonsági másolatot a felhőre, az **online védelmi adatok megadása** lapon válassza ki azokat a munkaterheléseket, amelyekről biztonsági másolatot szeretne készíteni az Azure-ba.

1. Az **online biztonsági mentési ütemterv megadása** lapon válassza ki, hogy milyen gyakran történjen a növekményes biztonsági mentés az Azure-ba.

    A biztonsági mentéseket ütemezheti minden nap, hét, hónap és év futtatásához. Kiválaszthatja azt az időpontot és dátumot is, amikor a biztonsági mentéseket futtatni szeretné. Naponta legfeljebb kétszer történhet biztonsági mentés. Minden alkalommal, amikor egy biztonsági mentés fut, egy adathelyreállítási pont jön létre az Azure-ban a biztonsági mentési kiszolgáló lemezén tárolt biztonsági mentési adatok másolatával.

1. Az **online adatmegőrzési szabály megadása** lapon válassza ki, hogyan történjen a napi, heti, havi és éves biztonsági másolatok alapján létrehozott helyreállítási pontok tárolása az Azure-ban.

1. Az **online replikáció kiválasztása** lapon válassza ki, hogyan történjen az adatkezdeti teljes replikálás.

    A replikálást végezheti a hálózaton keresztül, vagy offline biztonsági mentést végezhet (kapcsolat nélküli előkészítés). Az offline biztonsági mentés az Azure importálási szolgáltatását használja. További információ: [Offline biztonsági mentési munkafolyamat Azure Backup-ben](offline-backup-azure-data-box.md).

1. Az  **Összefoglalás** lapon tekintse át a beállításokat. Miután kiválasztotta a **csoport létrehozása** lehetőséget, az adatműveletek kezdeti replikálása történik. Amikor az adatreplikálás befejeződik, az **állapot** lapon a védelmi csoport állapota **OK**. A biztonsági mentések a védelmi csoport beállításai szerint történnek.

## <a name="recover-system-state-or-bmr"></a>Rendszerállapot vagy BMR helyreállítása

A BMR, illetve a rendszerállapot mentése helyreállítható egy hálózati helyre. Ha biztonsági mentést készít a BMR, akkor a Windows helyreállítási környezet (WinRE) segítségével indítsa el a rendszert, és kapcsolódjon a hálózathoz. Ezután a Windows Server biztonsági másolat (WSB) eszközével végezzen helyreállítást a hálózati helyről. Ha biztonsági mentést készít a rendszerállapotról, a hálózati helyről történő helyreállításhoz használja a Windows Server biztonsági másolat.

### <a name="restore-bmr"></a>BMR-visszaállítás

A helyreállítás futtatása a biztonsági mentési kiszolgáló számítógépén:

1. A **helyreállítás** ablaktáblán keresse meg a helyreállítani kívánt számítógépet. Ezután válassza az operációs rendszer nélküli **helyreállítás** lehetőséget.

1. Az elérhető helyreállítási pontok félkövérrel szedve jelennek meg a naptárban. Válassza ki a használni kívánt helyreállítási pont dátumát és időpontját.

1. A  **helyreállítási típus kiválasztása** lapon válassza a **Másolás hálózati mappába** lehetőséget.

1. A **célhely megadása** lapon válassza ki a másolt adatelem célját.

    Ne feledje, hogy a célhelynek elegendő helyet kell biztosítania az adatmennyiséghez. Javasoljuk, hogy hozzon létre egy új mappát a célhelyhez.

1. A **helyreállítási beállítások megadása** lapon válassza ki a biztonsági beállításokat. Ezután válassza ki, hogy szeretné-e használni a Tárolóhálózati (SAN) alapú hardveres pillanatképeket a gyorsabb helyreállítás érdekében. Ez a beállítás csak akkor érhető el, ha:
    * Van egy SAN, amely biztosítja ezt a funkciót.
    * Létrehozhatja és feloszthatja a klónt, hogy az írható legyen.
    * A védett számítógép és a biztonságimásolat-kiszolgáló számítógép ugyanahhoz a hálózathoz csatlakozik.

1. Értesítési beállítások beállítása.
1. A **jóváhagyás** lapon válassza a **helyreállítás** lehetőséget.

A megosztás helyének beállítása:

1. A visszaállítási helyen lépjen a biztonsági mentést tartalmazó mappára.

1. Ossza meg a *WindowsImageBackup* felett egy szinttel rendelkező mappát, hogy a megosztott mappa gyökerében a *WindowsImageBackup* mappa legyen.

    Ha nem osztja meg ezt a mappát, a RESTORE utasítás nem találja a biztonsági mentést. A WinRE használatával történő kapcsolódáshoz szüksége van egy olyan megosztásra, amelyet a megfelelő IP-címmel és hitelesítő adatokkal a WinRE-ben érhet el.

A System visszaállítása:

1. Indítsa el azt a számítógépet, amelyen vissza kívánja állítani a rendszerképet a visszaállítani kívánt rendszerhez tartozó Windows DVD használatával.

1. Az első lapon ellenőrizze a nyelv és a területi beállítás beállításait. A **telepítés** lapon válassza a **számítógép javítása** lehetőséget.

1. A **rendszer-helyreállítási beállítások** lapon válassza a **számítógép visszaállítása a korábban létrehozott rendszerkép használatával** lehetőséget.

1. A rendszerkép **biztonsági mentésének kiválasztása** lapon válassza a rendszerkép   >  **speciális**  >  **keresése a hálózaton rendszerképekhez** lehetőséget. Ha megjelenik egy figyelmeztetés, válassza az **Igen** lehetőséget. Lépjen a megosztás elérési útjára, adja meg a hitelesítő adatokat, majd válassza ki a helyreállítási pontot. A rendszer megkeresi az adott helyreállítási pontban elérhető biztonsági másolatokat. Válassza ki a használni kívánt helyreállítási pontot.

1. A **biztonsági másolat visszaállítási módjának kiválasztása** lapon válassza a **lemezek formázása és újraparticionálása** elemet. A következő lapon ellenőrizze a beállításokat.

1. A visszaállítás megkezdéséhez válassza a **Befejezés** lehetőséget. Újraindítás szükséges.

### <a name="restore-system-state"></a>Rendszerállapot visszaállítása

A helyreállítás futtatása a biztonsági mentési kiszolgálón:

1. A **helyreállítás** ablaktáblán keresse meg a helyreállítani kívánt számítógépet, majd válassza az operációs rendszer nélküli **helyreállítás** lehetőséget.

1. Az elérhető helyreállítási pontok félkövérrel szedve jelennek meg a naptárban. Válassza ki a használni kívánt helyreállítási pont dátumát és időpontját.

1. A **helyreállítási típus kiválasztása** lapon válassza a **Másolás hálózati mappába** lehetőséget.

1. A **célhely megadása** lapon válassza ki, hova szeretné másolni az Adatmásolás helyét.

    Ne feledje, hogy a kiválasztott célhelynek elegendő mozgásteret kell biztosítania az adatmennyiséghez. Javasoljuk, hogy hozzon létre egy új mappát a célhelyhez.

1. A **helyreállítási beállítások megadása** lapon válassza ki a biztonsági beállításokat. Ezután válassza ki, hogy SAN-alapú hardveres pillanatképeket kíván-e használni a gyorsabb helyreállítás érdekében. Ez a beállítás csak akkor érhető el, ha:
    * Van egy SAN, amely biztosítja ezt a funkciót.
    * Létrehozhatja és feloszthatja a klónt, hogy az írható legyen.
    * A védett számítógép és a biztonságimásolat-kiszolgáló kiszolgáló ugyanahhoz a hálózathoz csatlakozik.

1. Értesítési beállítások beállítása.
1. A **jóváhagyás** lapon válassza a **helyreállítás** lehetőséget.

Windows Server biztonsági másolat futtatása:

1. Válassza ki  >    >  **a**  >  **következőt** a kiszolgáló helyreállítása műveletekkel.

1. Válasszon **másik kiszolgálót**, válassza a **hely típusának megadása** lapot, majd kattintson a **távoli megosztott mappa** lehetőségre. Adja meg a helyreállítási pontot tartalmazó mappa elérési útját.

1. A **helyreállítási típus kiválasztása** lapon válassza a **rendszerállapot** lehetőséget.

1. A **rendszerállapot-helyreállítás helyének kiválasztása** lapon válassza az  **eredeti hely** lehetőséget.

1. A **jóváhagyás** lapon válassza a **helyreállítás** lehetőséget.

1. A visszaállítás után indítsa újra a kiszolgálót.

A rendszerállapot-visszaállítást a parancssorból is futtathatja:

1. Indítsa el Windows Server biztonsági másolat a helyreállítani kívánt számítógépen.

1. A termékazonosító verziójának lekéréséhez írja be a következőt a parancssorba:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. A verzió azonosítójának használatával indítsa el a rendszerállapot-visszaállítást. A parancssorba írja be a következőt:

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Erősítse meg, hogy el szeretné indítani a helyreállítást. A folyamatot a parancssori ablakban tekintheti meg. Létrejön egy visszaállítási napló.

1. A visszaállítás után indítsa újra a kiszolgálót.
