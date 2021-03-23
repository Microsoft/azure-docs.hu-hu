---
title: Az Azure HPC cache használati modelljei
description: Ismerteti a gyorsítótár-használat különböző modelljeit, valamint azt, hogyan választhat közülük, hogy csak olvasási vagy írási/olvasási gyorsítótárazást és egyéb gyorsítótárazási beállításokat állítsanak be.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 3ad252520ca0cf7acdb3c84ef1da87c8076f3172
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775714"
---
# <a name="understand-cache-usage-models"></a>Gyorsítótár-használati modellek ismertetése

A gyorsítótár-használati modellek segítségével testre szabhatja, hogy az Azure HPC-gyorsítótár hogyan tárolja a fájlokat a munkafolyamat felgyorsításához.

## <a name="basic-file-caching-concepts"></a>Alapszintű fájl gyorsítótárazásával kapcsolatos fogalmak

A fájl gyorsítótárazásával az Azure HPC cache felgyorsítja az ügyfelek kérelmeit. Ezeket az alapszintű gyakorlatokat használja:

* **Olvasási gyorsítótárazás** – az Azure HPC cache tárolja az ügyfelek által a tárolási rendszer által kért fájlok másolatát. Amikor az ügyfél legközelebb ugyanazt a fájlt kéri, a HPC-gyorsítótár megadhatja a verziót a gyorsítótárában ahelyett, hogy újra be kellene olvasnia a háttér-tárolási rendszerből.

* **Írási gyorsítótárazás** – opcionálisan az Azure HPC cache képes tárolni az ügyfélgépekről eljuttatott módosított fájlok másolatát. Ha egy rövid időszakon belül több ügyfél is módosítja ugyanazt a fájlt, akkor a gyorsítótár a gyorsítótárban lévő összes változást összegyűjtheti ahelyett, hogy az egyes módosításokat külön kell írni a háttérrendszer-tároló rendszerbe.

  A módosítást követően megadott idő elteltével a gyorsítótár áthelyezi a fájlt a hosszú távú tárolási rendszerbe.

  Ha az írási gyorsítótárazás le van tiltva, a gyorsítótár nem tárolja a módosított fájlt, és azonnal a háttér-tárolási rendszerbe írja azt.

* **Visszaírási késleltetés** – az írási gyorsítótárazást engedélyező gyorsítótár esetén a visszaírási késleltetés azt az időtartamot írja le, ameddig a gyorsítótár további módosításokat vár a fájlnak a háttérbeli tárolási rendszerbe való másolása előtt.

* **Háttér-** ellenőrzés – a háttér-ellenőrzési beállítás határozza meg, hogy a gyorsítótár milyen gyakran hasonlítja össze a fájl helyi példányát a háttér-tárolási rendszeren található távoli verzióval. Ha a háttérbeli másolat újabb, mint a gyorsítótárazott másolat, a gyorsítótár beolvassa a távoli másolatot, és a későbbi kérelmekhez tárolja azt.

  A háttér-ellenőrzési beállítás azt jeleníti meg, hogy mikor a gyorsítótár *automatikusan* összehasonlítja a fájljait a távoli tárolóban lévő forrásfájlokat. Azonban az Azure HPC cache-gyorsítótárat kényszerítheti a fájlok összehasonlítására egy readdirplus kérelmet tartalmazó címtár-művelet végrehajtásával. A Readdirplus egy szabványos NFS API (más néven kiterjesztett olvasás), amely a címtár metaadatait adja vissza, ami miatt a gyorsítótár összehasonlítja és frissíti a fájlokat.

Az Azure HPC-gyorsítótárba beépített használati modellek eltérő értékekkel rendelkeznek ezeknek a beállításoknak a megadásához, így kiválaszthatja a legjobb kombinációt a helyzethez.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Válassza ki a munkafolyamat megfelelő használati modelljét

Az összes használt NFS-tárolási cél használati modelljét ki kell választania. Az Azure Blob Storage-célok olyan beépített használati modellel rendelkeznek, amely nem szabható testre.

A HPC gyorsítótár-használati modelljei lehetővé teszik a gyors reagálás egyensúlyának kiválasztását az elavult adatok beszerzésének kockázatával. Ha optimalizálni szeretné a fájlok olvasásának sebességét, előfordulhat, hogy nem biztos benne, hogy a gyorsítótárban lévő fájlok be vannak-e jelölve a háttérbeli fájlokban. Ha azonban azt szeretné, hogy a fájlok mindig naprakészek legyenek a távoli tárterülettel, válasszon olyan modellt, amely gyakran ellenőrzi a fájlokat.

A használati modell beállításai:

