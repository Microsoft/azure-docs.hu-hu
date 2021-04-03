---
title: 'Teljesítmény hangolása: Storm, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Az Azure Storm-topológiák teljesítményének hangolásához szükséges irányelvek ismertetése Azure HDInsight-fürtön és Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4db85357ee970d13d6b4fcce195cae66932bed18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912790"
---
# <a name="tune-performance-storm-hdinsight--azure-data-lake-storage-gen2"></a>Teljesítmény hangolása: Storm, HDInsight & Azure Data Lake Storage Gen2

Megismerheti azokat a tényezőket, amelyeket figyelembe kell venni az Azure Storm-topológiák teljesítményének finomhangolása során. Például fontos megérteni a kiöntő és a csavarok által végzett munka jellemzőit (legyen szó akár I/O-, akár a memória-intenzív működésről). Ez a cikk a teljesítmény-hangolási irányelvek széles körét ismerteti, beleértve a gyakori problémák elhárítását is.

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen2-fiók**. A létrehozásával kapcsolatos utasításokért lásd: rövid útmutató [: Storage-fiók létrehozása analitikai szolgáltatáshoz](../common/storage-account-create.md).
* **Azure HDInsight-fürt** Data Lake Storage Gen2 fiókhoz való hozzáféréssel. Lásd: [Azure Data Lake Storage Gen2 használata az Azure HDInsight-fürtökkel](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md). Győződjön meg arról, hogy engedélyezi Távoli asztal a fürt számára.
* **Storm-fürt futtatása Data Lake Storage Gen2on**. További információ: [Storm on HDInsight](../../hdinsight/storm/apache-storm-overview.md).
* **Teljesítmény-finomhangolási irányelvek a Data Lake Storage Gen2**.  Az általános teljesítménnyel kapcsolatos fogalmakat lásd: [Data Lake Storage Gen2 teljesítmény-finomhangolási útmutató](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>A topológia párhuzamosságának hangolása

Előfordulhat, hogy a teljesítmény növelése érdekében az I/O és a Data Lake Storage Gen2 közötti párhuzamosságot kell növelni. A Storm-topológia olyan konfigurációkat tartalmaz, amelyek meghatározzák a párhuzamosságot:
* A munkavégző folyamatok száma (a feldolgozók egyenletesen oszlanak meg a virtuális gépek között).
* A kiöntő végrehajtó példányainak száma.
* A bolt végrehajtó példányainak száma.
* A kiöntő feladatok száma.
* A bolti feladatok száma.

Például egy 4 virtuális géppel és 4 munkavégző folyamattal rendelkező fürtön, 32 kiöntő végrehajtó és 32 kiöntő feladat, valamint 256 bolt végrehajtói és 512 bolti feladatok, vegye figyelembe a következőket:

Minden olyan felügyelő, amely egy feldolgozói csomópont, egyetlen feldolgozó Java virtuális gép (JVM) folyamattal rendelkezik. Ez a JVM folyamat 4 kiöntő szálat és 64 bolti szálat kezel. Az egyes szálakon belül a feladatok egymás után futnak. Az előző konfigurációval minden kiöntő szál 1 feladatot tartalmaz, és minden egyes bolti szál 2 feladattal rendelkezik.

A Storm-ben itt láthatók a különböző érintett összetevők, és hogy azok milyen mértékben befolyásolják a párhuzamosság szintjét:
* A feladatok küldéséhez és kezeléséhez a fő csomópontot (a Storm Nimbus néven ismert) használja a rendszer. Ezek a csomópontok nem befolyásolják a párhuzamosság mértékét.
* A felügyelő csomópontjai. A HDInsight-ben ez az Azure virtuális gép munkavégző csomópontjának felel meg.
* A feldolgozói feladatok a virtuális gépeken futó Storm-folyamatok. Minden feldolgozói feladat egy JVM-példánynak felel meg. A Storm a lehető legegyenletesebb módon osztja el a munkavégző csomópontok számára megadott munkavégző folyamatokat.
* Kiöntő és bolt végrehajtó példányai. Minden végrehajtó példány egy, a feldolgozón (JVMs) belül futó szálnak felel meg.
* Storm-feladatok. Ezek azok a logikai feladatok, amelyeket ezek a szálak futtatnak. Ez nem változtatja meg a párhuzamosság szintjét, ezért érdemes kiértékelnie, hogy a végrehajtónak több feladatra van szüksége, vagy nem.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>A Data Lake Storage Gen2 legjobb teljesítményének beolvasása

Data Lake Storage Gen2 használatakor a legjobb teljesítményt kapja, ha a következőket hajtja végre:
* Egyesítse a kis méretű hozzáfűzéseket nagyobb méretekben.
* Annyi egyidejű kérést, amennyit csak tud. Mivel minden egyes bolti szál blokkolja az olvasásokat, egy mag 8-12 szálon belül szeretne lenni. Ez tartja a hálózati adaptert és a CPU-t is. Egy nagyobb méretű virtuális gép több párhuzamos kérést tesz lehetővé.  

### <a name="example-topology"></a>Példa topológia

Tegyük fel, hogy egy D13v2 Azure-beli virtuális géppel rendelkező 8 munkavégző csomópont-fürtöt tartalmaz. Ez a virtuális gép 8 maggal rendelkezik, így a 8 feldolgozó csomópont között összesen 64 mag van.

Tegyük fel, hogy 8 csavaros szálat teszünk mag-onként. Az adott 64-es magok esetében ez azt jelenti, hogy a 512 összes bolt végrehajtó példányát (azaz a szálakat) szeretnénk használni. Ebben az esetben tegyük fel, hogy a virtuális gépen egy JVM indítunk, és főleg a JVM belüli szál-párhuzamosságot használják a párhuzamosság eléréséhez. Ez azt jelenti, hogy 8 feldolgozói feladatra van szükség (egy Azure-beli virtuális gépen eggyel) és a 512-es bolt végrehajtói Ebben a konfigurációban a Storm a munkavégző csomópontok (más néven felügyeleti csomópontok) között egyenletesen osztja el a dolgozókat, így az egyes munkavégző csomópontok 1 JVM. A Storm most már a felügyelők között igyekszik a végrehajtókat egyenletesen terjeszteni a felügyelők között, így mindegyik felügyelő (azaz JVM) 8 szálat biztosít.

## <a name="tune-additional-parameters"></a>További paraméterek hangolása
Az alapszintű topológia megadását követően megtekintheti, hogy a következő paramétereket szeretné-e használni:
* **A JVMs száma munkavégző csomóponton.** Ha nagy adatstruktúrával (például egy keresési táblával) rendelkezik a memóriában, minden JVM külön másolat szükséges. Azt is megteheti, hogy az adatszerkezetet több szálon is használhatja, ha kevesebb JVMs van. A bolt I/O esetében a JVMs száma nem annyira különbözik, mint a JVMs-k között hozzáadott szálak száma. Az egyszerűség kedvéért jó ötlet, hogy egy JVM egy munkatársa legyen. Attól függően, hogy mit csinál a bolt, vagy milyen alkalmazások feldolgozására van szükség, de előfordulhat, hogy módosítania kell ezt a számot.
* **Kiöntő végrehajtók száma.** Mivel az előző példa a Data Lake Storage Gen2 írásához a boltokat használja, a kiöntők száma nem kapcsolódik közvetlenül a bolt teljesítményéhez. A kifolyóban megjelenő feldolgozási vagy I/O-mennyiségtől függően azonban érdemes a kiöntő teljesítményt a legjobb teljesítmény érdekében hangolni. Győződjön meg arról, hogy elegendő kiöntő áll rendelkezésre, hogy megőrizze a csavarok elfoglalt mennyiségét. A kifolyók kimeneti sebességének meg kell egyeznie a csavarok teljesítményével. A tényleges konfiguráció a kiöntőtől függ.
* **Feladatok száma.** Minden egyes bolt egyetlen szálként fut. Az egyes boltokban további feladatok nem biztosítanak további párhuzamosságot. Az egyetlen hasznos alkalom, ha a rekord visszaigazolási folyamata a bolt végrehajtási idejének nagy hányadát veszi igénybe. Érdemes a sok rekordok egy nagyobb hozzáfűzéssel csoportosítani, mielőtt elküld egy nyugtát a boltból. Így a legtöbb esetben több feladat nem biztosít további kedvezményt.
* **Helyi vagy véletlenszerű csoportosítás.** Ha ez a beállítás engedélyezve van, a rendszer a rekordok az ugyanazon munkavégző folyamaton belüli boltoknak továbbítja. Ez csökkenti a folyamatok közötti kommunikációt és a hálózati hívásokat. Ez a legtöbb topológiához ajánlott.

Ez az alapszintű forgatókönyv jó kiindulási pont. Tesztelje a saját adatait az előző paraméterek finomhangolásához az optimális teljesítmény érdekében.

## <a name="tune-the-spout"></a>A kiöntő hangolása

A kiöntő hangolásához a következő beállításokat módosíthatja.

- **Rekord időtúllépése: topológia. Message. timeout. mp**. Ez a beállítás határozza meg, hogy az üzenet mennyi időt vesz igénybe, és fogadja a visszaigazolást, mielőtt a rendszer nem tudta figyelembe venni.

- **Maximális memória/munkavégző folyamat: Worker. childopts**. Ezzel a beállítással további parancssori paramétereket adhat meg a Java-feldolgozókhoz. Az itt leggyakrabban használt beállítás a XmX, amely meghatározza a JVM halom számára lefoglalt maximális memóriát.

- **Maximális kiöntő függőben: topológia. max. kiöntő. függőben**. Ezzel a beállítással megadható, hogy hány rekordok lehet a repülési (a topológia összes csomópontján még nem ismerhető fel) egy kiöntő szálon, bármikor.

  Egy jó számítási módszer a rekordok méretének becslése. Ebből kiderítheti, hogy mekkora memória van egy kiöntő szálban. A szál számára lefoglalt teljes memória, amelyet ez az érték oszt meg, meg kell adnia a felső korlátot a kiöntő függőben lévő paraméterhez.

Az alapértelmezett Data Lake Storage Gen2 Storm-bolt mérete szinkronizálási házirend paraméterrel (fileBufferSize) rendelkezik, amely a paraméter finomhangolásához használható.

Az I/O-igényes topológiákban érdemes az egyes csavarokat a saját fájljába írni, és egy fájl-rotációs szabályzatot (fileRotationSize) beállítani. Ha a fájl elér egy adott méretet, a rendszer automatikusan kiüríti az adatfolyamot, és egy új fájlt ír. Az elforgatás ajánlott fájlmérete 1 GB.

## <a name="monitor-your-topology-in-storm"></a>A topológia figyelése a Storm-ben  
Amíg a topológia fut, nyomon követheti a Storm felhasználói felületén. A következő fő paramétereket tekintheti meg:

* **A folyamat végrehajtásának teljes késése.** Ez az az átlagos idő, amikor egy rekordot a kiöntő, a bolt által feldolgozott, és a rendszer elismerte.

* **A bolt összes folyamatának késése.** Ez az átlagos idő, amelyet a tömb a rekordban eltöltött, egészen addig, amíg nem kap nyugtát.

* **A bolt összes végrehajtási késése.** Ez az az átlagos idő, amelyet a bolt hajt végre a végrehajtási metódusban.

* **Hibák száma.** Ez azon rekordok számát jelenti, amelyeket a rendszer nem tudott teljes mértékben feldolgozni, mielőtt időtúllépés történt.

* **Kapacitás.** Ez azt méri, hogy a rendszer mennyire foglalt. Ha ez a szám 1, a csavarok olyan gyorsan működnek, amennyit csak tud. Ha kevesebb, mint 1, növelje a párhuzamosságot. Ha az érték nagyobb, mint 1, csökkentse a párhuzamosságot.

## <a name="troubleshoot-common-problems"></a>Gyakori problémák elhárítása
Íme néhány gyakori hibaelhárítási forgatókönyv.
* **Számos rekordok időtúllépéssel van elvégezve.** A szűk keresztmetszetek meghatározásához tekintse meg a topológia egyes csomópontjait. Ennek a leggyakoribb oka az, hogy a csavarok nem képesek lépést tartani a kifolyókkal. Ez azt eredményezi, hogy a rendszer feldolgozza a belső pufferek rekordok eltömődését. Vegye fontolóra az időtúllépési érték növelését vagy a függőben lévő maximális kiöntő csökkentését.

* **A folyamat végrehajtási késése magas, de a folyamat alacsony feldolgozási késéssel jár.** Ebben az esetben lehetséges, hogy a rekordok nem elég gyorsan nyugtázni. Győződjön meg arról, hogy elegendő számú nyugtázás van. Egy másik lehetőség, hogy túl sokáig várakozik a várólistán, mielőtt a csavarok megkezdik a feldolgozást. Csökkentse a függőben lévő maximális kiöntőt.

* **Magas a bolt késése.** Ez azt jelenti, hogy a bolt Execute () metódusa túl sokáig tart. Optimalizálja a kódot, vagy nézze meg az írási méreteket és a kiürítési viselkedést.

### <a name="data-lake-storage-gen2-throttling"></a>Data Lake Storage Gen2 szabályozás
Ha eléri a Data Lake Storage Gen2 által biztosított sávszélesség korlátait, akkor felmerülhet a feladat hibái. A hibák szabályozásához keresse meg a naplókat. Csökkentheti a párhuzamosságot a tároló méretének növelésével.    

Ha szeretné megtekinteni, hogy a rendszer leszabályozza-e a szabályozást, engedélyezze a hibakeresési naplózást az ügyféloldali oldalon:

1. A **Ambari**  >  **Storm**  >  **config**  >  **Advanced Storm-Worker-log4j**, Change **&lt; root level = "info &gt; "** a **&lt; root level = "debug &gt; "**. A konfiguráció érvénybe léptetéséhez indítsa újra az összes csomópontot/szolgáltatást.
2. Figyelje a Storm-topológiák naplóit a munkavégző csomópontokon (a/var/log/Storm/Worker-artifacts/ &lt; TopologyName &gt; / &lt; &gt; -port/Worker.log alatt) Data Lake Storage Gen2 szabályozási kivételek esetében.

## <a name="next-steps"></a>Következő lépések
A Storm további teljesítmény-finomhangolását ebben a [blogban](/archive/blogs/shanyu/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs)lehet hivatkozni.

Ha további példát szeretne futtatni, tekintse [meg ezt a githubon](https://github.com/hdinsight/storm-performance-automation).