---
title: Több HDInsight-fürt & egy Azure Data Lake Storage fiókot
description: Megtudhatja, hogyan használhat egynél több HDInsight-fürtöt egyetlen Data Lake Storage-fiókkal
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 6e220592f53103320c3bdb586fcbd0106219bfed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939543"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Több HDInsight-fürt használata Azure Data Lake Storage fiókkal

A HDInsight 3,5-es verziójától kezdve a Azure Data Lake Storage-fiókokkal rendelkező HDInsight-fürtöket alapértelmezett fájlrendszerként is létrehozhatja.
Data Lake Storage támogatja a korlátlan tárhelyet, amely nem csak nagy mennyiségű adattárolást tesz lehetővé. Ugyanakkor több HDInsight-fürt üzemeltetéséhez, amelyek egyetlen Data Lake Storage-fiókkal osztoznak. A HDInsight-fürt Data Lake Storage tárolóként való létrehozásával kapcsolatos útmutatásért lásd [: gyors útmutató: fürtök beállítása a HDInsight-ben](./hdinsight-hadoop-provision-linux-clusters.md).

Ez a cikk ajánlásokat nyújt a Data Lake Storage rendszergazdának egy olyan önálló és megosztott Data Lake Storage-fiók beállításához, amely több **aktív** HDInsight-fürtön is felhasználható. Ezek az ajánlások több biztonságos és nem biztonságos Apache Hadoop-fürtök üzemeltetésére vonatkoznak egy megosztott Data Lake Storage-fiókon.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage fájl-és mappa szintű ACL-ek

A cikk további része feltételezi, hogy jól ismeri a fájl-és mappa szintű ACL-eket a Azure Data Lake Storageban, amely részletesen ismerteti a [Azure Data Lake Storage hozzáférés-vezérlésének](../data-lake-store/data-lake-store-access-control.md)részleteit.

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Több HDInsight-fürt Data Lake Storage beállítása

Hozzon létre egy kétszintű mappa-hierarchiát, amely ismerteti a több HDInsight-fürt Data Lake Storage-fiókkal való használatának javaslatait. Vegye figyelembe, hogy rendelkezik egy Data Lake Storage-fiókkal a mappastruktúrát **/Clusters/Finance**. Ezzel a struktúrával a pénzügyi szervezet által igényelt összes fürt a/Clusters/Finance-t használhatja tárolási helyként. A jövőben, ha egy másik szervezet, a marketing, a HDInsight-fürtöket ugyanazzal a Data Lake Storage fiókkal szeretné létrehozni, létrehozhatnak/Clusters/marketing. Egyelőre csak használjuk a **/Clusters/Finance**-t.

Annak engedélyezéséhez, hogy a HDInsight-fürtök hatékonyan használják a mappastruktúrát, a Data Lake Storage rendszergazdának hozzá kell rendelnie a megfelelő engedélyeket a táblázatban leírtak szerint. A táblázatban megjelenő engedélyek hozzáférés-ACL-ek, és nem alapértelmezett ACL-ek.

|Mappa  |Engedélyek  |Tulajdonos felhasználó  |Tulajdonoscsoport  | Megnevezett felhasználó | Elnevezett felhasználói engedélyek | Elnevezett csoport | Névvel ellátott csoport engedélyei |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |felügyelet |felügyelet  |Szolgáltatásnév |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |felügyelet |felügyelet |Szolgáltatásnév |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |felügyelet |FINGRP  |Szolgáltatásnév |rwx  |-  |-     |

A táblázatban

- a **rendszergazda** a Data Lake Storage fiók létrehozója és rendszergazdája.
- Az **egyszerű szolgáltatásnév** a fiókhoz társított Azure Active Directory (HRE) egyszerű szolgáltatásnév.
- A **FINGRP** a HRE-ben létrehozott felhasználói csoport, amely a pénzügyi szervezet felhasználóit tartalmazza.

