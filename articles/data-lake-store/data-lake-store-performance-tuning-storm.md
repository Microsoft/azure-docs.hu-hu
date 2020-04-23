---
title: Azure Data Lake Storage Gen1 Storm Performance tuning iránymutatásai | Microsoft Docs
description: Azure Data Lake Storage Gen1 Storm Performance tuning iránymutatásai
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 8066a759cf80be6e9ca232bcd3693a5fa4d2f2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436477"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Teljesítmény-finomhangolási útmutató a Storm on HDInsight és Azure Data Lake Storage Gen1

Megismerheti azokat a tényezőket, amelyeket figyelembe kell venni az Azure Storm-topológiák teljesítményének finomhangolása során. Például fontos megérteni a kiöntő és a csavarok által végzett munka jellemzőit (legyen szó akár I/O-, akár a memória-intenzív működésről). Ez a cikk a teljesítmény-hangolási irányelvek széles körét ismerteti, beleértve a gyakori problémák elhárítását is.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen1-fiók**. A létrehozásával kapcsolatos útmutatásért tekintse meg a [Azure Data Lake Storage Gen1 első lépései](data-lake-store-get-started-portal.md)című témakört.
* Egy Data Lake Storage Gen1 fiókhoz hozzáféréssel rendelkező **Azure HDInsight-fürt** . Lásd: [HDInsight-fürt létrehozása Data Lake Storage Gen1sal](data-lake-store-hdinsight-hadoop-use-portal.md). Győződjön meg arról, hogy engedélyezi Távoli asztal a fürt számára.
* **Storm-fürt futtatása Data Lake Storage Gen1on**. További információ: [Storm on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Teljesítmény-finomhangolási irányelvek a Data Lake Storage Gen1**.  Az általános teljesítménnyel kapcsolatos fogalmakat lásd: [Data Lake Storage Gen1 teljesítmény-finomhangolási útmutató](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

## <a name="tune-the-parallelism-of-the-topology"></a>A topológia párhuzamosságának hangolása

Előfordulhat, hogy a teljesítmény növelése érdekében az I/O és a Data Lake Storage Gen1 közötti párhuzamosságot kell növelni. A Storm-topológia olyan konfigurációkat tartalmaz, amelyek meghatározzák a párhuzamosságot:
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

### <a name="get-the-best-performance-from-data-lake-storage-gen1"></a>A Data Lake Storage Gen1 legjobb teljesítményének beolvasása

Data Lake Storage Gen1 használatakor a legjobb teljesítményt kapja, ha a következőket hajtja végre:
* A kis méretű hozzáfűzéseket nagyobb méretekben (ideális esetben 4 MB) egyesítheti.
* Annyi egyidejű kérést, amennyit csak tud. Mivel minden egyes bolti szál blokkolja az olvasásokat, egy mag 8-12 szálon belül szeretne lenni. Ez tartja a hálózati adaptert és a CPU-t is. Egy nagyobb méretű virtuális gép több párhuzamos kérést tesz lehetővé.  

### <a name="example-topology"></a>Példa topológia

Tegyük fel, hogy egy D13v2 Azure-beli virtuális géppel rendelkező 8 munkavégző csomópont-fürtöt tartalmaz. Ez a virtuális gép 8 maggal rendelkezik, így a 8 feldolgozó csomópont között összesen 64 mag van.

Tegyük fel, hogy 8 csavaros szálat teszünk mag-onként. Az adott 64-es magok esetében ez azt jelenti, hogy a 512 összes bolt végrehajtó példányát (azaz a szálakat) szeretnénk használni. Ebben az esetben tegyük fel, hogy a virtuális gépen egy JVM indítunk, és főleg a JVM belüli szál-párhuzamosságot használják a párhuzamosság eléréséhez. Ez azt jelenti, hogy 8 feldolgozói feladatra van szükség (egy Azure-beli virtuális gépen eggyel) és a 512-es bolt végrehajtói Ebben a konfigurációban a Storm a munkavégző csomópontok (más néven felügyeleti csomópontok) között egyenletesen osztja el a dolgozókat, így az egyes munkavégző csomópontok 1 JVM. A Storm most már a felügyelők között igyekszik a végrehajtókat egyenletesen terjeszteni a felügyelők között, így mindegyik felügyelő (azaz JVM) 8 szálat biztosít.

## <a name="tune-additional-parameters"></a>További paraméterek hangolása
Az alapszintű topológia megadását követően megtekintheti, hogy a következő paramétereket szeretné-e használni:
* **A JVMs száma munkavégző csomóponton.** Ha nagy adatstruktúrával (például egy keresési táblával) rendelkezik a memóriában, minden JVM külön másolat szükséges. Azt is megteheti, hogy az adatszerkezetet több szálon is használhatja, ha kevesebb JVMs van. A bolt I/O esetében a JVMs száma nem annyira különbözik, mint a JVMs-k között hozzáadott szálak száma. Az egyszerűség kedvéért jó ötlet, hogy egy JVM egy munkatársa legyen. Attól függően, hogy mit csinál a bolt, vagy milyen alkalmazások feldolgozására van szükség, de előfordulhat, hogy módosítania kell ezt a számot.
* **Kiöntő végrehajtók száma.** Mivel az előző példa a Data Lake Storage Gen1 írásához a boltokat használja, a kiöntők száma nem kapcsolódik közvetlenül a bolt teljesítményéhez. A kifolyóban megjelenő feldolgozási vagy I/O-mennyiségtől függően azonban érdemes a kiöntő teljesítményt a legjobb teljesítmény érdekében hangolni. Győződjön meg arról, hogy elegendő kiöntő áll rendelkezésre, hogy megőrizze a csavarok elfoglalt mennyiségét. A kifolyók kimeneti sebességének meg kell egyeznie a csavarok teljesítményével. A tényleges konfiguráció a kiöntőtől függ.
* **Feladatok száma.** Minden egyes bolt egyetlen szálként fut. Az egyes boltokban további feladatok nem biztosítanak további párhuzamosságot. Az egyetlen hasznos alkalom, ha a rekord visszaigazolási folyamata a bolt végrehajtási idejének nagy hányadát veszi igénybe. Érdemes a sok rekordok egy nagyobb hozzáfűzéssel csoportosítani, mielőtt elküld egy nyugtát a boltból. Így a legtöbb esetben több feladat nem biztosít további kedvezményt.
* **Helyi vagy véletlenszerű csoportosítás.** Ha ez a beállítás engedélyezve van, a rendszer a rekordok az ugyanazon munkavégző folyamaton belüli boltoknak továbbítja. Ez csökkenti a folyamatok közötti kommunikációt és a hálózati hívásokat. Ez a legtöbb topológiához ajánlott.

Ez az alapszintű forgatókönyv jó kiindulási pont. Tesztelje a saját adatait az előző paraméterek finomhangolásához az optimális teljesítmény érdekében.

## <a name="tune-the-spout"></a>A kiöntő hangolása

A kiöntő hangolásához a következő beállításokat módosíthatja.

- **Rekord időtúllépése: topológia. Message. timeout. mp**. Ez a beállítás határozza meg, hogy az üzenet mennyi időt vesz igénybe, és fogadja a nyugtát, mielőtt a rendszer nem veszi figyelembe a hibát.

- **Maximális memória/munkavégző folyamat: Worker. childopts**. Ezzel a beállítással további parancssori paramétereket adhat meg a Java-feldolgozókhoz. Az itt leggyakrabban használt beállítás a XmX, amely meghatározza a JVM halom számára lefoglalt maximális memóriát.

- **Maximális kiöntő függőben: topológia. max. kiöntő. függőben**. Ezzel a beállítással megadható, hogy hány rekordok lehet a repülési (a topológia összes csomópontján még nem ismerhető fel) egy kiöntő szálon, bármikor.

  Egy jó számítási módszer a rekordok méretének becslése. Ebből kiderítheti, hogy mekkora memória van egy kiöntő szálban. A szál számára lefoglalt teljes memória, amelyet ez az érték oszt meg, meg kell adnia a felső korlátot a kiöntő függőben lévő paraméterhez.

## <a name="tune-the-bolt"></a>A bolt hangolása
Data Lake Storage Gen1 írásakor a méret szinkronizálására szolgáló szabályzatot (az ügyféloldali puffert) állítsa 4 MB-ra. A rendszer csak akkor hajt végre kiürítést vagy vízszintes szinkronizálás nevezőjének (), ha a puffer mérete ezen az értéken van. A munkavégző virtuális gép Data Lake Storage Gen1 illesztőprogramja automatikusan ezt a pufferelést hajtja végre, kivéve, ha explicit módon elvégez egy vízszintes szinkronizálás nevezőjének ().

Az alapértelmezett Data Lake Storage Gen1 Storm-bolt mérete szinkronizálási házirend paraméterrel (fileBufferSize) rendelkezik, amely a paraméter finomhangolásához használható.

Az I/O-igényes topológiákban érdemes az egyes csavarokat a saját fájljába írni, és egy fájl-rotációs szabályzatot (fileRotationSize) beállítani. Ha a fájl elér egy adott méretet, a rendszer automatikusan kiüríti az adatfolyamot, és egy új fájlt ír. Az elforgatás ajánlott fájlmérete 1 GB.

### <a name="handle-tuple-data"></a>Rekordos adatkezelés

A Storm-ben a kiöntő egy rekordra tart, amíg a bolt kifejezetten nem ismeri el. Ha a bolt beolvasott egy rekordot, de még nem ismerte fel, előfordulhat, hogy a kiöntő nem marad meg Data Lake Storage Gen1 háttérbe. Egy rekord beérkezése után a kiöntő garantálhatja a bolt által biztosított adatmegőrzést, és törölheti a forrásadatokat a forrásból származó adatokból.  

A Data Lake Storage Gen1 legjobb teljesítményéhez a bolt 4 MB-nyi rekordot tartalmaz. Ezután írjon a Data Lake Storage Gen1 háttérként egy 4 MB-os írási értékre. Miután az adatgyűjtés sikeresen megtörtént a tárolóban (a hflush () hívásával), a bolt visszaigazolhatja az kiöntőt. Ez az a példa, amelyet az itt megadott. A hflush () hívásának elvégzése és a rekordok elfogadása előtt is elfogadható, hogy nagyobb számú rekordok tartson fenn. Ez azonban növeli a rekordok azon számát, amelyet a kiöntőnek tárolnia kell, és így növeli a JVM szükséges memória mennyiségét.

> [!NOTE]
> Előfordulhat, hogy az alkalmazásoknak a rekordok gyakrabban kell megismerniük (4 MB-nál kisebb adatmennyiség esetén) más nem teljesítménybeli okokból. Azonban ez hatással lehet a tárolási háttér I/O-átviteli sebességére. Gondosan mérjük meg ezt a kompromisszumot a bolt I/O-teljesítményével szemben.

Ha a rekordok bejövő sebessége nem magas, ezért a 4 MB-os puffer hosszú időt vesz igénybe, érdemes lehet ezt a következő módon enyhíteni:
* Csökkentse a csavarok számát, így kevesebb puffert kell kitölteni.
* Olyan időalapú vagy Count-alapú szabályzattal rendelkezik, amelyben a hflush () minden x ürítéssel vagy y ezredmásodperctel aktiválódik, és az eddig felhalmozott rekordok visszaigazolja.

Vegye figyelembe, hogy ebben az esetben az átviteli sebesség alacsonyabb, de a lassú események miatt a maximális átviteli sebesség nem a legnagyobb cél. Ezek a megoldások segítenek csökkenteni a rekordnak az áruházba való áthaladásához szükséges teljes időt. Ez akkor fordulhat elő, ha egy valós idejű adatcsatorna esetében is alacsony esemény arányt szeretne használni. Azt is vegye figyelembe, hogy ha a bejövő rekord sebessége alacsony, állítsa be a topológia. Message. timeout_secs paramétert, hogy a rekordok ne legyen időtúllépés, amíg a rendszer pufferelt vagy feldolgozott értéket kap.

## <a name="monitor-your-topology-in-storm"></a>A topológia figyelése a Storm-ben  
Amíg a topológia fut, nyomon követheti a Storm felhasználói felületén. A következő fő paramétereket tekintheti meg:

* **A folyamat végrehajtásának teljes késése.** Ez az az átlagos idő, amikor egy rekordot a kiöntő, a bolt által feldolgozott, és a rendszer elismerte.

* **A bolt összes folyamatának késése.** Ez az átlagos idő, amelyet a rendszer a rekordban töltött le, amíg nem kap nyugtát.

* **A bolt összes végrehajtási késése.** Ez az az átlagos idő, amelyet a bolt hajt végre a végrehajtási metódusban.

* **Hibák száma.** Ez azon rekordok számát jelenti, amelyeket a rendszer nem tudott teljes mértékben feldolgozni, mielőtt időtúllépés történt.

* **Kapacitás.** Ez azt méri, hogy a rendszer mennyire foglalt. Ha ez a szám 1, a csavarok olyan gyorsan működnek, amennyit csak tud. Ha kevesebb, mint 1, növelje a párhuzamosságot. Ha az érték nagyobb, mint 1, csökkentse a párhuzamosságot.

## <a name="troubleshoot-common-problems"></a>Gyakori problémák elhárítása
Íme néhány gyakori hibaelhárítási forgatókönyv.
* **Számos rekordok időtúllépéssel van elvégezve.** A szűk keresztmetszetek meghatározásához tekintse meg a topológia egyes csomópontjait. Ennek a leggyakoribb oka az, hogy a csavarok nem képesek lépést tartani a kifolyókkal. Ez azt eredményezi, hogy a rendszer feldolgozza a belső pufferek rekordok eltömődését. Vegye fontolóra az időtúllépési érték növelését vagy a függőben lévő maximális kiöntő csökkentését.

* **A folyamat végrehajtási késése magas, de a folyamat alacsony feldolgozási késéssel jár.** Ebben az esetben lehetséges, hogy a rekordok nem elég gyorsan nyugtázni. Győződjön meg arról, hogy elegendő számú nyugtázás van. Egy másik lehetőség, hogy túl sokáig várakozik a várólistán, mielőtt a csavarok megkezdik a feldolgozást. Csökkentse a függőben lévő maximális kiöntőt.

* **Magas a bolt késése.** Ez azt jelenti, hogy a bolt Execute () metódusa túl sokáig tart. Optimalizálja a kódot, vagy nézze meg az írási méreteket és a kiürítési viselkedést.

### <a name="data-lake-storage-gen1-throttling"></a>Data Lake Storage Gen1 szabályozás
Ha eléri a Data Lake Storage Gen1 által biztosított sávszélesség korlátait, akkor felmerülhet a feladat hibái. A hibák szabályozásához keresse meg a naplókat. Csökkentheti a párhuzamosságot a tároló méretének növelésével.    

Ha szeretné megtekinteni, hogy a rendszer leszabályozza-e a szabályozást, engedélyezze a hibakeresési naplózást az ügyféloldali oldalon:

1. A **Ambari** > **Storm** > **Config**config > **Advanced Storm-Worker-log4j**, Change ** &lt;root level = "info&gt; "** a ** &lt;root level = "debug&gt;"**. A konfiguráció érvénybe léptetéséhez indítsa újra az összes csomópontot/szolgáltatást.
2. Figyelje a Storm-topológiák naplóit a munkavégző csomópontokon&gt;/&lt;(&gt;a/var/log/Storm/Worker-artifacts/&lt;TopologyName-port/Worker.log alatt) Data Lake Storage Gen1 szabályozási kivételek esetében.

## <a name="next-steps"></a>További lépések
A Storm további teljesítmény-finomhangolását ebben a [blogban](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/)lehet hivatkozni.

Ha további példát szeretne futtatni, tekintse [meg ezt a githubon](https://github.com/hdinsight/storm-performance-automation).
