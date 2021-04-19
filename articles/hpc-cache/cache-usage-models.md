---
title: Azure HPC Cache használati modellek
description: Ismerteti a különböző gyorsítótár-használati modelleket, és hogy hogyan választhat közülük a csak olvasható vagy írási/írási gyorsítótárazás beállítására, valamint az egyéb gyorsítótárazási beállítások szabályozására
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/08/2021
ms.author: v-erkel
ms.openlocfilehash: 7e1b11fd15cca9b11fc627222318f08d31743336
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107719186"
---
# <a name="understand-cache-usage-models"></a>A gyorsítótár-használati modellek

A gyorsítótár használati modelljeivel testre szabhatja, hogy a Azure HPC Cache hogyan tárolja a fájlokat a munkafolyamat felgyorsítása érdekében.

## <a name="basic-file-caching-concepts"></a>A fájl-gyorsítótárazás alapfogalmai

A fájlok gyorsítótárazása Azure HPC Cache felgyorsítja az ügyfélkéréseket. A következő alapvető eljárásokat használja:

* **Olvasási gyorsítótárazás** – Azure HPC Cache másolatot tart az ügyfelek által a tárolórendszertől lekért fájlokról. Amikor egy ügyfél legközelebb ugyanazt a fájlt kéri, a HPC Cache meg tudja adni a verziót a gyorsítótárban ahelyett, hogy újra lekérné a háttértárrendszerből.

* **Írási gyorsítótárazás** – A Azure HPC Cache az ügyfélszámítógépről küldött módosított fájlok másolatát is tárolhatja. Ha több ügyfél is módosítja ugyanazt a fájlt egy rövid idő alatt, a gyorsítótár képes összegyűjteni a gyorsítótárban történt összes módosítást ahelyett, hogy minden módosítást külön kellene írnia a háttértárrendszerbe.

  Meghatározott idő és módosítás nélkül a gyorsítótár áthelyezi a fájlt a hosszú távú tárolórendszerbe.

  Ha az írási gyorsítótárazás le van tiltva, a gyorsítótár nem tárolja a módosított fájlt, és azonnal a háttértárrendszerbe írja.

* **Késleltetett** visszaírás – Bekapcsolt írási gyorsítótár esetén a késleltetett visszaírás az az idő, amíg a gyorsítótár további fájlváltozásokat vár, mielőtt átmásolja a fájlt a háttértárrendszerbe.

* **Háttérbeli ellenőrzés** – A háttérbeli ellenőrzési beállítás határozza meg, hogy a gyorsítótár milyen gyakran hasonlítja össze egy fájl helyi példányát a háttértárrendszer távoli verziójával. Ha a háttérbeli másolat újabb, mint a gyorsítótárazott másolat, a gyorsítótár lekéri a távoli másolatot, és tárolja a későbbi kérések számára.

  A háttér-ellenőrzési beállítás azt mutatja meg, hogy a *gyorsítótár* mikor hasonlítja össze automatikusan a fájljait a távoli tárolóban lévő forrásfájlokkal. Azonban kényszerítheti a Azure HPC Cache, hogy fájlokat hasonlítson össze egy readdirplus kérést tartalmazó könyvtárművelet elvégzésével. A Readdirplus egy szabványos NFS API (más néven bővített olvasás), amely visszaadja a könyvtár metaadatait, ami miatt a gyorsítótár összehasonlítja és frissíti a fájlokat.

A Azure HPC Cache beépített használati modellek különböző értékekkel vannak felépítve ezekhez a beállításokhoz, így kiválaszthatja a helyzetnek leginkább megfelelő kombinációt.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>A munkafolyamatnak megfelelő használati modell kiválasztása

Minden egyes használt NFS-protokoll tárolási célhoz ki kell választania egy használati modellt. Az Azure Blob Storage-célok beépített használati modellel vannak, amely nem szabható testre.

HPC Cache modellekkel kiválaszthatja, hogyan egyensúlyba hozza a gyors reagálást az elavult adatok kieső kockázatával. Ha optimalizálni szeretné a fájlok olvasásának sebességét, nem biztos, hogy a rendszer ellenőrzi a gyorsítótárban lévő fájlokat a háttérfájlok között. Ha azonban meg szeretne győződni arról, hogy a fájlok mindig naprakészek a távoli tárolóval, válasszon olyan modellt, amely gyakran ellenőrzi a fájlokat.

