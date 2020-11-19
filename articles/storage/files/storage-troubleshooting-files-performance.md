---
title: Az Azure file shares teljesítményének hibaelhárítási útmutatója
description: Az Azure-fájlmegosztás ismert teljesítményével kapcsolatos problémák elhárítása. A problémák előfordulásakor észlelheti a lehetséges okokat és a kapcsolódó megkerülő megoldásokat.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: a49dbdace01396656c3114df0bc0d4589aff57c1
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916491"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>Az Azure file shares teljesítményével kapcsolatos problémák elhárítása

Ez a cikk az Azure-fájlmegosztás szolgáltatással kapcsolatos gyakori problémákat sorolja fel. A probléma lehetséges okait és megkerülő megoldásait biztosítja.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Nagy késés, alacsony átviteli sebesség és általános teljesítménnyel kapcsolatos problémák

### <a name="cause-1-share-was-throttled"></a>1. ok: a megosztás szabályozása megvolt

A rendszer a kérelmeket akkor szabályozza, ha a fájlmegosztás I/O-műveletei másodpercenként (IOPS), bejövő vagy kimenő korláttal rendelkeznek. A standard és a prémium szintű fájlmegosztás korlátainak megismeréséhez tekintse meg a [fájlmegosztás és a fájlméret céljait](./storage-files-scale-targets.md#file-share-and-file-scale-targets)ismertető témakört.

Annak ellenőrzéséhez, hogy a megosztást szabályozzák-e, elérheti és használhatja az Azure-metrikákat a portálon.

1. A Azure Portal nyissa meg a Storage-fiókját.

1. A bal oldali ablaktábla **figyelés** területén válassza a **metrikák** lehetőséget.

1. Válassza a **fájl** lehetőséget a Storage-fiók hatókörének metrikai névterében.

1. Metrikaként válassza a **tranzakciók** lehetőséget.

1. Adjon hozzá egy szűrőt a **Válasz típusához**, és ellenőrizze, hogy a kérelmek rendelkeznek-e a következő válasz kódok valamelyikével:
   * **SuccessWithThrottling**: kiszolgálói üzenetblokk (SMB) esetén
   * **ClientThrottlingError**: Rest esetén

   ![Képernyőkép a prémium szintű fájlmegosztás mérőszámok lehetőségeiről, amely a "Response type" tulajdonság szűrőjét jeleníti meg.](media/storage-troubleshooting-premium-fileshares/metrics.png)

   > [!NOTE]
   > A riasztások fogadásához tekintse meg a cikk későbbi, ["a riasztások létrehozása, ha a fájlmegosztás szabályozása"](#how-to-create-an-alert-if-a-file-share-is-throttled) című szakaszát.

### <a name="solution"></a>Megoldás

- Ha szabványos fájlmegosztást használ, engedélyezze a [nagyméretű fájlmegosztást](./storage-files-how-to-create-large-file-share.md?tabs=azure-portal) a Storage-fiókban. A nagyméretű fájlmegosztás akár 10 000 IOPS-t is támogat.
- Ha prémium szintű fájlmegosztást használ, növelje a kiosztott fájlmegosztás méretét, hogy növelje a IOPS korlátját. További információ: "a prémium szintű fájlmegosztás kiépítés ismertetése" szakasz, [Azure Files tervezési útmutató](./storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>2. ok: a metaadatok vagy a névtér nagy terhelése

Ha a kérések többsége metaadat-központú (például CreateFile, OpenFile, closefile, QueryInfo vagy querydirectory), a késés az írási/olvasási műveleteknél rosszabb lesz.

Annak megállapításához, hogy a kérések többsége metaadat-központú-e, indítsa el az 1. lépésben leírt módon az 1-4-as lépéseket követve. Az 5. lépésnél a **Válasz típusához** tartozó szűrő hozzáadása helyett adjon hozzá egy tulajdonság-szűrőt az **API-névhez**.

![Képernyőkép a prémium szintű fájlmegosztás mérőszámok lehetőségeiről, amely az "API Name" tulajdonság szűrőjét jeleníti meg.](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Áthidaló megoldás

- Ellenőrizze, hogy az alkalmazás módosítható-e a metaadat-műveletek számának csökkentése érdekében.
- Adjon hozzá egy virtuális merevlemezt (VHD-t) a fájlmegosztás számára, és csatlakoztassa a VHD-t SMB-kapcsolaton keresztül az adatokhoz való fájl-műveletek végrehajtásához. Ez a megközelítés egyetlen író és több olvasó esetében is működik, és lehetővé teszi a metaadatok helyi használatát. A telepítő a helyi közvetlenül csatlakoztatott tárolóhoz hasonló teljesítményt nyújt.

### <a name="cause-3-single-threaded-application"></a>3. ok: egyszálas alkalmazás

Ha a használt alkalmazás egyetlen szálból áll, akkor ez a beállítás a kiosztott megosztás méretétől függően jelentősen csökkentheti a IOPS átviteli sebességet, mint a maximális lehetséges átviteli sebesség.

### <a name="solution"></a>Megoldás

- Növelje az alkalmazások párhuzamosságát a szálak számának növelésével.
- Váltson olyan alkalmazásokra, ahol a párhuzamosság lehetséges. A másolási műveletek esetében például használhatja a AzCopy vagy a RoboCopy elemet a Windows-ügyfelektől, illetve a Linux-ügyfelektől származó **Parallel** paranccsal.

## <a name="very-high-latency-for-requests"></a>Nagyon nagy késés a kérelmek esetében

### <a name="cause"></a>Ok

A virtuális gép (VM) a fájlmegosztás egy másik régiójában található.

### <a name="solution"></a>Megoldás

- Futtassa az alkalmazást egy olyan virtuális gépről, amely ugyanabban a régióban található, mint a fájlmegosztás.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Az ügyfél nem tudja elérni a hálózat által támogatott maximális átviteli sebességet

### <a name="cause"></a>Ok
Az egyik lehetséges ok a szabványos fájlmegosztás esetén a többcsatornás SMB-támogatás hiánya. A Azure Files jelenleg csak egyetlen csatornát támogat, így az ügyfél virtuális gépe csak egyetlen kapcsolatban áll a-kiszolgálóval. Ez az egyetlen kapcsolódás az ügyfél virtuális gépe egyetlen magját, így a virtuális gépről elérhető maximális átviteli sebességet egyetlen mag köti.

### <a name="workaround"></a>Áthidaló megoldás

- A prémium fájlmegosztás esetében [engedélyezze a többcsatornás SMB-t egy FileStorage-fiókon](storage-files-enable-smb-multichannel.md).
- A nagyobb mag-t tartalmazó virtuális gépek beszerzése segíthet az átviteli sebesség javításában.
- Az ügyfélalkalmazás több virtuális gépről való futtatása növeli az átviteli sebességet.
- Ha lehetséges, használja a REST API-kat.

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>A Linux-ügyfelek átviteli sebessége jelentősen alacsonyabb, mint a Windows-ügyfeleknél

### <a name="cause"></a>Ok

Ez egy ismert probléma az SMB-ügyfél Linux rendszeren való megvalósításával kapcsolatban.

### <a name="workaround"></a>Áthidaló megoldás

- A terhelés elosztása több virtuális gép között.
- Ugyanazon a virtuális gépen használjon több csatlakoztatási pontot egy **nosharesock** kapcsolóval, majd a terhelést a csatlakoztatási pontok között.
- Linux rendszeren próbálja meg a csatlakoztatást egy **nostrictsync** beállítással, hogy ne kényszerítse az SMB-kiürítést minden **Fsync** -híváson. Azure Files esetén ez a beállítás nem zavarja az adatkonzisztenciaot, de az elavult fájlok metaadatait is eredményezheti a címtár-listázásban (**ls-l** parancs). A fájl metaadatainak közvetlen lekérdezése a **stat** parancs használatával a legfrissebb fájl-metaadatokat fogja visszaadni.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Magas késés a metaadatok esetében – nagy mennyiségű nyitott/záró műveletet igénylő számítási feladatok

### <a name="cause"></a>Ok

A címtár-bérletek támogatásának hiánya.

### <a name="workaround"></a>Áthidaló megoldás

- Ha lehetséges, kerülje a túlzott nyitó/záró fogantyú használatát egy rövid időn belül ugyanazon a címtáron.
- Linux rendszerű virtuális gépek esetén növelje a címtár-bejegyzés gyorsítótárának időtúllépését a **actimeo = \<sec>** csatlakoztatási lehetőség megadásával. Alapértelmezés szerint az időtúllépés 1 másodperc, így a nagyobb érték (például 3 vagy 5 másodperc) segíthet.
- CentOS Linux vagy Red Hat Enterprise Linux (RHEL) virtuális gépek esetében frissítse a rendszert a CentOS Linux 8,2 vagy a RHEL 8,2 rendszerre. Más Linux rendszerű virtuális gépek esetében frissítse a kernelt 5,0-re vagy újabb verzióra.

## <a name="low-iops-on-centos-linux-or-rhel"></a>Alacsony IOPS a CentOS Linux vagy a RHEL

### <a name="cause"></a>Ok

Az 1 értéknél nagyobb I/O-mélység nem támogatott CentOS Linux vagy RHEL esetén.

### <a name="workaround"></a>Áthidaló megoldás

- Frissítsen a CentOS Linux 8 vagy a RHEL 8 verzióra.
- Váltson Ubuntu-ra.

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>Lassú fájlmásolás az Azure-fájlmegosztás és a Linux között

Ha lassú a fájlok másolása, tekintse meg a [Linux hibaelhárítási útmutatójának](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)"lassú fájlok másolása az Azure-ba és az Azure-fájlmegosztás a Linux rendszerben" című szakaszát.

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>Vibrálás vagy fűrészfog minta a IOPS

### <a name="cause"></a>Ok

Az ügyfélalkalmazás konzisztensen meghaladja az alapkonfiguráció IOPS. Jelenleg nem áll rendelkezésre a kérések terhelése. Ha az ügyfél túllépi az alapkonfiguráció IOPS, akkor a szolgáltatás szabályozza a szolgáltatást. A szabályozás azt eredményezheti, hogy az ügyfél ideges vagy fűrészfog IOPS mintát tapasztal. Ebben az esetben az ügyfél által elért átlagos IOPS alacsonyabb lehet, mint az alapkonfiguráció IOPS.

### <a name="workaround"></a>Áthidaló megoldás
- Csökkentse a kérések terhelését az ügyfélalkalmazás alapján, hogy a megosztás ne legyen szabályozva.
- Növelje a megosztás kvótáját úgy, hogy a megosztás ne legyen szabályozva.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Túlzott DirectoryOpen/DirectoryClose-hívások

### <a name="cause"></a>Ok

Ha a **DirectoryOpen/DirectoryClose** hívások száma a legnépszerűbb API-hívások egyike, és nem várja meg, hogy az ügyfél sok hívást hajtson végre, a problémát az Azure-ügyfél virtuális gépre telepített víruskereső szoftver okozhatja.

### <a name="workaround"></a>Áthidaló megoldás

- A probléma megoldása a [Windows áprilisi platformjának frissítésében](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)érhető el.

## <a name="file-creation-is-slower-than-expected"></a>A fájl létrehozása a vártnál lassabb

### <a name="cause"></a>Ok

A nagy mennyiségű fájl létrehozására támaszkodó munkaterhelések nem jelentenek jelentős különbséget a prémium fájlmegosztás és a normál fájlmegosztás közötti teljesítményben.

### <a name="workaround"></a>Áthidaló megoldás

- Nincsenek.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Lassú teljesítmény a Windows 8,1 vagy a Server 2012 R2 rendszerből

### <a name="cause"></a>Ok

Nagyobb, mint a várt késés a nagy I/O-igényű számítási feladatokhoz való hozzáféréshez az Azure-fájlmegosztás esetében.

### <a name="workaround"></a>Áthidaló megoldás

- Telepítse az elérhető [gyorsjavítást](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).

## <a name="smb-multichannel-option-not-visible-under-file-share-settings"></a>A többcsatornás SMB beállítás nem látható a fájlmegosztás beállításai között. 

### <a name="cause"></a>Ok

Vagy az előfizetés nincs regisztrálva a szolgáltatáshoz, vagy a régió és a fiók típusa nem támogatott.

### <a name="solution"></a>Megoldás

Győződjön meg arról, hogy az előfizetés regisztrálva van a többcsatornás SMB szolgáltatáshoz. A fiók áttekintése oldalon tekintse meg az [első lépéseket](storage-files-enable-smb-multichannel.md#getting-started) , hogy a fiók típusa FileStorage (prémium szintű fájl fiók) legyen. 

## <a name="smb-multichannel-is-not-being-triggered"></a>A többcsatornás SMB nincs aktiválva.

### <a name="cause"></a>Ok

A többcsatornás SMB konfigurációs beállításainak legutóbbi módosításai újracsatlakoztatás nélkül.

### <a name="solution"></a>Megoldás
 
-   A Windows SMB-ügyfél vagy a fiók többcsatornás SMB-konfigurációs beállításainak módosítása után le kell választania a megosztást, várnia kell a 60 mp-re, majd újra kell csatlakoztatnia a megosztást a többcsatornás aktiváláshoz.
-   Windows rendszerű ügyfél operációs rendszer esetén a nagy üzenetsor-mélységű i/o-terhelést a QD = 8 értékre kell állítani, például egy fájl másolásával a többcsatornás SMB aktiválására.  A kiszolgáló operációs rendszer esetén a többcsatornás SMB-t a rendszer a QD = 1 értékkel indítja el, ami azt jelenti, hogy ha elindít egy IO-t a megosztásra.

## <a name="high-latency-on-web-sites-hosted-on-file-shares"></a>Nagy késleltetés a fájlmegosztás által üzemeltetett webhelyek esetében 

### <a name="cause"></a>Ok  

A fájlmegosztás nagy számú fájl-változási értesítése jelentős késést okozhat. Ez általában a részletesen beágyazott címtár-struktúrával rendelkező fájlmegosztás során futtatott webhelyekkel történik. Egy tipikus forgatókönyv az IIS által üzemeltetett webalkalmazás, amelyben az alapértelmezett konfigurációban az egyes könyvtárakra vonatkozóan a fájl módosítására vonatkozó értesítés van beállítva. Az SMB-ügyfél által regisztrált megosztás minden változása ([ReadDirectoryChangesW](https://docs.microsoft.com/windows/win32/api/winbase/nf-winbase-readdirectorychangesw)) leküldéses értesítéseket küld a fájlszolgáltatásoktől az ügyfélnek, amely a rendszererőforrásokat veszi igénybe, és a változások számával romlik a probléma. Ez megoszthatja a megosztás szabályozását, és így magasabb ügyféloldali késést eredményezhet. 

A megerősítéshez használhatja az Azure-mérőszámokat a portálon – 

1. A Azure Portal nyissa meg a Storage-fiókját. 
1. A bal oldali menüben a Figyelés területen válassza a metrikák lehetőséget. 
1. Válassza a fájl lehetőséget a Storage-fiók hatókörének metrikai névterében. 
1. Metrikaként válassza a tranzakciók lehetőséget. 
1. Adjon hozzá egy szűrőt a ResponseType, és ellenőrizze, hogy vannak-e olyan kérelmek, amelyek SuccessWithThrottling (SMB) vagy ClientThrottlingError (REST esetén) rendelkeznek.

### <a name="solution"></a>Megoldás 

- Ha a fájl módosítására vonatkozó értesítés nem használatos, tiltsa le a fájl módosítására vonatkozó értesítést (előnyben részesített).
    - A FCNMode frissítésével [Tiltsa le a fájl módosítására vonatkozó értesítést](https://support.microsoft.com/help/911272/fix-asp-net-2-0-connected-applications-on-a-web-site-may-appear-to-sto) . 
    - Frissítse az IIS munkavégző folyamat (W3WP) lekérdezési intervallumát 0 értékre a `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` beállításjegyzékben, és indítsa újra a W3wp-folyamatot. Ennek a beállításnak a megismeréséhez tekintse meg az [IIS számos részében használt általános beállításkulcsokat](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp).
- Növelje a fájl módosítási értesítésének lekérdezési időközét a kötet csökkentése érdekében.
    - Módosítsa a W3WP munkavégző folyamat lekérdezési intervallumát magasabb értékre (például 10mins vagy 30mins) a követelmény alapján. Állítsa be a `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` [beállításjegyzékbe](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp) , és indítsa újra a W3wp-folyamatot.
- Ha a webhely leképezett fizikai könyvtára beágyazott címtár-struktúrával rendelkezik, megpróbálhatja korlátozni a fájl módosítási értesítésének hatókörét az értesítési kötet csökkentése érdekében. Alapértelmezés szerint az IIS Web.config fájlokból származó konfigurációt használ abban a fizikai könyvtárban, amelyhez a virtuális könyvtár le van képezve, valamint az adott fizikai könyvtárban található bármely alárendelt könyvtárban. Ha nem kívánja Web.config fájlokat használni a gyermek címtárakban, akkor a allowSubDirConfig attribútumnál a FALSE értéket kell megadnia a virtuális könyvtárban. További részleteket [itt](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)találhat. 
    - Állítsa be az IIS virtuális könyvtár "allowSubDirConfig" beállítását a Web.Config *hamis* értékre a leképezett fizikai alárendelt könyvtárak hatókörből való kizárásához.  

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Riasztás létrehozása a fájlmegosztás szabályozása esetén

1. A Azure Portal nyissa meg a Storage-fiókját.
1. A **figyelés** szakaszban válassza a **riasztások** lehetőséget, majd válassza az **új riasztási szabály** lehetőséget.
1. Válassza az **erőforrás szerkesztése** lehetőséget, válassza ki a Storage-fiók **fájljának erőforrás-típusát** , majd kattintson a **kész** gombra. Ha például a Storage-fiók neve *contoso*, válassza ki a contoso/fájl erőforrást.
1. Feltétel hozzáadásához válassza a **feltétel kiválasztása** lehetőséget.
1. A Storage-fiók által támogatott jelek listájában válassza ki a **tranzakciók** metrikáját.
1. A **jel logikai beállítása** ablaktábla **dimenzió neve** legördülő listájában válassza a **Válasz típusa** lehetőséget.
1. A **dimenzió értékei** legördülő listában válassza a **SuccessWithThrottling** (SMB esetében) vagy a **ClientThrottlingError** (REST) lehetőséget.

   > [!NOTE]
   > Ha sem a **SuccessWithThrottling** , sem a **ClientThrottlingError** dimenzió értéke nem szerepel a felsorolásban, ez azt jelenti, hogy az erőforrás nincs szabályozva. A dimenzió értékének hozzáadásához a **dimenzióértékek** legördülő lista mellett válassza az **Egyéni érték hozzáadása** lehetőséget, adja meg a **SuccessWithThrottling** vagy a **ClientThrottlingError** értéket, majd kattintson **az OK gombra**, majd ismételje meg a 7. lépést.

1. A **dimenzió neve** legördülő listában válassza a **fájlmegosztás** lehetőséget.
1. A **dimenzió értékei** legördülő listában válassza ki azt a fájlmegosztást vagy megosztásokat, amelyekről riasztást szeretne kapni.

   > [!NOTE]
   > Ha a fájlmegosztás szabványos fájlmegosztás, válassza a **minden aktuális és jövőbeli értéket**. A dimenzióértékek legördülő lista nem sorolja fel a fájlmegosztást, mert a megosztási mérőszámok nem érhetők el a normál fájlmegosztás esetén. A normál fájlmegosztás esetén a rendszer elindítja a riasztásokat, ha a Storage-fiókon belül bármilyen fájlmegosztás szabályozva van, és a riasztás nem azonosítja, hogy mely fájlmegosztás lett szabályozva. Mivel a megosztási mérőszámok nem érhetők el a normál fájlmegosztás esetében, azt javasoljuk, hogy használjon egy fájlmegosztást a Storage-fiókon.

1. Adja meg a riasztási paramétereket a **küszöbérték**, az **operátor**, az **Összesítés részletessége** és **a kiértékelés gyakoriságának** megadásával, majd válassza a **kész** lehetőséget.

    > [!TIP]
    > Ha statikus küszöbértéket használ, a metrikai diagram segítségével meghatározhatja az ésszerű küszöbértéket, ha a fájlmegosztást jelenleg szabályozzák. Ha dinamikus küszöbértéket használ, a metrika diagram a legutóbbi adatok alapján megjeleníti a számított küszöbértékeket.

1. Válassza a **műveleti csoport kiválasztása** lehetőséget, majd adjon hozzá egy műveleti csoportot (például e-mailt vagy SMS-t) a riasztáshoz egy meglévő műveleti csoport kiválasztásával vagy egy új műveleti csoport létrehozásával.
1. Adja meg a riasztás részleteit, például a **riasztási szabály nevét**, **leírását** és **súlyosságát**.
1. A riasztás létrehozásához válassza a **riasztási szabály létrehozása** lehetőséget.

Ha többet szeretne megtudni a Azure Monitor riasztások konfigurálásáról, tekintse meg [a Microsoft Azure riasztások áttekintése]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)című témakört.

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>Riasztások létrehozása, ha a prémium fájlmegosztás a szabályozás irányába közeledik

1. A Azure Portal nyissa meg a Storage-fiókját.
1. A **figyelés** szakaszban válassza a **riasztások** lehetőséget, majd válassza az **új riasztási szabály** lehetőséget.
1. Válassza az **erőforrás szerkesztése** lehetőséget, válassza ki a Storage-fiók **fájljának erőforrás-típusát** , majd kattintson a **kész** gombra. Ha például a Storage-fiók neve *contoso*, válassza ki a contoso/fájl erőforrást.
1. Feltétel hozzáadásához válassza a **feltétel kiválasztása** lehetőséget.
1. A Storage-fiók által támogatott jelek listájában válassza ki a **kimenő** forgalom metrikáját.

   > [!NOTE]
   > Három külön riasztást kell létrehoznia, ha a bejövő, kimenő vagy tranzakciós értékek meghaladják a beállított küszöbértékeket. Ennek az az oka, hogy a riasztás csak akkor aktiválódik, ha az összes feltétel teljesül. Ha például az összes feltételt egy riasztásban helyezi el, akkor a rendszer csak akkor küld riasztást, ha a bejövő, a kimenő forgalom és a tranzakciók túllépik a küszöbértéket.

1. Görgessen le. A **dimenzió neve** legördülő listában válassza a **fájlmegosztás** lehetőséget.
1. A **dimenzió értékei** legördülő listában válassza ki azt a fájlmegosztást vagy megosztásokat, amelyekről riasztást szeretne kapni.
1. Adja meg a riasztási paramétereket az **operátor**, a **küszöbérték**, az **Összesítés részletessége** és **a kiértékelési** legördülő lista értékeinek kiválasztásával, majd válassza a **kész** lehetőséget.

   A kimenő forgalom, a bejövő forgalom és a tranzakciók mérőszáma percenként van kifejezve, de a kimenő forgalom, a bejövő forgalom és az I/O másodpercenkénti száma kiépítve. Ezért ha például a kiépített kimenő forgalom 90 &nbsp; mebibytes másodpercenként (MIB/s), és azt szeretné, hogy a küszöbérték a &nbsp; kiépített kimenő forgalom 80 százalékát adja meg, válassza a következő riasztási paramétereket: 
   - **Küszöbérték**: *75497472* 
   - **Operátor** esetén: *nagyobb vagy egyenlő*
   - **Összesítési típus**: *átlag*
   
   Attól függően, hogy milyen zajos a riasztás, kiválaszthatja az **Összesítés részletességének** és **a kiértékelés gyakoriságának** értékét is. Ha például azt szeretné, hogy a riasztás megtekintse az átlagos bejövő forgalmat az 1 órás időszakban, és szeretné, hogy a riasztási szabály óránként fusson, válassza a következőt:
   - **Összesítési részletesség**: *1 óra*
   - **Értékelés gyakorisága**: *1 óra*

1. Válassza a **műveleti csoport kiválasztása** lehetőséget, majd adjon hozzá egy műveleti csoportot (például e-mailt vagy SMS-t) a riasztáshoz egy meglévő műveleti csoport kiválasztásával vagy egy új létrehozásával.
1. Adja meg a riasztás részleteit, például a **riasztási szabály nevét**, **leírását** és **súlyosságát**.
1. A riasztás létrehozásához válassza a **riasztási szabály létrehozása** lehetőséget.

    > [!NOTE]
    > - Ha értesítést szeretne kapni arról, hogy a prémium szintű fájlmegosztás a *kiépített bejövő forgalom miatt* már a szabályozáshoz közeledik, kövesse az előző utasításokat, de a következő módosítással:
    >    - Az 5. lépésben válassza a **bejövő** forgalom metrikáját a **kimenő** forgalom helyett.
    >
    > - Ha értesítést szeretne kapni arról, hogy a prémium fájlmegosztás a *kiépített IOPS miatt* a szabályozáshoz közeledik, kövesse az előző utasításokat, de a következő módosításokkal:
    >    - Az 5. lépésben válassza ki a **tranzakciók** metrikáját a **kimenő** forgalom helyett.
    >    - A 10. lépésben az egyetlen lehetőség az **összesítési típushoz** *összesen*. Ezért a küszöbérték a kiválasztott összesítési részletességtől függ. Ha például azt szeretné, hogy a küszöbérték 80%- &nbsp; a kiépített alapkonfiguráció IOPS, és *1 órát* választ az **Összesítés Részletességéhez**, a **küszöbérték** az alapszintű IOPS (bájtban) &times; &nbsp; 0,8 &times; &nbsp; 3600. 

Ha többet szeretne megtudni a Azure Monitor riasztások konfigurálásáról, tekintse meg [a Microsoft Azure riasztások áttekintése]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)című témakört.

## <a name="see-also"></a>További információ
- [A Windows Azure Files hibáinak megoldása](storage-troubleshoot-windows-file-connection-problems.md)  
- [A Linux Azure Files hibáinak megoldása](storage-troubleshoot-linux-file-connection-problems.md)  
- [Gyakori kérdések az Azure Files-szal kapcsolatban](storage-files-faq.md)