A HRE-alkalmazások létrehozásával kapcsolatos utasításokért (amely egyben egyszerű szolgáltatásnevet is létrehoz) tekintse meg a [HRE-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)című témakört. A felhasználói csoportok HRE-ben való létrehozásával kapcsolatos utasításokért lásd: [csoportok kezelése a Azure Active Directoryban](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Néhány fontos szempontot figyelembe kell venni.

- A fürthöz tartozó Storage-fiók használata **előtt** létre kell hozni és a megfelelő Data Lake Storage engedélyekkel kell kiépíteni a két szintű **/Clusters/Finance/**. Ez a struktúra nem jön létre automatikusan a fürtök létrehozása során.
- A fenti példa azt ajánlja, hogy a **/Clusters/Finance** tulajdonosi csoportját **FINGRP** -ként állítsa be, és az **r-x** hozzáférést a FINGRP-hoz a teljes mappa-hierarchiára a gyökértől kezdve. Ez biztosítja, hogy a FINGRP tagjai a root-től kezdődően navigálják a mappa szerkezetét.
- Abban az esetben, ha a különböző HRE-szolgáltatások a **/Clusters/Finance** területen hozhatnak létre fürtöket, a Sticky-bit (ha a **pénzügyi** mappában van beállítva) biztosítja, hogy az egyik egyszerű szolgáltatásnév által létrehozott mappákat a másik ne törölje.
- Ha a mappa szerkezete és engedélyei teljesülnek, a HDInsight-fürt létrehozási folyamata létrehoz egy fürtre vonatkozó tárolási helyet a **/Clusters/Finance/** területen. Például a fincluster01 nevű fürt tárterülete **/Clusters/Finance/fincluster01** lehet. A HDInsight-fürt által létrehozott mappák tulajdonjoga és engedélyei itt jelennek meg a táblázatban.

    |Mappa  |Engedélyek  |Tulajdonos felhasználó  |Tulajdonoscsoport  | Megnevezett felhasználó | Elnevezett felhasználói engedélyek | Elnevezett csoport | Névvel ellátott csoport engedélyei |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Szolgáltatásnév |FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>A feladatok bemeneti és kimeneti adataira vonatkozó javaslatok

Javasoljuk, hogy a bemeneti adatokat egy adott feladathoz, a feladatból származó kimeneteket pedig a **/Clusters**-en kívüli mappában tárolja. Ez biztosítja, hogy akkor is, ha a fürtre vonatkozó mappa törölve lett egy tárolóhely visszaigényléséhez, a feladathoz tartozó bemenetek és kimenetek továbbra is elérhetők lesznek későbbi használatra. Ilyen esetben ügyeljen arra, hogy a feladathoz tartozó bemenetek és kimenetek tárolására szolgáló mappa-hierarchia megfelelő szintű hozzáférést biztosítson az egyszerű szolgáltatáshoz.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Egyetlen Storage-fiókot megosztó fürtök korlátozása

Az egy Data Lake Storage-fiókot megosztható fürtök számának korlátja a fürtökön futó munkaterheléstől függ. Ha túl sok fürt vagy nagyon nagy mennyiségű számítási feladat van a Storage-fiókot használó fürtökön, akkor a Storage-fiók bejövő vagy kimenő adatforgalmának szabályozásához vezethet.

## <a name="support-for-default-acls"></a>Default-ACLs támogatása

Névvel ellátott szolgáltatásnév létrehozásakor (ahogy az a fenti táblázatban is látható) azt javasoljuk, hogy **ne** adja hozzá az alapértelmezett ACL-t a nevesített felhasználóhoz. Megnevezett-felhasználói hozzáférés alapértelmezett ACL-ek használatával történő kiosztása 770 engedélyek hozzárendelését eredményezi a tulajdonos-felhasználó, a tulajdonos és mások számára. Habár ez az alapértelmezett érték 770 nem fogad el engedélyeket a tulajdonosi (7) vagy a tulajdonosi csoport (7) számára, minden engedélyt elvesz mások számára (0). Ez egy adott használati esettel kapcsolatos ismert problémát eredményez, amelyet az [ismert problémák és a megkerülő megoldások](#known-issues-and-workarounds) szakaszban részletesen ismertetünk.

## <a name="known-issues-and-workarounds"></a>Ismert problémák és megkerülő megoldások

Ez a szakasz felsorolja a HDInsight és a Data Lake Storage használatának ismert problémáit, valamint azok megkerülő megoldásait.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Nyilvánosan látható honosított Apache Hadoop fonal-erőforrások

Új Azure Data Lake Storage-fiók létrehozásakor a rendszer automatikusan kiépíti a gyökérkönyvtárat a hozzáférés-ACL engedélyekkel a 770 értékre állítva. A gyökérmappa tulajdonos felhasználója arra a felhasználóra van beállítva, aki létrehozta a fiókot (a Data Lake Storage rendszergazda), és a tulajdonos csoport a fiókot létrehozó felhasználó elsődleges csoportjára van beállítva. A "mások" számára nincs hozzáférés megadva.

Ezek a beállítások a 247-as [fonalban](https://hwxmonarch.atlassian.net/browse/YARN-247)rögzített HDInsight-használati esetekre is érvényesek. A feladat-beadványok a következőhöz hasonló hibaüzenettel meghiúsulnak:

```output
Resource XXXX is not publicly accessible and as such cannot be part of the public cache.
```

Ahogy azt a korábban a JIRA csatolta, a nyilvános erőforrások honosítása közben, a Localizer ellenőrzi, hogy az összes kért erőforrás valóban nyilvános-e, ha ellenőrzi a távoli fájlrendszerre vonatkozó engedélyeiket. Minden olyan LocalResource, amely nem fér el ehhez a feltételhez, elutasítja a honosítást. Az engedélyek keresése, a "mások" számára olvasási hozzáférés a fájlhoz. Ez a forgatókönyv a HDInsight-fürtök Azure Data Lake-on való üzemeltetése során nem működik, mert a Azure Data Lake megtagadja a "mások" hozzáférését a gyökérmappa szintjén.

#### <a name="workaround"></a>Áthidaló megoldás

Állítsa be az olvasási végrehajtás engedélyeit **mások** számára a hierarchián keresztül, például a következő **/** táblázatban látható módon:, **/Clusters** és **/Clusters/Finance** .

## <a name="see-also"></a>Lásd még

- [Rövid útmutató: Fürtök beállítása a HDInsightban](./hdinsight-hadoop-provision-linux-clusters.md)
- [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](hdinsight-hadoop-use-data-lake-storage-gen2.md)