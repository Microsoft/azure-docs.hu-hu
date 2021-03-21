---
title: StorSimple 8000 sorozat biztonsági mentési célként a Backup Exec | Microsoft Docs
description: Ismerteti a StorSimple biztonsági mentési célhelyének konfigurációját a Veritas Backup Exec szolgáltatással.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/05/2016
ms.author: matd
ms.openlocfilehash: 66a1e22282864d0425173504735d6beb42b76ad7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967261"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple biztonsági mentési célként a Backup Exec

## <a name="overview"></a>Áttekintés

Az Azure StorSimple egy hibrid felhőalapú tárolási megoldás a Microsofttól. A StorSimple az exponenciális Adatnövekedés bonyolultságát az Azure Storage-fióknak a helyszíni megoldás kiterjesztéseként való használatával, valamint a helyszíni tárolók és a Felhőbeli tárolók közötti automatikus adatmennyiség-növelésével foglalkozik.

Ebből a cikkből megbeszéljük a StorSimple-integrációt a Veritas Backup Exec és az ajánlott eljárásokkal mindkét megoldás integrálásához. Javaslatot teszünk arra is, hogyan állíthatja be a Backup Exec szolgáltatást a StorSimple-vel való legjobb integrációhoz. A legjobb módszer, ha a biztonsági mentést az egyéni biztonsági mentési követelmények és a szolgáltatói szerződések (SLA-EK) teljesítése érdekében elvárjuk az ajánlott eljárásokkal, a biztonsági mentési építészekkel és a rendszergazdákkal.

Bár bemutatjuk a konfigurációs lépéseket és a legfontosabb fogalmakat, ez a cikk nem jelent részletes konfigurációs vagy telepítési útmutatót. Feltételezzük, hogy az alapszintű összetevők és az infrastruktúra üzemképes állapotban van, és készen áll arra, hogy támogassa a leírt fogalmakat.

### <a name="who-should-read-this"></a>Kinek érdemes ezt elolvasni?

A cikkben található információk a rendszergazdák, a Storage-rendszergazdák és a Storage, a Windows Server 2012 R2, az Ethernet, a Cloud Services és a Backup Exec szolgáltatásokkal kapcsolatos ismeretekkel rendelkező tároló-építészek számára nyújtanak segítséget.

## <a name="supported-versions"></a>Támogatott verziók

