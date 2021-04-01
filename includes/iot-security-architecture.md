---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 28609ad27330ae4ea5ea7c0d02d5a61181fbe0df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95559341"
---
A rendszer tervezésekor fontos megérteni a rendszer potenciális veszélyforrásait, és ennek megfelelően hozzá kell adnia a megfelelő védelmi lehetőségeket, ahogy a rendszert tervezték és felépíteni. Fontos, hogy megtervezze a terméket a kezdetektől a biztonsággal szem előtt tartva, mert megértette, hogy a támadók hogyan veszélyeztethetik a rendszereket, így biztosítható, hogy a megfelelő enyhítések a kezdetektől fogva megtörténjenek.

## <a name="security-starts-with-a-threat-model"></a>A biztonság a veszélyforrások modelljével kezdődik

A Microsoft már régóta használta a veszélyforrásokat a termékeihez, és nyilvánosan elérhetővé tette a vállalat fenyegetés-modellezési folyamatát. A vállalati élmény azt mutatja be, hogy a modellezés nem várt előnyökkel jár, mint az, hogy a leginkább milyen fenyegetéseket érint. Létrehozhat például egy utat is a fejlesztői csapaton kívüli másokkal folytatott nyílt beszélgetésekhez, ami új ötleteket és fejlesztéseket eredményezhet a termékben.

A veszélyforrások modellezésének célja annak megértése, hogy a támadók hogyan veszélyeztethetik a rendszer biztonságát, és hogy a megfelelő enyhítések érvényben legyenek. A veszélyforrások modellezése arra kényszeríti a tervezési csapatot, hogy a rendszer központi telepítésének megtervezése helyett a rendszer tervezésekor vegye figyelembe a megoldást. Ez a tény kritikus fontosságú, mivel a biztonsági védelem több, a területen található eszközre való utólagos beszerelése nem valósítható meg, és az ügyfeleket veszélyezteti.

Számos fejlesztői csapat kiváló feladattal rögzíti az ügyfelek számára előnyös rendszer működési követelményeit. Ha azonban nem nyilvánvaló módszereket azonosít, hogy valaki visszaélést okozhat a rendszeren, nagyobb kihívást jelent. A veszélyforrások modellezése segíthet a fejlesztési csapatoknak megérteni, hogy mit tehetnek a támadók, és miért is. A veszélyforrások modellezése egy strukturált folyamat, amely vitát készít a rendszeren a biztonsági tervezési döntésekről, valamint a biztonsági szempontokat érintő tervezési változásokról. Habár a veszélyforrások modellje egyszerűen egy dokumentum, ez a dokumentáció ideális megoldást kínál az ismeretek folytonosságának biztosítására, a tanulságok megőrzésére és az új csapat gyors előkészítésére. Végül a veszélyforrások modellezésének eredménye, hogy a biztonság más szempontjait is figyelembe veszi, például azt, hogy milyen biztonsági kötelezettségeket szeretne biztosítani az ügyfeleknek. Ezek a kötelezettségvállalások a veszélyforrások modellezésével és a eszközök internetes hálózata-(IoT-) megoldás tesztelésével foglalkoznak.

### <a name="when-to-do-threat-modeling"></a>Mikor érdemes elvégezni a veszélyforrások modellezését

A [Threat Modeling](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) a legnagyobb értéket kínálja a tervezési fázisba való beépítésekor. A tervezésekor a lehető legnagyobb rugalmassággal rendelkezik a fenyegetések elhárítása érdekében. A fenyegetések megtervezése a kívánt eredmény. Ez sokkal egyszerűbb, mint a mérséklések hozzáadása, a tesztelés, és annak biztosítása, hogy továbbra is naprakészek maradjanak, továbbá az ilyen eltávolítás nem mindig lehetséges. Nehezebbé válik a fenyegetések kiiktatása, mivel a termék még érettebb lesz, és végül a fejlesztés során egyre több munkát és sokkal nehezebb kompromisszumokat igényel, mint a veszélyforrások modellezése.

### <a name="what-to-consider-for-threat-modeling"></a>Mit érdemes figyelembe venni a veszélyforrások modellezéséhez

Tekintse meg a megoldás egészét, és az alábbi területekre is koncentrálhat:

* A biztonsági és adatvédelmi funkciók
* Azok a funkciók, amelyek meghibásodása biztonsági jelentőséggel bír
* A megbízhatósági kapcsolat határát érintő funkciók

### <a name="who-performs-threat-modeling"></a>Ki végzi el a fenyegetések modellezését

A veszélyforrások modellezése olyan folyamat, mint bármely más. Érdemes a veszélyforrást modellező dokumentumot a megoldás bármely más összetevőjéhez hasonlóan kezelni, és érvényesíteni. Számos fejlesztői csapat kiváló feladattal rögzíti az ügyfelek számára előnyös rendszer működési követelményeit. Ha azonban nem nyilvánvaló módszereket azonosít, hogy valaki visszaélést okozhat a rendszeren, nagyobb kihívást jelent. A veszélyforrások modellezése segíthet a fejlesztési csapatoknak megérteni, hogy mit tehetnek a támadók, és miért is.

### <a name="how-to-perform-threat-modeling"></a>A veszélyforrások modellezése

A veszélyforrások modellezési folyamata négy lépésből áll; a lépések a következők:

* Az alkalmazás modellezése
* Fenyegetések számbavétele
* Fenyegetések enyhítése
* A enyhítések ellenőrzése

