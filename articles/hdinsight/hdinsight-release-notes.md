---
title: Az Azure HDInsight kiadási megjegyzései
description: Az Azure HDInsight legújabb kibocsátási megjegyzései. A Hadoop, a Spark, a R Server, a kaptár és sok más fejlesztéssel kapcsolatos tippeket és információkat kaphat.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 6e37501a56bdf247bce2111e8e5a5bf02f37d898
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626121"
---
# <a name="azure-hdinsight-release-notes"></a>Az Azure HDInsight kibocsátási megjegyzései

Ez a cikk az Azure HDInsight **legújabb** kiadási frissítéseivel kapcsolatos információkat tartalmaz. A korábbi kiadásokkal kapcsolatos információkért lásd: [HDInsight kibocsátási megjegyzések archívuma](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Összefoglalás

Az Azure HDInsight az egyik legnépszerűbb szolgáltatás a nagyvállalati ügyfelek körében az Azure-beli nyílt forráskódú elemzésekhez.

Ha a kibocsátási megjegyzésekre szeretne előfizetni, tekintse meg a [jelen GitHub-adattár](https://github.com/hdinsight/release-notes/releases)kiadásait.

## <a name="release-date-11182020"></a>Kiadás dátuma: 11/18/2020

Ez a kiadás a 3,6-es és a HDInsight 4,0-es HDInsight egyaránt érvényes. A HDInsight-kiadás több napon keresztül elérhetővé válik minden régióban. A kiadás dátuma itt jelzi az első régió kiadásának dátumát. Ha nem látja az alábbi módosításokat, várja meg, amíg a kiadás több napon belül élő marad a régióban.

## <a name="new-features"></a>Új funkciók
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>Ügyfél által felügyelt kulcs titkosításának automatikus kulcsának elforgatása nyugalmi állapotban
Ettől a kiadástól kezdődően az ügyfelek az Azure KeyValut-verzió-kevesebb titkosítási kulcs URL-címét használhatják az ügyfelek által felügyelt kulcs titkosítására nyugalmi állapotban. A HDInsight automatikusan elforgatja a kulcsokat, és lecseréli azokat új verzióra. További információt [itt talál](./disk-encryption.md).

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>A Spark, a Hadoop és a ML szolgáltatások Zookeeper eltérő virtuálisgép-méretek kiválasztásának lehetősége
A HDInsight korábban nem támogatta a Zookeeper-csomópontok méretének testreszabását a Spark, a Hadoop és a ML Services típusú fürtök esetében. Alapértelmezés szerint A2_v2/a2 virtuálisgép-méreteket tartalmaz, amelyek díjmentesen állnak rendelkezésére. Ebből a kiadásból kiválaszthatja a forgatókönyvhöz leginkább megfelelő Zookeeper virtuális gépek méretét. A A2_v2/a2 eltérő virtuálisgép-mérettel rendelkező Zookeeper-csomópontokat a rendszer felszámítja. A A2_v2 és az A2-es virtuális gépek továbbra is díjmentesen elérhetők.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Áttérés Azure-beli virtuálisgép-méretezési csoportokra
A HDInsight mostantól Azure-beli virtuális gépeket használ a fürt kiépítéséhez. Ettől a kiadástól kezdve a szolgáltatás fokozatosan migrálva lesz az [Azure virtuálisgép-méretezési csoportokra](../virtual-machine-scale-sets/overview.md). A teljes folyamat hónapokat is igénybe vehet. A régiók és az előfizetések migrálása után az újonnan létrehozott HDInsight-fürtök felhasználói műveletek nélkül futnak a virtuálisgép-méretezési csoportokban. A rendszer nem várt változást.

## <a name="deprecation"></a>Elavulás
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>A HDInsight 3,6 ML Services-fürt elavult változata
A HDInsight 3,6 ML Services-fürt típusa a támogatás végére kerül a 31 2020 decemberében. Az ügyfelek nem tudnak új 3,6 ML-es szolgáltatási fürtöket létrehozni a 31 2020. december után. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Tekintse meg a HDInsight-verziók és-fürtök támogatásának lejárati idejét [itt](./hdinsight-component-versioning.md#available-versions).

### <a name="disabled-vm-sizes"></a>Letiltott virtuálisgép-méretek
A HDInsight november 16 2020-től kezdődően megakadályozza, hogy az új ügyfelek standand_A8, standand_A9, standand_A10 és standand_A11 virtuálisgép-méretekkel hozzanak létre fürtöket. Az elmúlt három hónapban ezeket a virtuálisgép-méreteket használó meglévő ügyfeleket nem érinti a rendszer. A HDInsight január 9 2021-től kezdődően a standand_A8, standand_A9, standand_A10 és standand_A11 virtuálisgép-méretek használatával letiltja a fürtöket létrehozó összes ügyfelet. A meglévő fürtök futtatása a következőképpen történik:. Vegye fontolóra a HDInsight 4,0-re való áttérést, hogy elkerülje a lehetséges rendszer/támogatás megszakadását.

## <a name="behavior-changes"></a>Viselkedési változások
### <a name="add-nsg-rule-checking-before-scaling-operation"></a>NSG-szabály-ellenőrzés hozzáadása a skálázási művelet előtt
A HDInsight hozzáadta a hálózati biztonsági csoportokat (NSG) és a felhasználó által megadott útvonalakat (UDR) a skálázási művelettel. Ugyanez az érvényesítés történik a fürt létrehozásakor a fürtök skálázása mellett. Ez az ellenőrzés segít megakadályozni a kiszámíthatatlan hibákat. Ha az érvényesítés nem történik meg, a skálázás sikertelen lesz. További információ a NSG és a UDR helyes konfigurálásáról: [HDInsight-felügyeleti IP-címek](./hdinsight-management-ip-addresses.md).

## <a name="upcoming-changes"></a>Közelgő változások
A következő módosítások a közelgő kiadásokban fognak történni.

### <a name="breaking-change-for-net-for-apache-spark-100"></a>A .NET Apache Spark 1.0.0-hoz való feltörésének változása
A HDInsight bevezeti a .NET első jelentős kiadását Apache Spark a következő kiadásban. Biztosítja a Spark 2.4. x és a Spark 3.0. x DataFrame API-teljességét más funkciókkal együtt. Ebben a főverzióban módosulnak a változások, ezért tekintse át [ezt az áttelepítési útmutatót](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) a kód és a folyamatok frissítéséhez szükséges lépések megismeréséhez. További információ [itt](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight).

### <a name="default-cluster-vm-size-will-be-changed-to-ev3-family"></a>A fürt alapértelmezett virtuálisgép-mérete a Ev3 családra lesz módosítva
A következő kiadástól kezdve (a január végéig) az alapértelmezett fürt virtuálisgép-méretei a D családtól a Ev3 családig változnak. Ez a változás a főcsomópontokra és a munkavégző csomópontokra vonatkozik. A módosítás elkerüléséhez határozza meg az ARM-sablonban használni kívánt virtuális gépek méretét.

### <a name="default-cluster-version-will-be-changed-to-40"></a>A fürt alapértelmezett verziója 4,0-re változik
Február 2021-én a HDInsight-fürt alapértelmezett verziója 3,6-ről 4,0-ra módosul. További információ az elérhető verziókról: [elérhető verziók](./hdinsight-component-versioning.md#available-versions). További információ a [HDInsight 4,0](./hdinsight-version-release.md) újdonságáról

### <a name="os-version-upgrade"></a>Operációs rendszer verziófrissítése
A HDInsight az operációs rendszer 16,04 és 18,04 közötti verzióját frissíti. A frissítés a 2021. április előtt fejeződik be.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6, 30 2021. június vége
A HDInsight 3,6 támogatás megszűnik. A 30 2021. június megkezdése után az ügyfelek nem hozhatnak létre új HDInsight 3,6-fürtöket. A meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Vegye fontolóra a HDInsight 4,0-re való áttérést, hogy elkerülje a lehetséges rendszer/támogatás megszakadását.

## <a name="bug-fixes"></a>Hibajavítások
A HDInsight továbbra is a fürt megbízhatóságának és teljesítményének növelését teszi elérhetővé. 

## <a name="component-version-change"></a>Összetevő verziójának módosítása
Ehhez a kiadáshoz nem módosult az összetevő verziószáma. A HDInsight 4,0 és a HDInsight 3,6 aktuális összetevő-verzióit ebben a [dokumentumban](./hdinsight-component-versioning.md)találja.

## <a name="known-issues"></a>Ismert problémák
### <a name="prevent-hdinsight-cluster-vms-from-rebooting-periodically"></a>A HDInsight-fürt virtuális gépei időszakos újraindításának megakadályozása

A 2020 november közepétől kezdődően előfordulhat, hogy észrevette, hogy a HDInsight-fürt virtuális gépei rendszeresen újraindulnak. Ezt a következő okok okozhatják:

1.  A ClamAV engedélyezve van a fürtön. Az új azsec-ClamAV csomag nagy mennyiségű memóriát használ, amely elindítja a csomópontok újraindítását. 
2.  A rendszer naponta ütemez egy CRON-feladatot, amely figyeli az Azure-szolgáltatások által használt hitelesítésszolgáltatók (CAs) listájának változásait. Új HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány elérhetővé tétele esetén a parancsfájl hozzáadja a tanúsítványt a JDK megbízhatósági kapcsolati tárolóhoz, és újraindítást ütemezhet.

A HDInsight rögzíti a javításokat, és az összes futó fürt javítását alkalmazza mindkét probléma esetén. A javítás azonnali alkalmazásához és a váratlan virtuális gépek újraindításának elkerüléséhez futtassa az alábbi parancsfájl-műveleteket az összes fürtcsomóponton, állandó parancsfájl-műveletként. A HDInsight a javítás és a javítás befejezése után újabb értesítést küld.
```
https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/replace_cacert_script.sh
https://healingscriptssa.blob.core.windows.net/healingscripts/ChangeOOMPolicyAndApplyLatestConfigForClamav.sh
```
