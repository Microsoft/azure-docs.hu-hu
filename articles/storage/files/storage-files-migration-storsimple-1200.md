---
title: StorSimple 1200 migrálása az Azure File Syncre
description: Megtudhatja, hogyan telepíthet át egy StorSimple 1200 sorozatú virtuális készüléket Azure File Syncba.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/09/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8562d63bf227fff665c70674c7fe66922bce9992
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882280"
---
# <a name="storsimple-1200-migration-to-azure-file-sync"></a>StorSimple 1200 migrálása az Azure File Syncre

A StorSimple 1200 sorozat egy virtuális berendezés, amely egy helyszíni adatközpontban fut. Az adatok áttelepíthetők a készülékről egy Azure File Sync környezetbe. A Azure File Sync az alapértelmezett és a stratégiai hosszú távú Azure-szolgáltatás, amelyet a StorSimple berendezések áttelepíthetnek.

A StorSimple 1200-es sorozat [életciklusa](https://support.microsoft.com/en-us/lifecycle/search?alpha=StorSimple%201200%20Series) december 2022-ig ér véget.  Fontos, hogy a lehető leghamarabb megkezdje az áttelepítés megtervezését. Ez a cikk a Azure File Syncba való sikeres áttelepítéshez szükséges háttérbeli ismereteket és áttelepítési lépéseket ismerteti. 

## <a name="azure-file-sync"></a>Azure File Sync

> [!IMPORTANT]
> A Microsoft elkötelezte magát, hogy segítse az ügyfeleket az áttelepítés során. E-mail-cím a AzureFilesMigration@microsoft testreszabott áttelepítési tervhez, valamint segítségnyújtás az áttelepítés során.

A Azure File Sync egy Microsoft Cloud Service, amely két fő összetevő alapján érhető el:

* A fájlok szinkronizálása és a felhő szintjei.
* A fájlmegosztás natív tárolóként az Azure-ban, amely több protokollon keresztül érhető el, például az SMB és a file REST használatával. Az Azure-fájlmegosztás összehasonlítható a Windows Server egy fájlmegosztási fájljával, amelyet a hálózati meghajtóként natív módon csatlakoztathat. Támogatja a fontos fájl-megbízhatósági szempontokat, például az attribútumokat, az engedélyeket és az időbélyegeket. A StorSimple eltérően a felhőben tárolt fájlok és mappák értelmezéséhez nincs szükség alkalmazásra vagy szolgáltatásra. A felhőben az általános célú fájlkiszolgáló adatai, valamint egyes alkalmazásadatok tárolásának ideális és legrugalmasabb megközelítése.

Ez a cikk az áttelepítési lépésekre összpontosít. Ha az áttelepítés előtt szeretne többet megtudni a Azure File Syncről, javasoljuk a következő cikkeket:

* [Azure File Sync – áttekintés](./storage-sync-files-planning.md "Áttekintés")
* [Azure File Sync – üzembe helyezési útmutató](storage-sync-files-deployment-guide.md)

## <a name="migration-goals"></a>Migrálási célok

A cél a termelési adatok integritásának garantálása, valamint a rendelkezésre állás garantálása. Az utóbbi megköveteli, hogy a leállások minimálisra kerüljenek, így az csak kis mértékben meghaladhatja a normál karbantartási időszakokat.

## <a name="storsimple-1200-migration-path-to-azure-file-sync"></a>A StorSimple 1200 áttelepítési útvonala Azure File Sync

Azure File Sync-ügynök futtatásához helyi Windows Server szükséges. A Windows Server legalább 2012R2-kiszolgáló lehet, de ideális esetben a Windows Server 2019.

Számos alternatív áttelepítési útvonal létezik, és az is előfordulhat, hogy túl sok cikket hoz létre, és bemutatjuk, hogy miért viselik a kockázatot vagy hátrányt az útvonalon az ajánlott eljárásnak megfelelően ebben a cikkben.

:::image type="content" source="media/storage-files-migration-storsimple-shared/storsimple-1200-migration-overview.png" alt-text="Áttelepítési útvonal áttekintése a cikkben lejjebb található lépéseket követve.":::

Az előző képen a cikk fejezeteinek megfelelő lépések láthatók.

### <a name="step-1-provision-your-on-premises-windows-server-and-storage"></a>1. lépés: a helyszíni Windows Server-kiszolgáló és-tároló kiépítése

1. Hozzon létre egy Windows Server 2019-at a minimális 2012R2 virtuális gép vagy fizikai kiszolgálóként. A Windows Server feladatátvételi fürt is támogatott.
2. Hozzon létre vagy adjon hozzá közvetlenül csatlakoztatott tárolót (a DAS-t a NAS-hez képest, amely nem támogatott). A Windows Server-tároló méretének egyenlőnek vagy annál nagyobbnak kell lennie, mint a virtuális StorSimple 1200 készülék rendelkezésre álló kapacitásának mérete.

### <a name="step-2-configure-your-windows-server-storage"></a>2. lépés: a Windows Server-tároló konfigurálása

Ebben a lépésben a StorSimple tárolási struktúráját (köteteit és megosztásait) a Windows Server tárolási struktúrájára képezi le.
Ha módosítani kívánja a tárolási struktúrát, azaz a kötetek számát, a kötetek adatmappáinak társítását, vagy az aktuális SMB/NFS-megosztások feletti vagy alatti almappa-struktúrát, akkor most itt az idő, hogy figyelembe vegye ezeket a változtatásokat.
Ha Azure File Sync konfigurálása után módosítja a fájl-és a mappa szerkezetét, nehézkes, és el kell kerülni.
Ez a cikk feltételezi, hogy Ön a 1:1 leképezését használja, ezért a cikk lépéseinek követése során figyelembe kell vennie a megfeleltetések változásait.

* Az éles környezetek egyikének sem kell végződnie a Windows Server rendszer kötetén. A felhő-rétegek nem támogatottak a rendszerköteteken. Azonban ez a funkció szükséges az áttelepítéshez, valamint a folyamatos műveletek StorSimple-helyettesítéshez.
* Adja meg a Windows Serveren a StorSimple 1200 virtuális készüléken megegyező számú kötetet.
* Konfigurálja a szükséges Windows Server-szerepköröket, szolgáltatásokat és beállításokat. Javasoljuk, hogy a Windows Server frissítéseinek használatát az operációs rendszer biztonságának és naprakészen tartása érdekében válassza. Hasonlóképpen azt javasoljuk, hogy Microsoft Update a Microsoft-alkalmazások naprakészen tartása, beleértve a Azure File Sync-ügynököt is.
* A következő lépések elolvasása előtt ne konfigurálja a mappákat és a megosztásokat.

### <a name="step-3-deploy-the-first-azure-file-sync-cloud-resource"></a>3. lépés: az első Azure File Sync felhőalapú erőforrás üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

### <a name="step-4-match-your-local-volume-and-folder-structure-to-azure-file-sync-and-azure-file-share-resources"></a>4. lépés: a helyi kötetek és mappák struktúrájának egyeztetése Azure File Sync és Azure-fájlmegosztás erőforrásaihoz

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="step-5-provision-azure-file-shares"></a>5. lépés: az Azure-fájlmegosztás kiépítése

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

### <a name="step-6-configure-windows-server-target-folders"></a>6. lépés: a Windows Server cél mappáinak konfigurálása

Az előző lépések során minden olyan szempontot figyelembe vett, amely a szinkronizálási topológiák összetevőit fogja meghatározni. Itt az ideje, hogy felkészítse a kiszolgálót a feltöltéshez szükséges fájlok fogadására.

Hozza létre az **összes** mappát, amely a saját Azure-fájlmegosztást szinkronizálja.
Fontos, hogy kövesse a korábban dokumentált mappastruktúrát. Ha például úgy döntött, hogy több, helyi SMB-megosztást szinkronizál egyetlen Azure-fájlmegosztást, akkor a köteten egy közös gyökérmappa alá kell helyeznie őket. Hozza létre a célként megadott gyökérmappa-mappát a köteten.

Az Ön által kiépített Azure-fájlmegosztás számának meg kell egyeznie az ebben a lépésben létrehozott mappák számával és a legfelső szinten szinkronizálni kívánt kötetek számával.

### <a name="step-7-deploy-the-azure-file-sync-agent"></a>7. lépés: a Azure File Sync ügynök üzembe helyezése

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

### <a name="step-8-configure-sync"></a>8. lépés: a szinkronizálás konfigurálása

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!WARNING]
> **Ügyeljen arra, hogy bekapcsolja a Felhőbeli rétegek bekapcsolását.** Erre akkor van szükség, ha a helyi kiszolgálón nincs elég hely a StorSimple felhőalapú tárolóban tárolt adatai teljes méretének tárolására. Állítsa be az áttelepítéshez ideiglenesen az 99%-os mennyiségű szabad területre vonatkozó előállítási szabályzatot.