A használati modell lehetőségei a következőek:

* **Olvasás nagy, ritkán** használt írások – Ezt a beállítást akkor használja, ha szeretné felgyorsítni a statikus vagy ritkán módosított fájlok olvasási hozzáférését.

  Ez a beállítás gyorsítótárazza az ügyfelek olvasását, de nem gyorsítótárazza az írásokat. Az írásokat azonnal továbbítja a háttértárba.
  
  A rendszer nem hasonlítja össze automatikusan a gyorsítótárban tárolt fájlokat az NFS-tárolóköteten lévő fájlokkal. (Olvassa el a fenti háttér-ellenőrzés leírását, hogy megtudja, hogyan hasonlíthatja össze őket manuálisan.)

  Ne használja ezt a beállítást, ha fennáll annak a kockázata, hogy egy fájl közvetlenül a tárolórendszeren, anélkül módosítható, hogy először a gyorsítótárba kellene írnia. Ha ez történik, a fájl gyorsítótárazott verziója nem lesz szinkronizálva a háttérfájllal.

* **15%-nál nagyobb írási arány** – Ez a beállítás mind az olvasási, mind az írási teljesítményt felgyorsítja. Ha ezt a beállítást használja, minden ügyfélnek hozzá kell férnie a fájlokhoz a Azure HPC Cache a háttértár közvetlen csatlakoztatása helyett. A gyorsítótárazott fájlok legutóbbi módosításai még nem lesznek átmásolva a háttérbe.

  Ebben a használati modellben a rendszer csak nyolc óránként ellenőrzi a gyorsítótárban lévő fájlokat a háttértárban lévő fájlokon. A rendszer feltételezi, hogy a fájl gyorsítótárazott verziója aktuálisabb. Miután 20 percig a gyorsítótárban volt, a rendszer egy módosított fájlt ír a háttértárrendszerbe<!-- an hour --> további módosítások nélkül.

* **Az ügyfelek** az NFS-célhelyre írnak, megkerülve a gyorsítótárat – Akkor válassza ezt a beállítást, ha a munkafolyamatban lévő bármelyik ügyfél közvetlenül a tárolórendszerbe ír adatokat anélkül, hogy először a gyorsítótárba írna, vagy ha optimalizálni szeretné az adatkonzisztenciát. Az ügyfelek által kért fájlok gyorsítótárazva vannak (olvasások), de az ügyféltől származó fájlok (írások) módosításai nem. Ezeket a rendszer közvetlenül a háttértárrendszernek továbbkűszi.

  Ezzel a használati modellel a gyorsítótárban lévő fájlokat gyakran , 30 másodpercenként ellenőrzi a rendszer a frissítéseket a háttérverziókban. Ez az ellenőrzés lehetővé teszi a fájlok gyorsítótáron kívüli, adatkonzisztenciát fenntartó módosíthatóságát.

  > [!TIP]
  > Ez az első három alapszintű használati modell használható a munkafolyamatok Azure HPC Cache kezelésére. A következő lehetőségek kevésbé gyakori forgatókönyvekhez használhatók.

* 15%-nál nagyobb írási műveletek, a háttérkiszolgáló **30** másodpercenkénti változásainak és **15%-nál** nagyobb írási műveleteknek a ellenőrzése, a háttérkiszolgáló 60 másodpercenkénti változásainak ellenőrzése – Ezek a beállítások olyan munkafolyamatokhoz vannak kialakítva, amelyekben az olvasást és az írást is fel szeretné gyorsítsa, de van esély arra, hogy egy másik felhasználó közvetlenül a háttértárrendszerbe ír. Ha például több ügyfélkészlet dolgozik ugyanazon a különböző helyekről származó fájlokon, akkor ezek a használati modellek egyensúlyba hoznak a gyors fájlelérés és a forrásból származó elavult tartalom alacsony tűréshatára között.