#### <a name="the-process-steps"></a>A folyamat lépései

Három szabályt kell szem előtt tartani a fenyegetések modell létrehozásakor:

1. Hozzon létre egy diagramot a hivatkozási architektúrából.

2. Kezdési szélesség – első. Tekintse át az áttekintést, és tekintse át a rendszer egészét, mielőtt a Deep-Diving. Ezzel a megközelítéssel biztosíthatja, hogy a megfelelő helyeken mélyen elmerülnek.

3. Hajtsa meg a folyamatot, ne hagyja, hogy a folyamat elvezesse. Ha hibát talál a modellezési fázisban, és azt szeretné felfedezni, keresse fel! Ne habozzon, hogy kövesse az alábbi lépéseket slavishly.

#### <a name="threats"></a>Fenyegetések

A veszélyforrások modellezésének négy fő eleme a következők:

* Folyamatok, például webszolgáltatások, Win32-szolgáltatások és * nix démonok. Bizonyos összetett entitások (például a helyszíni átjárók és érzékelők) olyan folyamatként vonhatók el, amelyben nem lehet technikai részletezést biztosítani ezekben a területeken.

* Adattárak (bárhol tárolva, például konfigurációs fájl vagy adatbázis)

* Adatfolyam (ahol az adatforgalom az alkalmazás más elemei között mozog)

* Külső entitások (minden, ami a rendszerrel kommunikál, de nem az alkalmazás felügyelete alá tartozik, például a felhasználók és a műholdas csatornák)

Az építészeti diagram minden eleme különböző fenyegetésekre vonatkozik; Ez a cikk a Stride hívóbetűje. Olvassa el [újra a veszélyforrások modellezését,](/archive/blogs/larryosterman/threat-modeling-again-stride) és tekintse meg a lépéseket a lépéshossz elemek megismeréséhez.

Az alkalmazás diagramjának különböző elemei bizonyos átlépési fenyegetésekre vonatkoznak:

* A folyamatok a következő lépések hatálya alá esnek
* Az adatfolyamatok a TID-nek vannak kitéve
* Az adattárakat a TID-nek, és esetenként az R-nek kell alávetni, amikor az adattárak naplófájlok.
* A külső entitások SRD vonatkoznak

## <a name="security-in-iot"></a>Biztonság a IoT

A csatlakoztatott speciális célú eszközök jelentős számú potenciális interakciós felülettel és interakciós mintákkal rendelkeznek, és mindegyiknek figyelembe kell vennie az eszközök digitális elérésének biztosításához szükséges keretrendszert. A "digitális hozzáférés" kifejezés azért használatos, hogy megkülönböztesse azokat a műveleteket, amelyek a közvetlen eszköz interakcióján keresztül valósulnak meg, ha a hozzáférés biztonságát fizikai hozzáférés-vezérléssel biztosítjuk. Tegyük fel például, hogy az eszközt egy lakattal rendelkező helyiségbe helyezi. Habár a fizikai hozzáférés nem tagadható meg a szoftver és a hardver használatával, a rendszer meggátolja, hogy a fizikai hozzáférés megakadályozza a rendszerbeavatkozást.

Az interakciós minták megismeréséhez tekintse meg a "Device Control" és az "Device-adatmennyiség" kifejezést ugyanazzal a szintű figyelemmel. Az "eszközök vezérlése" a bármely fél által az eszközre adott bármilyen információ besorolva, amelynek célja, hogy megváltoztatja vagy befolyásolja a viselkedését az állapotára vagy a környezetének állapotára vonatkozóan. Az "eszköz adatai" olyan információk besorolására alkalmasak, amelyeket az eszköz az állapotáról és a környezete megfigyelt állapotáról bocsát ki.

Az ajánlott biztonsági eljárások optimalizálása érdekében javasoljuk, hogy egy tipikus IoT architektúra több összetevőre vagy zónára legyen osztva a veszélyforrások modellezésének gyakorlása során. Ezeket a zónákat a szakasz teljes egészében ismerteti, és a következőket tartalmazza:

* Eszköz
* Field Gateway,
* Felhőbeli átjárók és
* Services.

A zónák széles körűen használhatók a megoldások szegmentálásához; az egyes zónákhoz gyakran a saját adat-és hitelesítési és engedélyezési követelmények vonatkoznak. A zónák is felhasználhatók a sérülések elkülönítésére, és az alacsony megbízhatósági zónák hatásának korlátozására a magasabb szintű megbízhatósági zónákban.

Minden zónához egy megbízhatósági kapcsolati határ van elválasztva, amely a következő ábrán pontozott piros vonalként van feltüntetve. Az adatok/információk egyik forrásról a másikra való átállását jelenti. Az áttérés során az adatok/információk a hamisítás, a hamisítás, a Megtagadás, az információ-közzététel, a szolgáltatásmegtagadás és a jogosultsági szint megemelése (LÉPÉSHOSSZ) alapján változhatnak.

