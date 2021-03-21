---
title: Az Azure Data Catalog bevezetésének módszere és folyamata
description: Ez a cikk az Azure Data Catalog bevezetését megfontoló szervezetek számára mutat be egy megközelítést és egy folyamatot, beleértve a stratégiai célok kitűzését, a fő üzleti alkalmazási esetek azonosítását, valamint egy kísérleti projekt kiválasztását.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: ebfaa59f1800da084d611f6e050a59da63a7f195
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674750"
---
# <a name="approach-and-process-for-adopting-azure-data-catalog"></a>Az Azure Data Catalog bevezetésének módszere és folyamata

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Ez a cikk segít bevezetni az **Azure Data Catalog** szolgáltatást a szervezetében. Az **Azure Data Catalog** sikeres bevezetéséhez három kulcsfontosságú dologra kell összpontosítani: a stratégiai célok kitűzésére, a fő üzleti alkalmazási esetek azonosítására és egy kísérleti projekt kiválasztására.

## <a name="introducing-the-azure-data-catalog"></a>Az Azure Data Catalog bemutatása

A munka világában megváltoztak az azzal kapcsolatos elvárások, hogy hogyan szeretnénk az adategységekre vonatkozó szakmai információkhoz hozzájutni. A Yammer és más közösségi médiaeszközök munkahelyi használatának köszönhetően mára elvárás lett, hogy számos különböző témában juthassunk gyors segítségnyújtáshoz és tanácsadáshoz. Az **Azure Data Catalog** segítségével a cégek és a munkacsoportok egyetlen központi adattárban vonhatják össze a céges adategységekre vonatkozó információkat. Az adatfelhasználók hozzájuthatnak az adategységekhez, és birtokba vehetik a szakértők által közzétett tudásanyagot.

Ez a cikk egy, az **Azure Data Catalog** használatának megkezdésére vonatkozó megközelítést mutat be. A cikk egy tipikus Data Catalog-bevezetési tervet ír le egy fiktív cég, az Adventure Works számára.

**Mi az az Azure Data Catalog?**

