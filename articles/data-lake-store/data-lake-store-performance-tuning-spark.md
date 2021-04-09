---
title: Performance tuning – Spark és Azure Data Lake Storage Gen1
description: Ismerkedjen meg a Spark Azure HDInsight-és Azure Data Lake Storage Gen1-beli teljesítmény-hangolási iránymutatásaival.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: twooley
ms.openlocfilehash: 8a39e14b70827947687b7613b9ff86f18017ddfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97722504"
---
# <a name="performance-tuning-guidance-for-spark-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Teljesítmény-finomhangolási útmutató a Spark on HDInsight és Azure Data Lake Storage Gen1

A Spark teljesítményének finomhangolásakor figyelembe kell vennie a fürtön futó alkalmazások számát. Alapértelmezés szerint négy alkalmazást futtathat egyszerre a HDI-fürtön (Megjegyzés: az alapértelmezett beállítás a módosítás tárgya). Dönthet úgy, hogy kevesebb alkalmazást használ, így felülbírálhatja az alapértelmezett beállításokat, és több fürtöt is használhat ezekhez az alkalmazásokhoz.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen1-fiók**. A létrehozásával kapcsolatos útmutatásért tekintse meg az Ismerkedés [a Azure Data Lake Storage Gen1rel](data-lake-store-get-started-portal.md) című témakört.
* **Azure HDInsight-fürt** Data Lake Storage Gen1 fiókhoz való hozzáféréssel. Lásd: [HDInsight-fürt létrehozása Data Lake Storage Gen1sal](data-lake-store-hdinsight-hadoop-use-portal.md). Győződjön meg arról, hogy engedélyezi Távoli asztal a fürt számára.
* **Spark-fürt futtatása Data Lake Storage Gen1on**. További információ: a [HDInsight Spark-fürt használata az adatok elemzéséhez Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md)
* **Teljesítmény-finomhangolási irányelvek a Data Lake Storage Gen1**. Az általános teljesítménnyel kapcsolatos fogalmakat lásd: [Data Lake Storage Gen1 teljesítmény-finomhangolási útmutató](./data-lake-store-performance-tuning-guidance.md) 

## <a name="parameters"></a>Paraméterek

A Spark-feladatok futtatásakor a legfontosabb beállítások a Data Lake Storage Gen1 teljesítményének növelésére használhatók:

* **NUM-végrehajtók** – a végrehajtás alatt álló egyidejű feladatok száma.

* **Végrehajtó – memória** – az egyes végrehajtók számára lefoglalt memória mennyisége.

* **Végrehajtó – magok** – az egyes végrehajtók számára lefoglalt magok száma.

**NUM-végrehajtók** A NUM-végrehajtók a párhuzamosan futtatható feladatok maximális számát fogják beállítani. A párhuzamosan futtatható feladatok tényleges számát a fürtben elérhető memória-és processzor-erőforrások kötik.

**Végrehajtó – memória** Ez az egyes végrehajtók számára lefoglalt memória mennyisége. Az egyes végrehajtók számára szükséges memória a feladatoktól függ. Összetett műveletek esetén a memóriának nagyobbnak kell lennie. Az olyan egyszerű műveletekhez, mint az olvasás és az írás, a memória követelményei alacsonyabbak lesznek. Az egyes végrehajtók memóriájának mennyisége megtekinthető a Ambari. A Ambari-ben navigáljon a Spark elemre, és tekintse meg a **konfigurációk** lapot.

**Végrehajtó – magok** Ezzel beállítja a végrehajtó által használt magok mennyiségét, ami meghatározza a végrehajtónál futtatható párhuzamos szálak számát. Ha például a végrehajtó-magok = 2, akkor minden végrehajtó két párhuzamos feladatot tud futtatni a végrehajtóban. A szükséges végrehajtói magok a feladatoktól függenek. Az I/O-feladatok nem igénylik a nagy mennyiségű memóriát, így minden végrehajtó több párhuzamos feladatot képes kezelni.

