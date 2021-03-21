---
title: A Geo-redundancia használata a magasan elérhető alkalmazások kialakításához
titleSuffix: Azure Storage
description: Ismerje meg, hogyan használható a földrajzilag redundáns tárolás egy olyan, magasan elérhető alkalmazás kialakításához, amely elég rugalmas az kimaradások kezeléséhez.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a07acedadfaf3d5158ba8e494d4527301655425
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102035101"
---
# <a name="use-geo-redundancy-to-design-highly-available-applications"></a>A Geo-redundancia használata a magasan elérhető alkalmazások kialakításához

A felhőalapú infrastruktúrák (például az Azure Storage) közös funkciója, hogy egy magasan elérhető és tartós platformot biztosítanak az adattároláshoz és az alkalmazások üzemeltetéséhez. A felhőalapú alkalmazások fejlesztőinek alaposan meg kell fontolniuk, hogyan használhatják ezt a platformot, hogy maximalizálják ezeket az előnyöket a felhasználók számára. Az Azure Storage a földrajzilag redundáns tárolással biztosítja a magas rendelkezésre állást, akár regionális kimaradás esetén is. A Geo-redundáns replikációhoz konfigurált Storage-fiókok szinkron módon replikálódnak az elsődleges régióba, majd aszinkron módon replikálódnak egy olyan másodlagos régióra, amely több száz kilométerre van.

Az Azure Storage két lehetőséget kínál a földrajzilag redundáns replikációhoz. Az egyetlen különbség a két lehetőség között, hogy az elsődleges régióban hogyan replikálódnak az adathalmazok:

* [Geo-Zone-redundáns tárolás (GZRS)](storage-redundancy.md): Az adatreplikáció szinkron módon történik az elsődleges régió három Azure-beli rendelkezésre állási zónájában, a *zóna REDUNDÁNS tárolás (ZRS)* használatával, majd aszinkron módon replikálva a másodlagos régióba. Ha olvasási hozzáférést szeretne a másodlagos régióban lévő adathozzáféréshez, engedélyezze az olvasási hozzáférésű geo-Zone-redundáns tárolást (RA-GZRS).

    A Microsoft a GZRS/RA-GZRS használatát javasolja olyan forgatókönyvek esetében, amelyek maximális rendelkezésre állást és tartósságot igényelnek.

* [Geo-redundáns tárolás (GRS)](storage-redundancy.md): a rendszer a *helyileg REDUNDÁNS tárolás (LRS)* használatával háromszor replikálja az adatfájlokat az elsődleges régióban, majd aszinkron módon replikálja a másodlagos régióba. A másodlagos régióban lévő adatolvasási hozzáférés engedélyezéséhez engedélyezze az olvasási hozzáférésű geo-redundáns tárolást (RA-GRS).

Ez a cikk bemutatja, hogyan tervezheti meg az alkalmazást az elsődleges régióban leállások kezeléséhez. Ha az elsődleges régió elérhetetlenné válik, az alkalmazás alkalmazkodik a másodlagos régióra irányuló olvasási műveletek végrehajtásához. Mielőtt elkezdené, győződjön meg róla, hogy a Storage-fiókja RA-GRS vagy RA-GZRS van konfigurálva.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Az alkalmazás kialakításával kapcsolatos szempontok a másodlagosról való olvasáskor

Ennek a cikknek a célja, hogy bemutassa, hogyan tervezhet meg egy olyan alkalmazást, amely továbbra is működni fog (bár csak korlátozott kapacitásban), még akkor is, ha az elsődleges adatközpontban súlyos katasztrófa következik be. Úgy is megtervezheti az alkalmazást, hogy az átmeneti vagy hosszan futó problémákat a másodlagos régióból olvasson, ha probléma merül fel az elsődleges régióból való olvasással. Ha az elsődleges régió újra elérhetővé válik, az alkalmazás visszatérhet az elsődleges régióból való olvasásra.