Ismételje meg a szinkronizálási csoport létrehozásának lépéseit és a megfelelő kiszolgáló mappa hozzáadását kiszolgálói végpontként az összes Azure-fájlmegosztás/-kiszolgáló helye számára, amelyet a szinkronizáláshoz kell konfigurálni.

### <a name="step-9-copy-your-files"></a>9. lépés: a fájlok másolása

Az alapszintű áttelepítési módszer egy RoboCopy a StorSimple virtuális készülékről a Windows Serverre, és Azure File Sync az Azure-fájlmegosztást.

Futtassa az első helyi másolatot a Windows Server célmappájában:

* Azonosítsa a virtuális StorSimple-berendezés első helyét.
* Azonosítsa a Windows Server megfelelő mappáját, amely már konfigurálva van Azure File Sync.
* A másolás elindítása a RoboCopy használatával

A következő RoboCopy-parancs felidézi a StorSimple Azure Storage-ból származó fájlokat a helyi StorSimple, majd áthelyezi őket a Windows Server célmappába. A Windows Server szinkronizálja az Azure-fájlmegosztás (ok) val. Mivel a helyi Windows Server-kötet betelik, a Felhőbeli rétegek beindulnak, és a már sikeresen szinkronizált fájlokat. A Felhőbeli rétegek elég helyet teremtenek a StorSimple virtuális készülékről történő másolás folytatásához. A Felhőbeli rétegek ellenőrzése óránként egyszer megtekintheti, hogy mi szinkronizált, és szabadítson fel lemezterületet a 99%-os mennyiségű szabad terület eléréséhez.