-   [Backup Exec 16 és újabb verziók](https://www.veritas.com/content/support/en_US/article.100040087)
-   [StorSimple 3. frissítés és újabb verziók](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Miért StorSimple biztonsági mentési célként?

A StorSimple jó választás a biztonsági mentési célra, mert:

-   Szabványos, helyi tárterületet biztosít a biztonsági mentési alkalmazások számára, amely a gyors biztonsági mentési célhelyként használható a módosítások nélkül. A legutóbbi biztonsági mentések gyors visszaállításához használhatja a StorSimple-t is.
-   A felhőalapú rétegek zökkenőmentesen integrálva vannak egy Azure Cloud Storage-fiókkal a költséghatékony Azure Storage használatához.
-   A szolgáltatás automatikusan kihelyezett tárhelyet biztosít a vész-helyreállításhoz.

## <a name="key-concepts"></a>Fő fogalmak

A tárolási megoldásokhoz hasonlóan a megoldás tárolási teljesítményére, SLA-ra, a változás mértékére és a kapacitás növekedésére vonatkozó igények alapos kiértékelése elengedhetetlen a sikerhez. A fő elképzelés az, hogy a felhőalapú rétegek bevezetésével a felhőbe való hozzáférési idő és az átviteli sebesség alapvető szerepet játszik a StorSimple a feladatához.

A StorSimple úgy lett kialakítva, hogy olyan alkalmazások számára biztosítson tárhelyet, amelyek jól meghatározott munkakészleten (forró adatokon) működnek. Ebben a modellben a munkakészletet a helyi szinten tárolja a rendszer, és a fennmaradó nem működő/hideg/archivált adathalmazt a felhőbe tömöríti. Ez a modell a következő ábrán látható. A közel lapos zöld vonal a StorSimple-eszköz helyi szintjein tárolt adatmennyiséget jelöli. A piros vonal a StorSimple-megoldásban tárolt összes adatmennyiséget jelenti az összes szinten. A Flat zöld vonal és az exponenciális piros görbe közötti térköz a felhőben tárolt összes adatmennyiséget jelöli.

**StorSimple-rétegek** 
 ![ StorSimple-rétegű diagram](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

Ebben az architektúrában azt tapasztalhatja, hogy a StorSimple ideális megoldás biztonsági mentési célként való működésre. A StorSimple a következőre használhatja:
-   Végezze el a leggyakoribb visszaállításokat a helyi munkakészletből.
-   A felhőben a telephelyen kívüli vész-helyreállítást és a régebbi adatmennyiségeket is használhatja, ahol a visszatárolás kevésbé gyakori.

## <a name="storsimple-benefits"></a>A StorSimple előnyei

A StorSimple olyan helyszíni megoldást biztosít, amely zökkenőmentesen integrálható Microsoft Azureekkel a helyszíni és a Felhőbeli tárolás zökkenőmentes elérésének kihasználásával.

A StorSimple a helyszíni eszköz (SSD) és a soros csatlakozású SCSI (SAS) tároló, valamint az Azure Storage közötti automatikus adatréteget használ. Az automatikus rétegek az SSD-és SAS-rétegek gyakran hozzáférnek a helyi adatterületekhez. A ritkán használt adatátvitelt az Azure Storage-ba helyezi át.

A StorSimple az alábbi előnyöket kínálja:

-   Egyedi deduplikált és tömörítési algoritmusok, amelyek a felhőben használják a példátlanul megkettőzési szintek elérését
-   Magas rendelkezésre állás
-   Geo-replikáció az Azure geo-Replication használatával
-   Azure-integráció
-   Adattitkosítás a felhőben
-   A vész-helyreállítás és-megfelelőség javítása

Bár a StorSimple két fő telepítési forgatókönyvet mutat be (elsődleges biztonsági mentési cél és másodlagos biztonsági mentési cél), alapvetően egyszerű, blokkos tárolóeszköz. A StorSimple végzi a tömörítést és a demásolást. Zökkenőmentesen küldi és kéri le az adatokat a felhő és az alkalmazás és a fájlrendszer között.

További információ a StorSimple [: StorSimple 8000 sorozat: hibrid felhőalapú tárolási megoldás](storsimple-overview.md). Emellett áttekintheti a [technikai StorSimple 8000 sorozatának specifikációit](./storsimple-8000-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> A StorSimple-eszköz biztonsági mentési célként való használata csak a StorSimple 8000 Update 3 és újabb verziók esetében támogatott.

## <a name="architecture-overview"></a>Az architektúra áttekintése

Az alábbi táblázatok az eszköz modell-architektúra kezdeti útmutatóját mutatják be.

**StorSimple-kapacitások helyi és Felhőbeli tároláshoz**

| Tárkapacitás       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Helyi tárolási kapacitás | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Felhőalapú tárolási kapacitás | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* A tárterület mérete feltételezi, hogy nincs ismétlődés vagy tömörítés.

**StorSimple kapacitások elsődleges és másodlagos biztonsági mentésekhez**

| Biztonsági mentési forgatókönyv  | Helyi tárolási kapacitás  | Felhőalapú tárolási kapacitás  |
|---|---|---|
| Elsődleges biztonsági mentés  | A helyi tárterületen tárolt legutóbbi biztonsági másolatok a helyreállítási időkorlát (RPO) kielégítése érdekében a gyors helyreállítás érdekében | A biztonsági mentési előzmények (RPO) a Felhőbeli kapacitásban is elférnek |
| Másodlagos biztonsági mentés | A biztonsági mentési adatmennyiség másodlagos másolata a Felhőbeli kapacitásban tárolható  | N/A  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple elsődleges biztonsági mentési célként

Ebben a forgatókönyvben a StorSimple-kötetek a biztonsági mentési alkalmazásba kerülnek, mint a biztonsági mentések egyetlen tárháza. Az alábbi ábra egy megoldási architektúrát mutat be, amelyben az összes biztonsági mentés StorSimple rétegű köteteket használ a biztonsági mentéshez és a visszaállításhoz.

![StorSimple elsődleges biztonsági mentési cél logikai diagramként](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Elsődleges cél biztonsági mentési logikai lépései

1.  A biztonsági mentési kiszolgáló kapcsolatba lép a cél biztonsági mentési ügynökkel, és a biztonsági mentési ügynök adatokat küld a biztonsági mentési kiszolgálónak.
2.  A biztonsági mentési kiszolgáló beírja az adatot a StorSimple rétegű kötetekre.
3.  A biztonsági mentési kiszolgáló frissíti a katalógus-adatbázist, majd befejezi a biztonsági mentési feladatot.
4.  A pillanatkép-parancsfájlok elindítja a StorSimple Cloud Snapshot Managert (Indítás vagy törlés).
5.  A biztonsági mentési kiszolgáló egy adatmegőrzési házirend alapján törli a lejárt biztonsági másolatokat.


### <a name="primary-target-restore-logical-steps"></a>Elsődleges cél visszaállításának logikai lépései

1.  A biztonsági mentési kiszolgáló elindítja a megfelelő adatok visszaállítását a tároló adattárból.
2.  A biztonságimásolat-készítő ügynök fogadja az adatait a biztonsági mentési kiszolgálóról.
3.  A biztonsági mentési kiszolgáló befejezi a visszaállítási feladatot.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple másodlagos biztonsági mentési célként

Ebben az esetben a StorSimple-köteteket elsősorban hosszú távú adatmegőrzésre vagy archiválásra használják.

Az alábbi ábra egy olyan architektúrát mutat be, amelyben a kezdeti biztonsági mentések és visszaállítja a cél nagy teljesítményű kötetet. Ezeket a biztonsági másolatokat a rendszer átmásolja és archiválja egy StorSimple rétegű kötetre egy beállított ütemezés szerint.

Fontos a nagy teljesítményű kötet méretezése, hogy kezelni tudja az adatmegőrzési házirend kapacitását és a teljesítményre vonatkozó követelményeket.

![StorSimple másodlagos biztonsági mentési célként szolgáló logikai diagramként](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Másodlagos cél biztonsági mentési logikai lépései

1.  A biztonsági mentési kiszolgáló kapcsolatba lép a cél biztonsági mentési ügynökkel, és a biztonsági mentési ügynök adatokat küld a biztonsági mentési kiszolgálónak.
2.  A biztonsági mentési kiszolgáló a nagy teljesítményű tárolóba írja az adatot.
3.  A biztonsági mentési kiszolgáló frissíti a katalógus-adatbázist, majd befejezi a biztonsági mentési feladatot.
4.  A biztonsági mentési kiszolgáló egy adatmegőrzési házirend alapján másolja a biztonsági másolatokat a StorSimple.
5.  A pillanatkép-parancsfájlok elindítja a StorSimple Cloud Snapshot Managert (Indítás vagy törlés).
6.  A biztonsági mentési kiszolgáló egy adatmegőrzési házirend alapján törli a lejárt biztonsági másolatokat.

### <a name="secondary-target-restore-logical-steps"></a>Másodlagos cél visszaállításának logikai lépései

1.  A biztonsági mentési kiszolgáló elindítja a megfelelő adatok visszaállítását a tároló adattárból.
2.  A biztonságimásolat-készítő ügynök fogadja az adatait a biztonsági mentési kiszolgálóról.
3.  A biztonsági mentési kiszolgáló befejezi a visszaállítási feladatot.

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

A megoldás üzembe helyezéséhez három lépés szükséges:
1. Készítse elő a hálózati infrastruktúrát.
2. Telepítse a StorSimple-eszközt biztonsági mentési célként.
3. A Backup Exec üzembe helyezése.

A következő szakaszokban részletesen ismertetjük az egyes lépéseket.

### <a name="set-up-the-network"></a>A hálózat beállítása

Mivel a StorSimple olyan megoldás, amely integrálva van az Azure-felhővel, a StorSimple aktív és működő kapcsolatra van szüksége az Azure-felhőhöz. Ez a kapcsolat olyan műveletekhez használatos, mint a Felhőbeli Pillanatképek, a felügyelet és a metaadatok átvitele, valamint a régebbi, kevésbé elérhető adatok leválasztása az Azure Cloud Storage-ba.

A megoldás optimális végrehajtása érdekében javasoljuk, hogy kövesse ezeket a hálózatkezelési ajánlott eljárásokat:

-   A StorSimple-rétegek Azure-hoz való csatlakoztatására szolgáló hivatkozásnak meg kell felelnie a sávszélességre vonatkozó követelményeknek. Ennek eléréséhez alkalmazza a szükséges szolgáltatásminőség-(QoS-) szintet az infrastruktúra-kapcsolókra, hogy az megfeleljen a RPO és a helyreállítási időre vonatkozó célkitűzés (RTO) SLA-nak.
-   Az Azure Blob Storage-hozzáférési késések maximális száma 80 MS lehet.

### <a name="deploy-storsimple"></a>StorSimple üzembe helyezése

A részletes StorSimple üzembe helyezési útmutató: a helyszíni StorSimple- [eszköz üzembe helyezése](./storsimple-8000-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Backup Exec üzembe helyezése

A Backup Exec telepítési ajánlott eljárásai: [ajánlott eljárások a Backup Exec telepítéséhez](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464).

## <a name="set-up-the-solution"></a>A megoldás beállítása

Ebben a szakaszban néhány konfigurációs példát mutatunk be. Az alábbi példák és javaslatok a legalapvetőbb és alapvető implementációt szemléltetik. Előfordulhat, hogy ez a megvalósítás nem vonatkozik közvetlenül az adott biztonsági mentési követelményekre.

### <a name="set-up-storsimple"></a>StorSimple beállítása

| StorSimple üzembe helyezési feladatai  | További megjegyzések |
|---|---|
| A helyszíni StorSimple-eszköz üzembe helyezése. | Támogatott verziók: 3. frissítés és újabb verziók. |
| Kapcsolja be a biztonsági mentési célt. | Ezekkel a parancsokkal bekapcsolhatja vagy kikapcsolhatja a biztonsági mentési cél üzemmódot, és lekérheti az állapotot. További információ: [távoli kapcsolódás StorSimple-eszközhöz](./storsimple-8000-remote-connect.md).</br> A biztonsági mentési mód bekapcsolása: `Set-HCSBackupApplianceMode -enable` . </br> A biztonsági mentési mód kikapcsolásához: `Set-HCSBackupApplianceMode -disable` . </br> A biztonsági mentési mód beállításainak aktuális állapotának beolvasása: `Get-HCSBackupApplianceMode` . |
| Hozzon létre egy közös mennyiségi tárolót a kötethez, amely a biztonsági mentési adatait tárolja. A mennyiségi tárolóban lévő összes érték deduplikált. | A StorSimple mennyiségi tárolók a deduplikáló tartományokat határozzák meg.  |
| Hozzon létre StorSimple-köteteket. | Hozzon létre a méretekkel rendelkező köteteket a lehető leghamarabb a várt használathoz, mert a kötet mérete befolyásolja a Felhőbeli pillanatkép időtartamát. További információ a kötetek méretének [megtartásáról: adatmegőrzési szabályzatok](#retention-policies).</br> </br> Használjon StorSimple rétegű köteteket, és jelölje be a **kötet használata a ritkábban használt archiválási** mezőkhöz jelölőnégyzetet. </br> Csak a helyileg rögzített kötetek használata nem támogatott. |
| Hozzon létre egy egyedi StorSimple biztonsági mentési szabályzatot az összes biztonsági mentési cél kötethez. | A StorSimple biztonsági mentési szabályzata határozza meg a kötet konzisztencia-csoportját. |
| Tiltsa le az ütemtervet, mivel a pillanatképek lejárnak. | A pillanatképek feldolgozás utáni műveletként lesznek aktiválva. |

### <a name="set-up-the-host-backup-server-storage"></a>A gazdagép biztonsági mentési kiszolgáló tárterületének beállítása

Állítsa be a gazdagép biztonsági mentési kiszolgálójának tárolóját az alábbi irányelvek szerint:  

- Ne használjon átfedő köteteket (amelyet a Windows Lemezkezelés hozott létre). Az átfedő lemezek nem támogatottak.
- Formázza a köteteket az NTFS használatával az 64-KB foglalási mérettel.
- Rendelje hozzá a StorSimple-köteteket közvetlenül a Backup Exec-kiszolgálóhoz.
    - Használjon iSCSI-t fizikai kiszolgálókhoz.
    - Használjon csatlakoztatott lemezeket a virtuális kiszolgálókhoz.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Ajánlott eljárások a StorSimple és a Backup Exec számára

Állítsa be a megoldást az alábbi fejezetek útmutatásai szerint.

### <a name="operating-system-best-practices"></a>Az operációs rendszerre vonatkozó ajánlott eljárások

- Tiltsa le a Windows Server-titkosítást és a deduplikált fájlrendszert az NTFS fájlrendszerben.
- Tiltsa le a Windows Server töredezettségmentesítését a StorSimple köteteken.
- Tiltsa le a Windows Server-indexelést a StorSimple köteteken.
- Futtasson víruskereső-vizsgálatot a forrás gazdagépen (ne a StorSimple-köteteken).
- A [Windows Server alapértelmezett karbantartásának](/windows/win32/w8cookbook/automatic-maintenance) kikapcsolása a Feladatkezelő eszközben. Ezt a következő módszerek egyikével teheti meg:
  - Kapcsolja ki a karbantartási konfigurátort a Windows Feladatütemezőben.
  - Töltse le a [PsExec](/sysinternals/downloads/psexec) a Windows Sysinternals webhelyről. A PsExec letöltése után futtassa a Azure PowerShell rendszergazdaként, és írja be a következőt:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple ajánlott eljárások

  -   Győződjön meg arról, hogy a StorSimple-eszköz frissült a [3. vagy újabb frissítéssel](./index.yml).
  -   Az iSCSI-és a Felhőbeli forgalom elkülönítése. Dedikált iSCSI-kapcsolatok használata a StorSimple és a biztonsági mentési kiszolgáló közötti adatforgalomhoz.
  -   Győződjön meg arról, hogy a StorSimple-eszköz dedikált biztonsági mentési cél. A vegyes munkaterhelések nem támogatottak, mert hatással vannak a RTO és a RPO.

### <a name="backup-exec-best-practices"></a>A Backup Exec ajánlott eljárásai

-   A Backup Exec szolgáltatást a kiszolgáló helyi meghajtójára kell telepíteni, nem pedig StorSimple-köteten.
-   Állítsa be a Backup Exec Storage **párhuzamos írási műveleteit** a maximálisan engedélyezett értékre.
    -   Állítsa be a Backup Exec Storage **blokkot és a puffer méretét** 512 kb-ra.
    -   Kapcsolja be a Backup Exec Storage **pufferelt olvasását és írását**.
-   A StorSimple támogatja a Backup Exec teljes és növekményes biztonsági mentését. Azt javasoljuk, hogy ne használjon szintetikus és különbözeti biztonsági mentést.
-   A biztonsági mentési adatfájloknak csak egy adott feladatokhoz kell tartalmazniuk. Például a különböző feladatokhoz tartozó adathordozók nem adhatók hozzá.
-   A feladatok ellenőrzésének letiltása. Ha szükséges, az ellenőrzést a legutóbbi biztonsági mentési feladatot követően kell ütemezni. Fontos tisztában lenni azzal, hogy ez a művelet hatással van a biztonsági mentés ablakára.
-   Válassza   >  **a lemez**  >  **adatai**  >  **Tulajdonságok** lehetőséget. Kikapcsolhatja a **lemezterület előzetes lefoglalását**.

A legújabb Backup Exec-beállításokkal és a követelmények végrehajtásának ajánlott eljárásaival kapcsolatban tekintse meg [a Veritas webhelyét](https://www.veritas.com).

## <a name="retention-policies"></a>Adatmegőrzési házirendek

Az egyik leggyakoribb biztonsági mentési adatmegőrzési szabály a nagyapa, az Atya és a Son (GFS) szabályzat. A GFS-szabályzatokban a növekményes biztonsági mentés naponta történik, és a teljes biztonsági mentés hetente és havonta történik. Ez a házirend hat StorSimple rétegű kötetet eredményez. Az egyik kötet tartalmazza a hetente, havonta és évenként teljes biztonsági mentést. A másik öt kötet napi növekményes biztonsági mentéseket tárol.

A következő példában egy GFS rotációt használunk. A példa a következőket feltételezi:

-   A nem deduplikált vagy tömörített adatértékek használatban vannak.
-   A teljes biztonsági mentések mindegyike 1 TiB.
-   A napi növekményes biztonsági mentések a 500 GiB.
-   Négy heti biztonsági mentést tartanak egy hónapig.
-   A tizenkét havi biztonsági mentés egy évig tart.
-   Egy éves biztonsági mentés 10 évig tart.

Az előző feltételezések alapján hozzon létre egy 26 TiB-es StorSimple-rétegbeli kötetet a havi és az éves teljes biztonsági mentéshez. Hozzon létre egy 5 TiB-es StorSimple a növekményes napi biztonsági mentésekhez.

| Biztonságimásolat-típus megőrzése | Méret (TiB) | GFS szorzó\* | Teljes kapacitás (TiB)  |
|---|---|---|---|
| Hetente megtelt | 1 | 4  | 4 |
| Napi növekmény | 0,5 | 20 (ciklusban egyenlő számú hét havonta) | 12 (2 a további kvóta esetében) |
| Havi teljes | 1 | 12 | 12 |
| Évenként megtelt | 1  | 10 | 10 |
| GFS követelmény |   | 38 |   |
| További kvóta  | 4  |   | 42 teljes GFS-követelmény  |

\* A GFS-szorzó a védelemhez szükséges másolatok száma, és megtartja a biztonsági mentési szabályzat követelményeinek teljesítését.

## <a name="set-up-backup-exec-storage"></a>Backup Exec Storage beállítása

### <a name="to-set-up-backup-exec-storage"></a>A Backup Exec Storage beállítása

1.  A Backup Exec felügyeleti konzolján válassza a **Storage**  >    >  **lemezes** tárolás konfigurálása  >  **következő** lehetőséget.

    ![Backup Exec felügyeleti konzol, tároló konfigurálása lap](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Válassza a **Disk Storage** lehetőséget, majd kattintson a **tovább** gombra.

    ![Backup Exec felügyeleti konzol, válassza a tár lapot](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Adja meg a képviselő nevét, például: **szombat Full**, és a leírását. Kattintson a **Tovább** gombra.

    ![Backup Exec felügyeleti konzol, név és leírás lap](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Válassza ki azt a lemezt, amelyen létre szeretné hozni a lemezes tárolóeszközt, majd kattintson a **tovább** gombra.

    ![Backup Exec felügyeleti konzol, Storage Disk kiválasztási oldal](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Növelje az írási műveletek számát **16**-ra, majd kattintson a **tovább** gombra.

    ![Backup Exec felügyeleti konzol, egyidejű írási műveletek beállításai lap](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Tekintse át a beállításokat, majd kattintson a **Befejezés gombra**.

    ![Backup Exec felügyeleti konzol, tárolási konfiguráció összegzése lap](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Az egyes kötetek hozzárendelésének végén módosítsa a tárolóeszköz beállításait úgy, hogy azok megfeleljenek a [StorSimple és Backup Exec ajánlott eljárásainak](#best-practices-for-storsimple-and-backup-exec).

    ![Backup Exec felügyeleti konzol, tárolási eszköz beállításai lap](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Ismételje meg a 1-7 lépést, amíg be nem fejeződik a StorSimple-kötetek a Backup Exechez való hozzárendelésének befejezése.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>A StorSimple beállítása elsődleges biztonsági mentési célként

> [!NOTE]
> A felhőbe felkészített biztonsági mentésből származó adatok visszaállítása Felhőbeli sebességgel történik.

Az alábbi ábra egy tipikus kötet hozzárendelését mutatja be egy biztonsági mentési feladathoz. Ebben az esetben az összes heti biztonsági mentés a szombat teljes lemezre mutat, a növekményes biztonsági mentések pedig Monday-Friday növekményes lemezeket képeznek. Az összes biztonsági mentés és visszaállítás egy StorSimple rétegű kötetről származik.

![Elsődleges biztonsági mentési cél konfigurációjának logikai diagramja](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple elsődleges biztonsági mentési cél GFS – példa

Íme egy példa egy GFS-rotációs ütemezésre négy hétig, havonta és évente:

| Gyakoriság/biztonsági mentés típusa | Összes | Növekményes (nap 1-5)  |   
|---|---|---|
| Hetente (1-4 hét) | Szombat | Monday-Friday |
| Havonta  | Szombat  |   |
| Éves | Szombat  |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>StorSimple-kötetek kiosztása Backup Exec biztonsági mentési feladatokhoz

A következő folyamat azt feltételezi, hogy a Backup Exec és a célként megadott gazdagép a Backup Exec ügynök irányelveinek megfelelően van konfigurálva.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>StorSimple-kötetek társítása egy Backup Exec biztonsági mentési feladatokhoz

1.  A Backup Exec felügyeleti konzolján válassza a **gazdagép**  >  **biztonsági** mentése  >  **lemezre** lehetőséget.

    ![Backup Exec felügyeleti konzol, válassza a gazdagép, a biztonsági mentés és a lemezre történő biztonsági mentés lehetőséget.](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  A **biztonsági mentés definíciójának tulajdonságai** párbeszédpanel **biztonsági mentés** területén válassza a **Szerkesztés** lehetőséget.

    ![Backup Exec felügyeleti konzol, biztonsági mentési definíció tulajdonságai párbeszédpanel](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Állítsa be a teljes és növekményes biztonsági mentéseket, hogy azok megfeleljenek a RPO és a RTO követelményeinek, és megfeleljenek a Veritas ajánlott eljárásainak.

4.  A **biztonsági mentési beállítások** párbeszédpanelen válassza a **tároló** elemet.

    ![Backup Exec felügyeleti konzol, Backup Options Storage párbeszédpanel](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Rendelje hozzá a megfelelő StorSimple-köteteket a biztonsági mentési ütemtervhez.

    > [!NOTE]
    > A **tömörítés** és a **titkosítás típusa** **none** értékre van állítva.

6.  Az **ellenőrzés** területen jelölje be a nem **ellenőrzi a feladathoz** jelölőnégyzetet. Ez a beállítás a StorSimple-rétegek befolyásolására is hatással lehet.

    > [!NOTE]
    > A töredezettségmentesítés, az indexelés és a háttérben történő ellenőrzés negatív hatással van a StorSimple-rétegek szintjére.

    ![Backup Exec felügyeleti konzol, biztonsági mentési beállítások ellenőrizze a beállításokat](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Ha úgy állította be a biztonsági mentési beállításokat, hogy az megfeleljen a követelményeknek, kattintson **az OK** gombra a befejezéshez.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>A StorSimple beállítása másodlagos biztonsági mentési célként

> [!NOTE]
>Az adatok olyan biztonsági másolatból állnak vissza, amely a felhőre van bontva.

Ebben a modellben a (StorSimple-től eltérő) tároló adathordozót kell kiszolgálni ideiglenes gyorsítótárként. Használhatja például a független lemezek (RAID) kötetének redundáns tömbjét a tárhely, a bemeneti/kimeneti (I/O) és a sávszélesség befogadásához. A RAID 5, a 50 és a 10 használatát javasoljuk.

Az alábbi ábrán a tipikus rövid távú adatmegőrzési helyi (a kiszolgáló) és a hosszú távú adatmegőrzési Archívum kötetek láthatók. Ebben a forgatókönyvben minden biztonsági mentés a helyi (a kiszolgáló) RAID köteten fut. Ezeket a biztonsági másolatokat a rendszer rendszeresen duplikálja és archiválja egy archív kötetre. Fontos a helyi (a kiszolgáló) RAID-kötetre méretezni, hogy az képes legyen kezelni a rövid távú adatmegőrzési kapacitást és a teljesítményre vonatkozó követelményeket.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple másodlagos biztonsági mentési célként megadott GFS-példa

![StorSimple másodlagos biztonsági mentési cél logikai diagramként](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

A következő táblázat bemutatja, hogyan állíthatja be a biztonsági mentéseket a helyi és StorSimple lemezeken való futtatáshoz. Magában foglalja az egyéni és a teljes kapacitásra vonatkozó követelményeket.

### <a name="backup-configuration-and-capacity-requirements"></a>A biztonsági mentési konfiguráció és a kapacitásra vonatkozó követelmények

| Biztonsági mentés típusa és megőrzése | Konfigurált tároló | Méret (TiB) | GFS szorzó | Teljes kapacitás \* (TiB) |
|---|---|---|---|---|
| 1. hét (teljes és növekményes) |Helyi lemez (rövid távú)| 1 | 1 | 1 |
| StorSimple hét 2-4 |StorSimple lemez (hosszú távú) | 1 | 4 | 4 |
| Havi teljes |StorSimple lemez (hosszú távú) | 1 | 12 | 12 |
| Évenként megtelt |StorSimple lemez (hosszú távú) | 1 | 1 | 1 |
|GFS-kötetek méretére vonatkozó követelmény |  |  |  | 18|

\* A teljes kapacitás magában foglalja a 17 TiB StorSimple-lemezeket és 1 TiB helyi RAID-kötetet.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS-példa: GFS rotációs hetente, havonta és évenkénti ütemezés

| Hét | Összes | 1. növekményes nap | 2. növekményes nap | 3. növekményes nap | 4. növekményes nap | 5. növekményes nap |
|---|---|---|---|---|---|---|
| 1. hét | Helyi RAID-kötet  | Helyi RAID-kötet | Helyi RAID-kötet | Helyi RAID-kötet | Helyi RAID-kötet | Helyi RAID-kötet |
| 2. hét | StorSimple hét 2-4 |   |   |   |   |   |
| 3. hét | StorSimple hét 2-4 |   |   |   |   |   |
| 4. hét | StorSimple hét 2-4 |   |   |   |   |   |
| Havonta | StorSimple havonta |   |   |   |   |   |
| Éves | StorSimple évente  |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>StorSimple-kötetek kiosztása a Backup Exec Archive és a deduplikáló feladatokhoz

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>StorSimple-kötetek kiosztása a Backup Exec Archive és az ismétlődő feladatokhoz

1.  A Backup Exec felügyeleti konzolon kattintson a jobb gombbal arra a feladatokra, amelyet StorSimple szeretne archiválni, majd válassza a **biztonsági mentési definíció tulajdonságai**  >  **Szerkesztés** lehetőséget.

    ![Backup Exec felügyeleti konzol, biztonsági mentési definíció tulajdonságai lap](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Válassza az ismétlődő **szakasz hozzáadása**  >  **lemezre**  >  **Szerkesztés** lehetőséget.

    ![Backup Exec felügyeleti konzol, fázis hozzáadása](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  A **duplikált beállítások** párbeszédpanelen válassza ki a **forráshoz** és az **ütemtervhez** használni kívánt értékeket.

    ![Backup Exec felügyeleti konzol, biztonsági mentési definíciók tulajdonságai és duplikált beállítások](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  A **tároló** legördülő listában válassza ki azt a StorSimple-kötetet, amelyben az archiválási feladatot tárolni kívánja az adatok tárolásához.

    ![Képernyőkép, amely megjeleníti a tároló kiválasztásához szükséges listát.](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Jelölje be az **ellenőrzés** jelölőnégyzetet, majd jelölje be a **feladatok nem ellenőrzik a feladatokhoz** jelölőnégyzetet.

    ![Képernyőkép, amely azt mutatja be, hogy hol jelölje be az adatok mellőzése ehhez a feladatsorhoz lehetőséget.](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Válassza az **OK** lehetőséget.

    ![A biztonsági mentési definíció tulajdonságait megjelenítő képernyőkép.](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  A **biztonsági mentés** oszlopban adjon hozzá egy új szakaszt. A forráshoz használja a **növekményes** használatot. A cél mezőben válassza ki azt a StorSimple-kötetet, amelyben a növekményes biztonsági mentési feladatot archiválja. Ismételje meg a 1-6. lépést.

## <a name="storsimple-cloud-snapshots"></a>StorSimple Felhőbeli Pillanatképek

A StorSimple-Felhőbeli Pillanatképek védik a StorSimple-eszközön található összes adatát. A Felhőbeli pillanatképek létrehozása egyenértékű a helyi biztonsági mentési szalagok egy külső létesítménybe való szállításával. Ha az Azure geo-redundáns tárterületet használja, a Felhőbeli pillanatkép létrehozása egyenértékű a biztonsági mentési szalagok több helyre történő szállításával. Ha vészhelyzet után vissza kell állítania egy eszközt, lehet, hogy egy másik StorSimple-eszközt online, és feladatátvételt hajt végre. A feladatátvételt követően a legutóbbi Felhőbeli pillanatképből férhet hozzá az adatokhoz (a Felhőbeli sebességgel).

A következő szakasz azt ismerteti, hogyan hozhat létre egy rövid parancsfájlt a StorSimple Felhőbeli Pillanatképek elindításához és törléséhez a biztonsági mentés utáni feldolgozás során.

> [!NOTE]
> A manuálisan vagy programozott módon létrehozott Pillanatképek nem követik a StorSimple-pillanatkép elévülési szabályzatát. Ezeket a pillanatképeket manuálisan vagy programozott módon kell törölni.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Felhőbeli Pillanatképek elindítása és törlése parancsfájl használatával

> [!NOTE]
> A StorSimple-pillanatkép törlése előtt gondosan mérje fel a megfelelőségi és az adatmegőrzési hatásokat. A biztonsági mentés utáni parancsfájlok futtatásával kapcsolatos további információkért lásd a [Backup Exec dokumentációját](https://www.veritas.com/support/en_US/article.100032497.html).

### <a name="backup-lifecycle"></a>Biztonsági mentés életciklusa

![Biztonsági mentési életciklus diagramja](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

### <a name="requirements"></a>Követelmények

-   A parancsfájlt futtató kiszolgálónak hozzá kell férnie az Azure felhőalapú erőforrásaihoz.
-   A felhasználói fióknak rendelkeznie kell a szükséges engedélyekkel.
-   Be kell állítania egy StorSimple biztonsági mentési szabályzatot a társított StorSimple-kötetekkel, de nincs bekapcsolva.
-   Szüksége lesz a StorSimple erőforrás nevére, a regisztrációs kulcsra, az eszköz nevére és a biztonsági mentési szabályzat AZONOSÍTÓra.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Felhőbeli pillanatkép elindítása vagy törlése

1. [Telepítse az Azure PowerShellt](/powershell/azure/).
2. Töltse le és állítsa be [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell-szkriptet.
3. A parancsfájlt futtató kiszolgálón futtassa a PowerShellt rendszergazdaként. Győződjön meg arról, hogy a parancsfájl futtatásával `-WhatIf $true` megtekintheti, hogy a parancsfájl milyen módosításokat hajt végre. Az ellenőrzés befejezése után adja át a t `-WhatIf $false` . Futtassa az alábbi parancsot:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Adja hozzá a parancsfájlt a biztonsági mentési feladatokhoz a Backup Execban a Backup Exec-feladatok előfeldolgozása és a feldolgozás utáni parancsok szerkesztésével.

   ![Backup Exec Console, biztonsági mentési beállítások, előkészítő és feldolgozás utáni parancsok lap](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Javasoljuk, hogy a napi biztonsági mentési feladatok végén a StorSimple Cloud Snapshot biztonsági mentési szabályzatát post-Processing szkriptként futtassa. Ha további információt szeretne arról, hogyan készíthet biztonsági mentést és visszaállítást a biztonságimásolat-alkalmazási környezetből, hogy az megfeleljen a RPO és a RTO, forduljon a biztonsági mentési építészhez.

## <a name="storsimple-as-a-restore-source"></a>StorSimple visszaállítási forrásként

Visszaállít egy StorSimple-eszközről, például helyreállítja az összes blokk Storage-eszközről. A felhőbe felkészített, Felhőbeli adattárolók visszaállnak. Helyi adatmennyiség esetén a rendszer az eszköz helyi lemezének sebességét állítja vissza. A visszaállítás végrehajtásáról a Backup Exec dokumentációjában olvashat bővebben. Javasoljuk, hogy megfeleljen a Backup Exec Restore ajánlott eljárásainak.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple feladatátvétel és vész-helyreállítás

> [!NOTE]
> A biztonsági mentési cél forgatókönyvek esetében a StorSimple Cloud Appliance nem támogatott visszaállítási célként.

A katasztrófák számos tényezőt okozhatnak. A következő táblázat az általános vész-helyreállítási forgatókönyveket ismerteti.

| Eset | Hatás | Helyreállítás | Jegyzetek |
|---|---|---|---|
| StorSimple-eszköz meghibásodása | A biztonsági mentési és visszaállítási műveletek megszakadnak. | Cserélje le a meghibásodott eszközt, és hajtsa végre a [StorSimple feladatátvételt és a vész-helyreállítást](./storsimple-8000-device-failover-disaster-recovery.md). | Ha az eszköz helyreállítása után visszaállítást kell végeznie, a teljes adathalmazok beolvasása a felhőből az új eszközre történik. Minden művelet Felhőbeli sebességgel történik. Előfordulhat, hogy az indexelési és katalogizálás-újraellenőrzési folyamat az összes biztonságimásolat-készletet megkeresi és lekéri a felhő szintjéről a helyi eszköz rétegre, amely időigényes folyamat lehet. |
| A Backup Exec Server meghibásodása | A biztonsági mentési és visszaállítási műveletek megszakadnak. | Hozza létre újra a biztonsági mentési kiszolgálót, és végezze el az adatbázis-visszaállítást részletesen a [Backup Exec (BEDB) adatbázis manuális biztonsági mentésének és visszaállításának módjában](http://www.veritas.com/docs/000041083). | Újra kell építenie vagy vissza kell állítania a Backup Exec-kiszolgálót a vész-helyreállítási helyen. Állítsa vissza az adatbázist a legutóbbi pontra. Ha a visszaállított Backup Exec-adatbázis nincs szinkronban a legújabb biztonsági mentési feladatokkal, az indexelés és a katalogizálás szükséges. Az index és a katalógus újraellenőrzésének folyamata okozhatja, hogy az összes biztonságimásolat-készlet beolvasható, és a felhő szintjéről a helyi eszköz szintjére kell húzni. Ez további időigényesvé teszi. |
| A hely meghibásodása, amely a biztonsági mentési kiszolgáló és a StorSimple elvesztését eredményezi | A biztonsági mentési és visszaállítási műveletek megszakadnak. | Először állítsa vissza a StorSimple, majd állítsa vissza a Backup exect. | Először állítsa vissza a StorSimple, majd állítsa vissza a Backup exect. Ha az eszköz helyreállítása után visszaállítást kell végeznie, a teljes adatfeldolgozási készletek a felhőből az új eszközre lesznek beolvasva. Minden művelet Felhőbeli sebességgel történik. |

## <a name="references"></a>Hivatkozások

Ehhez a cikkhez a következő dokumentumok hivatkoznak:

- [StorSimple többutas I/O-telepítés](./storsimple-8000-configure-mpio-windows-server.md)
- [Tárolási helyzetek: dinamikus kiépítés](/windows-hardware/drivers/storage/thin-provisioning)
- [GPT-meghajtók használata](/previous-versions/windows/hardware/design/dn653580(v=vs.85)#EHD)
- [Árnyékmásolatok beállítása megosztott mappákhoz](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771893(v=ws.11))

## <a name="next-steps"></a>Következő lépések

- További információ a [biztonságimásolat-készletből való visszaállításról](./storsimple-8000-restore-from-backup-set-u2.md).
- További információ az [eszközök feladatátvételének és a vész-helyreállításnak](./storsimple-8000-device-failover-disaster-recovery.md)a végrehajtásáról.