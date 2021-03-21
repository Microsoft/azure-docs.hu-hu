---
title: Az Azure IoT Hub X. 509 biztonságával kapcsolatos fogalmak | Microsoft Docs
description: Koncepció – az X. 509 hitelesítésszolgáltatói tanúsítványok értékének megértése a IoT-eszközök gyártásában és hitelesítésében.
author: eustacea
manager: arjmands
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/18/2017
ms.author: eustacea
ms.openlocfilehash: 877200cbafbe68fa6161025572abfddad651e172
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490720"
---
# <a name="conceptual-understanding-of-x509-ca-certificates-in-the-iot-industry"></a>Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok fogalmi megértése a IoT-iparágban

Ez a cikk az X. 509 hitelesítésszolgáltatók (CA) tanúsítványának értékét mutatja be a IoT-eszközök gyártásában és a IoT Hub történő hitelesítésben. Ide tartozik az ellátási lánc beállításával kapcsolatos információk, valamint az előnyök kiemelése.

Ez a cikk a következőket ismerteti:

* Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok és azok beszerzése

* X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány regisztrálása IoT Hub

* Gyártási ellátási lánc beállítása X. 509 CA-alapú hitelesítéshez

* Az X. 509 HITELESÍTÉSSZOLGÁLTATÓval aláírt eszközök csatlakoztatása IoT Hub

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="overview"></a>Áttekintés

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI hitelesítés olyan módszer, amellyel az eszközök hitelesítése IoT Hub egy olyan metódus használatával, amely jelentősen leegyszerűsíti az eszköz identitásának létrehozását és életciklus-felügyeletét az ellátási láncban.

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI hitelesítés megkülönböztető attribútuma egy-a-többhöz kapcsolat, amelyhez egy HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány rendelkezik alsóbb rétegbeli eszközökkel. Ez a kapcsolat lehetővé teszi, hogy tetszőleges számú eszközt regisztráljon a IoT Hub egy X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány egyszeri regisztrálásával, ellenkező esetben az eszközökhöz való csatlakozás előtt minden eszközre egyedi tanúsítványt kell regisztrálni. Ez az egy-a-többhöz kapcsolat egyszerűsíti az eszköz-tanúsítványok életciklus-kezelési műveleteit is.

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI hitelesítés egy másik fontos attribútuma az ellátási lánc logisztikájának egyszerűsítése. Az eszközök biztonságos hitelesítéséhez az szükséges, hogy mindegyik eszköz egyedi titkos kulcsot, például a megbízhatóság alapját adja. A tanúsítványalapú hitelesítésben ez a titok egy titkos kulcs. Egy tipikus eszköz-előállítási folyamat több lépést és gyámot is magában foglal. Az eszköz titkos kulcsainak biztonságos kezelése több gyámon keresztül és a megbízhatóság fenntartása nehéz és költséges. A hitelesítésszolgáltatók használata megoldja ezt a problémát úgy, hogy az egyes felügyelőket egy megbízhatósági láncba aláírja, nem pedig az eszköz titkos kulcsaival. Az egyes gyámok a gyártási folyamat során aláírják az eszközöket. A teljes eredmény egy optimális ellátási lánc, amely beépített elszámoltathatóságot biztosít a titkosítási lánc megbízhatósági láncának használatával. Érdemes megjegyezni, hogy ez a folyamat a legnagyobb biztonságot eredményezi, ha az eszközök védik egyedi titkos kulcsaikat. Ebből a célból arra buzdítjuk a hardveres biztonságos modulok (HSM) használatát, amely olyan titkos kulcsok belső generálására alkalmas, amely soha nem fog megjelenni a nap világosságában.

Ez a cikk teljes körű áttekintést nyújt az X. 509 HITELESÍTÉSSZOLGÁLTATÓI hitelesítés használatáról az ellátási lánc beállítása és az eszköz közötti kapcsolatok között, miközben a valós világ példáját használja az adatok megszilárdulása érdekében.

A beléptetési csoportokat az Azure IoT Hub Device Provisioning Service (DPS) használatával is használhatja az eszközök hubokba való kiosztásának kezeléséhez. A DPS X. 509 tanúsítványok kiépítéséhez való használatáról további információt az [oktatóanyag: több X. 509 eszköz kiépítése regisztrációs csoportokkal](../iot-dps/tutorial-custom-hsm-enrollment-group-x509.md)című témakörben talál.