Alapértelmezés szerint a Spark on HDInsight-on való futtatásakor két virtuális fonal-mag van definiálva mindegyik fizikai mag számára. Ez a szám egy jó párhuzamosságot és a több szálon átváltott környezetek mennyiségét biztosítja.

## <a name="guidance"></a>Útmutató

Ha a Spark analitikus számítási feladatait a Data Lake Storage Gen1ban lévő adatokkal kívánja használni, javasoljuk, hogy a legújabb HDInsight-verziót használja a legjobb teljesítmény eléréséhez a Data Lake Storage Gen1. Ha a feladata nagyobb I/O-igényű, a teljesítmény javítása érdekében bizonyos paraméterek is konfigurálhatók. A Data Lake Storage Gen1 egy nagy mértékben méretezhető tárolási platform, amely képes a magas átviteli sebesség kezelésére. Ha a feladatnak többnyire olvasási vagy írási művelete van, akkor a Data Lake Storage Gen1 az I/O-hoz és a-ból való párhuzamosság növelése növelheti a teljesítményt.

Az I/O-igényes feladatok esetében néhány általános módszer az egyidejűség növelésére.

**1. lépés: a fürtön futó alkalmazások számának meghatározása** – tudnia kell, hogy hány alkalmazás fut a fürtön, beleértve az aktuálisat is. Az egyes Spark-beállítások alapértelmezett értékei feltételezik, hogy jelenleg 4 alkalmazás fut egyszerre. Ezért az egyes alkalmazásokhoz csak a fürt 25%-a lesz elérhető. A jobb teljesítmény érdekében felülbírálhatja az alapértelmezett értékeket a végrehajtók számának megváltoztatásával.

**2. lépés: a végrehajtó – memória beállítása** – az első beállítás a végrehajtó – memória. A memória a futtatni kívánt feladatoktól függ. A párhuzamosságot úgy növelheti, hogy kevesebb memóriát foglal le a végrehajtón. Ha a feladatok futtatásakor nem jelenik meg a memória kivételei, akkor növelje a paraméter értékét. Egy másik lehetőség, hogy több memóriát kell használnia egy olyan fürt használatával, amely nagyobb mennyiségű memóriával rendelkezik, vagy növeli a fürt méretét. További memória több végrehajtót is lehetővé tesz, ami nagyobb párhuzamosságot jelent.

**3. lépés: a végrehajtó-magok beállítása** – olyan I/O-igényű számítási feladatokhoz, amelyek nem rendelkeznek összetett műveletekkel, érdemes nagy számú végrehajtói magot kezdeni a párhuzamos feladatok számának növelésére. A végrehajtó-magok beállítása jó kezdés.

```console
executor-cores = 4
```

A végrehajtói magok számának növelése nagyobb párhuzamosságot biztosít, így különböző végrehajtói magokkal kísérletezhet. Az összetettebb műveletekkel rendelkező feladatok esetében csökkentse a magok számát a végrehajtón. Ha a végrehajtó-magok értéke 4-es nagyobb, akkor a szemetet nem lehet hatékonyan és csökkenhet a teljesítmény.

**4. lépés: a szálbeli memória mennyiségének meghatározása a fürtben** – ez az információ a Ambari-ben érhető el. Navigáljon a FONALhoz, és tekintse meg a Contigs lapot. Ebben az ablakban a szál memóriája jelenik meg.
Vegye figyelembe, hogy az ablakban az alapértelmezett fonal-tároló mérete is látható. A fonal tárolójának mérete megegyezik a memória/végrehajtó paraméter értékével.

Összes fonal memóriája = csomópontok * FONÁL memória/csomópont

**5. lépés: a NUM-végrehajtók kiszámítása**

**Memória-megkötés kiszámítása** – a NUM-végrehajtók paramétert a memória vagy a CPU korlátozza. A memória megkötését az alkalmazáshoz rendelkezésre álló fonal-memória mennyisége határozza meg. A fonal teljes memóriájának és osztásának elvégzése a végrehajtó által a memóriában. A megkötést az alkalmazások számának megfelelően ki kell méretezni, ezért az alkalmazások száma alapján osztjuk.

