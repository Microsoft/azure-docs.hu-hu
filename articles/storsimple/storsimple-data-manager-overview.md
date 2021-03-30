---
title: Microsoft Azure StorSimple Data Manager áttekintése | Microsoft Docs
description: Tudjon meg többet a StorSimple Data Manager megoldásról, és arról, hogyan használhatja ezt a szolgáltatást a StorSimple-és egyéb Azure-szolgáltatásokat használó alkalmazások írásához.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: alkohli
ms.openlocfilehash: d683f49cadb384ef59d3bae819156733691813cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88183496"
---
# <a name="storsimple-data-manager-solution-overview"></a>StorSimple Data Manager megoldás áttekintése

## <a name="overview"></a>Áttekintés

Microsoft Azure StorSimple a Felhőbeli tárolót használja a helyszíni megoldás kiterjesztéseként, és automatikusan felhasználja a helyszíni tárterület és a felhő közötti adatmennyiséget. Az adattárolást a felhőben egy deduplikált és tömörített formátumban tárolja a rendszer a maximális hatékonyság és a költségek csökkentése érdekében. Mivel az StorSimple formátumban tárolja az adattárolást, a más Felhőbeli alkalmazások nem könnyen használhatók, amelyeket érdemes használni.

A StorSimple Data Manager segítségével zökkenőmentesen érheti el és használhatja a felhőben tárolt StorSimple-formátumot. Ez a StorSimple formátum natív blobokra és fájlokra való átalakításával történik, amelyet más szolgáltatásokkal, például az Azure Media Services, az Azure Hdinsight és a Azure Machine Learningekkel használhat.

Ez a cikk áttekintést nyújt a StorSimple Data Manager megoldásról. Azt is ismerteti, hogyan használhatja ezt a szolgáltatást a StorSimple-és más Azure-szolgáltatásokat használó alkalmazások írására a felhőben.

## <a name="how-it-works"></a>Hogyan működik?

A StorSimple Data Manager szolgáltatás a felhőben lévő StorSimple-adatok azonosítására szolgál egy StorSimple 8000 sorozatú helyszíni eszközről. A felhőben lévő StorSimple-adathalmazok deduplikált, tömörített StorSimple formátumúak. A Data Manager szolgáltatás API-kat biztosít a StorSimple formátumú adatok kinyeréséhez és más formátumokba, például az Azure-blobokhoz és a Azure Files alakításához. Ezt az átalakított adatmennyiséget az Azure HDInsight és az Azure Media Services azonnal felhasználja. Az adatátalakítás így lehetővé teszi, hogy ezek a szolgáltatások a StorSimple 8000 Series helyszíni eszközről származó átalakított StorSimple-adatokon működjenek. Ezt a folyamatot az alábbi ábra szemlélteti.