![IoT biztonsági zónák](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Az egyes határokon belül ábrázolt összetevők is meghaladják a lépéseket, ami lehetővé teszi a megoldás teljes 360-es veszélyforrás-modellezési nézetét. Az alábbi részekben az egyes összetevőkre, valamint a szükséges konkrét biztonsági kérdésekre és megoldásokra vonatkozó részletes információk találhatók.

Az alábbi részek a szabványos összetevőket tárgyalják, amelyek általában a következő zónákban találhatók.

### <a name="the-device-zone"></a>Az eszköz zónája

Az eszköz környezete az eszköz körüli közvetlen fizikai terület, ahol a fizikai hozzáférés és/vagy a "helyi hálózat" társközi digitális hozzáférése az eszközhöz lehetséges. A "helyi hálózat" azt feltételezi, hogy egy különálló és szigetelt hálózat – a nyilvános interneten keresztül is –, valamint olyan rövid hatótávolságú vezeték nélküli rádiófrekvenciás technológiát tartalmaz, amely lehetővé teszi az eszközök egyenrangú kommunikációját. *Nem* tartalmaz olyan hálózati virtualizációs technológiát, amely az ilyen helyi hálózatok illúzióját hozza létre, és nem tartalmaz olyan nyilvános operátori hálózatokat, amelyek két eszközt igényelnek a nyilvános hálózati térben való kommunikációhoz, ha a társ-társ kommunikációs kapcsolatot kell megadniuk.

### <a name="the-field-gateway-zone"></a>A mező-átjáró zónája

A Field Gateway egy eszköz/berendezés vagy egy olyan általános célú kiszolgáló számítógépes szoftver, amely kommunikációt tesz lehetővé, és potenciálisan eszköz-ellenőrzési rendszerként és eszköz-adatfeldolgozó központként működik. A mező átjáró zónája magába foglalja a mező átjáróját és a hozzá csatolt összes eszközt. Ahogy a név is jelenti, a helyszíni átjárók a dedikált adatfeldolgozási létesítményeken kívül járnak el, általában a helyhez kötöttek, és a fizikai behatolásra is alkalmasak, és korlátozott működési redundanciával rendelkeznek. Minden, ami azt jelenti, hogy egy adott mező átjárója általában egy olyan dolog, amely a működésének ismeretében megtudhatja és szabotálhatja.

A mező-átjáró különbözik a puszta forgalmú útválasztótól, hogy aktív szerepkörrel rendelkezik a hozzáférés és az információáramlás kezelésében, ami azt jelenti, hogy egy alkalmazás által kezelt entitás és hálózati kapcsolat vagy munkamenet-terminál. Egy NAT-eszköz vagy tűzfal ezzel szemben nem felel meg a mező-átjárónak, mivel nem kifejezetten kapcsolati vagy munkamenet-terminálok, hanem egy útvonal (vagy blokk) kapcsolatok vagy a rajtuk keresztül végrehajtott munkamenetek. A Field Gateway két különálló felülettel rendelkezik. Az egyik a hozzá csatlakozó eszközökre néz, és a zónán belüli, a másik pedig az összes külső felet és a zóna szélét jelképezi.

### <a name="the-cloud-gateway-zone"></a>A Felhőbeli átjáró zónája

A Cloud Gateway egy olyan rendszer, amely lehetővé teszi a távoli kommunikációt több különböző helyről az eszközökre vagy a helyszíni átjáróra a nyilvános hálózati térben, jellemzően a felhőalapú vezérlési és adatelemzési rendszer, az ilyen rendszerek összevonása felé. Bizonyos esetekben előfordulhat, hogy a Cloud Gateway azonnal megkönnyíti a speciális célú eszközökhöz, például a tablettákhoz vagy telefonokhoz való hozzáférést. Az itt tárgyalt kontextusban a "Cloud" kifejezés arra szolgál, hogy egy dedikált adatfeldolgozó rendszerre hivatkozzon, amely nem ugyanahhoz a helyhez van kötve, mint a csatlakoztatott eszközök vagy a helyszíni átjárók. A Felhőbeli zónákban a működési intézkedések meggátolják a célként megadott fizikai hozzáférést, és nem feltétlenül teszik közzé a "nyilvános felhő" infrastruktúráját.  

Előfordulhat, hogy egy Felhőbeli átjáró egy hálózati virtualizálási átfedésben van, hogy elszigetelje a felhő-átjárót és annak összes csatlakoztatott eszközét vagy helyszíni átjáróját bármilyen más hálózati forgalomból. Maga a Felhőbeli átjáró nem eszköz-vagy adatfeldolgozási vagy-tárolási létesítmény; Ezek a létesítmények a Cloud Gateway felülettel rendelkeznek. A felhőalapú átjáró zónája magába foglalja a felhő-átjárót, valamint az összes helyszíni átjárót és eszközt, amely közvetlenül vagy közvetve hozzá van rendelve. A zóna szegélye egy különálló terület, ahol az összes külső fél kommunikál.

### <a name="the-services-zone"></a>A szolgáltatások zóna

A "Service" definiálva van ehhez a környezethez, mint bármely olyan szoftver-összetevő vagy-modul, amely egy mező-vagy felhőalapú átjárón keresztül kapcsolódik az adatgyűjtéshez és az elemzéshez, valamint a parancs-és vezérlési szolgáltatáshoz. A szolgáltatások közvetítők. Az identitásuk az átjárók és más alrendszerek felé, az adatok tárolása és elemzése, az eszközökre vonatkozó, az adatelemzések vagy az ütemtervek alapján, valamint a jogosult végfelhasználók számára elérhető információ-és vezérlési képességekkel való önálló kiállítók.

### <a name="information-devices-versus-special-purpose-devices"></a>Információ – eszközök és speciális célú eszközök

A számítógépek, telefonok és tabletták elsősorban interaktív információs eszközök. A telefonok és a tabletták kifejezetten optimalizálva vannak az akkumulátorok élettartamának maximalizálására. Lehetőleg kikapcsolhatják részben, ha nem kapcsolódnak azonnal egy személyhez, vagy ha nem biztosítanak olyan szolgáltatásokat, mint a zene lejátszása vagy a tulajdonosuk egy adott helyre való irányítása. A rendszerek szemszögéből ezek az információtechnológiai eszközök elsősorban proxyként működnek a felhasználók felé. A "People-indítószerkezetek" azt sugallják, hogy a műveletek és a "személyek érzékelők" begyűjtik a bemenetet.

A speciális célú eszközök, az egyszerű hőmérséklet-érzékelőktől kezdve a különböző, több ezer összetevővel rendelkező összetett gyárbeli üzemi vonalaktól eltérőek. Ezek az eszközök sokkal nagyobb hatókörben vannak, és akkor is, ha bizonyos felhasználói felületet biztosítanak, nagyrészt a fizikai világban lévő eszközökhöz való kapcsolódásra, illetve azok integrálására terjednek ki. Ezek a környezeti körülményeket mérik és jelentik, bekapcsolják a szelepeket, vezérlik a szervókat, a hangriasztásokat, a fények váltását és sok más feladatot. Olyan munkát tesznek lehetővé, amellyel egy információs eszköz túl általános, túl drága, túl nagy vagy túl törékeny. A konkrét cél azonnal a technikai kialakítását, valamint a rendelkezésre álló pénzügyi költségvetést a termelési és ütemezett életciklus-művelethez. Ennek a két kulcsfontosságú tényezőnek a kombinációja korlátozza a rendelkezésre álló működési energia-költségvetést, a fizikai lábnyomot, és így elérhető tárolási, számítási és biztonsági képességeket.

Ha valami "hibás", az automatizált vagy távoli felügyelhető eszközökkel, például fizikai hibákkal, illetve a jogosulatlan behatolást és manipulációt befolyásoló hibás hibákkal. Az éles üzemben megsemmisíthető, hogy az épületek kifosztották vagy leírhatók, és a személyek megsérülhetnek vagy akár meghalnak is. Ez egy teljes, különböző típusú károsodás, mint valaki maxing egy ellopott hitelkártya-korlátot. A műveleteket elvégező eszközök biztonsági sávja, valamint azok az érzékelők, amelyek végül olyan parancsokat eredményeznek, amelyek a dolgok áthelyezését okozzák, magasabbnak kell lenniük, mint bármely e-kereskedelmi vagy banki forgatókönyv esetében.

### <a name="device-control-and-device-data-interactions"></a>Az eszközök és az eszközök közötti adatkapcsolatok

A csatlakoztatott speciális célú eszközök jelentős számú potenciális interakciós felülettel és interakciós mintákkal rendelkeznek, és mindegyiknek figyelembe kell vennie az eszközök digitális elérésének biztosításához szükséges keretrendszert. A "digitális hozzáférés" kifejezés azért használatos, hogy megkülönböztesse azokat a műveleteket, amelyek a közvetlen eszköz interakcióján keresztül valósulnak meg, ha a hozzáférés biztonságát fizikai hozzáférés-vezérléssel biztosítjuk. Tegyük fel például, hogy az eszközt egy lakattal rendelkező helyiségbe helyezi. Habár a fizikai hozzáférés nem tagadható meg a szoftver és a hardver használatával, a rendszer meggátolja, hogy a fizikai hozzáférés megakadályozza a rendszerbeavatkozást.

Ahogy az interakciós mintákat vizsgálja, tekintse meg az "eszköz vezérlése" és az "eszköz adatmennyisége" kifejezést, miközben a veszélyforrások modellezése is megfigyelhető. Az "eszközök vezérlése" a bármely fél által az eszközre adott bármilyen információ besorolva, amelynek célja, hogy megváltoztatja vagy befolyásolja a viselkedését az állapotára vagy a környezetének állapotára vonatkozóan. Az "eszköz adatai" olyan információk besorolására alkalmasak, amelyeket az eszköz az állapotáról és a környezete megfigyelt állapotáról bocsát ki.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Veszélyforrások modellezése az Azure IoT hivatkozási architektúrája esetében

A Microsoft a korábban ismertetett keretrendszert használja az Azure IoT kapcsolatos veszélyforrások modellezéséhez. A következő szakasz az Azure IoT Reference Architecture konkrét példáját használja annak bemutatására, hogyan gondolja át a IoT veszélyforrások modellezését, és hogy miként lehet kezelni az azonosított fenyegetéseket. Ez a példa a fókusz négy fő területét azonosítja:

* Eszközök és adatforrások,
* Adatátviteli szolgáltatás,
* Eszközök és események feldolgozása, valamint
* Megjelenítés

![Veszélyforrások modellezése az Azure IoT](media/iot-security-architecture/iot-security-architecture-fig2.png)

A következő ábra a Microsoft IoT architektúrájának egyszerűsített nézetét mutatja be a Microsoft Threat Modeling Tool által használt adatfolyam-diagram modell használatával:

![Veszélyforrások modellezése az Azure IoT az MS Threat Modeling Tool használatával](media/iot-security-architecture/iot-security-architecture-fig3.png)

Fontos megjegyezni, hogy az architektúra elkülöníti az eszköz és az átjáró képességeit. Ez a módszer lehetővé teszi a felhasználó számára, hogy a biztonságosabb átjárókat használja: képesek legyenek a biztonságos protokollok használatával kommunikálni a felhő-átjáróval, ami általában nagyobb feldolgozási terhelést igényel, amelyet a natív eszköz – például a termosztát – saját maga biztosíthat. Az Azure-szolgáltatások zónában tegyük fel, hogy a Felhőbeli átjárót az Azure IoT Hub szolgáltatás képviseli.

### <a name="device-and-data-sourcesdata-transport"></a>Eszköz-és adatforrások/adatátviteli szolgáltatás

Ez a szakasz a korábban a veszélyforrások modellezése során felvázolt architektúrát vizsgálja, és áttekintést nyújt arról, hogyan kell kezelni az egyes rejlő problémákat. Ez a példa egy fenyegetési modell alapvető elemeire koncentrál:

* Folyamatok (a vezérlő és a külső elemek alatt)
* Kommunikáció (más néven adatfolyamatok)
* Storage (más néven adattárak)

#### <a name="processes"></a>Folyamatok

Az Azure IoT architektúrában ismertetett kategóriák mindegyikében a következő példa megpróbál számos különböző veszélyforrást elhárítani a különböző fázisokban lévő adatok/információk között: folyamat, kommunikáció és tárolás. Az alábbiakban áttekintjük a "folyamat" kategóriához tartozó leggyakoribb tudnivalókat, majd egy áttekintést arról, hogy ezek a fenyegetések hogyan lehetségesek a legjobban enyhíteni:

**Hamisítás (ok)**: egy támadó kinyerheti a titkosítási kulcs anyagát egy eszközről a szoftver vagy a hardver szintjén, majd ezt követően egy másik fizikai vagy virtuális eszközzel fér hozzá a rendszerhez az eszköz identitása alatt. A jó illusztráció olyan távoli vezérlők, amelyek bármilyen TV-t és népszerű tréfacsináló-eszközt is kikapcsolhatnak.

A **szolgáltatás megtagadása (D)**: az eszközök nem tudnak működni vagy kommunikálni a rádiófrekvenciák vagy a kivágási vezetékek zavarásával. Például egy felügyeleti kamera, amely az áramellátással vagy a hálózati kapcsolattal szándékosan kiütötte, nem tud jelentést készíteni.

**Illetéktelen módosítás (T)**: a támadó részlegesen vagy teljes mértékben lecserélheti az eszközön futó szoftvert, ami lehetővé teszi, hogy a lecserélt szoftver kihasználja az eszköz valódi identitását, ha a kulcsfontosságú anyagok vagy a kulcsfontosságú anyagokat birtokló titkosítási létesítmények elérhetővé válnak a tiltott program számára. Előfordulhat például, hogy egy támadó kinyerte a kulcsfontosságú anyagokat a kommunikációs útvonalon lévő adatok elfogására és letiltására, és az ellopott kulcs anyagával hitelesített hamis adatokkal helyettesíti azokat.

**Információ-közzététel (I)**: Ha az eszköz manipulált szoftvert futtat, az ilyen manipulált szoftverek esetleg illetéktelen feleknek is elvégezhetik az adatokat. Előfordulhat például, hogy egy támadó kinyerte a kulcsfontosságú anyagokat, hogy az eszköz és a vezérlő vagy a mező átjárója vagy a Felhőbeli átjáró közötti kommunikációs útvonalon beinjektálja az adatokat a szifon kikapcsolásához.

**Jogosultságszint-emelés (E)**: egy adott függvényt használó eszköz kényszeríthető valamilyen másra. Például, ha egy olyan szelepet nyitunk meg, amely a nyílt félidőben van kiválasztva, megnyithatja az egész utat.

| **Összetevő** | **Fenyegetés** | **Kockázatcsökkentés** | **Kockázat** | **Implementálás** |
| --- | --- | --- | --- | --- |
| Eszköz |S |Identitás kiosztása az eszközhöz és az eszköz hitelesítése |Az eszköz vagy az eszköz egy részének cseréje egy másik eszközre. Honnan tudhatja, hogy a megfelelő eszközről van szó? |Az eszköz hitelesítése Transport Layer Security (TLS) vagy IPSec használatával. Az infrastruktúrának támogatnia kell az előmegosztott kulcs (PSK) használatát azokon az eszközökön, amelyek nem képesek a teljes aszimmetrikus titkosítás kezelésére. Az Azure AD és a [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) kihasználása |
|| TRID |Alkalmazzon tamperproof mechanizmusokat az eszközre, például azáltal, hogy a kulcsokat és egyéb titkosítási anyagokat nem lehet kinyerni az eszközről. |A kockázat az, hogy valaki illetéktelenül módosítja az eszközt (fizikai interferencia). Hogyan biztos benne, hogy az eszköz nem lett illetéktelenül módosítva. |A leghatékonyabb megoldás egy platformmegbízhatósági modul (TPM) képesség, amely lehetővé teszi a kulcsok tárolását olyan speciális, chipes áramkörben, amelyből a kulcsok nem olvashatók, de csak olyan titkosítási műveletekhez használhatók, amelyek használják a kulcsot, de soha nem hozzák nyilvánosságra a kulcsot. Az eszköz memóriájának titkosítása. Az eszköz kulcskezelő szolgáltatásának kezelése. A kód aláírása. |
|| E |Az eszköz hozzáférés-vezérlése. Engedélyezési séma. |Ha az eszköz lehetővé teszi, hogy az egyes műveleteket egy külső forrásból származó, illetve akár sérült érzékelők parancsai alapján végezze el, az lehetővé teszi, hogy a támadás más módon nem hozzáférhető műveleteket hajtson végre. |Engedélyezési séma az eszközhöz |
| Mező átjáró |S |A helyszíni átjáró hitelesítése a Felhőbeli átjárón (például CERT-alapú, PSK vagy jogcím alapján) |Ha valaki meghamisítja a Field Gatewayt, akkor azt bármely eszközként bejelenthetheti. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Az eszközök általános tárolási és igazolási problémái általában – a legjobb esetben a TPM használata. 6LowPAN-bővítmény az IPSec számára a vezeték nélküli érzékelő hálózatok (WSN) támogatásához. |
|| TRID |A helyszíni átjáró védelmének megakadályozása a jogosulatlan hozzáférés ellen (TPM?) |A Felhőbeli átjárót támadó támadásokkal kapcsolatos hamisítások, amelyek arra utalnak, hogy a helyszíni átjáróval kommunikálnak, az adatokhoz való illetéktelen hozzáférés |Memória titkosítása, TPM, hitelesítés. |
|| E |A Field Gateway hozzáférés-vezérlési mechanizmusa | | |

Íme néhány példa a veszélyforrásokra ebben a kategóriában:

**Hamisítás**: a támadó kinyerheti a titkosítási kulcs anyagát egy eszközről a szoftver vagy a hardver szintjén, majd ezt követően egy másik fizikai vagy virtuális eszközzel fér hozzá a rendszerhez az eszköz identitása alatt.

**Szolgáltatásmegtagadás**: egy eszköz nem tud működni, illetve kommunikálni a rádiófrekvenciák vagy a kivágási vezetékek zavarásával. Például egy felügyeleti kamera, amely az áramellátással vagy a hálózati kapcsolattal szándékosan kiütötte, nem tud jelentést készíteni.

**Illetéktelen módosítás**: a támadó részlegesen vagy teljes mértékben lecserélheti az eszközön futó szoftvert, ami lehetővé teszi, hogy a kicserélt szoftver kihasználja az eszköz valódi identitását, ha a kulcsfontosságú anyagok vagy a kulcsfontosságú anyagokat birtokló titkosítási létesítmények elérhetővé válnak a tiltott program számára.

**Illetéktelen módosítás**: egy olyan megfigyelő kamera, amely egy üres folyosó látható spektrumú képét jeleníti meg, egy ilyen előszoba fényképét célozza meg. A füst-vagy tűzjelzők jelenthetnek olyan személyeket, akik könnyebben megtartják az informatikai szervezetet. Az eszköz mindkét esetben műszakilag teljesen megbízható a rendszer felé, de a manipulált információkat is jelenti.

**Illetéktelen** hozzáférés: a támadó kinyerte a kulcsfontosságú anyagokat a kommunikációs útvonalon tárolt adatok elfogására és letiltására, valamint a lopott kulcsokkal hitelesített, hamis adatokra cserélve.

**Illetéktelen módosítás**: a támadó részben vagy teljesen lecserélheti az eszközön futó szoftvereket, ami lehetővé teszi, hogy a kicserélt szoftver kihasználja az eszköz valódi identitását, ha a kulcsfontosságú anyagok vagy a kulcsfontosságú anyagokat tároló kriptográfiai létesítmények elérhetővé válnak a tiltott program számára.

**Információ-közzététel**: Ha az eszköz manipulált szoftvert futtat, akkor az ilyen manipulált szoftverek esetleg jogosulatlan feleknek is elvégezhetik az adatszivárgást.

**Információk közzététele**: a támadó kinyerte a kulcsfontosságú anyagokat, hogy az eszköz és a vezérlő vagy a mező átjárója és a Felhőbeli átjáró közötti kommunikációs útvonalon beinjektálja az adatokat a szifon kikapcsolásához.

**Szolgáltatás megtagadása**: az eszköz kikapcsolható, vagy olyan üzemmódba kapcsolható, amelyben a kommunikáció nem lehetséges (ami számos iparági gépen szándékos).

**Illetéktelen** hozzáférés: az eszköz újrakonfigurálható úgy, hogy az a vezérlőrendszer (az ismert kalibrációs paramétereken kívül) számára ismeretlen állapotba működjön, és így olyan adatokkal lássa el, amelyeket helytelenül lehet értelmezni.

**Jogosultságszint-emelési jogosultság**: egy adott függvényt használó eszköz kényszeríthető más művelet végrehajtására. Például, ha egy olyan szelepet nyitunk meg, amely a nyílt félidőben van kiválasztva, megnyithatja az egész utat.

**Szolgáltatásmegtagadás**: az eszköz olyan állapotba állítható be, amelyben a kommunikáció nem lehetséges.

**Illetéktelen** hozzáférés: az eszköz újrakonfigurálható úgy, hogy a vezérlőrendszer ismeretlen állapotában működjön (az ismert kalibrációs paramétereken kívül), és így olyan adatokkal lássa el, amelyeket helytelenül lehet értelmezni.

**Hamisítás/illetéktelen módosítás/megtagadás**: Ha nem védett (ami ritkán fordul elő a fogyasztó távvezérlésével), a támadók névtelenül kezelhetik az eszközök állapotát. A jó illusztráció olyan távoli vezérlők, amelyek bármilyen TV-t és népszerű tréfacsináló-eszközt is kikapcsolhatnak.

#### <a name="communication"></a>Kommunikáció

Az eszközök, az eszközök és a helyszíni átjárók, valamint az eszközök és a Felhőbeli átjáró közötti kommunikációs útvonal elleni fenyegetések. Az alábbi táblázat az eszközön/VPN-en található nyitott szoftvercsatornák használatával kapcsolatos útmutatást tartalmaz:

| **Összetevő** | **Fenyegetés** | **Kockázatcsökkentés** | **Kockázat** | **Implementálás** |
| --- | --- | --- | --- | --- |
| Eszköz IoT Hub |TID |D TLS (PSK/RSA) a forgalom titkosításához |Az eszköz és az átjáró közötti kommunikáció lehallgatásának vagy megzavarásának megakadályozása |Biztonság a protokoll szintjén. Egyéni protokollok esetén meg kell állapítania, hogyan kell védelemmel ellátni. A legtöbb esetben a kommunikáció az eszközről a IoT Hubra történik (az eszköz kezdeményezi a kapcsolatot). |
| Eszközről az eszközre |TID |D TLS (PSK/RSA) a forgalom titkosításához. |Az eszközök közötti átvitel során beolvasott adatforgalom. Illetéktelenül módosítható az adatkezelés. Az eszköz túlterhelése új kapcsolatokkal |A protokoll szintjének biztonsága (MQTT/AMQP/HTTP/CoAP. Egyéni protokollok esetén meg kell állapítania, hogyan kell védelemmel ellátni. A DoS-fenyegetés enyhítése egy Felhőbeli vagy egy helyszíni átjárón keresztüli társ-eszköz, és csak a hálózat felé irányuló ügyfelekként működhet. Előfordulhat, hogy a társítás közvetlen kapcsolatot létesít a társak között, miután az átjáró felügyelte azt. |
| Külső entitás eszköz |TID |A külső entitás erős párosítása az eszközhöz |Az eszközhöz való kapcsolódás lehallgatásával. Az eszközzel folytatott kommunikáció megakadályozása |A külső entitás biztonságos párosítása az eszköz NFC/Bluetooth LE. Az eszköz operatív paneljének vezérlése (fizikai) |
| Field Gateway Cloud Gateway |TID |TLS (PSK/RSA) a forgalom titkosításához. |Az eszköz és az átjáró közötti kommunikáció lehallgatásának vagy megzavarásának megakadályozása |A protokoll szintjének biztonsága (MQTT/AMQP/HTTP/CoAP). Egyéni protokollok esetén meg kell állapítania, hogyan kell védelemmel ellátni. |
| Device Cloud Gateway |TID |TLS (PSK/RSA) a forgalom titkosításához. |Az eszköz és az átjáró közötti kommunikáció lehallgatásának vagy megzavarásának megakadályozása |A protokoll szintjének biztonsága (MQTT/AMQP/HTTP/CoAP). Egyéni protokollok esetén meg kell állapítania, hogyan kell védelemmel ellátni. |

Íme néhány példa a veszélyforrásokra ebben a kategóriában:

**Szolgáltatás megtagadása**: a korlátozott eszközök általában a DOS-fenyegetés alatt állnak, amikor aktívan figyelik a bejövő kapcsolatokat vagy a kéretlen datagramokat a hálózaton, mert egy támadó egyszerre több kapcsolatot nyithat meg, és nem végezheti el őket, és nem tudja őket lassan kiszolgálni, vagy ha az eszköz nem kért forgalommal van elárasztva. Mindkét esetben az eszköz gyakorlatilag működésképtelenné válik a hálózaton.

**Hamisítás, tájékoztatás**: a korlátozott eszközök és a speciális célú eszközök gyakran rendelkeznek egy az összes biztonsági szolgáltatással, például a jelszóval vagy a PIN-kóddal kapcsolatos védelemmel, vagy teljes mértékben támaszkodnak a hálózat megbízhatóságára, ami azt jelenti, hogy hozzáférést biztosítanak az információkhoz, amikor egy eszköz ugyanazon a hálózaton van, és a hálózatot gyakran csak megosztott kulcs védi. Ez azt jelenti, hogy amikor az eszköz vagy a hálózat közös titkát kizárták, az eszközt szabályozhatja, vagy megfigyelheti az eszközről kibocsátott adatmennyiséget.  

**Hamisítás**: a támadó feltartóztathatja vagy részlegesen felülbírálhatja a szórást, és meghamisíthatja a kezdeményezőt (az ember középen)

**Illetéktelen** hozzáférés: a támadó elfoghatja vagy részlegesen felülbírálhatja a szórást, és hamis adatokat küldhet 

**Információ-közzététel:** a támadók hallgatózik a szórást, és a szolgáltatás engedélyezési **szolgáltatásmegtagadás** nélkül szerezhetnek be adatokat: a támadó elakadhat a szórási jel, és megtagadhatja az információk terjesztését

#### <a name="storage"></a>Tárolás

Minden eszköz és mező átjárója rendelkezik valamilyen tárolási formával (ideiglenes az adatok sorba állításához, az operációs rendszer (OS) rendszerképének tárolásához).

| **Összetevő** | **Fenyegetés** | **Kockázatcsökkentés** | **Kockázat** | **Implementálás** |
| --- | --- | --- | --- | --- |
| Eszköz tárterülete |TRID |Storage-titkosítás, a naplók aláírása |Adatok olvasása a tárolóból (személyes adatok), a telemetria-adatokkal való illetéktelen módosítás. Az üzenetsor-kezelési vagy a gyorsítótárazott parancs-vezérlési adattal való illetéktelen módosítás. A konfigurációs vagy a belső vezérlőprogram frissítési csomagjainak a gyorsítótárba vagy helyileg történő gyorsítótárazása közben történő illetéktelen módosításával az operációs rendszer és/vagy a rendszerösszetevők biztonsága is sérül |Titkosítás, üzenet-hitelesítési kód (MAC) vagy digitális aláírás. Ahol lehetséges, az erőforrás-hozzáférés-vezérlési listák (ACL-ek) vagy engedélyek használatával történő erős hozzáférés-vezérlés. |
| Eszköz operációs rendszerének képe |TRID | |Az operációs rendszer/replacing az operációs rendszer összetevőinek illetéktelen módosítása |Írásvédett operációsrendszer-partíció, aláírt operációsrendszer-rendszerkép, titkosítás |
| Field Gateway Storage (az adatsorba állítása) |TRID |Storage-titkosítás, a naplók aláírása |Adatok olvasása a tárolóból (személyes adatok), a telemetria adatokkal való illetéktelen módosítás, a várólistára helyezett vagy a gyorsítótárazott parancs-vezérlési adatok módosítása. A konfigurációval vagy a belső vezérlőprogram frissítési csomagjaival (az eszközök vagy a mező-átjárók számára szánt) való illetéktelen hozzáférés a gyorsítótárazott vagy a helyileg várólistára kerül, az operációs rendszer és/vagy a rendszerösszetevők biztonsága érdekében |BitLocker |
| Field Gateway operációsrendszer-rendszerkép |TRID | |Az operációs rendszer/replacing az operációs rendszer összetevőinek illetéktelen módosítása |Írásvédett operációsrendszer-partíció, aláírt operációsrendszer-rendszerkép, titkosítás |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Eszköz-és esemény-feldolgozás/Felhőbeli átjáró zóna

A Cloud Gateway egy olyan rendszer, amely lehetővé teszi a távoli kommunikációt több különböző helyről az eszközökre vagy a helyszíni átjáróra a nyilvános hálózati térben, jellemzően a felhőalapú vezérlési és adatelemzési rendszer, az ilyen rendszerek összevonása felé. Bizonyos esetekben előfordulhat, hogy a Cloud Gateway azonnal megkönnyíti a speciális célú eszközökhöz, például a tablettákhoz vagy telefonokhoz való hozzáférést. Az itt tárgyalt kontextusban a "Cloud" kifejezés arra szolgál, hogy egy dedikált adatfeldolgozó rendszerre hivatkozzon, amely nem ugyanahhoz a helyhez van kötve, mint a csatlakoztatott eszközök vagy a helyszíni átjárók, és ahol a működési intézkedések meggátolják a célzott fizikai hozzáférést, de nem feltétlenül a "nyilvános felhő" infrastruktúrára. Előfordulhat, hogy egy Felhőbeli átjáró egy hálózati virtualizálási átfedésben van, hogy elszigetelje a felhő-átjárót és annak összes csatlakoztatott eszközét vagy helyszíni átjáróját bármilyen más hálózati forgalomból. Maga a Felhőbeli átjáró nem eszköz-vagy adatfeldolgozási vagy-tárolási létesítmény; Ezek a létesítmények a Cloud Gateway felülettel rendelkeznek. A felhőalapú átjáró zónája magába foglalja a felhő-átjárót, valamint az összes helyszíni átjárót és eszközt, amely közvetlenül vagy közvetve hozzá van rendelve.

A Cloud Gateway többnyire a kihelyezett végpontokkal rendelkező, a helyszíni átjáró és az eszközök csatlakoztatására szolgáló, beépített szoftvereket futtató szolgáltatás. Ennek megfelelően a biztonságot szem előtt tartva kell tervezni. A szolgáltatás tervezéséhez és létrehozásához kövesse az [SDL](https://www.microsoft.com/sdl) -folyamatot.

#### <a name="services-zone"></a>Szolgáltatási zóna

A vezérlőrendszer (vagy vezérlő) olyan szoftveres megoldás, amely egy eszközhöz, egy mezőhöz vagy egy helyszíni átjáróhoz, vagy a Felhőbeli átjáróhoz csatlakozik egy vagy több eszköz vezérléséhez, illetve az eszköz adatainak begyűjtéséhez és/vagy az adatok megjelenítéséhez, illetve az azt követő ellenőrzési célokra. A jelen vita hatálya alá tartozó egyetlen entitás a vezérlési rendszerek, amely azonnal megkönnyíti az emberekkel való interakciót. A kivételek köztes fizikai vezérlési felületek az eszközökön, például egy kapcsoló, amely lehetővé teszi, hogy egy személy kikapcsolja az eszközt, vagy más tulajdonságokat módosítson, és amelyeknél nincs olyan funkcionális egyenértékű, amely digitálisan elérhető.

A köztes fizikai vezérlési felületek olyanok, amelyekben a logika szabályozza a fizikai vezérlő felületének működését, például hogy egy egyenértékű függvényt távolról lehet kezdeményezni, vagy a távoli bemenettel való ütközés elkerülhető – az ilyen közbenső vezérlési felületek fogalmilag egy helyi vezérlési rendszerhez vannak csatolva, amely ugyanazt a mögöttes funkciót használja, mint bármely más távvezérlési rendszer, amelyet az eszköz párhuzamosan A Felhőbeli számítástechnika leggyakoribb fenyegetéseit a [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) lapon olvashatja.

## <a name="additional-resources"></a>További források

További információért tekintse át a következő cikkeket:

* [SDL Threat Modeling Tool](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Microsoft Azure IoT-hivatkozás architektúrája](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)