Vegye figyelembe ezeket a kulcsfontosságú pontokat az alkalmazás tervezésekor az RA-GRS vagy RA-GZRS:

* Az Azure Storage egy másodlagos régió elsődleges régiójában tárolt adatai írásvédett másolatát tárolja. A fentiekben leírtaknak megfelelően a Storage szolgáltatás meghatározza a másodlagos régió helyét.

* A csak olvasható másolat [végül konzisztens](https://en.wikipedia.org/wiki/Eventual_consistency) az elsődleges régióban lévő értékekkel.

* A Blobok, táblák és várólisták esetében lekérdezheti a másodlagos régiót az *utolsó szinkronizálási idő* értékhez, amely megadja, hogy mikor történt az utolsó replikáció az elsődlegesről a másodlagos régióra. (Azure Files nem támogatott, mert jelenleg nem rendelkezik az RA-GRS redundanciával.)

* A Storage ügyféloldali kódtár használatával az elsődleges vagy a másodlagos régióban is olvashatók és írhatók az adattípusok. Az olvasási kérelmeket automatikusan átirányíthatja a másodlagos régióba, ha az elsődleges régió olvasási kérelme időtúllépést tapasztal.

* Ha az elsődleges régió elérhetetlenné válik, kezdeményezheti a fiók feladatátvételét. Ha átadja a feladatátvételt a másodlagos régiónak, az elsődleges régióra mutató DNS-bejegyzések a másodlagos régióra mutatnak. A feladatátvétel befejezése után a rendszer visszaállítja az írási hozzáférést a GRS és az RA-GRS fiókok számára. További információkért lásd a vész [-helyreállítási és a Storage-fiók feladatátvételét](storage-disaster-recovery-guidance.md)ismertető témakört.

### <a name="using-eventually-consistent-data"></a>Végül konzisztens adatszolgáltatások használata

A javasolt megoldás azt feltételezi, hogy elfogadható, hogy a hívó alkalmazás potenciálisan elavult adatbevitelt ad vissza. Mivel a másodlagos régióban lévő adategységek végül konzisztensek, lehetséges, hogy az elsődleges régió elérhetetlenné válhat, mielőtt a másodlagos régió frissítése befejezte a replikálást.

Tegyük fel például, hogy az ügyfél sikeresen elküld egy frissítést, de az elsődleges régió meghibásodik a frissítésnek a másodlagos régióba való propagálása előtt. Amikor az ügyfél az adatok visszaolvasását kéri, a frissített adatok helyett a másodlagos régióból kapják meg az elavult adatok listáját. Az alkalmazás tervezésekor el kell döntenie, hogy ez elfogadható-e, és ha igen, hogyan fog üzenetet adni az ügyfélnek. 

A cikk későbbi részében bemutatjuk, hogyan ellenőrizhető a másodlagos adatok utolsó szinkronizálási ideje annak megállapítására, hogy a másodlagos érték naprakész-e.

### <a name="handling-services-separately-or-all-together"></a>Szolgáltatások külön vagy együtt történő kezelésére

Habár nem valószínű, lehetséges, hogy az egyik szolgáltatás elérhetetlenné válik, amíg a többi szolgáltatás továbbra is teljesen működőképes. Az egyes szolgáltatásokhoz tartozó újrapróbálkozásokat és írásvédett üzemmódot külön kezelheti (Blobok, várólisták, táblák), vagy az újrapróbálkozásokat általános módon kezelheti az összes tárolási szolgáltatáshoz.

Ha például várólistákat és blobokat használ az alkalmazásban, dönthet úgy, hogy külön kódot helyez el az újrapróbálkozást lehetővé tevő hibák kezelésére. Ezután, ha újra próbálkozik a blob szolgáltatással, de a várólista-szolgáltatás továbbra is működik, a rendszer csak az alkalmazásnak a blobokat kezelő részét érinti. Ha úgy dönt, hogy az összes tárolási szolgáltatást általános próbálkozással kezeli, és a blob szolgáltatás hívása újrapróbálkozást lehetővé tevő hibát ad vissza, akkor a blob Service-hez és a várólista-szolgáltatáshoz intézett kérések is hatással lesznek.

Végső soron ez az alkalmazás összetettségétől függ. Dönthet úgy, hogy nem kezeli a hibákat a szolgáltatással, hanem az összes tárolási szolgáltatás olvasási kérelmeit átirányítja a másodlagos régióba, és az alkalmazást írásvédett módban futtatja, amikor az elsődleges régióban található tárolási szolgáltatásokkal kapcsolatos problémát észlel.

### <a name="other-considerations"></a>További szempontok

A további szempontokat a cikk további részében tárgyaljuk.

* Olvasási kérelmek újrapróbálkozásai az áramkör-megszakító mintájának használatával

* Végül – konzisztens adatértékek és a legutóbbi szinkronizálás időpontja

* Tesztelés

## <a name="running-your-application-in-read-only-mode"></a>Az alkalmazás futtatása írásvédett módban

Ahhoz, hogy az elsődleges régióban ténylegesen felkészüljön a leállás, képesnek kell lennie a sikertelen olvasási kérelmek és a sikertelen frissítési kérések kezelésére (ebben az esetben a lapkákat, a frissítéseket és a törléseket). Ha az elsődleges régió meghibásodik, az olvasási kérések átirányíthatók a másodlagos régióba. A frissítési kérelmek azonban nem irányíthatók át a másodlagosra, mert a másodlagos írásvédett. Ezért meg kell terveznie, hogy az alkalmazás csak olvasható módban fusson.

Beállíthat például egy olyan jelzőt, amelyet a rendszer az Azure Storage-ba irányuló frissítési kérések elküldése előtt ellenőriz. Ha az egyik frissítési kérelem átkerül, kihagyhatja, és megfelelő választ adhat vissza az ügyfélnek. Előfordulhat, hogy a probléma megoldása érdekében akár le is tilthatja bizonyos szolgáltatásokat, és értesíti a felhasználókat arról, hogy ezek a szolgáltatások átmenetileg nem érhetők el.

Ha úgy dönt, hogy az egyes szolgáltatások hibáit külön kezeli, akkor azt is kezelni kell, hogy az alkalmazás csak olvasható módban fusson a szolgáltatással. Előfordulhat például, hogy az egyes szolgáltatásokhoz engedélyezhető és letiltható csak olvasható jelzők találhatók. Ezt követően a jelölőt a kód megfelelő helyein kezelheti.

Az alkalmazás csak olvasható módban való futtatásának lehetősége további előnyt jelent – lehetővé teszi a korlátozott funkcionalitás biztosítását az alkalmazások jelentős frissítése során. Az alkalmazás csak olvasható módban futtatható, és a másodlagos adatközpontra mutat, így biztosítva, hogy a frissítés során senki ne férhessenek hozzá az elsődleges régióban lévő adatokhoz.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Frissítések kezelése írásvédett módban való futtatáskor

A frissítési kérelmek többféleképpen is kezelhetnek, ha csak olvasható módban futnak. Ezt az átfogóan nem fogjuk lefedni, de általában néhány minta is van.

1. Válaszolhat a felhasználóra, és megadhatja, hogy jelenleg nem fogadja el a frissítéseket. A kapcsolattartási felügyeleti rendszer például lehetővé teheti, hogy az ügyfelek hozzáférhessenek a kapcsolattartási adatokhoz, de nem teszik elérhetővé a frissítéseket.

2. A frissítéseket egy másik régióban is sorba helyezni. Ebben az esetben a függőben lévő frissítési kérelmeket egy másik régióba tartozó várólistára írja, majd az elsődleges adatközpont újbóli elvégzése után feldolgozza a kérelmeket. Ebben a forgatókönyvben az ügyfélnek tudnia kell, hogy a kért frissítés várólistára kerül a későbbi feldolgozáshoz.

3. A frissítéseket egy másik régióban lévő Storage-fiókba is írhatja. Azután, hogy az elsődleges adatközpont ismét online állapotba kerül, az adatszerkezettől függően kialakíthatja ezeket a frissítéseket az elsődleges adatba. Ha például külön fájlokat hoz létre egy dátum-/időbélyegzővel a névben, akkor ezeket a fájlokat visszamásolhatja az elsődleges régióba. Ez bizonyos számítási feladatokhoz (például naplózási és iOT) működik.

## <a name="handling-retries"></a>Újrapróbálkozások kezelésére

Az Azure Storage ügyféloldali kódtár segítségével meghatározhatja, hogy mely hibákat lehet újrapróbálni. Például egy 404-es hiba (az erőforrás nem található) nem próbálkozik újra, mert a próbálkozás nem valószínű, hogy sikeres. Másrészről egy 500-es hiba is újrapróbálkozhat, mert kiszolgálóhiba miatt a probléma egyszerűen átmeneti probléma lehet. További részletekért tekintse meg a [ExponentialRetry osztály nyílt forráskódját](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) a .net Storage ügyféloldali kódtáraban. (Keresse meg a ShouldRetry metódust.)

### <a name="read-requests"></a>Olvasási kérelmek

Az olvasási kérések átirányíthatók a másodlagos tárolóba, ha probléma van az elsődleges tárolóval. Amint azt a fentiekben említettük, a [végül konzisztens](#using-eventually-consistent-data)adatszolgáltatások használata esetén elfogadhatónak kell lennie ahhoz, hogy az alkalmazás az elavult adatolvasást is tudja Ha a Storage ügyféloldali kódtár használatával fér hozzá az adatokhoz a másodlagosból, megadhatja az olvasási kérelem újrapróbálkozási viselkedését úgy, hogy a **blobrequestoptions locationmode** tulajdonság értékét a következők egyikére állítja be:

* **PrimaryOnly** (alapértelmezett)

* **PrimaryThenSecondary**

* **SecondaryOnly**

* **SecondaryThenPrimary**

Ha a **blobrequestoptions locationmode** a **PrimaryThenSecondary** értékre állítja, ha az elsődleges végponthoz tartozó kezdeti olvasási kérelem meghiúsul, és az újrapróbálkozási hiba történik, akkor az ügyfél automatikusan egy másik olvasási kérelmet küld a másodlagos végpontnak. Ha a hiba a kiszolgáló időtúllépése, akkor az ügyfélnek várnia kell, amíg az időtúllépés lejár, mielőtt újrapróbálkozó hibát kap a szolgáltatástól.

Az újrapróbálkozást lehetővé tevő hibákra vonatkozó döntés során alapvetően két forgatókönyvnek kell megfontolnia:

* Ez egy elszigetelt probléma, és az elsődleges végpontra irányuló további kérések nem adnak vissza újrapróbálkozást lehetővé tevő hibát. Ilyen eset lehet például, ha átmeneti hálózati hiba történt.

    Ebben az esetben nincs jelentős teljesítménybeli szankció a **Blobrequestoptions locationmode** **PrimaryThenSecondary** való beállításakor, mivel ez csak ritkán fordul elő.

* Ez a probléma az elsődleges régióban található tárolási szolgáltatások közül legalább egy, az elsődleges régióban pedig az adott szolgáltatásra vonatkozó összes további kérelem valószínűleg az újrapróbálkozást lehetővé tevő hibákat fogja visszaadni egy adott időtartamra. Ilyen például, ha az elsődleges régió teljesen elérhetetlenné válik.

    Ebben a forgatókönyvben egy teljesítménnyel kapcsolatos szankció van, mivel az összes olvasási kérelem először az elsődleges végpontot próbálja megvárni, várjon, amíg lejár az időtúllépés, majd váltson a másodlagos végpontra.

Ezekben az esetekben meg kell állapítania, hogy az elsődleges végponton van-e folyamatban lévő probléma, és az összes olvasási kérést közvetlenül a másodlagos végpontnak küldje el úgy, hogy a **blobrequestoptions locationmode** tulajdonságot **SecondaryOnly** értékre állítja. Jelenleg az alkalmazás írásvédett módban történő futtatását is módosítania kell. Ezt a megközelítést nevezzük áramkör- [megszakító mintának](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Frissítési kérelmek

Az áramkör-megszakító minta is alkalmazható a frissítési kérelmekre. A frissítési kérelmek azonban nem irányíthatók át másodlagos tárolóba, amely csak olvasható. Ezekben a kérelmekben hagyja a **blobrequestoptions locationmode** tulajdonságot a **PrimaryOnly** értékre (az alapértelmezett értékre). Ezeknek a hibáknak a kezeléséhez mérőszámot alkalmazhat ezekre a kérelmekre – például egy sor 10 hibáját –, és ha a küszöbérték teljesül, az alkalmazást csak olvasható módba állíthatja. Ugyanezeket a metódusokat használhatja a frissítési módba való visszatéréshez, az áramkör-megszakító mintájának következő részében leírtak szerint.

## <a name="circuit-breaker-pattern"></a>Áramkör-megszakító minta

Az áramkör-megszakító mintájának alkalmazása megakadályozza, hogy egy olyan műveletet próbálkozzon újra, amely valószínűleg többször is sikertelen lesz. Ez lehetővé teszi, hogy az alkalmazás továbbra is fusson ahelyett, hogy időt kelljen kipróbálnia a művelet exponenciális megkezdése közben. Azt is észleli, ha a hiba kijavítva lett, és ekkor az alkalmazás újra próbálkozhat a művelettel.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Az áramkör-megszakító minta implementálása

Annak megállapításához, hogy van-e folyamatban probléma az elsődleges végponttal, megfigyelheti, hogy az ügyfél milyen gyakran jelentkezik az újrapróbálkozást lehetővé tevő hibákkal. Mivel minden eset eltérő, meg kell határoznia azt a küszöbértéket, amelyet a másodlagos végpontra való váltáshoz kíván használni, és az alkalmazást csak olvasható módban futtathatja. Dönthet például úgy, hogy végrehajtja a kapcsolót, ha 10 hiba történt egy olyan sorban, amely nem rendelkezik sikerrel. Egy másik példa arra, hogy ha a kérések 90%-a 2 percenként meghiúsul.

Az első forgatókönyv esetében egyszerűen megtarthatja a hibák számát, és ha a maximális érték elérése előtt sikeres lehet, állítsa vissza a számlálót nullára. A második forgatókönyvben a megvalósításának egyik módja az MemoryCache objektum használata (.NET-ben). Minden kérelem esetében adjon hozzá egy CacheItem a gyorsítótárhoz, állítsa be az értéket a sikeres (1) vagy a sikertelen (0) értékre, majd állítsa a lejárati időt 2 percre (vagy bármilyen időkorlátra). A bejegyzés lejárati idejének elérésekor a rendszer automatikusan eltávolítja a bejegyzést. Ez egy 2 perces gördülő időszakot nyújt. Minden alkalommal, amikor kérelmet küld a Storage szolgáltatásnak, először egy LINQ-lekérdezést használ a MemoryCache objektumon a százalékos siker kiszámításához az értékek összegzésével és a darabszám szerinti osztással. Ha a sikeres százalékos arány néhány küszöbérték alá csökken (például 10%), állítsa a **blobrequestoptions locationmode** tulajdonságot olvasási kérelmek **SecondaryOnly** , és a folytatás előtt váltson írásvédett módba.

Azon hibák küszöbértéke, amelyekkel meghatározható, hogy a kapcsoló Mikor hajthatja végre a szolgáltatást a szolgáltatástól az alkalmazásban, ezért konfigurálható paramétereket kell megfontolnia. Emellett az is előfordulhat, hogy az újrapróbálkozási hibákat az egyes szolgáltatásokból külön vagy egy, a korábban tárgyalt módon kezeli.

Egy másik szempont, hogyan kezelhető egy alkalmazás több példánya, és mi a teendő az egyes példányok újrapróbálkozási hibáinak észlelése során. Előfordulhat például, hogy 20 virtuális gépet futtat ugyanazzal az alkalmazással. Külön kezeli az egyes példányokat? Ha az egyik példány problémákba ütközik, szeretné korlátozni a választ erre az egyetlen példányra, vagy szeretné, hogy az összes példány ugyanúgy reagáljon, amikor az egyik példány problémát észlel? A példányok külön kezelésének módja sokkal egyszerűbb, mint a válaszok összehangolása, de ez az alkalmazás architektúráján alapul.

### <a name="options-for-monitoring-the-error-frequency"></a>A hibák gyakoriságának figyelésére szolgáló beállítások

Három fő lehetősége van az újrapróbálkozások gyakoriságának figyelésére az elsődleges régióban, hogy meghatározza, mikor kell átváltani a másodlagos régióba, és hogy az alkalmazás csak olvasható módban fusson.

* Adjon hozzá egy kezelőt az [**újrapróbálkozási**](/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) eseményhez a Storage-kérelmeknek átadott [**OperationContext**](/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) objektumban – ez a jelen cikkben és a kísérő mintában használt metódus. Ezek az események akkor következnek be, amikor az ügyfél újrapróbálkozik egy kéréssel, így nyomon követheti, hogy az ügyfél milyen gyakran találkozik az újrapróbálkozást lehetővé tevő hibákkal egy elsődleges végponton.

    # <a name="net-v12"></a>[.NET V12](#tab/current)

    Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

    # <a name="net-v11"></a>[.NET-v11](#tab/legacy)

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```
    ---

* Az egyéni újrapróbálkozási szabályzat [**kiértékelési**](/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) metódusában egyéni kódokat futtathat, amikor újra megtörténik az újrapróbálkozások. Az újrapróbálkozások bekövetkezésekor a rögzítés mellett lehetősége van az újrapróbálkozási viselkedés módosítására is.

    # <a name="net-v12"></a>[.NET V12](#tab/current)

    Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

    # <a name="net-v11"></a>[.NET-v11](#tab/legacy)

    ```csharp
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```
    ---

* A harmadik módszer egy egyéni figyelési összetevő megvalósítása az alkalmazásban, amely folyamatosan Pingeli az elsődleges tárolási végpontot a dummy olvasási kérelmekkel (például egy kis blob olvasásával) az állapotának meghatározásához. Ez némi erőforrást is igénybe vehet, de nem jelent jelentős mennyiséget. Ha olyan problémát észlel, amely eléri a küszöbértéket, akkor a kapcsolót a **SecondaryOnly** és csak olvasható módra kell végrehajtania.

Egy ponton vissza kell térnie az elsődleges végpont használatára, és engedélyezni kell a frissítéseket. Ha a fent felsorolt két módszer egyikét használja, egyszerűen visszaválthat az elsődleges végpontra, és engedélyezheti a frissítési módot a tetszőlegesen kijelölt idő vagy a műveletek számának elvégzése után. Ezután ismét átléphet az újrapróbálkozási logikára. Ha a probléma kijavítva lett, továbbra is az elsődleges végpontot fogja használni, és engedélyezi a frissítéseket. Ha a probléma továbbra is fennáll, akkor a beállított feltételek végrehajtása után a rendszer még egyszer visszavált a másodlagos végpontra és a írásvédett módra.

A harmadik forgatókönyv esetén, amikor az elsődleges tároló végpontjának pingelése ismét sikeres lesz, aktiválhatja a váltást a **PrimaryOnly** , és folytathatja a frissítések engedélyezését.

## <a name="handling-eventually-consistent-data"></a>A végül konzisztens adatkezelés

A Geo-redundáns tárolás úgy működik, hogy az elsődlegesről a másodlagos régióba replikálja a tranzakciókat. Ez a replikációs folyamat garantálja, hogy a másodlagos régióban lévő adategységek *végül konzisztensek*. Ez azt jelenti, hogy az elsődleges régió összes tranzakciója végül megjelenik a másodlagos régióban, de előfordulhat, hogy a megjelenésük előtt késésben van, és nem garantálható, hogy a tranzakciók ugyanabban a sorrendben érkeznek a másodlagos régióba, mint ahogyan eredetileg az elsődleges régióban voltak alkalmazva. Ha a tranzakciók sorrendben érkeznek meg a másodlagos régióban, akkor *Előfordulhat* , hogy a másodlagos régióban lévő adatai inkonzisztens állapotba kerülnek, amíg a szolgáltatás fel nem zárkózik.

Az alábbi táblázat egy példát mutat be arra, hogy mi történhet, ha egy alkalmazott adatait frissíti, hogy azok a *rendszergazdák* szerepkör tagjai legyenek. Ennek a példának a kedvéért ehhez frissítenie kell az **alkalmazott** entitást, és frissítenie kell egy **rendszergazdai szerepkör** entitást a rendszergazdák teljes száma számával. Figyelje meg, hogy a frissítések nem sorrendben vannak alkalmazva a másodlagos régióban.

| **Idő** | **Tranzakció**                                            | **Replikáció**                       | **Utolsó szinkronizálás ideje** | **Eredmény** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | A tranzakció: <br> Alkalmazott beszúrása <br> elsődleges entitás |                                   |                    | Egy beszúrt tranzakció az elsődlegesbe<br> még nincs replikálva. |
| T1       |                                                            | A tranzakció <br> replikálva a következőre<br> másodlagos | T1 | A tranzakció replikálása másodlagosra történik. <br>A legutóbbi szinkronizálás ideje frissítve.    |
| T2       | B tranzakció:<br>Frissítés<br> alkalmazotti entitás<br> az elsődleges  |                                | T1                 | A B tranzakció elsődlegesre lett írva,<br> még nincs replikálva.  |
| T3       | C tranzakció:<br> Frissítés <br>adminisztrátor<br>szerepkör entitása a<br>elsődleges |                    | T1                 | A C tranzakció elsődlegesre lett írva,<br> még nincs replikálva.  |
| *T4*     |                                                       | C tranzakció <br>replikálva a következőre<br> másodlagos | T1         | A C tranzakció replikálása másodlagosra történik.<br>A LastSyncTime nem frissült, mert <br>a B tranzakció még nincs replikálva.|
| *T5*     | Entitások olvasása <br>másodlagosról                           |                                  | T1                 | Az alkalmazott elavult értékét kapja <br> entitás, mert a B tranzakció nem <br> még replikálva. A következő új értéket kapja:<br> rendszergazdai szerepkör entitása, mert C<br> replikált. A legutóbbi szinkronizálás ideje még nem<br> Frissítve, mert a B tranzakció<br> még nincs replikálva. Megtudhatja, hogy<br>a rendszergazdai szerepkör entitása inkonzisztens <br>mivel az entitás dátuma/időpontja a következő után van <br>a legutóbbi szinkronizálás ideje. |
| *T6*     |                                                      | B tranzakció<br> replikálva a következőre<br> másodlagos | T6                 | *T6* – a C-n keresztül lebonyolított tranzakciók száma <br>replikálva, utolsó szinkronizálás ideje<br> frissült. |

Ebben a példában feltételezzük, hogy az ügyfél a T5 másodlagos régiójából való olvasásra vált. Ekkor sikeresen beolvashatja a **rendszergazdai szerepkör** entitást, de az entitás tartalmazza a rendszergazdák számának értékét, amely nem konzisztens a másodlagos régióban rendszergazdaként megjelölt **alkalmazotti** entitások számával. Az ügyfél egyszerűen megjelenítheti ezt az értéket azzal a kockázattal, hogy inkonzisztens információ. Azt is megteheti, hogy az ügyfél megpróbálta megállapítani, hogy a **rendszergazdai szerepkör** valószínűleg inkonzisztens állapotban van-e, mert a frissítések sorrendje nem megfelelő, majd tájékoztatja a felhasználót erről a tényről.

Annak felismeréséhez, hogy potenciálisan inkonzisztens adatmennyiséggel rendelkezik, az ügyfél használhatja a *legutóbbi szinkronizálás időpontját* , amelyet bármikor lekérhet a tárolási szolgáltatás lekérdezésével. Ez azt jelzi, hogy mikor történt a másodlagos régióban lévő adategység utolsó egységessége, és ha a szolgáltatás az adott időpontot megelőzően az összes tranzakciót alkalmazta. A fenti példában látható példa azt követően, hogy a szolgáltatás beszúrja az **alkalmazott** entitást a másodlagos régióban, a legutóbbi szinkronizálási idő a *T1* értékre van állítva. Addig marad *T1* , amíg a szolgáltatás frissíti a másodlagos régióban lévő **Employee** entitást, ha a *T6* értékre van állítva. Ha az ügyfél lekéri a legutóbbi szinkronizálási időt, amikor beolvassa az entitást a *T5*-ben, akkor összehasonlíthatja azt az entitás időbélyegével. Ha az entitás időbélyege későbbi, mint az utolsó szinkronizálás ideje, akkor az entitás potenciálisan inkonzisztens állapotban van, és az alkalmazásra vonatkozó megfelelő művelet is megtehető. Ennek a mezőnek a használatával tudnia kell, hogy mikor fejeződött be az elsődleges frissítés.

A legutóbbi szinkronizálás időpontjának vizsgálatával kapcsolatban lásd: [Storage-fiók utolsó szinkronizálási idejének tulajdonságának](last-sync-time-get.md)megkeresése.

## <a name="testing"></a>Tesztelés

Fontos ellenőrizni, hogy az alkalmazás a várt módon viselkedik-e, amikor az újrapróbálkozást lehetővé tevő hibákba ütközik. Például tesztelni kell, hogy az alkalmazás a másodlagosra vált, és csak olvasható módba váltson, amikor problémát észlel, és visszavált, amikor az elsődleges régió újra elérhetővé válik. Ehhez az újrapróbálkozást lehetővé tevő hibákat szimulálni kell, és meg kell határozni, hogy milyen gyakran történjenek.

A [Hegedűs](https://www.telerik.com/fiddler) a http-válaszok lehallgatására és módosítására használható egy parancsfájlban. Ez a szkript azonosíthatja az elsődleges végponttól érkező válaszokat, és a HTTP-állapotkódot úgy változtathatja meg, hogy a Storage ügyféloldali kódtár újrapróbálkozást lehetővé tevő hibát észlel. Ez a kódrészlet egy olyan Hegedűs-parancsfájl egyszerű példáját mutatja be, amely elfogja a **employeedata** tábla olvasási kéréseire adott válaszokat, hogy visszaadja a 502 állapotot:


# <a name="java-v12"></a>[Java V12](#tab/current)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v11"></a>[Java v11](#tab/legacy)

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

Ezt a példát úgy is kiterjesztheti, hogy a kérések szélesebb körét elhallgassa, és csak néhányat módosítsa a **responseCode** , hogy jobban szimulálja a valós helyzetet. A Hegedűs-parancsfájlok testreszabásával kapcsolatos további információkért lásd: [kérelem vagy válasz módosítása](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) a Hegedűs dokumentációjában.

Ha elvégezte az alkalmazás csak olvasható módra való váltásának küszöbértékeit, akkor könnyebben tesztelheti a nem éles tranzakciós kötetekkel kapcsolatos viselkedést.

## <a name="next-steps"></a>Következő lépések

Az elsődleges és másodlagos végpontok közötti váltás menetét bemutató teljes minta: [Azure-minták – az áramkör-megszakító minta és ra-GRS tároló használatával](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