```console
Robocopy /MT:32 /UNILOG:<file name> /TEE /B /MIR /COPYALL /DCOPY:DAT <SourcePath> <Dest.Path>
```

Háttér

:::row:::
   :::column span="1":::
      /MT
   :::column-end:::
   :::column span="1":::
      Lehetővé teszi, hogy a RoboCopy több szálon fusson. Az alapértelmezett érték 8, Max 128.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /UNILOG:<file name>
   :::column-end:::
   :::column span="1":::
      Az állapotot a NAPLÓFÁJLba UNICODE-ként adja vissza (felülírja a meglévő naplót).
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /TEE
   :::column-end:::
   :::column span="1":::
      A konzol ablakának kimenete. Egy naplófájlban a kimenettel együtt használatos.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /B
   :::column-end:::
   :::column span="1":::
      A RoboCopy szolgáltatást ugyanazon a módban futtatja, amikor a biztonságimásolat-készítő alkalmazás használni fogja. Lehetővé teszi, hogy a RoboCopy olyan fájlokat helyezzen át, amelyekhez az aktuális felhasználónak nincs engedélye.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /MIR
   :::column-end:::
   :::column span="1":::
      Lehetővé teszi a RoboCopy parancs többszöri futtatását ugyanazon cél/cél esetén egymás után. Ez azonosítja a korábban másolt fájlt, és kihagyja azt. Csak a módosítások, kiegészítések és *törlések* lesznek feldolgozva, amelyek az utolsó Futtatás óta történtek. Ha a parancs korábban nem volt futtatva, semmi nincs megadva. Ez egy kiváló megoldás, amely továbbra is aktívan használt és módosítható.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      a fájlmásolás hűsége (az alapértelmezett érték a/COPY: DAT), a másolási jelzők: D = adat, A = attribútumok, T = időbélyeg, S = biztonság = NTFS ACL, O = tulajdonos adatai, U = naplózási információ
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /COPYALL
   :::column-end:::
   :::column span="1":::
      A fájl összes adatának másolása (egyenértékű a következő/COPY: DATSOU)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="1":::
      /DCOPY: copyflag [s]
   :::column-end:::
   :::column span="1":::
      a címtárak másolásának hűsége (az alapértelmezett érték a/DCOPY: DA), a másolási jelzők: D = az adat, A = attribútumok, A T = timestamps
   :::column-end:::
:::row-end:::

Amikor első alkalommal futtatja a RoboCopy parancsot, a felhasználók és az alkalmazások továbbra is hozzáférnek a StorSimple fájlokhoz és mappákhoz, és esetleg módosíthatják azt. Lehetséges, hogy a RoboCopy feldolgozta a címtárat, a következőre lép, majd egy felhasználó a forrás helye (StorSimple) egy olyan fájlt ad hozzá, módosít vagy töröl, amely most nem lesz feldolgozva ebben a RoboCopy-futtatásban. Ez rendben van.

Az első futtatás arról szól, hogy az adatmennyiséget a helyi környezetbe helyezi át, a Windows Serverre és a felhőbe történő biztonsági mentést Azure File Sync használatával. Ez hosszú időt is igénybe vehet, attól függően, hogy:

* letöltési sávszélesség
* a StorSimple Cloud Service felidézési sebessége
* a feltöltési sávszélesség
* azon elemek (fájlok és mappák) száma, amelyeket bármelyik szolgáltatásnak fel kell dolgoznia

