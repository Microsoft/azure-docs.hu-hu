---
title: Data Lake Storage és WANdisco LiveData platform for Azure (előzetes verzió)
description: A WANdisco LiveData platform for Azure használatával áttelepítheti a helyszíni Hadoop-adataikat a Azure Data Lake Storage Gen2ba.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/17/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: a0d02530ba2b8758b467b77ff639437675e4cc81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99508929"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>A WANdisco LiveData platform for Azure (előzetes verzió) követelményeinek kielégítése igényes áttelepítési követelményekkel

A [WANdisco LiveData platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/)használatával áttelepítheti a helyszíni Hadoop-adataikat a Azure Data Lake Storage Gen2ba. Ez a platform szükségtelenné teszi az alkalmazások leállását, az adatvesztés esélyét, valamint az adatkonzisztencia biztosítását, miközben a műveletek továbbra is a helyszínen maradnak.  

> [!NOTE]
> A WANdisco LiveData platform for Azure nyilvános előzetes verzióban érhető el. A regionális elérhetőséget lásd: [támogatott régiók](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions).

A platform két szolgáltatásból áll: a [LiveData-Migrálás az Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) -ba a helyszíni környezetekről az Azure Storage-ba, illetve az Azure-ba irányuló [LiveData-síkon](https://www.wandisco.com/products/livedata-plane-for-azure) , amely biztosítja, hogy a rendszer minden módosított adatot és adatbevitelt következetesen replikáljon. 

> [!div class="mx-imgBorder"]
> ![Az élő adatplatform áttekintése – illusztráció](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Mindkét szolgáltatást a Azure Portal és az Azure CLI használatával is kezelheti, és mindkettőt követheti a többi Azure-szolgáltatáshoz hasonlóan mért, utólagos elszámolású számlázási modellként is. Az Azure-beli LiveData platform ugyanazon a havi Azure-számlán jelenik meg, és egységes és kényelmes módot biztosít a használat nyomon követésére és figyelésére.

Az adatok _Offline_ áttelepítésével ellentétben, ha [a statikus adatokat Azure Data boxba másolja](./data-lake-storage-migrate-on-premises-hdfs-cluster.md), vagy Hadoop-eszközöket (például [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html)) használ, az _online_ áttelepítés során teljes mértékben megtarthatja üzleti rendszereit az Azure-beli WANdisco-LiveData. Az Azure-ba való áttéréskor is megtarthatja big data környezetét.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Az Azure-hoz készült WANdisco LiveData platform főbb jellemzői

Az Azure-hoz készült [WANdisco LiveData-platform](https://docs.wandisco.com/live-data-platform/docs/landing/) egy egyedi, széles körben elérhető, nagy kiterjedésű hálózatot használó konszenzust biztosít az adatkonzisztencia eléréséhez, valamint az adatreplikáció méretezését, miközben az alkalmazások továbbra is módosíthatják az adatreplikációt. <br><br>

>[!VIDEO https://www.youtube.com/embed/KRrmcYPxEho] 

A platform főbb funkciói közé tartoznak a következők:

- **Adatkonzisztencia**: megoldhatók a nagy adatmennyiségek környezetek közötti áttelepítésének kihívásai, és a tárolási rendszerek átviteli sebességének átállása konzisztens, még akkor is, ha azok folyamatos változás alatt állnak. A WANdisco egyedi, széles körben elérhető, az Azure-ban közvetlenül az Azure-ban az adatkonzisztencia eléréséhez és az adatváltozások konzisztencia-garanciákkal való átépítéséhez.

- **Műveletek fenntartása**: mivel az alkalmazások továbbra is létrehozhatják, módosíthatják, olvashatják és törölhetik az adatok áttelepítését az áttelepítés során, nem kell megszakítani az üzleti műveleteket, vagy egy kimaradási időszakot kell bevezetni az Azure-ba való Big Data Továbbra is működtetheti az alkalmazásokat, az elemzési infrastruktúrát, a feladatok betöltését és más feldolgozást.

- Ellenőrzések **eredményeinek ellenőrzése**: teljes körű ellenőrzés, amely az Azure-ba való Migrálás után hatékonyan használható az adatokhoz, hogy éles alkalmazás-munkaterheléseket futtasson rajtuk. Csak egy LiveData szolgáltatás biztosítja ezt az Adateltérés kockázatának bemutatása nélkül, az adatkonzisztencia fenntartásával függetlenül attól, hogy az áttelepítés forrása vagy célja megváltozik-e. Az alkalmazások viselkedésének tesztelése és érvényesítése a folyamatok és rendszerek veszélyeztetése nélkül.

- A **bonyolultság csökkentése**: nincs szükség az adatok másolására az adatoknak az automatizálással történő áttelepítésével ütemezett feladatok létrehozásához és kezeléséhez. Az áttelepítési folyamat kezeléséhez és figyeléséhez használja az Azure-nal való szoros integrációt, beleértve a szelektív adatreplikálást, a kaptár metaadatait, az adatbiztonságot és a titkosságot.

- **Hatékonyság**: a nagy átviteli sebesség és a teljesítmény fenntartása, valamint az Big Data kötetek méretezése egyszerűen. A sávszélesség-használat szabályozásával gondoskodhat arról, hogy az áttelepítési célokat az egyéb rendszerműveletek befolyásolása nélkül is kielégítse.

## <a name="migrate-big-data-faster-without-risk"></a>big data gyorsabb Migrálás kockázat nélkül

Az Azure-hoz készült WANdisco LiveData platform első szolgáltatása az Azure-beli [LiveData-áttelepítési](https://www.wandisco.com/products/livedata-migrator-for-azure)szolgáltatás. megoldás a helyszíni környezetekről az Azure Storage-ba történő aktív használatú adatok áttelepítéséhez. Az Azure-hoz készült LiveData-Migrálás teljes mértékben a Azure Portal vagy az Azure parancssori felületről lett kiépítve, és a helyszíni Hadoop-fürt mellett a konfiguráció módosítása, az alkalmazás módosítása vagy a szolgáltatás újraindítása nélkül is működik, és azonnal megkezdi az adatok áttelepítését.

> [!div class="mx-imgBorder"]
> ![LiveData-Migrálás az Azure architektúrához](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

A Big adatáttelepítés bonyolult és nagy kihívást jelenthet. Az üzleti műveletek megszakítása nélküli petabájt az offline adatmásolási technológiákkal nem lehet elérni. Az Azure-hoz készült LiveData-Áttelepítő egyszerű üzembe helyezést biztosít, és folyamatos adatáttelepítést és replikálást biztosító LiveData szolgáltatást tud létrehozni, miközben az alkalmazások olvasási, írási és módosítási [műveleteket](https://www.wandisco.com/products/livedata-migrator-for-azure) végeznek az áttelepítéskor.

Az áttelepítés elvégzése a következő három lépéssel egyszerű:

1. Telepítse az LiveData-áttelepítési példányt a Azure Portal a helyszíni Hadoop-fürtre. Nincs szükség a fürt módosítására vagy állásidőre, és az alkalmazások továbbra is működőképesek maradnak.

   > [!div class="mx-imgBorder"]
   >![LiveData-áttelepítési példány létrehozása](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. A cél Azure Data Lake Storage Gen2 használatára képes Storage-fiók megadása.

   > [!div class="mx-imgBorder"]
   >![LiveData-áttelepítési cél létrehozása](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. Adja meg az áttelepíteni kívánt adatelérési helyet, például: `/user/hive/warehouse` , és indítsa el az áttelepítést.

   > [!div class="mx-imgBorder"]
   > ![LiveData-áttelepítési áttelepítés létrehozása](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Az áttelepítési folyamat monitorozása a szabványos Azure-eszközökön keresztül, beleértve az Azure CLI-t és a Azure Portalt, és a helyszíni környezetet továbbra is használhatja a teljes egészében. Mielőtt elkezdené, tekintse át ezeket az [előfeltételeket](https://docs.wandisco.com/live-data-platform/docs/prereq/).

## <a name="replicate-data-under-active-change"></a>Adatreplikálás az aktív módosítás alatt

A helyszíni adattavak nagy méretű migrálása az Azure-ba az alkalmazások teszteléséhez és érvényesítéséhez szükséges. Ez az olyan adatváltozások bevezetésének kockázata nélkül válik lehetővé, amelyek több olyan igazságot hoznak létre, amely nem könnyen egyeztethető, és így az Azure-ba való áttérés költségeit minimalizálva is kritikus fontosságú. [Az Azure](https://www.wandisco.com/products/livedata-plane-for-azure) -hoz készült LiveData-sík a WANdisco koordinációs motorjának technológiáját használja, hogy elhárítsa ezeket a problémákat.

> [!div class="mx-imgBorder"]
> ![LiveData-sík az Azure architektúrához](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

A kezdeti áttelepítés után az Azure-ba irányuló LiveData-síkon megtarthatja az adatait a helyszíni Hadoop-fürtök és az Azure Storage között:

1. Kiépítheti a helyszíni és az Azure-beli LiveData-síkot a Azure Portaltől kezdve. Nincs szükség alkalmazás-módosításra.

2. Konfigurálja azokat a replikációs szabályokat, amelyek kiterjednek a megőrizni kívánt adatterületekre, például: `/user/contoso/sales/region/WA` .

3. Olyan alkalmazásokat futtathat, amelyek Hadoop-kompatibilis fájlrendszerként férnek hozzá és módosíthatnak.

Az Azure-hoz készült LiveData-sík a fürt működésének vagy az alkalmazás teljesítményének jelentős terhelése nélkül tartja konzisztensen az adatokat. Módosíthatja vagy betöltheti az adatmennyiséget, miközben az összes módosítás konzisztens módon replikálódik.

## <a name="next-steps"></a>Következő lépések

- Az Azure for Azure-hoz készült [LiveData platform](https://docs.wandisco.com/live-data-platform/docs/landing/) a többi Azure-erőforráshoz hasonlóan használatos, és jelenleg előzetes verzióban érhető el. 

- Ismerje meg az [előfeltételeket](https://docs.wandisco.com/live-data-platform/docs/prereq/), tervezze meg az áttelepítést, és fejezze be a nagy léptékű áttelepítést az Azure-hoz készült LiveData-áttelepítéssel.

- Próbálja ki a LiveData-áttelepítést anélkül, hogy helyszíni Hadoop-fürtöt kellene használnia a [HDFS](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/)-alapú sandbox használatával.

## <a name="see-also"></a>Lásd még

- [LiveData-Migrálás az Azure-ban az Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [LiveData-sík az Azure-ban az Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [LiveData-Migrálás Azure-csomagokhoz és díjszabáshoz](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [Az Azure-csomagok és-díjszabás LiveData síkja](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [LiveData platform az Azure-hoz – gyakran ismételt kérdések](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Ismert problémák az Azure-hoz készült LiveData platformmal](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Az Azure Data Lake Storage Gen2 bemutatása](data-lake-storage-introduction.md)