![Magas szintű diagram](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Data Manager használati esetek

A Data Manager a Azure Functions, a Azure Automation és a Azure Data Factory használatával az adatain futó munkafolyamatok is StorSimple. Előfordulhat, hogy fel szeretné dolgozni a StorSimple tárolt médiatartalmakat Azure Media Services, vagy futtasson egy Machine Learning algoritmust az adott adathalmazon, vagy egy Hadoop-fürtöt, hogy elemezze a StorSimple tárolt adatokat. Az Azure-ban az StorSimple-on keresztül elérhető szolgáltatások hatalmas választékával feloldható az adatmennyiség.


## <a name="region-availability"></a>Régiónkénti elérhetőség

A StorSimple Data Manager a következő 7 régióban érhető el:

 - Délkelet-Ázsia
 - USA keleti régiója
 - USA nyugati régiója
 - USA 2. nyugati régiója
 - USA nyugati középső régiója
 - Észak-Európa
 - Nyugat-Európa

A StorSimple Data Manager azonban a következő régiókban található adatátalakításra használható. 

![Az adatterületek elérhetők](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Ez a készlet nagyobb, mert az erőforrás-telepítés a fenti régiókban a következő régiókban képes az átalakítási folyamat létrehozására. Tehát mindaddig, amíg az adatai a 19 régió valamelyikében találhatók, az adatait a szolgáltatás használatával alakíthatja át.


## <a name="choosing-a-region"></a>Régió kiválasztása

A következő megoldást javasoljuk:
 - A forrásként szolgáló Storage-fiók (amely a StorSimple-eszközhöz van társítva) és a célként megadott Storage-fiók (ahol a natív formátumú adatforrások) ugyanabban az Azure-régióban található.
 - A Data Manager és a feladatdefiníció a StorSimple Storage-fiókot tartalmazó régióban jelenik meg. Ha ez nem lehetséges, hozza létre a Data Manager a legközelebbi Azure-régióban, majd hozza létre a feladattípust ugyanabban a régióban, mint a StorSimple Storage-fiók. 

    Ha a StorSimple Storage-fiókja nem a feladattípus-létrehozást támogató 26 régióban található, javasoljuk, hogy ne futtasson StorSimple Data Manager, mert a késések és a kimenő forgalom várható költségei nem láthatók.
    
A Microsoft igyekszik biztosítani, hogy az Azure-szolgáltatások mindig elérhetők legyenek minden régióban. Azonban előfordulhat, hogy a nem tervezett szolgáltatások rövid időszakokra vonatkozhatnak egy adott régióban. Ilyen esetekben létrehozhat egy Data Manager és egy feladatdefiníció olyan régióban, amelyet a leállás nem érint, és futtathatja az átalakítási feladatot. Előfordulhat, hogy egy ilyen helyzetben további késés tapasztalható, de ez lehet a helyreállítási stratégia a regionális leállás ritka eseménye esetén is.

## <a name="security-considerations"></a>Biztonsági szempontok

A StorSimple Data Manager szüksége van a szolgáltatási adatok titkosítási kulcsára, hogy StorSimple formátumról natív formátumra alakítsa át. A szolgáltatás adattitkosítási kulcsa akkor jön létre, amikor az első eszköz regisztrálja magát a StorSimple szolgáltatásban. Erről a kulcsról a [StorSimple biztonság](storsimple-8000-security.md)című témakörben olvashat bővebben.

A bemenetként megadott szolgáltatási adatok titkosítási kulcsát egy Data Manager létrehozásakor létrehozott kulcstartó tárolja. A tároló ugyanabban az Azure-régióban található, mint a StorSimple Data Manager. Ezt a kulcsot a Data Manager szolgáltatás törlésekor törli a rendszer.

Ezt a kulcsot a számítási erőforrások használják a transzformáció végrehajtásához. Ezek a számítási erőforrások abban az Azure-régióban találhatók, mint a feladatdefiníció. Ez a régió lehet, vagy nem egyezhet meg azzal a régióval, ahol a Data Managert hozza létre.

Ha a Data Manager régiója eltér a feladatdefiníció régiójától, fontos tisztában lennie azzal, hogy milyen adatok/metaadatok találhatók ezekben a régiókban. A következő ábra azt szemlélteti, hogy a különböző régiók a Data Manager és a feladatdefiníció esetében milyen hatással vannak.

![Szolgáltatás-és feladatdefiníció különböző régiókban](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Személyes adatok kezelése

A StorSimple Data Manager nem gyűjt és nem jelenít meg semmilyen személyes adatot. További információkért lásd a Microsoft szabályzatát a [biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Ismert korlátozások

A szolgáltatás jelenleg a következő korlátozásokkal rendelkezik:
- A StorSimple Data Manager jelenleg nem működik olyan kötetekkel, amelyek BitLocker-titkosítással vannak titkosítva. A feladatok meghibásodása akkor jelenik meg, ha a szolgáltatást titkosított meghajtóval próbálja futtatni.
- Az átalakított adatokban nem maradnak meg a fájlok (ACL-eket is beleértve) metaadatainak.
- Ez a szolgáltatás csak NTFS-kötetekkel működik.
- A fájl elérési útjának hosszának 256 karakternél rövidebbnek kell lennie, mert a feladat sikertelen lesz.

## <a name="next-steps"></a>Következő lépések

[Az adatátalakításhoz használja StorSimple Data Manager felhasználói felületét](storsimple-data-manager-ui.md).
