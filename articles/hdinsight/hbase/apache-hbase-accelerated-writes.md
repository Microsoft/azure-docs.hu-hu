---
title: Azure HDInsight-beli gyorsított írási műveletek Apache HBase-hez
description: Áttekintést nyújt az Azure HDInsight gyorsított írási funkcióról, amely prémium szintű felügyelt lemezeket használ az Apache HBase Write Ahead-napló teljesítményének javítására.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/24/2020
ms.openlocfilehash: c42a9d47cabd803944b858fc470c1bedc39d2d77
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868902"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight-beli gyorsított írási műveletek Apache HBase-hez

Ez a cikk a **gyorsított írások** funkciójának hátterét mutatja be az Apache HBase az Azure HDInsight, valamint azt, hogy miként használható hatékonyan az írási teljesítmény javítása érdekében. A **gyorsított írások** az [Azure Premium SSD Managed Disks](../../virtual-machines/disks-types.md#premium-ssd) használatával javítják az Apache HBase Write Ahead log (Wal) teljesítményét. Ha többet szeretne megtudni az Apache HBase, olvassa el a [Mi az Apache HBase a HDInsight-ben](apache-hbase-overview.md)című témakört.

## <a name="overview-of-hbase-architecture"></a>A HBase architektúra áttekintése

A HBase-ben egy **sor** egy vagy több **oszlopból** áll, és egy **sor kulcs** azonosítja. Több sor alkot egy **táblát**. Az oszlopok **cellákat** tartalmaznak, amelyek az oszlopban szereplő érték időbélyegzős verziói. Az oszlopok **oszlopos családokba** vannak csoportosítva, és egy adott oszlopban lévő összes oszlop együtt tárolódik a **HFiles** nevű tárolási fájlokban.

Az adatfeldolgozási terhelés kiegyensúlyozására a HBase **régióit** használják. A HBase először a tábla sorait tárolja egyetlen régióban. A sorok több régióban oszlanak el, mivel a tábla adatai mennyisége növekszik. A **régió-kiszolgálók** több régióban is kezelhetik a kérelmeket.

## <a name="write-ahead-log-for-apache-hbase"></a>Írási napló az Apache HBase

A HBase először az adatfrissítéseket írja be egy írási előre írásos naplónak (WAL) nevezett véglegesítő naplóba. A frissítés a WAL-ben való tárolása után a memóriában lévő **MemStore** van írva. Ha a memóriában lévő adatmennyiség eléri a maximális kapacitást, a lemezként **HFile** van írva.

Ha egy **RegionServer** összeomlik vagy elérhetetlenné válik a MemStore kiürítése előtt, az írási előre napló használható a frissítések visszajátszására. A WAL nélkül, ha egy **RegionServer** összeomlik a frissítések egy **HFile** való kiürítése előtt, az összes frissítés elvész.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Gyorsított írási funkciók az Azure HDInsight for Apache HBase

A gyorsított írási funkció megoldja a Felhőbeli tárolásban lévő írási és olvasási műveletek által okozott nagyobb írási késések problémáját.  Az Apache HBase-fürtök HDInsight gyorsított írási funkciója minden RegionServer (feldolgozó csomópont) prémium szintű SSD által felügyelt lemezeket csatol. Az írási előre megadott naplók ezután a prémium szintű felügyelt lemezekre csatlakoztatott Hadoop fájlrendszerbe (HDFS) lesznek írva, a felhőalapú tárolás helyett.  A prémium szintű felügyelt lemez Solid-State lemezeket (SSD-ket) használ, és kiváló I/O-teljesítményt biztosít hibatűréssel.  A nem felügyelt lemezekkel ellentétben, ha az egyik tárolási egység leáll, az nem érinti az azonos rendelkezésre állási csoportba tartozó más tárolási egységeket.  Ennek eredményeképpen a felügyelt lemezek alacsony írási késést és nagyobb rugalmasságot biztosítanak alkalmazásai számára. Az Azure által felügyelt lemezekről az [Azure Managed Disks bemutatása](../../virtual-machines/managed-disks-overview.md)című témakörben olvashat bővebben.

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Gyorsított írások engedélyezése a HBase a HDInsight-ben

A gyorsított írási funkcióval rendelkező új HBase-fürt létrehozásához kövesse a [fürtök beállítása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md)című témakör lépéseit. Az **alapvető beállítások** lapon válassza ki a fürt típusát HBaseként, adjon meg egy összetevő-verziót, majd kattintson a melletti jelölőnégyzetre a **HBase gyorsított írások engedélyezése** lehetőség mellett. Ezután folytassa a fürt létrehozásához szükséges további lépéseket.

:::image type="content" source="./media/apache-hbase-accelerated-writes/azure-portal-create-hbase-wals.png" alt-text="Gyorsított írási lehetőségek engedélyezése a HDInsight Apache HBase" border="true":::

## <a name="other-considerations"></a>További szempontok

Az adattartósság megőrzése érdekében hozzon létre egy legalább három munkavégző csomóponttal rendelkező fürtöt. A létrehozást követően nem méretezheti le a fürtöt kevesebb mint három munkavégző csomópontra.

A fürt törlése előtt Ürítse ki vagy tiltsa le a HBase-táblákat, hogy ne veszítse el az előre nem látott napló adatait.

```
flush 'mytable'
```

```
disable 'mytable'
```

Hajtsa végre a hasonló lépéseket a fürt skálázásakor: Ürítse ki a táblákat, és tiltsa le a táblákat a bejövő adatértékek leállításához. A fürt nem méretezhető le kevesebb mint három csomópontra.

Az alábbi lépések végrehajtásával biztosítható, hogy a rendszer sikeres legyen, és elkerülje a namenode, ha a replikált vagy ideiglenes fájlok miatt a biztonságos módba kerül.

Ha a namenode egy vertikális leskálázás után is a safemode-ba kerül, a hdfs-parancsok használatával végezze el újra a replikált blokkokat, és a hdfs-t biztonságos módból válassza. Ez az újrareplikálás lehetővé teszi a HBase sikeres újraindítását.

## <a name="next-steps"></a>Következő lépések

* A hivatalos Apache HBase dokumentációja az [előre írható log szolgáltatásról](https://hbase.apache.org/book.html#wal)
* Ha a HDInsight Apache HBase-fürtöt a gyorsított írások használatára szeretné frissíteni, tekintse meg [az Apache HBase-fürt áttelepítését egy új verzióra](apache-hbase-migrate-new-version.md).