## <a name="introduction"></a>Bevezetés

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány egy olyan digitális tanúsítvány, amelynek birtokosa más tanúsítványokat is aláírhat. Ez a digitális tanúsítvány X. 509, mert megfelel egy, az IETF RFC 5280 szabvány által előírt tanúsítvány-formázási szabványnak, és egy hitelesítésszolgáltató (CA), mert a tulajdonosa aláírhat más tanúsítványokat is.

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓ használatát egy konkrét példához képest legjobban érdemes értelmezni. Tekintse meg a céges X-et, amely professzionális telepítésre tervezett, intelligens X-widgetek készítője. A céges X a gyártást és a telepítést is kiszervezi. Az IT-szerződés gyártója a Factory-Y az intelligens X-widgetek és a Service Provider technikus-Z telepítéséhez szükséges. A vállalat X kívánsága, hogy a Smart-X-widget közvetlenül a Factory-Y-től a technikus-Z-ig szállítja a telepítést, és közvetlenül a vállalat-X IoT Hub a telepítés után csatlakozik a vállalat-x rendszerhez való további beavatkozás nélkül. Ennek elvégzéséhez a vállalati X-nek néhány egyszeri telepítési műveletet kell végrehajtania a Prime Smart-X-Widget használatával az automatikus kapcsolódáshoz. A teljes körű forgatókönyvet szem előtt tartva a cikk további részeit a következőképpen strukturáljuk:

* Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány beolvasása

* X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány regisztrálása IoT Hub

* Eszközök aláírása megbízhatósági láncba

* Eszköz csatlakoztatása

## <a name="acquire-the-x509-ca-certificate"></a>Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány beolvasása

A vállalat-X lehetőséggel X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt vásárolhat egy nyilvános legfelső szintű hitelesítésszolgáltatótól, vagy létrehozhat egy önaláírt folyamaton keresztül. Az alkalmazási forgatókönyvtől függően az egyik lehetőség optimális lehet a másikon. A beállítástól függetlenül a folyamat két alapvető lépést von maga után, a nyilvános/titkos kulcspár létrehozása és a nyilvános kulcs aláírása egy tanúsítványba.

![X509CA-tanúsítványok generálására szolgáló folyamat](./media/iot-hub-x509ca-concept/csr-flow.png)

A lépések elvégzésének részleteit különböző szolgáltatók biztosítják.

### <a name="purchasing-an-x509-ca-certificate"></a>X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány vásárlása

A HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megvásárlásának előnye, hogy egy jól ismert legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ megbízható harmadik fél, amely a IoT-eszközöknek a csatlakozáskor való törvényességét tanúsítja. Akkor válassza ezt a lehetőséget, ha intelligens X-widgetet szeretne használni harmadik féltől származó termékekkel vagy szolgáltatásokkal a IoT Hub való kezdeti kapcsolódás után.

X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány megvásárlásához a vállalat-X válasszon ki egy főtanúsítvány-szolgáltatási szolgáltatót. A "legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ" kifejezéshez tartozó internetes keresés jó eredményeket fog eredményezni. A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ útmutatást nyújt a céges X számára a nyilvános/titkos kulcspár létrehozásáról, valamint a szolgáltatásokhoz tartozó tanúsítvány-aláírási kérés (CSR) létrehozásáról. A CSR az a formális folyamat, amellyel a tanúsítványokat a hitelesítésszolgáltatótól kell alkalmazni. A vásárlás eredménye egy tanúsítvány, amely a hitelesítésszolgáltató tanúsítványa. Az X. 509 tanúsítványok elterjedtsége miatt a tanúsítvány valószínűleg az IETF RFC 5280 szabványnak megfelelően lett formázva.

### <a name="creating-a-self-signed-x509-ca-certificate"></a>Self-Signed X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozása

A Self-Signed X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozásának folyamata hasonló a vásárláshoz, kivéve, ha a külső gyártó, például a legfelső szintű hitelesítésszolgáltató. A példánkban a vállalat-X nem a legfelső szintű hitelesítésszolgáltató, hanem a hitelesítésszolgáltató tanúsítványát fogja aláírni. A vállalat – X esetén ezt a lehetőséget kiválaszthatja a teszteléshez, amíg készen nem áll a szolgáltatói tanúsítvány megvásárlására. A vállalat-X egy önaláírt X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt is használhat éles környezetben, ha az intelligens X-widget nem a IoT Hub kívüli harmadik féltől származó szolgáltatásokhoz való kapcsolódásra szolgál.