A kezdeti Futtatás befejezése után futtassa újra a parancsot.

A második alkalommal, amikor a rendszer gyorsabban befejeződik, mert csak az utolsó Futtatás óta végrehajtott módosításokat kell továbbítania. Ezek a változások valószínűleg helyiek a StorSimple, mert a közelmúltban vannak. Ez tovább csökkenti az időt, mert a felhőből való visszahívás szükségessége csökken. A második futtatás során a rendszer továbbra is felhalmozhat új módosításokat.

Ismételje meg ezt a folyamatot, amíg meggyőződött arról, hogy a befejezéshez szükséges idő egy elfogadható állásidő.

Ha úgy gondolja, hogy az állásidő elfogadható, és készen áll arra, hogy offline állapotba hozza a StorSimple helyet, tegye meg a következőt: például távolítsa el az SMB-megosztást, hogy egyetlen felhasználó sem férhet hozzá a mappához, vagy tegyen meg semmilyen más olyan lépést, amely megakadályozza, hogy a tartalom megváltozzon a StorSimple a mappában.

Futtasson egy utolsó RoboCopy kört. Ezzel felveszi a módosításokat, amelyek esetleg kimaradtak.
Az utolsó lépés elvégzésének időtartama a RoboCopy vizsgálat sebességétől függ. A korábbi Futtatás időtartamának mérésével megbecsülheti az időt (amely az állásidővel egyenlő).

Hozzon létre egy megosztást a Windows Server mappában, és módosítsa a DFS-N központi telepítését úgy, hogy mutasson rá. Ügyeljen arra, hogy ugyanazokat a megosztási szintű engedélyeket adja meg, mint a StorSimple SMB-megosztáson.

Elvégezte a megosztások/csoportok egy közös gyökerébe vagy kötetbe való áttelepítését. (Attól függően, hogy mit rendelt le, és úgy döntött, hogy az adott Azure-fájlmegosztás szükséges.)

A másolatok közül néhányat párhuzamosan is futtathat. Javasoljuk, hogy egyszerre egy Azure-fájlmegosztás hatókörét dolgozza fel.

> [!WARNING]
> Ha áthelyezte a StorSimple összes adatait a Windows Serverre, és az áttelepítés befejeződött: térjen vissza a Azure Portal **minden** _ szinkronizálási csoportba, és állítsa be a felhő-rétegek kötetének szabad területének százalékos értékét a gyorsítótár kihasználtságának megfelelő értékre, azaz 20%-ot. 

A felhő-rétegek kötetének szabad területére vonatkozó házirend olyan kötet szintjén működik, amelynek több kiszolgálói végpontja is szinkronizálva van. Ha a szabad területet még egy kiszolgálói végponton is módosítani szeretné, a szinkronizálás továbbra is alkalmazza a legszigorúbb szabályt, és megkísérli a 99%-os szabad lemezterület fenntartását, így a helyi gyorsítótár nem végezhető el a várt módon. Kivéve, ha a célja, hogy csak a ritkán használt, archivált adatmennyiséget tartalmazó kötet névterét adja meg.

## <a name="troubleshoot"></a>Hibaelhárítás

A legvalószínűbb probléma az, hogy a RoboCopy parancs a Windows Server oldalon a "Volume Full" * értékkel meghiúsul. Ha ez a helyzet, akkor a letöltési sebesség valószínűleg jobb, mint a feltöltési sebesség. A felhő-rétegek a szinkronizált helyi Windows Server-lemezről óránként egyszer elürítik a tartalmat.

A szinkronizálási folyamat és a Felhőbeli rétegek felszabadítása szabad lemezterületet szabadít fel. Megfigyelheti, hogy a Fájlkezelőben a Windows Serveren.

Ha a Windows-kiszolgáló elegendő rendelkezésre álló kapacitással rendelkezik, a parancs újbóli futtatása megoldja a problémát. Ha ezt a helyzetet tapasztalja, nem szakítja meg a biztonságot, és nyugodtan mozoghat. A parancs futtatásának kellemetlensége az egyetlen következmény.

Más Azure File Sync problémákra is futtathat.
Ha ez megtörténik, tekintse meg a **hivatkozást Azure file Sync hibaelhárítási útmutatót**.

## <a name="relevant-links"></a>Kapcsolódó hivatkozások

Áttelepítési tartalom:

* [StorSimple 8000 sorozat áttelepítési útmutató](storage-files-migration-storsimple-8000.md)

Azure File Sync tartalom:

* [Az AFS áttekintése](./storage-sync-files-planning.md)
* [Az AFS telepítési útmutatója](./storage-how-to-create-file-share.md)
* [AFS – hibaelhárítás](storage-sync-files-troubleshoot.md)