* **Súlyos, ritka írások olvasása** – ezt a beállítást akkor használja, ha a statikus vagy ritkán módosított fájlok olvasási hozzáférését szeretné felgyorsítani.

  Ez a beállítás gyorsítótárazza az ügyfél olvasási műveleteit, de nem gyorsítótárazza az írásokat. Azonnal továbbítja az írásokat a háttérbeli tárolóba.
  
  A gyorsítótárban tárolt fájlok nincsenek automatikusan összehasonlítva az NFS-tároló kötetén található fájlokkal. (Olvassa el a fenti háttér-ellenőrzés leírását, amelyből megtudhatja, hogyan hasonlíthatja össze őket manuálisan.)

  Ne használja ezt a beállítást, ha fennáll a kockázata annak, hogy egy fájl közvetlenül a tárolási rendszeren módosul, anélkül, hogy először a gyorsítótárba kellene írni. Ha ez történik, a fájl gyorsítótárazott verziója nem lesz szinkronizálva a háttér-fájllal.

* **15%-nál nagyobb írások** – ez a beállítás az olvasási és írási teljesítményt is felgyorsítja. Ha ezt a beállítást használja, az összes ügyfélnek az Azure HPC cache-en keresztül kell hozzáférnie a fájlokhoz ahelyett, hogy közvetlenül a háttér-tárolót kellene csatlakoztatnia. A gyorsítótárazott fájlok olyan legutóbbi módosításokat fognak tartalmazni, amelyek még nem lettek átmásolva a háttérbe.

  Ebben a használati modellben a gyorsítótárban lévő fájlokat a rendszer csak a háttérbeli tároló fájljain, nyolc óránként ellenőrzi. A rendszer azt feltételezi, hogy a fájl gyorsítótárazott verziója nagyobb áramerősséget mutat. A gyorsítótárban lévő módosított fájl a háttér-tárolási rendszerbe kerül, miután a gyorsítótárban 20 percen át lett írva.<!-- an hour --> További módosítások nélkül.

* Az **ügyfelek az NFS-célhelyre írhatnak, és megkerülik a gyorsítótárat** – ezt a beállítást akkor válassza, ha a munkafolyamatban lévő bármelyik ügyfél közvetlenül a tárolási rendszerbe írja az adatait anélkül, hogy először a gyorsítótárba írna, vagy ha az adatkonzisztenciát szeretné optimalizálni. Az ügyfelek által kért fájlok gyorsítótárazva vannak (olvasások), de az ügyfélen (írásokban) lévő fájlok módosításai nincsenek gyorsítótárazva. Ezeket közvetlenül a háttér-tárolási rendszer továbbítja.

  Ezzel a használati modellel a gyorsítótárban lévő fájlokat gyakran ellenőrzik a frissítések háttér-verzióiban – 30 másodpercenként. Ez az ellenőrzés lehetővé teszi, hogy a fájlok a gyorsítótáron kívülre legyenek módosítva az adatkonzisztencia fenntartása mellett.

  > [!TIP]
  > Ezek az első három alapszintű használati modellek az Azure HPC cache-munkafolyamatok többségének kezelésére használhatók. A következő lehetőségek kevésbé gyakori forgatókönyvekre vonatkoznak.

* **15%-nál nagyobb írások, a biztonsági mentést biztosító kiszolgáló ellenőrzése 30 másodpercenként** , **15%-nál nagyobb írási műveletek esetén, a biztonsági mentést kiszolgáló változásának ellenőrzése 60 másodpercenként** – ezek a beállítások olyan munkafolyamatokhoz lettek kialakítva, ahol az olvasások és az írások felgyorsítására van szükség, de van esély arra, hogy egy másik felhasználó közvetlenül a háttérrendszer-tároló Ha például több ügyfél is dolgozik ugyanazokon a fájlokon különböző helyekről, ezek a használati modellek a forrástól származó elavult tartalomhoz való gyors hozzáférés szükségességét is érdemes ellensúlyozni.

* **15%-nál nagyobb írások esetén 30 másodpercenként írja vissza a kiszolgálót** – ez a beállítás olyan forgatókönyvhöz lett tervezve, amelyben több ügyfél is aktívan módosítja ugyanazt a fájlt, vagy ha egyes ügyfelek közvetlenül a gyorsítótár csatlakoztatása helyett a háttérbeli tárolóhoz férnek hozzá.

  A gyakori háttérbeli írások hatással vannak a gyorsítótár teljesítményére, ezért érdemes megfontolni a **15%-nál nagyobb** használati modell használatát, ha a fájlok ütközése alacsony, például ha tudja, hogy a különböző ügyfelek több különböző területen dolgoznak ugyanazon a fájlon.

* **Nagy mennyiségű, a háttérrendszer 3 óránkénti ellenőrzése** – ez a beállítás rangsorolja a gyors olvasási műveleteket az ügyféloldali oldalon, de a gyorsítótárazott fájlokat is rendszeresen frissíti a háttérrendszer-tárolási rendszerből, a **nagy olvasási** sebességtől eltérően.

Ez a táblázat a használati modell eltéréseit foglalja össze:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Ha kérdése van az Azure HPC gyorsítótár-munkafolyamatának legjobb használati modelljével kapcsolatban, forduljon az Azure-képviselőjéhez, vagy nyisson meg egy támogatási kérést a segítségért.

## <a name="next-steps"></a>Következő lépések

* [Tárolási célok hozzáadása](hpc-cache-add-storage.md) az Azure HPC-gyorsítótárhoz