## <a name="register-the-x509-certificate-to-iot-hub"></a>Regisztrálja az X. 509 tanúsítványt IoT Hub

A vállalat-X regisztrálnia kell az X. 509 HITELESÍTÉSSZOLGÁLTATÓT, hogy IoT Hub, ahol az a kapcsolódáskor a Smart-X-widgetek hitelesítéséhez fog szolgálni. Ez egy egyszeri folyamat, amely lehetővé teszi tetszőleges számú intelligens X-widget-eszköz hitelesítését és kezelését. Ez egy egyszeri folyamat, mert egy-a-többhöz kapcsolat van a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány és az eszköz tanúsítványai között, amelyeket a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány vagy egy köztes tanúsítvány írt alá. Ez a kapcsolat az X. 509 HITELESÍTÉSSZOLGÁLTATÓI hitelesítési módszer egyik legfőbb előnyét képezi. A másik lehetőség, hogy feltölti az egyes tanúsítványok ujjlenyomatai megfelelnek minden egyes intelligens X-widget-eszközre, és ezzel felveszi a működési költségeket.

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány regisztrálása kétlépéses folyamat, a tanúsítvány feltöltése és a tanúsítvány igazolása.

![X509CA-tanúsítvány regisztrálása](./media/iot-hub-x509ca-concept/pop-flow.png)

### <a name="x509-ca-certificate-upload"></a>X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány feltöltése

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány feltöltési folyamata csak a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány feltöltése IoT Hub. IoT Hub a tanúsítványt egy fájlban várja. A vállalat – X egyszerűen feltölti a tanúsítványfájl-fájlt. A tanúsítványfájl semmilyen körülmények között nem tartalmazhat titkos kulcsot. A nyilvános kulcsokra épülő infrastruktúrát (PKI) szabályozó szabványok bevált eljárásai a vállalat és az X privát ismereteinek jelen esetben kizárólag az X vállalaton belül találhatók.