Memória korlátozás = (teljes fonal-memória/végrehajtó memória)/alkalmazások száma

**CPU-megkötés kiszámítása** – a rendszer kiszámítja a CPU-korlátozást, mivel a teljes virtuális magok száma a végrehajtón belüli magok számával egyenlő. Minden fizikai mag esetében 2 virtuális mag van. A memória megkötéséhez hasonlóan az alkalmazások száma is megoszlik.

Virtual magok = (a fürtben lévő csomópontok száma * # fizikai magok száma a következő csomópontban: * 2) CPU-megkötés = (teljes virtuális mag/végrehajtó által kijelölve)/alkalmazások száma

**NUM-végrehajtók beállítása** – a NUM-végrehajtók paramétert a rendszer a memória korlátozásának és a CPU-megkötés minimális értékének megadásával határozza meg. 

NUM-végrehajtók = min (teljes virtuális mag/végrehajtó által kiváltott, rendelkezésre álló szál memóriája/végrehajtó – memória) a nagyobb számú NUM-végrehajtó beállítása nem szükségszerűen növekszik a teljesítményben. Érdemes figyelembe vennie, hogy további végrehajtók hozzáadása további terhelést eredményez minden további végrehajtónál, ami potenciálisan csökkentheti a teljesítményt. A NUM-végrehajtók a fürt erőforrásaihoz vannak kötve.

## <a name="example-calculation"></a>Példa a számításra

Tegyük fel, hogy jelenleg egy olyan, 8 D4v2 csomópontból álló fürt található, amely két alkalmazást futtat, beleértve a futtatni kívánt programot.

**1. lépés: a fürtön futó alkalmazások számának meghatározása** – tudja, hogy a fürtön két alkalmazás van, beleértve a futtatni kívánt alkalmazást is.

**2. lépés: a végrehajtó-memória beállítása** – ebben a példában megállapítjuk, hogy a végrehajtói memória 6gb-je elegendő az I/O-igényes feladatokhoz.

```console
executor-memory = 6GB
```

**3. lépés: a végrehajtó-magok beállítása** – mivel ez egy I/O-igényes művelet, beállíthatja a magok számát az egyes végrehajtók számára négyre. A magoknak a négynél nagyobbra állítása a szemetet összegyűjtési problémákat okozhat.

```console
executor-cores = 4
```

**4. lépés: a szálbeli memória mennyiségének meghatározása a fürtben** – a Ambari navigálva megtudhatja, hogy minden D4v2 25 GB-os memóriával rendelkezik. Mivel 8 csomópont van, a rendelkezésre álló szál memóriája 8-szor van megszorozva.

Összes szál memóriája = csomópontok * FONÁL memóriája */csomópontok száma összesen FONÁL: 8 csomópont * 25 GB = 200 GB

**5. lépés: a NUM-végrehajtók kiszámítása** – a NUM-végrehajtók paramétert úgy kell meghatározni, hogy a memória megkötésének minimális számát és a CPU-korlátot a Spark-on futó alkalmazások száma alapján osztja el.

A **memória megkötésének kiszámítása** – a memória megkötésének kiszámítása a memória által lefuttatott memória teljes száma alapján történik.

Memória megkötése = (teljes fonal-memória/végrehajtó memória)/az alkalmazások memóriabeli megkötése = (200 GB/6 GB)/2 memória megkötése = 16 (lekerekített) a CPU-megkötés **kiszámítása** – a CPU-megkötés kiszámítása a teljes fonal-magok alapján történik, a magok száma alapján.

FONAL magok = a fürtben található csomópontok száma * 2 szálból álló magok száma * 2 fonal magok = 8 csomópont * 8 mag/D14 * 2 = 128 CPU-megkötés = (teljes fonal magok/végrehajtók száma)/a processzorok száma a CPU-megkötés = (128/4)/2 CPU-megkötés = 16

**NUM-végrehajtók beállítása**

NUM-végrehajtók = min (memória megkötése, CPU-megkötés) Num-végrehajtók = min (16, 16) Num-végrehajtók = 16