Az **Azure Data Catalog** egy teljes mértékben felügyelt Azure-alapú szolgáltatás, és egyben egy vállalati szintű, önkiszolgáló adatforrás-feltárást lehetővé tevő információ- és metaadat-katalógus. A Data Catalog segítségével adategységeket regisztrálhat, tárhat fel, láthat el jegyzetekkel, illetve kapcsolódhat hozzájuk. A Data Catalog úgy kezeli az egymástól eltérő információegységeket, hogy megkönnyíti az adategységek megtalálását, értelmezését és a hozzájuk történő kapcsolódást. Lerövidíti az elérhető adatok elemzéséhez szükséges időt, és növeli a cég számára képviselt értéküket. További tudnivalókért lásd: [Microsoft Azure Data Catalog](https://azure.microsoft.com/services/data-catalog/).

## <a name="azure-data-catalog-adoption-plan"></a>Az Azure Data Catalog bevezetési terve

Az **Azure Data Catalog** bevezetési terve leírja, hogy miképpen érdemes kommunikálnia a szolgáltatás használatának előnyeit az érdekelt felek és a felhasználók felé, valamint hogy milyen oktatást kell biztosítania a felhasználóknak. A Data Catalog bevezetésének sikere nagyban múlik azon, hogy Ön milyen hatékonyan tudja kommunikálni a szolgáltatás értékét a felhasználók és az érdekelt felek felé. A kezdeti bevezetési tervben az elsődleges célközönséget a szolgáltatás felhasználói jelentik. Nem számít, hogy az érdekelt felek mennyire támogatják a döntést – ha a Data Catalog szolgáltatás felhasználói vagy ügyfelei nem térnek át a használatára, a bevezetés nem lesz sikeres. Ennélfogva a jelen cikk feltételezi, hogy az érdekelt felek támogatása adott, és a Data Catalog felhasználói bevezetésére vonatkozó terv létrehozására összpontosít.
Egy hatékony bevezetési terv sikeresen felvázolja a felhasználóknak, hogy a Data Catalog használatával mi minden lehetséges, valamint információkat és útmutatást is ad nekik mindezek elérésében. A felhasználóknak tisztában kell lenniük azzal, hogy a Data Catalog milyen értéket képvisel a munkájuk elvégzésének elősegítésében. Ha a felhasználók átlátják, hogy a Data Catalog által biztosított adatok hogyan segítik őket a jobb eredmények elérésében, világossá válik a Data Catalog bevezetésének haszna. A váltás nehéz, így egy hatékony tervnek figyelembe kell vennie a váltással járó kihívásokat.

A bevezetési terv segít tudatni a felhasználókkal, mi az, ami a sikeresség és a céljaik elérése szempontjából kulcsfontosságú. Egy ilyen terv általában elmagyarázza, hogyan fogja a Data Catalog megkönnyíteni a felhasználói munkáját, és a következő részeket tartalmazza:

* **Stratégiai célkitűzés** – Segít röviden ismertetni a bevezetési tervet a felhasználókkal és az érdekelt felekkel. Itt foglalhatja össze tömören a terv lényegét.
* **Próbacsapat és véleményvezérek** – Egy próbacsapat és néhány véleményvezér segítségével megtervezheti, hogyan mutassa be a csapatainak és felhasználóinak a Data Catalog szolgáltatást. A véleményvezérek maguk is segíthetik a felhasználókat a tudnivalók elsajátításában. Így beazonosíthatók a bevezetés hátráltató és előremozdító tényezői is.
* **Kommunikációs és lelkesítő terv** – Segít a felhasználóknak megérteni, hogy miképpen segíthet nekik a Data Catalog, és hozzájárul ahhoz, hogy a szolgáltatás használata szervesen beépüljön előbb az egyes csapatok, majd az egész cég munkájába.
* **Képzési terv** – Az alapos képzések általában sikeres bevezetéshez és kedvező eredményekhez vezetnek.

Néhány tipp egy **Azure Data Catalog**-bevezetési terv meghatározásához:

## <a name="define-your-data-catalog-project-vision"></a>Tűzze ki a Data Catalog-projekttel kapcsolatos stratégiai céljait

Egy **Azure Data Catalog**-bevezetési terv meghatározásának első lépéseként állítson össze egy leírást arról, mi mindennek az elérésére törekszik. Érdemes a stratégiai célokat viszonylag tágan, de elég tömören megfogalmazni ahhoz,hogy a konkrét rövid és hosszú távú célok meghatározhatók legyenek.

Néhány tipp a stratégiai célok meghatározásához:

* **Azonosítsa az üzembe helyezés fő mozgatórugóját** – Gondoljon bele, milyen konkrét adatforrás-kezelési szükségletei vannak a vállalatnak, amelyek a Data Catalog használatával kielégíthetők. Ez segít kifejteni a Data Catalog használatának legfőbb előnyeit. Lehetnek például olyan közös adatforrások, amelyeket minden új alkalmazottnak ismernie és használnia kell, vagy olyan, fontos és összetett adatforrások, amelyekhez csak néhány kulcsfontosságú személy ért alaposabban. Az **Azure Data Catalog** segíthet könnyen megtalálhatóvá és értelmezhetővé tenni ezeket az adatforrásokat, hogy a jól ismert problémás pontok már a szolgáltatás bevezetésének kezdetén közvetlenül elsimíthatók legyenek.
* **Fogalmazzon tisztán és egyértelműen** – A stratégiai célok tisztázásával mindenkit azonos nézőpontra juttathat arra vonatkozóan, hogy a Data Catalog hogyan szolgálja a szervezet javát, és hogy az elképzelés hogyan támogatja a szervezet céljainak elérését.
* **Inspirálja a munkatársakat a Data Catalog használatára** – A stratégiai célkitűzések és a kommunikációs terv legyen inspiráló, és segítsen felismerni azt, hogy a Data Catalog hogyan segíthet az adatforrások megtalálásában és a hozzájuk való kapcsolódásban, így elősegítve a jobb eredmények elérését.
* **Szögezze le a konkrét célokat és határidőket** – Így biztosíthatja, hogy a bevezetési tervnek konkrét és elérhető céljai legyenek. A határidők használata segít a célokra koncentrálni, és ellenőrzőpontokat biztosít a sikeresség felméréséhez.

Íme egy példa egy Data Catalog-bevezetési terv stratégiai célkitűzésire egy fiktív cég, az Adventure Works esetében:

**Azure Data Catalog** lehetővé teszi, hogy az Adventure Works pénzügyi csapata közösen működjön együtt a kulcsfontosságú adatforrásokkal, így minden csapattag könnyedén megtalálhatja és használhatja a szükséges információkat, és megoszthatja tudásukat a csapat egészével.

Ha kitűzte a pontos stratégiai célokat, azok alapján kiválaszthatja a megfelelő próbaprojektet a Data Catalog bevezetéséhez. Általánosságban a Data Catalog számos forgatókönyv esetén használható, így a következő szakaszban néhány tippet olvashat a releváns használati esetek azonosításához.

## <a name="identify-data-catalog-business-use-cases"></a>A Data Catalog üzleti alkalmazási helyzeteinek azonosítása

A Data Catalog szempontjából releváns alkalmazási helyzetek és a megoldandó üzleti problémák azonosításához egyeztessen a különböző üzleti egységek szakértőivel. Vizsgálja meg, jelenleg milyen kihívásokat rejt a felhasználók számára az adategységek azonosítása és értelmezése. Ilyen kihívás például, ha a csapatok csak úgy szerezhetnek tudomást az adategységekről, ha a szervezet több munkatársát is megkérdezik arról, hogy ki rendelkezik releváns adatforrásokkal.

A legjobb olyan alkalmazási helyzeteket választani, amelyek az alacsonyan lógó gyümölcsöt jelképezik: ezek azok az esetek, amelyek fontosak, és a Data Catalog használatával jó eséllyel meg is oldhatók.

Néhány tipp az alkalmazási helyzetek azonosításához:

* **Határozza meg a csapat céljait** – Hogyan teljesíti a csapat a kitűzött célokat? Ekkor még ne a Data Catalog szolgáltatásra koncentráljon, hiszen ezen a ponton még objektívnek kell lennie. Ne feledje, az üzleti eredmények számítanak, nem a technológia.
* **Határozza meg az üzleti problémát** – Milyen problémákkal néz szembe a csapat az adategységek megtalálását és megismerését illetően? Előfordulhat például, hogy a fontos adatforrásokra vonatkozó információk egy hálózati mappában vannak Excel-munkafüzetekben, amelyeket a csapat csak hosszas kereséssel talál meg.
* **Vegye figyelembe, hogyan viszonyul a csapat a változásokhoz** – A bevezetés támasztotta kihívások sokszor nem egy új eszköz bevezetésében, hanem a változásnak való ellenállásban rejlenek. A csapat változásokhoz való hozzáállása fontos szerepet játszik a használati esetek azonosításában, mivel lehetséges, hogy a jelenlegi folyamat csak a „mindig így csináltuk” vagy a „ha nem romlott el, minek megjavítani?” indokok alapján van érvényben. Egy új eszköz vagy folyamat bevezetése mindig akkor a legegyszerűbb, ha az érintettek tisztában vannak a váltás nyújtotta előnyökkel, és elismerik a megoldandó problémák fontosságát.
* **Tartsa Fókuszban az adategységeket** – a csapat által felmerülő üzleti problémák megvitatásakor "a gyomnövények átvágása", és a vállalati adategységek hatékonyabb kihasználásával kapcsolatos tudnivalókra kell összpontosítani.

Néhány példa a Data Catalog alkalmazási helyzeteire:

### <a name="example-use-cases"></a>Példák az alkalmazási helyzetekre

* **Nagy értékű központi adatforrások regisztrálása** – Az IT-részleg a szervezet egészében használt adatforrásokat felügyel. A Data Catalog használatával végzett informatikai felügyelet kezdő lépéseiként regisztrálhatók és megjegyzésekkel láthatók el a gyakran használt vállalati adatforrások.
* **Csapatszintű adatforrások regisztrálása** – A különböző csapatok különböző hasznos, üzletági adatforrásokkal rendelkeznek. Az **Azure Data Catalog** használatának kezdő lépéseiben azonosítsa és regisztrálja a különböző csapatok által használt fő adatforrásokat, és rögzítse **Azure Data Catalog**-megjegyzésekben a csapatok kollektív ismereteit.
* **Önkiszolgáló üzleti intelligencia** – A csapatoknak sokáig tart összefésülni a különböző forrásokból származó adatokat. Az adatforrások központi regisztrálásával és megjegyzésekkel való ellátásával kiiktatható a kézi adatforrás-keresési folyamatok szükségessége.

A Data Catalog-forgatókönyvekkel kapcsolatos további információkért lásd: [Azure Data Catalog common scenarios](data-catalog-common-scenarios.md) (Az Azure Data Catalog gyakori forgatókönyvei).

Ha azonosította a Data Catalog néhány alkalmazási helyzetét, világossá válnak a gyakori forgatókönyvek. A következő szakasz leírja, hogyan azonosíthatja az első próbaprojektet egy alkalmazási helyzet alapján.

## <a name="choose-a-data-catalog-pilot-project"></a>Data Catalog-próbaprojekt kiválasztása

A siker egyik kulcsa az egyszerűsítés és a kisebb lépésekben való bevezetés. Egy jól körülírt, korlátozott léptékű kísérlet segít fenntartani a projekt előrehaladását anélkül, hogy leragadnánk egy túl összetett vagy túl sok résztvevővel rendelkező projektnél. Fontos azonban, hogy a kísérletben többféle felhasználó is részt vegyen, a kezdetektől lelkes támogatóktól a szkeptikusokig. A megoldást támogató felhasználók segíthetnek a kommunikációs és lelkesítő terv későbbi finomításában. A szkeptikusok pedig segítenek a hátráltató tényezők azonosításában és kezelésében. Ahogy a szkeptikusok támogatókká válnak, a visszajelzéseik alapján azonosíthatók a siker mozgatórugói.

A próbatervben érdemes olyan üzleti célokat kitűzni, amelyeket a Data Catalog használatával kíván elérni. A kezdeti próbaterv tanulságainak levonásával párhuzamosan a felhasználói bázis is bővíthető. A kezdeti próbaterv lezárásával mérhetővé válik a siker, de a végső cél a természetes vagy ugrásszerű növekedés elérése. A Data Catalog természetes növekedésével a felhasználók maguk irányíthatják a saját adatfelhasználásukat, és másokat is ösztönözhetnek és bátoríthatnak a katalógus használatára és bővítésére.

### <a name="target-the-right-team"></a>A megfelelő csoport megcélzása

A próbaprojekt kiválasztásakor válassza azt a csoportot, amelyik a legkedvezőbb lehetőséget jelenti egy létező üzleti probléma megoldására. Ilyen például egy üzleti elemző, aki egy SQL Server-adatbázisból készít jelentéseket. A probléma az, hogy az adatforrást csak a több kollégával való beszélgetés után ismerte fel az adatforrás. Végül, miután a megtalálta, hogy mely adatforrásokat szeretné használni, egy Excel-munkafüzetet talált, amely az egyes adatforrások leírását tartalmazza. Bár az Excel-munkafüzet megfelelően leírja a szükséges táblákat, gyorsan megtalálhatja ezeket az adatforrásokat, ha regisztrálva lettek, és megjegyzésbe vették őket a **Azure Data Catalogban**.

### <a name="identify-data-heroes"></a>Adatkezelők kiválasztása

Az első próbaprojekt tartalmazzon néhány adatalkotó és adatfelhasználó személyt is, hogy a csapat kiegyensúlyozottan képviseltesse magát.

Az **adatalkotók** az adatforrások szakértői. David például egy másik csapatban már huzamosabb ideig dolgozott az Adventure Works néhány fő adatforrásával. Az **Azure Data Catalog** bevezetése előtt David létrehozott egy Excel-munkafüzetet, amelyben rögzítette az Adventure Works adatforrásaira vonatkozó információkat.

Az **adatfelhasználók** az üzleti problémák adatok felhasználásával történő megoldásának szakértői. Nancy például egy üzleti elemző, aki az Adventure Works SQL Server-adatforrásait használja az adatelemzéshez.

Az egyik üzleti probléma, amelyet az **Azure Data Catalog** megold, az **adatalkotók** és az **adatfelhasználók** összeköttetése. Ezt úgy valósítja meg, hogy központi adattárként szolgál a céges adatforrásokra vonatkozó információk számára. David a Data Catalog használatával nyilvántartásba veszi az Adventure Works és az SQL Server adatforrásait. A közösségi bármely olyan felhasználó, aki felderíti ezt az adatforrást, megoszthatja a saját véleményét az adatokon kívül az általuk felderített adatok használata mellett is. Nancy például a katalógusban való kereséssel megtalálja az adatforrásokat, és hozzájuk fűzve megosztja az adatokra vonatkozó szaktudását.  Mostantól az adatkatalógusban való kereséssel a cég többi munkatársa is felhasználhatja a megosztott szaktudást.

* Az adatforrások nyilvántartására vonatkozó további információkért lásd: [Register data sources](data-catalog-get-started.md) (Adatforrások regisztrálása).
* Az adatforrások felfedezésére vonatkozó további információkért lásd: [Search data sources](data-catalog-get-started.md) (Adatforrások keresése).

### <a name="start-small-and-focused"></a>Apró kezdeti lépések és összpontosítás

A legtöbb vállalati próbaprojekt esetében érdemes a katalógust nagy értékű adatforrásokkal feltölteni, hogy az üzleti felhasználók számára hamar láthatóvá váljon a Data Catalog értéke. Az informatikai részleg remek kezdőpont mindazon közös adatforrások azonosítására, amelyek hasznára lehetnek a próbacsapatnak. A támogatott adatforrások, mint például az SQL Server esetében javasoljuk az **Azure Data Catalog** adatforrás-regisztráló eszközének használatát. Az adatforrás-regisztráló eszközzel adatforrások széles köre regisztrálható, köztük az SQL Server- és Oracle-adatbázisok, illetve az SQL Server Reporting Services-jelentések is. A jelenlegi adatforrások teljes listájáért lásd: [Azure Data Catalog supported data sources](data-catalog-dsr.md) (Az Azure Data Catalog által támogatott adatforrások).

A fő adatforrások azonosítása és regisztrálása után lehetőség van más helyeken tárolt adatforrás-leírások importálására is. A Data Catalog API lehetővé teszi, hogy a fejlesztők leírásokat és megjegyzéseket töltsenek be egy másik helyről, például a David által létrehozott és fenntartott Excel-munkafüzetből.

A következő szakasz egy példaprojektet ír le az Adventure Works vállalattól.

### <a name="an-example-project"></a>Egy példaprojekt

Ebben a példában Nancy, az üzleti elemző egy SQL Server-adatbázisból származó adatkészletből készít jelentéseket a csapata számára. A probléma az, hogy az adatforrást csak a több kollégával való beszélgetés után ismerte fel az adatforrás. Ezek az adatforrások gyorsan megtalálhatók, ha regisztrálva lettek, és egy központi helyen (például **Azure Data Catalog**) vannak bejegyezve.

Azt, hogy Nancy és csapata milyen könnyen megtalálhatja az értékes adatokat, úgy szemléltetheti, hogy az adatforrásokra vonatkozó információkat (metaadatokat) az adatforrás-regisztráló eszköz segítségével feltölti a katalógusba. Így az adatbázisra vonatkozó információk nem csak néhány személy, de a csapat és a teljes vállalat számára is elérhetők lesznek. Miután az adatforrások regisztrálva lettek a Data Catalog szolgáltatásban, Nancy és csapata könnyedén hozzáférnek az általuk használt forrásokhoz. Az eredmény egy átfogóbb és relevánsabb adatkatalógus a csapat és a vállalat számára. Minél több csapat veszi használatba a Data Catalog szolgáltatást, annál könnyebb lesz az üzleti adatforrások megtalálása és használata, így az adatközpontúság növelésével további eredményeket hozhatnak ki az adatokból.

További információk az adatkatalógus-regisztráló eszközzel kapcsolatban: [Ismerkedés az Azure Data Catalog szolgáltatással](data-catalog-get-started.md).

A kísérleti projekt keretében Nancy csapata azokkal az adatforrásokkal is dolgozik, amelyeket a David csapata által tovább használt Excel-munkafüzet ír le. Mivel a vállalat további csapatai szintén Excel-munkafüzeteket használnak az adatforrások leírására, az IT-csapat úgy dönt, hogy létrehoznak egy eszközt, amellyel az Excel-munkafüzetek importálhatók a Data Catalog szolgáltatásba. A meglévő megjegyzéseknek a Data Catalog REST API használatával történő importálásával a kísérleti projekt csapata egy teljes adatkatalógust hozhat létre, amely tartalmazza az adatforrásokból az adatforrás-regisztráló eszköz által kinyert metaadatokat, valamint az adatalkotók és az adatfelhasználók által korábban dokumentált információkat is, anélkül, hogy ismételt kézi bevitelre lenne szükség. Ahogy a vállalati adatkatalógus növekszik, a szervezet a gyakori adatforrásokat a regisztráló eszközzel veheti nyilvántartásba, az egyéni források és szokatlan forgatókönyvek esetében pedig a Data Catalog API-kat használhatja.

> [!NOTE]
> Példaként írtunk egy eszközt, amely az **Azure Data Catalog** API használatával importál egy Excel-munkafüzetet a Data Catalog szolgáltatásba. A Data Catalog API-kkal és a példaeszközzel kapcsolatos további információkért [töltse le az Ad Hoc munkafüzetkód-példát](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets), és nézze át az [Azure Data Catalog REST API](/rest/api/datacatalog/) dokumentációját.

Miután a próbaprojekt felállt, ideje végrehajtani a Data Catalog bevezetési tervét.

### <a name="execute"></a>Végrehajtás

Mostanra azonosította a Data Catalog alkalmazási helyzeteit és az első projektet is. Ezenfelül regisztrálta az Adventure Works fő adatforrásait, valamint az informatikai részleg által létrehozott eszközzel hozzáadta a meglévő Excel-munkafüzetben tárolt információkat is. Ideje a próbacsapattal együttműködve elindítani a Data Catalog bevezetésének folyamatát.

Néhány tipp az első lépések megtételéhez:

* **Keltsen érdeklődést** – A céges felhasználók érdeklődőbbek lesznek, ha hisznek abban, hogy az **Azure Data Catalog** megkönnyíti a munkájukat. Igyekezzen a párbeszéd középpontjába a megoldást és az általa nyújtott előnyöket állítani, nem pedig a technológiát.
* **Könnyítse meg a változást** – Kezdje kis lépésekkel, és kommunikálja a tervet az üzleti felhasználók felé. A sikerhez elengedhetetlen, hogy a felhasználókat a kezdetektől fogva bevonja a bevezetés folyamatába, hogy hatással lehessenek az eredményre, és magukénak érezhessék a megoldást.
* **Készítsen fel korai támogatókat** – A korai támogatók azon üzleti felhasználók, akik lelkesen végzik a dolgukat, és készséggel mesélnek kollégáiknak az **Azure Data Catalog** előnyeiről.
* **Tartson célzott képzéseket** – Nem kell, hogy az üzleti felhasználók mindent tudjanak a Data Catalog szolgáltatásról: igazítsa a képzéseket a csapatokra vonatkozó célkitűzésekhez. Annak érdekében, hogy az **Azure Data Catalog** beépüljön a felhasználók napi gyakorlatába, állítsa középpontba a munkájukat, és azt, hogy miképpen változhatnak meg a feladataik.
* **Álljon készen a kudarcra** – Ha a kísérleti projekt nem váltja be a hozzá fűzött reményeket, értékelje ki a projektet, és azonosítsa a módosítandó részeket – javítsa a próbatervet, mielőtt továbblépne a szélesebb körű bevezetésre.

Mielőtt a próbacsapat beleveti magát a Data Catalog használatába, szervezzen egy induló értekezletet, ahol részletezheti a projekttel szemben támasztott elvárásokat, és egy kezdeti oktatást is tarthat.

### <a name="set-expectations"></a>Az elvárások felállítása

Az elvárások felállításával és célok kitűzésével az üzleti felhasználók könnyebben koncentrálnak megadott eredmények elérésére. A projekt zökkenőmentes haladása érdekében rendszeresen adjon ki házi feladatokat (a próbaterv méretétől és időtartamától függően például naponta vagy hetente). A Data Catalog egyik legértékesebb tulajdonsága a közösségi adatforrás-használat lehetősége, amelynek köszönhetően az üzleti felhasználók vállalati adatokat használhatnak. Remek házi feladatnak bizonyulhat, ha a kísérleti csapat minden tagjának regisztrálnia kell, vagy megjegyzéssel kell ellátnia legalább egy adatforrást, amelyet felhasznált. Lásd: [Register a data source](data-catalog-get-started.md) (Adatforrások regisztrálása) és [How to annotate data sources](data-catalog-get-started.md) (Adatforrások ellátása megjegyzésekkel).

Szervezzen a csoportnak rendszeres értekezleteket, amelyek során megbeszélnek bizonyos megjegyzéseket. A Data Catalog sikeres bevezetésének kulcsfontosságú tényezője az adatforrások hasznos megjegyzésekkel való ellátása, hogy a forrásokkal kapcsolatos hasznos észrevételek egy központi helyen elérhetők legyenek. Jó megjegyzések nélkül az adatforrások ismerete vállalati szinten szórványos marad. Lásd: [How to annotate data sources](data-catalog-get-started.md) (Adatforrások ellátása megjegyzésekkel).

A projekt végső próbája pedig az, hogy a felhasználók képesek-e megtalálni és értelmezni a munkájukhoz szükséges adatforrásokat. A próbaprojektben részt vevő felhasználóknak érdemes rendszeresen tesztelniük a katalógust, ezzel biztosítva, hogy a mindennapi munkájuk során használt adatforrások relevánsak legyenek. Ha egy szükséges adatforrás hiányzik, vagy nincs megfelelő megjegyzésekkel ellátva, az emlékeztetőként szolgálhat arra, hogy további adatforrásokat kell regisztrálni, vagy további megjegyzéseket kell megadni. Ez a gyakorlat nem csupán a próbatervet teszi sikeresebbé, de olyan hatékony szokásokat is meghonosít, amelyek a projekt lezárása után más csapatokban is elterjedhetnek.

### <a name="provide-training"></a>Képzés biztosítása

Az oktatásnak el kell érnie, hogy a felhasználók megismerjék a szolgáltatást, és igazodnia kell a próbacsapat tagjainak konkrét céljaihoz és felkészültségéhez. Az oktatás kezdéseképp végigkövetheti az [Ismerkedés az Azure Data Catalog szolgáltatással](data-catalog-get-started.md) cikk lépéseit. Emellett letöltheti a [Azure Data Catalog próbaprojektjeiről szóló oktatóbemutatót](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/blob/master/Azure%20Data%20Catalog%20Training.pptx?raw=true) is. Ez a PowerPoint-bemutató segít megismertetni a Data Catalog szolgáltatást a próbacsapat tagjaival.

## <a name="conclusion"></a>Összegzés

Ha a próbacsapat már viszonylag zökkenőmentesen dolgozik, és elérte az eredetileg kitűzött célokat, további csapatokra is kiterjesztheti a Data Catalog bevezetését. Használja fel és finomítsa tovább a próbaprojekt tanulságait a Data Catalog szolgáltatásnak a szervezeten belüli elterjesztése során.

A kísérletben részt vevő kezdeti támogatók segítséget nyújthatnak a Data Catalog bevezetésével járó előnyök további népszerűsítésében. Megoszthatják más csapatokkal, hogy a Data Catalog hogyan segített a csapatuknak az üzleti problémák megoldásában, hogyan könnyítette meg az adatforrások felfedezését, és észrevételeket oszthatnak meg az általuk használt adatforrásokra vonatkozóan. Az Adventure Works próbacsapatának kezdeti támogatói például megmutatták a munkatársaiknak, milyen könnyű információkat találni az Adventure Works azon adategységeiről, amelyeket korábban csak nehezen lehetett felkutatni és értelmezni.

Ez a cikk arról szólt, hogyan vezetheti be az **Azure Data Catalog** szolgáltatást a szervezeténél. Reméljük, sikerült elindítania egy Data Catalog-próbaprojektet, és elterjesztenie a szervezetnél a Data Catalog használatát.

## <a name="next-steps"></a>Következő lépések

[Azure Data Catalog létrehozása](data-catalog-get-started.md)