* 15%-nál nagyobb írási **arány, 30** másodpercenként visszaírás a kiszolgálóra – Ez a beállítás arra a helyzetre lett tervezve, amikor több ügyfél aktívan módosítja ugyanazon fájlokat, vagy ha egyes ügyfelek közvetlenül hozzáférnek a háttértárhoz a gyorsítótár csatlakoztatása helyett.

  A gyakori háttérbeli írások hatással vannak a gyorsítótár teljesítményére, ezért érdemes megfontolni a **15%-nál** nagyobb írási használati modell használatát, ha alacsony a fájlütközés kockázata – például ha tudja, hogy a különböző ügyfelek ugyanazon fájlkészlet különböző területein dolgoznak.

* Nagy olvasási terhelés, a háttérkiszolgáló **3** óránkénti ellenőrzése – Ez a beállítás az ügyféloldali gyors olvasásokat rangsorol, de rendszeresen frissíti a háttértárrendszer gyorsítótárazott fájljait, ellentétben a nagy olvasási terhelésű, ritkán írási műveletekkel kapcsolatos használati modellel. 

Ez a táblázat a használati modell eltéréseit foglalja össze:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Ha kérdése van a Azure HPC Cache-munkafolyamathoz legmegfelelőbb használati modellel kapcsolatban, kérje az Azure-képviselőjét, vagy nyisson támogatási kérést.

## <a name="know-when-to-remount-clients-for-nlm"></a>Tudja meg, mikor kell újra leválasztani az ügyfeleket az NLM-hez

Bizonyos esetekben előfordulhat, hogy újra kell leválasztani az ügyfeleket, ha módosítja a tárolási cél használati modelljét. Erre azért van szükség, mert a különböző használati modellek hogyan kezelik a Hálózatzárolás-kezelő (NLM) kéréseket.

A HPC Cache az ügyfelek és a háttértárrendszer között van. A gyorsítótár általában átadja az NLM-kéréseket a háttértárrendszernek, de bizonyos esetekben maga a gyorsítótár nyugtázza az NLM-kérést, és visszaad egy értéket az ügyfélnek. Ebben Azure HPC Cache esetben ez csak akkor fordul elő, ha a nagy olvasási **terhelésű,** ritkán használt írási modellt használja (vagy egy standard blobtároló-célhelytel, amely nem konfigurálható használati modellekkel).

A fájlütközés kis kockázattal jár, ha módosítja a nagy olvasási **terhelésű,** ritkán írási és más használati modell közötti váltást. Az aktuális NLM-állapotot nem lehet áthelyezni a gyorsítótárból a tárolórendszerbe, vagy fordítva. Így az ügyfél zárolási állapota pontatlan.

Az új zároláskezelővel szerelje le újra az ügyfeleket, és győződjön meg arról, hogy pontos NLM-állapotban vannak.

Ha az ügyfelek NLM-kérelmet küldenek, amikor a használati modell vagy a háttértár nem támogatja azt, hibaüzenetet kapnak.

### <a name="disable-nlm-at-client-mount-time"></a>Az NLM letiltása az ügyfél csatlakoztatási idejekor

Nem mindig könnyű tudni, hogy az ügyfélrendszerek küldenek-e NLM-kéréseket.

Letilthatja az NLM-et, ha az ügyfelek csatlakoztatják a fürtöt a parancs ``-o nolock`` ``mount`` kapcsolóját használva.

A beállítás pontos viselkedése az ügyfél operációs rendszerétől függ, ezért ellenőrizze az ügyfél operációs rendszerének csatlakoztatási ``nolock`` dokumentációját (man 5 nfs). A legtöbb esetben helyben áthelyezi a zárolást az ügyfélre. Legyen körültekintő, ha az alkalmazás több ügyfélen zárolja a fájlokat.

> [!NOTE]
> Az ADLS-NFS nem támogatja az NLM-et. A fenti csatlakoztatási beállítással tiltsa le az NLM-et, ha ADLS-NFS-tárolót használ.

## <a name="next-steps"></a>Következő lépések

* [Céltárolók hozzáadása](hpc-cache-add-storage.md) a Azure HPC Cache