### <a name="proof-of-possession-of-the-certificate"></a>A tanúsítvány igazolása

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány, mint bármely digitális tanúsítvány, a lehallgatásra fogékony nyilvános információ. Ilyen esetben a lehallgatási folyamat feltartóztathat egy tanúsítványt, és megpróbálhatja saját maga feltölteni. A példánkban IoT Hub szeretné meggyőződni arról, hogy a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány vállalata-X feltöltése valóban a vállalati X-hez tartozik. Ezt úgy teheti meg, hogy kihívást jelentő vállalat – X, hogy igazolja, hogy valóban rendelkeznek a tanúsítvánnyal a tanúsítványon alapuló [(POP) folyamaton](https://tools.ietf.org/html/rfc5280#section-3.1)keresztül. A megtartást igazoló folyamat a saját titkos kulcsával IoT Hub véletlenszerű szám létrehozásával jár. Ha a Company-X a PKI ajánlott eljárásait követte, és a titkos kulcsát megvédte, akkor csak abban az esetben kellene helyesen válaszolni az igazoló kihívásra. IoT Hub folytatja az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány regisztrálását, miután a rendszer sikeres választ adott a birtokukban lévő kihívásra.

IoT Hub befejezte az X. 509 HITELESÍTÉSSZOLGÁLTATÓI regisztrációt, és sikeres választ kapott a betartást igazoló kihívásra.

## <a name="sign-devices-into-a-certificate-chain-of-trust"></a>Eszközök aláírása megbízhatósági láncba

A IoT használatához minden eszköznek egyedi identitással kell rendelkeznie. Ezek az identitások tanúsítvány-alapú hitelesítési sémák formájában vannak hitelesítve. A példánkban ez azt jelenti, hogy minden egyes intelligens X-Widgetnek egyedi eszköz-tanúsítvánnyal kell rendelkeznie. Hogyan működik a vállalati X beállítás a saját ellátási láncában?

Ennek egyik módja az, ha az intelligens X-widgetek előállításához szükséges tanúsítványokat előre generálja, és a megfelelő egyedi eszköz titkos kulcsait az ellátási lánc partnereivel bízza meg. A vállalati X esetében ez azt jelenti, hogy a Factory-Y és a technikus-Z bízza. Habár ez egy érvényes módszer, olyan kihívásokat tartalmaz, amelyeket le kell küzdeni a megbízhatóság biztosításához a következőképpen:

1. Ahhoz, hogy az eszköz titkos kulcsait az ellátási lánc partnereivel is meg lehessen osztani, a titkos kulcsokat nem használó PKI ajánlott eljárásai figyelmen kívül hagyva a megbízhatóságot az ellátási láncban. Olyan sajáttőke-rendszereket jelent, mint például a biztonságos szobák elhelyezése az eszköz titkos kulcsainak, valamint a folyamatok, például az időszakos biztonsági naplózások telepítése. Vegye fel a költségeket az ellátási láncba.

2. Az ellátási láncban lévő eszközök biztonságos elszámolása és az üzembe helyezésük később a telepítés során egy-az-egyhez feladat lesz minden, az eszköz egyedi tanúsítványának (így a titkos kulcsnak) az eszközre való kivonásához szükséges, a kulcs és az eszköz közötti pár. Ez kizárja az eszközök csoportosítását, kivéve, ha a csoportok fogalma kifejezetten a folyamatba van építve. A biztonságos számlázás és az eszközök életciklusának kezelése ezért nagy terhelést jelent. A példánkban a vállalat-X ezt a terhet viseli.

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány-hitelesítés elegáns megoldásokat kínál a fent felsorolt kihívásokra a Tanúsítványsablonok használatával. Egy tanúsítványlánc olyan HITELESÍTÉSSZOLGÁLTATÓTÓL származik, amely egy közbenső HITELESÍTÉSSZOLGÁLTATÓ aláírásával aláír egy másik közbenső HITELESÍTÉSSZOLGÁLTATÓT, és így tovább tart, amíg a végső közbenső HITELESÍTÉSSZOLGÁLTATÓ aláír egy eszközt. A példánkban a vállalat-X aláírja a Factory-Y-t, amely viszont aláírja a Telepítéskezelőt, amely végül aláírja a Smart-X-widgetet.

![Tanúsítványlánc-hierarchia](./media/iot-hub-x509ca-concept/cert-chain-hierarchy.png)

A láncban lévő tanúsítványok lépcsőzetesen jelennek meg a szolgáltató logikai kikapcsolása. Számos ellátási láncok követik ezt a logikai bekapcsolást, amelynek során minden közbenső HITELESÍTÉSSZOLGÁLTATÓ bejelentkezett a láncba az összes felsőbb szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány fogadása közben, és az utolsó közbenső HITELESÍTÉSSZOLGÁLTATÓ végül aláírja az egyes eszközöket, és a lánc összes tanúsítványát beadja az eszközre. Ez gyakran előfordul, ha a szerződés előállító vállalata egy adott gyárhoz tartozó gyárakkal rendelkező szervezeti hierarchiával rendelkezik. Bár a hierarchia több szint mélysége is lehet (például földrajz/terméktípus/gyártási vonal alapján), csak a végén lévő gyár fogja használni az eszközt, de a láncot a hierarchia tetején tartja karban.

Előfordulhat, hogy az alternatív láncok különböző köztes HITELESÍTÉSSZOLGÁLTATÓval működnek együtt az eszközzel, és ebben az esetben az eszközhöz kapcsolódó HITELESÍTÉSSZOLGÁLTATÓ befecskendezi a tanúsítványlánc tartalmát ezen a ponton. A hibrid modellek akkor is lehetségesek, ha a CA-nak csak néhány fizikai interakciója van az eszközzel.

A példánkban a Factory-Y és a technikus-Z is együttműködik a Smart-X-widgettel. Habár a céges X a Smart-X-widgettel rendelkezik, valójában nem kommunikál a teljes ellátási láncban. Az intelligens X-widget megbízhatósági lánca ezért magában foglalja a vállalat-X aláíró Factory-Y jelet, amely viszont a technikus-Z emblémát adja meg, amely a Smart-X-widget végső aláírását fogja biztosítani. A Smart-X-widget előállítása és telepítése magában foglalja a Factory-Y és a technikus-Z a megfelelő köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat az egyes és minden egyes intelligens X-widget aláírásához. Ennek a teljes folyamatnak a végeredménye olyan intelligens X-widgetek, amelyek egyedi eszköz-tanúsítványokkal és megbízhatósági lánctal rendelkeznek a vállalati X HITELESÍTÉSSZOLGÁLTATÓI tanúsítványhoz.

![Egy vállalat tanúsítványainak megbízhatósági lánca egy másik vállalat tanúsítványai között](./media/iot-hub-x509ca-concept/cert-mfr-chain.png)

Ez egy jó pont az X. 509 HITELESÍTÉSSZOLGÁLTATÓI módszer értékének áttekintéséhez. Ahelyett, hogy előállítson és kiadja a tanúsítványokat minden egyes intelligens X-widgethez az ellátási láncban, a vállalati X-nek csak egyszer kell aláírnia a Factory-Y-t. Ahelyett, hogy az eszköz életciklusa során minden eszközt nyomon kellene követnie, az X vállalat mostantól nyomon követheti és felügyelheti az eszközöket olyan csoportokon keresztül, amelyek természetesen az ellátási lánc folyamatán alapulnak.

Végül, de nem utolsósorban a HITELESÍTÉSSZOLGÁLTATÓ hitelesítési módszere infúzióba veszi a biztonságos elszámoltathatóságot az eszköz gyártási ellátási láncában. A tanúsítványlánc folyamata miatt a láncban lévő összes tag műveletei kriptográfiailag rögzítve és ellenőrizhetők.

Ez a folyamat olyan feltételezésekre támaszkodik, amelyeket a teljességhez kell feldolgozni. Az eszköz egyedi nyilvános/titkos kulcspár független létrehozását igényli, és a titkos kulcsot az eszközön belül védeni kell. Szerencsére, a biztonságos szilícium-chipek olyan hardveres biztonságos modulok (HSM) formájában működnek, amelyek képesek belsőleg létrehozni kulcsokat, és védik a titkos kulcsokat. A vállalat-X verzióhoz csak az egyik ilyen zsetont kell hozzáadnia a Smart-X-widget összetevő-anyagjegyzékéhez.

## <a name="device-connection"></a>Eszköz csatlakoztatása

A fenti korábbi fejezetek az eszközre való kapcsolódásra épülnek. Ha egyszerűen regisztrálnia kell egy X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt IoT Hub egyszer, hogyan csatlakozhatnak akár több millió eszközhöz, és hogyan lehet az első alkalommal hitelesíteni?  Egyszerű ugyanazzal a tanúsítvány-feltöltési és bizonyítási folyamattal, amelyet korábban az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány regisztrálásával észlelt.

Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI hitelesítéshez gyártott eszközök az eszköz egyedi tanúsítványainak és egy tanúsítványlánc alapján vannak ellátva a megfelelő gyártási ellátási láncból. Az eszközhöz való csatlakoztatás még a legelső alkalommal is megtörténik egy kétlépéses folyamat során: a tanúsítványlánc feltöltése és a birtokon tartásának igazolása.

A tanúsítványlánc feltöltése során az eszköz feltölti az eszköz egyedi tanúsítványát a IoT Hubbe telepített tanúsítványláncot együtt. Az előre regisztrált X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány használatával a IoT Hub kriptográfiai módon ellenőrizheti néhány dolgot, hogy a feltöltött tanúsítványlánc belső konzisztens-e, és hogy a láncot az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány érvényes tulajdonosa hozta-e. Az X. 509 HITELESÍTÉSSZOLGÁLTATÓI regisztrációs folyamat során a IoT Hub egy próbára vonatkozó kérdés-válasz eljárást kezdeményez, amely megállapítja, hogy a lánc, és így az eszköz tanúsítványa ténylegesen az eszköz feltöltéséhez tartozik. Ezt úgy teheti meg, hogy egy véletlenszerű kihívást hoz létre, amelyet az eszköz a saját titkos kulcsával ír alá a IoT Hub általi érvényesítéshez. A sikeres válasz-eseményindítók IoT Hub az eszköz eredetiként való elfogadásához és az informatikai kapcsolatok biztosításához.

A példánkban minden egyes intelligens X-widget feltölti az eszköz egyedi tanúsítványát a Factory-Y és a technikus-Z X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokkal együtt, majd válaszol a IoT Hubtől kapott bizonyítási feladatra.

![Folyamat az egyik tanúsítványról a másikra, a hub pop-kihívása](./media/iot-hub-x509ca-concept/device-pop-flow.png)

Figyelje meg, hogy a bizalmi kapcsolat alapja a titkos kulcsok védelme, beleértve az eszköz titkos kulcsait. Ezért nem lehet eléggé hangsúlyozni, hogy a biztonságos szilícium-zsetonok a hardveres biztonságos modulok (HSM) formájában biztonságosak legyenek az eszköz titkos kulcsainak védelme érdekében, valamint a titkos kulcsok megosztásának általános bevált gyakorlatát, mint például az egyik gyár, amely egy másikat Bízzon meg a titkos kulccsal.
