---
title: X. 509 tanúsítványalapú hitelesítés Service Fabric fürtben
description: Ismerje meg a tanúsítványalapú hitelesítést Service Fabric-fürtökben, valamint a tanúsítványokkal kapcsolatos problémák észlelését, enyhítését és javítását.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 2d94e5cc78afbabde38eb38e0c4f89381bd67167
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729691"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>X. 509 tanúsítványalapú hitelesítés Service Fabric-fürtökben

Ez a cikk a [Service Fabric-fürt biztonságának](service-fabric-cluster-security.md)bevezetését egészíti ki, és bekerül a tanúsítványalapú hitelesítés részleteibe Service Fabric-fürtökben. Feltételezzük, hogy az olvasó ismeri az alapvető biztonsági fogalmakat, valamint azokat a vezérlőket is, amelyeket a Service Fabric a fürt biztonságának szabályozására tesz elérhetővé.  

A cím alá tartozó témakörök:

* Tanúsítványalapú hitelesítés alapjai
* Identitások és saját szerepkörök
* Tanúsítvány-konfigurációs szabályok
* Hibaelhárítás és gyakran ismételt kérdések

## <a name="certificate-based-authentication-basics"></a>Tanúsítványalapú hitelesítés alapjai
Rövid Frissítőként a tanúsítvány egy olyan eszköz, amely egy entitásra (a tárgyra) vonatkozó információk kötését jelenti, amelyek egy pár aszimmetrikus titkosítási kulcs birtokában vannak, és így a nyilvános kulcsú titkosítás alapszintű szerkezetét képezik. A tanúsítvány által képviselt kulcsok az adatok védelmére, illetve a kulcstárolók identitásának igazolására használhatók. a nyilvános kulcsokra épülő infrastruktúra (PKI) rendszerével együtt használva a tanúsítványok a tulajdonos további funkcióit is jelezhetik, például az internetes tartomány tulajdonjogát, vagy a tanúsítvány kibocsátója által biztosított bizonyos jogosultságokat (más néven hitelesítésszolgáltató, CA). A tanúsítványok közös alkalmazása a Transport Layer Security (TLS) titkosítási protokollt támogatja, amely lehetővé teszi a biztonságos kommunikációt a számítógépes hálózaton keresztül. Az ügyfél és a kiszolgáló pedig tanúsítványokat használ a kommunikáció titkosságának és integritásának biztosításához, valamint a kölcsönös hitelesítés végrehajtásához is.

Service Fabric a fürt alapvető rétege (összevonás) a TLS-t (más protokollok között) is létrehozza a résztvevő csomópontok megbízható és biztonságos hálózatának eléréséhez. A fürthöz Service Fabric ügyfél API-kon keresztül létesített kapcsolatok a TLS-t is használják a forgalom elleni védelemhez, valamint a felek identitásának létrehozásához is. Pontosabban, ha a Service Fabric hitelesítéshez használatos, a tanúsítvány a következő jogcímek bizonyítására használható: a) a tanúsítvány hitelesítő adatainak tulajdonosa rendelkezik a tanúsítvány "b" titkos kulcsával. a tanúsítvány SHA-1 kivonata ("ujjlenyomata") megfelel a fürt definíciójában szereplő deklarációnak, vagy c) a tanúsítvány megkülönböztető tulajdonos köznapi neve megegyezik a fürt definíciójában szereplő deklarációval , és a tanúsítvány kiállítója ismert vagy megbízható.

A fenti listában a "b" az "ujjlenyomat-rögzítés" néven ismert. Ebben az esetben a deklaráció egy adott tanúsítványra hivatkozik, és a hitelesítési séma erőssége arra a feltételezésre támaszkodik, hogy a számítási feladatokkal nem valósítható meg egy olyan tanúsítvány hamisítása, amely ugyanazt a kivonatoló értéket állítja elő, miközben az összes többi tekintetben érvényes, jól formázott objektum marad. A "c" elem a tanúsítvány deklarálása egy másik formáját jelöli, ahol a séma erőssége a tanúsítvány és a kiállító hatóság kombinációján nyugszik. Ebben az esetben a deklaráció egy tanúsítvány osztályára vonatkozik – az azonos tulajdonságokkal rendelkező két tanúsítvány teljes mértékben egyenértékűnek tekintendő. 

A következő szakaszokban részletesen ismertetjük, hogy az Service Fabric futtatókörnyezet hogyan használja és érvényesíti a tanúsítványokat a fürt biztonságának biztosítása érdekében.

## <a name="identities-and-their-respective-roles"></a>Identitások és saját szerepkörök
A hitelesítés részleteinek vagy a kommunikációs csatornák biztonságossá tételének megkezdése előtt fontos a résztvevő szereplők és a fürtben játszott megfelelő szerepkörök listázása:
- a Service Fabric futtatókörnyezet, amelyet "rendszernek" nevezünk: azoknak a szolgáltatásoknak a készletét, amelyek a fürtöt jelképező absztrakciókat és funkciókat biztosítanak. Amikor a rendszer-példányok közötti kommunikációra hivatkozik, a "fürt identitása" kifejezést fogjuk használni; Ha a fürtre a fürtön kívülről címzettként vagy a forgalom céljaként hivatkozik, a "kiszolgáló identitása" kifejezést fogjuk használni.
- üzemeltetett alkalmazások ("alkalmazások"): a fürt tulajdonosa által megadott kód, amely a fürtben van összehangolva és végrehajtva
- ügyfelek: az entitások a fürtkonfiguráció alapján csatlakozhatnak a fürthöz, és futtathatnak működést a fürtben. Megkülönböztetjük a két jogosultsági szint ("user" és "admin'") közötti különbséget. A "felhasználó" ügyfél elsődlegesen csak olvasási műveletekre korlátozódik (de nem az összes írásvédett funkcióra), míg az "admin'-ügyfél korlátlan hozzáféréssel rendelkezik a fürt működéséhez. (További részletekért tekintse meg a [Service Fabric-fürt biztonsági szerepköreit](service-fabric-cluster-security-roles.md).)
- (Csak az Azure-ban) a Service Fabric-szolgáltatások, amelyek összehangolják és teszik lehetővé a Service Fabric-fürtök működésének és felügyeletének irányítását, amelyet egyszerűen "szolgáltatásnak" nevezünk. A környezettől függően a "szolgáltatás" hivatkozhat az Azure Service Fabric erőforrás-szolgáltatóra vagy más, a Service Fabric csapata által birtokolt és üzemeltetett erőforrás-szolgáltatóra.

Egy biztonságos fürtben ezek a szerepkörök az előre meghatározott szerepkör neve és a hozzá tartozó hitelesítő adatok párosításával konfigurálhatók saját, különálló identitással. A Service Fabric támogatja a hitelesítő adatok tanúsítványként vagy tartományalapú szolgáltatásnévként való deklarálása. (A Windows-/Kerberos-based identitások is támogatottak, de a jelen cikk hatókörén kívül esnek a [Windows-alapú biztonság Service Fabric-fürtökben](service-fabric-windows-cluster-windows-security.md).) Az Azure-fürtökben az ügyfelek szerepkörei [Azure Active Directory-alapú identitásként](service-fabric-cluster-creation-setup-aad.md)is deklarálva lehetnek.

A fentieknek megfelelően a Service Fabric Runtime két jogosultsági szintet határoz meg egy fürtben: "admin'" és "user". A rendszergazda ügyfél és a "rendszer" összetevő is a "admin' jogosultságokkal" működik, így nem különböztethetők meg egymástól. A fürtön belüli kapcsolatok létrehozásakor a rendszer egy hitelesített hívót biztosít a két szerepkör Service Fabric futtatókörnyezete, amely a következő engedélyezés alapja. A következő fejezetekben részletesen megvizsgáljuk a hitelesítést.

## <a name="certificate-configuration-rules"></a>Tanúsítvány-konfigurációs szabályok
### <a name="validation-rules"></a>Érvényesítési szabályok
Egy Service Fabric-fürt biztonsági beállításai a következő szempontokat írják le:
- a hitelesítés típusa; Ez egy létrehozási idő, a fürt nem módosítható jellemzője. Ilyen beállítások például a következők: "ClusterCredentialType", "ServerCredentialType", és megengedett értékek: "None", "x509" vagy "Windows". Ez a cikk a x509-típus hitelesítésére koncentrál.
- a (hitelesítés) érvényesítési szabályok; ezeket a beállításokat a fürt tulajdonosa állítja be, és meghatározza, hogy mely hitelesítő adatokat fogadja el egy adott szerepkör. A példákat a rendszer közvetlenül az alábbi részletesen vizsgálja meg.
- a hitelesítés eredményének megváltoztatásához vagy finomhangolásához használt beállítások ilyenek például a tanúsítvány-visszavonási listák kényszerítését korlátozó jelzők (de-).

> [!NOTE]
> Az alábbiakban ismertetett fürtkonfiguráció a fürt jegyzékfájljának XML-formátumában található részletekből áll, mivel ez a legtöbb kivonatoló formátum, amely közvetlenül támogatja az ebben a cikkben leírt Service Fabric funkciót. Ugyanezek a beállítások közvetlenül is megadhatók egy fürt definíciójának JSON-ábrázolásában, akár egy önálló JSON-fürt jegyzékfájljában, akár egy Azure Resource Mangement-sablonban.

A tanúsítvány-ellenőrzési szabály a következő elemekből áll:
- a megfelelő szerepkör: ügyfél, rendszergazdai ügyfél (Kiemelt szerepkör)
- a szerepkör számára elfogadott hitelesítő adat ujjlenyomat vagy tulajdonos köznapi név alapján deklarálva

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Ujjlenyomat-alapú tanúsítvány-ellenőrzési nyilatkozatok
Ujjlenyomat-alapú érvényesítési szabályok esetén a fürtön vagy a-ben való kapcsolódást kérő hívó által megjelenített hitelesítő adatok a következőképpen lesznek érvényesítve:
  - a hitelesítő adat érvényes, jól formázott tanúsítvány: a lánca felépíthető, az aláírások egyeztetése
  - a tanúsítvány ideje érvényes (NotBefore <= most <
  - a tanúsítvány SHA-1 kivonata megfelel a deklarációnak, kis-és nagybetűk megkülönböztetése nélkül, az összes szóköz kivételével

A lánc létrehozásakor vagy érvényesítéskor észlelt megbízhatósági hibákat a rendszer letiltja az ujjlenyomat-alapú deklarációk esetében, kivéve a lejárt tanúsítványokat – Noha az adott esethez a céltartalékok is léteznek. A következőhöz kapcsolódó hibák: ismeretlen vagy offline állapotú, nem megbízható gyökér, érvénytelen kulcshasználat, a részleges lánc nem végzetes hibáknak minősül; Ebben az esetben az, hogy a tanúsítvány csupán egy kulcspár borítékja – a biztonság abban rejlik, hogy a fürt tulajdonosa a helyek területen beállította a titkos kulcs védelmét.

A fürt jegyzékfájljának következő részlete az ujjlenyomat-alapú érvényesítési szabályok egy részét példázza:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

A fenti bejegyzések mindegyike egy adott identitásra hivatkozik, a korábban leírtak szerint. az egyes bejegyzések több érték megadását is lehetővé teszik a karakterláncok vesszővel tagolt listájaként. Ebben a példában a beérkező hitelesítő adatok sikeres ellenőrzése után az SHA-1 ujjlenyomattal rendelkező tanúsítvány előadója 5ec... 4264 "a admin' szerepkört kapja meg; Ezzel szemben a hívó a következő tanúsítvánnyal hitelesíti a tanúsítványt: 7c8f... a 01b0 "felhasználói" szerepkört kap, amely kizárólag olvasási műveletekre korlátozódik. Egy bejövő hívó, amely olyan tanúsítványt mutat be, amelynek ujjlenyomata az "ABCD... 1234 "vagy" ef01... 5678 "a fürtben található társ-csomópontként lesz elfogadva. Végül pedig a fürt felügyeleti végpontját összekötő ügyfél a kiszolgálói tanúsítvány ujjlenyomatát a következőre fogja várni: "ef01... 5678 ". 

Ahogy korábban említettük, Service Fabric a lejárt tanúsítványok fogadására vonatkozó rendelkezéseket tesz. Ennek az az oka, hogy a tanúsítványok korlátozott élettartammal rendelkeznek, és ha az ujjlenyomat (amely egy adott tanúsítvány-példányra hivatkozik) alapján van deklarálva, a tanúsítvány lejáratának engedélyezése a fürthöz való csatlakozás vagy a fürt egy megfelelő összeomlása miatt sikertelen lesz. Az ujjlenyomattal rögzített tanúsítvány elforgatása vagy mellőzése túl egyszerű, és sajnos az ilyen helyzetből való helyreállítás nehéz.

Ebből a célból a fürt tulajdonosa explicit módon meghatározhatja, hogy az ujjlenyomattal deklarált önaláírt tanúsítványok érvényesnek minősülnek-e, az alábbiak szerint:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Ez a viselkedés nem terjed ki a CA által kiállított tanúsítványokra; Ebben az esetben egy visszavont, ismert – sérült lejárt tanúsítvány "érvényes" lehet, amint a HITELESÍTÉSSZOLGÁLTATÓ visszavont tanúsítványok listájában már nem szerepel, és így biztonsági kockázatot jelent. Önaláírt tanúsítványokkal a fürt tulajdonosa az egyetlen, a tanúsítvány titkos kulcsának védelméért felelős fél, amely nem a CA által kiállított tanúsítványok esetében fordul elő – a fürt tulajdonosa nem tudhatja, hogy a tanúsítványa hogyan vagy mikor lett bejelentve.

#### <a name="common-name-based-certificate-validation-declarations"></a>Köznapi név-alapú tanúsítvány-ellenőrzési deklarációk
A köznapi név alapú deklarációk a következő formák egyikét vehetik igénybe:
- tulajdonos köznapi neve (csak)
- tulajdonosi rögzítéssel rendelkező köznapi név

Először vegyünk fel egy részletet egy exemplifying a fürt jegyzékfájljában:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
A deklarációk a kiszolgáló és a fürt identitására vonatkoznak; vegye figyelembe, hogy a CN-alapú deklarációk a fürt jegyzékfájljának saját részeivel rendelkeznek, és elkülönítik a standard biztonsági szintet. Mindkét deklarációban a "név" a tanúsítvány megkülönböztető tulajdonos köznapi nevét jelöli, és az "érték" mező a várt kiállítót jelöli, az alábbiak szerint:

- az első esetben a deklaráció azt jelzi, hogy a kiszolgálói tanúsítvány megkülönböztető tulajdonosának köznapi név elemének meg kell egyeznie a következő karakterlánccal: "server.demo.system. servicefabric. Azure-int"; az üres "érték" mező azt jelzi, hogy a tanúsítványlánc gyökerének megbízhatónak kell lennie azon a csomóponton/gépen, amelyen a kiszolgálói tanúsítvány érvényesítve van; Windows rendszeren ez azt jelenti, hogy a tanúsítvány képes a "megbízható legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ" tárolóban telepített összes tanúsítvány összekapcsolására.
- a második esetben a deklaráció azt jelzi, hogy egy tanúsítvány előadója a fürt egyik társ csomópontja, ha a tanúsítvány köznapi neve megegyezik a "cluster.demo.system. servicefabric. Azure-int" karakterlánccal, *és* a tanúsítvány közvetlen kiállítójának ujjlenyomata megegyezik az "érték" mezőben található vesszővel tagolt bejegyzések egyikével. (Ez a Szabálytípus "köznapi név a kiállítói rögzítéssel" néven ismert.)

A tanúsítvány lánca mindkét esetben felépíthető, és a rendszer a várhatóan hibamentes lesz. Ez azt jelenti, hogy a visszavonási hibák, a részleges lánc vagy az idő – érvénytelen megbízhatósági hibák minősülnek végzetesnek, és a tanúsítvány ellenőrzése sikertelen lesz. A kiállítók rögzítése azt eredményezi, hogy a "nem megbízható legfelső szintű" állapotot nem végzetes hibaként veszi figyelembe. a megjelenések ellenére ez egy szigorúbb ellenőrzési űrlap, mivel lehetővé teszi, hogy a fürt tulajdonosa a saját PKI-re korlátozza a jogosult/elfogadott kiállítók készletét.

A tanúsítványlánc felépítése után a rendszer ellenőrzi, hogy a tanúsítvány egy szabványos TLS/SSL-házirenddel van-e érvényesítve, és a deklarált tulajdonos távoli névvel van-e ellátva. a tanúsítvány akkor minősül megfelelőnek, ha a tulajdonos köznapi neve vagy a tulajdonos alternatív neve megegyezik a fürt jegyzékfájljában található KN-deklarációval. A helyettesítő karakterek ebben az esetben támogatottak, és a karakterláncok egyeztetése a kis-és nagybetűk megkülönböztetése nélkül történik.

(Tisztázni kell, hogy a fent ismertetett sorozatot a tanúsítvány által deklarált egyes kulcshasználat típusoknál lehet végrehajtani; Ha a tanúsítvány megadja az ügyfél-hitelesítési kulcs használatát, a láncot először egy ügyfél-szerepkörhöz kell felépíteni és kiértékelni. Siker esetén a kiértékelés befejeződik, és az érvényesítés sikeres. Ha a tanúsítvány nem rendelkezik ügyfél-hitelesítéssel, vagy az ellenőrzés nem sikerült, a Service Fabric futtatókörnyezet létrehozza és kiértékeli a kiszolgáló hitelesítésének láncát.)

A példa elvégzéséhez az alábbi részlet szemlélteti az Ügyféltanúsítványok köznapi név szerinti deklarálása:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

A fenti deklarációk megfelelnek a rendszergazda és a felhasználói identitásnak; az ily módon deklarált tanúsítványok érvényesítése pontosan az előző példákkal, a fürt és a kiszolgáló tanúsítványainak leírásával történik.

> [!NOTE]
> Az általános név-alapú deklarációk célja, hogy leegyszerűsítse az elforgatást, és általánosságban a fürtözött tanúsítványok kezelését. A fürt folyamatos rendelkezésre állásának és biztonságának biztosításához azonban ajánlott a következő javaslatok betartása:
  * a kiállítói rögzítés előnyben részesített a megbízható gyökerekre való támaszkodás esetén
  * a kibocsátók különböző Erdőmbe való keveredésének elkerülése
  * Győződjön meg arról, hogy az összes várt kiállító szerepel a tanúsítvány deklarációjában. egy nem egyező kiállító hibát eredményez, ha az érvényesítés sikertelen lesz.
  * Győződjön meg arról, hogy a PKI tanúsítvány-házirend-végpontok felderíthetők, elérhetők és hozzáférhetők – ez azt jelenti, hogy az AIA, a CRL vagy az OCSP-végpontok deklarálva vannak a levél tanúsítványán, és elérhetők, hogy a tanúsítványlánc kiépítése befejeződjön.

A Service Fabric Runtime az X. 509 tanúsítványokkal védett fürthöz való csatlakozásra vonatkozó kérelem fogadása mellett a fürt biztonsági beállításait fogja használni a távoli fél hitelesítő adatainak ellenőrzéséhez a fent leírtak szerint. Ha ez sikeres, a hívó/távoli fél hitelesítésnek minősül. Ha a hitelesítő adat több érvényesítési szabálynak is megfelel, a futtatókörnyezet megadja a hívónak a megfeleltetett szabályok legmagasabb jogosultsági szintű szerepkörét. 

### <a name="presentation-rules"></a>Megjelenítési szabályok
Az előző szakasz azt ismerteti, hogyan működik a hitelesítés a tanúsítványokkal védett fürtben; Ez a szakasz azt ismerteti, hogy a Service Fabric futtatókörnyezet hogyan felfedezzék és betölti a fürtön belüli kommunikációhoz használt tanúsítványokat. ezeket a "bemutató" szabályokat nevezzük.

Az érvényesítési szabályokhoz hasonlóan a megjelenítési szabályok határozzák meg a szerepkört és a kapcsolódó hitelesítő adatokat, amelyek ujjlenyomat vagy köznapi név alapján vannak megadva. Az érvényesítési szabályoktól eltérően a köznapi név-alapú deklarációk nem rendelkeznek a kiállítói rögzítésre vonatkozó rendelkezésekkel; Ez nagyobb rugalmasságot és jobb teljesítményt tesz lehetővé. A megjelenítési szabályok deklarálva vannak a fürt jegyzékfájljának "NodeType" szakaszában, minden egyes különböző csomópont-típus esetében. a beállítások a fürt biztonsági szakaszaiból vannak felosztva, így az egyes csomópont-típusok teljes konfigurációját egyetlen szakaszban lehet megtenni. Az Azure Service Fabric-fürtökben a csomópont típusú tanúsítvány deklarációi alapértelmezés szerint a fürt definíciójának biztonság szakaszában a megfelelő beállításokra vonatkoznak.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Ujjlenyomat-alapú tanúsítvány-megjelenítési nyilatkozatok
Az előzőekben leírtaknak megfelelően a Service Fabric futtatókörnyezet megkülönbözteti a szerepkört a fürt más csomópontjainak társának és a fürt felügyeleti műveleteinek a kiszolgálója között. Elvileg ezeket a beállításokat külön lehet konfigurálni, de a gyakorlatban általában össze vannak igazítva. A cikk hátralévő részében feltételezzük, hogy az egyszerűség kedvéért megegyeznek a beállítások.

Tegyük fel, hogy a következő részletet vesszük figyelembe a fürt jegyzékfájljában:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
A "ClusterCertificate" elem a teljes sémát mutatja be, beleértve a választható paramétereket ("X509FindValueSecondary") vagy a megfelelő alapértékekkel rendelkezőket ("X509StoreName"); a többi deklaráció rövidített űrlapot mutat be. A fürt tanúsítványa a fentiekben kijelenti, hogy a "nt1vm" típusú csomópontok biztonsági beállításai inicializálva lettek a következő tanúsítvánnyal: "cc71.. 1984 "elsődlegesként, a" 49e2... 19d6 "tanúsítványa másodlagosként; mindkét tanúsítványnak szerepelnie kell a LocalMachine \' saját tanúsítványtárolójában (vagy a Linux-megfelelő elérési úton, a *var/lib/sfcerts*).

#### <a name="common-name-based-certificate-presentation-declarations"></a>Köznapi név alapú tanúsítvány-megjelenítési deklarációk
A csomópont típusú tanúsítványokat a tulajdonos köznapi neve is deklarálhatja, az alábbi példának megfelelően:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Bármelyik deklaráció esetében egy Service Fabric csomópont beolvassa a konfigurációt az indításkor, megkeresi és betölti a megadott tanúsítványokat, és a NotBefore attribútum csökkenő sorrendjében rendezi őket. a lejárt tanúsítványokat a rendszer figyelmen kívül hagyja, és a lista első eleme van kiválasztva az ügyfél hitelesítő adataiként a csomópont által megkísérelt bármely Service Fabric-kapcsolatban. (Valójában Service Fabric a legutóbb kiállított tanúsítványt támogatja.)

> [!NOTE]
> A 7.2.445 (7,2 CU4) verzió előtt Service Fabric kiválasztotta a legtávolabbi lejáró tanúsítványt (a legtávolabbi "nem tAfter" tulajdonsággal rendelkező tanúsítványt).

Vegye figyelembe, hogy a köznapi név alapú megjelenítési deklarációk esetében a tanúsítvány akkor minősül megfelelőnek, ha a tulajdonos köznapi neve megkülönbözteti a kis-és nagybetűket megkülönböztető X509FindValue (vagy X509FindValueSecondary). Ez ellentétben áll az érvényesítési szabályokkal, amely támogatja a helyettesítő karakterek egyeztetését, valamint a kis-és nagybetűk megkülönböztetését.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Egyéb tanúsítvány-konfigurációs beállítások
Korábban már említettük, hogy egy Service Fabric-fürt biztonsági beállításai is lehetővé teszik a hitelesítési kód viselkedésének finom megváltoztatását. A [Service Fabric fürtkonfiguráció](service-fabric-cluster-fabric-settings.md) című cikk a beállítások átfogó és legnaprakészebb listáját jeleníti meg, és az itt látható néhány biztonsági beállítás kiválasztásával bővítjük a tanúsítvány alapú hitelesítés teljes elérhetővé tételét. Minden beállításhoz meg fogjuk magyarázni a szándékot, az alapértelmezett értéket/viselkedést, a hitelesítést, valamint az elfogadható értékeket.

Ahogy említettük, a tanúsítvány ellenőrzése mindig magában foglalja a tanúsítvány láncának kiépítése és kiértékelése. A CA által kiállított tanúsítványok esetében ez a látszólag egyszerű operációsrendszer-API-hívás általában több kimenő hívást eredményez a nyilvános kulcsokra épülő PKI különböző végpontjai, a válaszok gyorsítótárazása és így tovább. A tanúsítvány-ellenőrzési hívások gyakorisága Service Fabric-fürtben a PKI-végpontok bármely problémája csökkentheti a fürt rendelkezésre állását, vagy lerövidítheti a lebontást. Amíg a kimenő hívásokat nem lehet letiltani (lásd alább a gyakori kérdések című szakaszt), a következő beállításokkal elvégezhető a CRL-hívások meghibásodása által okozott érvényesítési hibák maszkolása.

  * CrlCheckingFlag – a "biztonság" szakaszban a sztringet UINT konvertálták. Ennek a beállításnak az értékét a Service Fabric a tanúsítványlánc állapotával kapcsolatos hibák kiszűrésére használja a lánc kiépítése viselkedésének megváltoztatásával; a rendszer átadja a Win32 CryptoAPI [CertGetCertificateChain](/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) hívásának a "dwFlags" paraméternek, és a függvény által elfogadott jelzők érvényes kombinációja lehet. A 0 érték kikényszeríti a Service Fabric futtatókörnyezetet, hogy figyelmen kívül hagyja a megbízhatósági állapottal kapcsolatos hibákat – ez nem ajánlott, mivel a használata jelentős biztonsági kockázatot jelenthet. Az alapértelmezett érték a 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Mikor érdemes használni: helyi teszteléshez olyan önaláírt tanúsítványokkal vagy fejlesztői tanúsítványokkal, amelyek nincsenek teljesen kialakítva/nem rendelkeznek megfelelő nyilvános kulcsokra épülő infrastruktúrával a tanúsítványok támogatásához. A Erdőmbe közötti váltás során a gapped környezetekben is használhatja a megoldást.

  Használat: egy példa arra, hogy a visszavonási vizsgálat csak a gyorsítótárazott URL-címek elérését kényszeríti. Feltételezve
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  Ezután a fürt jegyzékfájljának deklarációja a következőket eredményezi:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError – a "biztonság" szakaszban található logikai érték, amelynek alapértelmezett értéke "false". A "visszavont kapcsolat nélküli" lánc kiépítési hibájának állapota (vagy egy további lánc házirend-érvényesítési hiba állapota) letiltására szolgáló parancsikont jelöl.

  Mikor kell használni: helyi tesztelés, vagy a megfelelő PKI által nem támogatott fejlesztői tanúsítványokkal. Gapped-környezetekben, vagy ha a nyilvános kulcsokra épülő infrastruktúra nem érhető el, a megoldást használja.

  Használat:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Egyéb jelentős beállítások (az összes "biztonság" szakaszban):
  * AcceptExpiredPinnedClusterCertificate – az ujjlenyomat-alapú tanúsítvány ellenőrzéséhez dedikált szakaszban tárgyalva; lehetővé teszi a lejárt önaláírt tanúsítványok fogadását. 
  * CertificateExpirySafetyMargin – intervallum, percben kifejezve a tanúsítvány még nem megfelelő időbélyegzője előtt, és amely alatt a tanúsítvány a lejárat kockázatának minősül. Service Fabric figyeli a fürtözött tanúsítvány (oka) t, és rendszeres időközönként kibocsátja az állapotjelentést a fennmaradó rendelkezésre állásuk alapján. A "biztonsági" intervallumban ezek az állapot-jelentések a "figyelmeztetés" állapotra vannak felemelve. Az alapértelmezett érték 30 nap.
  * CertificateHealthReportingInterval – a fürtcsomópontok hátralévő időtartamára vonatkozó állapotjelentés gyakoriságának szabályozása. A jelentések ezen az intervallumon belül csak egyszer lesznek kibocsátva. Az érték másodpercben kifejezve, alapértelmezett értéke pedig 8 óra.
  * EnforcePrevalidationOnSecurityChanges – Boolean (logikai) – a fürt frissítésének viselkedését szabályozza a biztonsági beállítások változásainak észlelése után. Ha a értéke "true" (igaz), a fürt frissítése megkísérli annak biztosítását, hogy legalább az egyik megjelenítési szabálynak megfelelő tanúsítvány is átadhat egy megfelelő érvényesítési szabályt. Az előérvényesítést az új beállítások bármely csomópontra való alkalmazása előtt hajtja végre a rendszer, de a frissítés megkezdésének időpontjában csak a Fürtfelügyelő szolgáltatás elsődleges replikáját futtató csomóponton fut. Ebben az írásban a beállítás alapértelmezett értéke "false", és az új Azure Service Fabric-fürtök esetében "true" értékre lesz állítva, amelynek futtatókörnyezet-verziója 7,1-től kezdődő.
 
### <a name="end-to-end-scenario-examples"></a>Végpontok közötti forgatókönyv (példák)
Megvizsgáltuk a megjelenítési szabályokat, az érvényesítési szabályokat és a csípés jelzőket, de ez hogyan működik együtt? Ebben a szakaszban két végpontok közötti példát dolgozunk fel, amelyek azt mutatják be, hogyan használhatók a biztonsági beállítások a biztonságos fürtök frissítései számára. Vegye figyelembe, hogy ez nem a Service Fabric megfelelő tanúsítványainak teljes körű disszertációja, hanem a témakörben talál egy kiegészítő cikket.

A bemutatási és érvényesítési szabályok elkülönítése az egyértelmű kérdés (vagy aggodalom), hogy eltérhetnek-e, és milyen következményekkel járhat. Valójában lehetséges, hogy a csomópontok hitelesítési tanúsítványának kiválasztása nem felel meg egy másik csomópont érvényesítési szabályainak. Valójában ez az eltérés a hitelesítéssel kapcsolatos incidensek elsődleges oka. Ugyanakkor a szabályok elkülönítése lehetővé teszi, hogy a fürt a frissítés során továbbra is működőképes maradjon, ami megváltoztatja a fürt biztonsági beállításait. Gondolja át, hogy az első lépésként az érvényesítési szabályok első lépéseként kibővítve az összes fürt csomópontjai az új beállításokon lesznek átszervezve, miközben továbbra is az aktuális hitelesítő adatokat használják. 

Ne felejtse el, hogy egy Service Fabric-fürtön a frissítés a (legfeljebb 5) "frissítési tartományon vagy frissítési keresztül halad át. A rendszer csak az aktuális UD csomópontokat frissíti/módosítja egy adott időpontban, és a frissítés csak akkor fog folytatódni a következő UD, ha a fürt rendelkezésre állása lehetővé teszi. (További részletekért tekintse meg [Service Fabric fürt frissítéseit](service-fabric-cluster-upgrade.md) és a további cikkeket.) A tanúsítvány/biztonsági változások különösen kockázatos, mivel elkülönítik a csomópontokat a fürtből, vagy meghagyják a fürtöt a kvórum elvesztésének szélén.

A csomópont biztonsági beállításainak leírásához a következő jelölést fogjuk használni:

NK: {P:{TP = A}, V:{TP = A}}, ahol:
  - Az "nk" a frissítési tartomány ( *k* ) csomópontját jelöli.
  - A "P" a csomópont aktuális megjelenítési szabályait jelöli (feltéve, hogy csak a fürtözött tanúsítványokra hivatkozunk); 
  - A "V" a csomópont aktuális ellenőrzési szabályait jelöli (csak a fürt tanúsítványa).
  - A "TP = A" ujjlenyomat-alapú deklarációt (TP) jelöl, amelynek "A" Tanúsítvány-ujjlenyomata
  - A "CN = B" nevű köznapi név-alapú deklaráció (CN) a tanúsítvány tulajdonos köznapi nevével rendelkező "B" névvel van ellátva. 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Az ujjlenyomattal deklarált fürtözött tanúsítvány elforgatása
A következő sorozat azt ismerteti, hogyan használható a kétfázisú frissítés az ujjlenyomattal deklarált másodlagos fürt biztonságos bevezetéséhez. az első fázisban be van vezetve az új tanúsítvány deklarációja az ellenőrzési szabályokban, a második fázis pedig a megjelenítési szabályokban mutatja be a következőket:
  - kezdeti állapot: N0 = {P:{TP = A}, V:{TP = A}},... NK = {P:{TP = A}, V:{TP = A}} – a fürt nyugalmi állapotban van, és minden csomópont közös konfigurációt oszt meg
  - A frissítési tartomány (0) befejezése után: N0 = {P:{TP = A}, V:{TP = A, TP = B}},... NK = {P:{TP = A}, V:{TP = A}} – a UD0 csomópontjai bemutatják az A tanúsítványt, és elfogadják A vagy B tanúsítványokat; minden más csomópont jelen van, és csak A tanúsítványt fogadja el
  - a legutóbbi frissítési tartomány befejezése után: N0 = {P:{TP = A}, V:{TP = A, TP = B}},... NK = {P:{TP = A}, V:{TP = A, TP = B}} – minden csomópont bemutat egy tanúsítványt, minden csomópont fogadja az A vagy A B tanúsítványt
      
Ekkor a fürt ismét egyensúlyban van, és a frissítés/módosítás biztonsági beállításainak második fázisa is megkezdődhet:
  - a frissítési tartomány (0) befejezése után: N0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... NK = {P:{TP = A}, V:{TP = A, TP = B}} – a UD0 csomópontjai elkezdik a B bemutatót, amelyet a fürt bármely más csomópontja elfogad.
  - a legutóbbi frissítési tartomány befejezése után: N0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... NK = {P:{TP = A, TP = B}, V:{TP = A, TP = B}} – az összes csomópont átváltott a B tanúsítvány bemutatására. az A tanúsítvány mostantól kivonható/eltávolítható a fürt definíciójában egy későbbi verziófrissítéssel.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Fürt átalakítása az ujjlenyomatról a common-name-based Certificate deklarációba
Hasonlóképpen, ha megváltoztatja a tanúsítvány deklarációjának típusát (az ujjlenyomatról a köznapi névre), a fentieknek megfelelően fogja követni a mintát. Vegye figyelembe, hogy az érvényesítési szabályok lehetővé teszik, hogy egy adott szerepkör tanúsítványait ujjlenyomattal és köznapi névvel deklarálja ugyanabban a fürt-definícióban. Ezzel szemben azonban a megjelenítési szabályok csak egyetlen formáját teszik lehetővé a deklarációban. Egyébként a fürtözött tanúsítvány ujjlenyomatról köznapi névre történő átalakításának biztonságos megközelítése az, hogy először az ujjlenyomattal kell bevezetni a kívánt célt, majd ezt a deklarációt egy köznapi névre kell módosítani. Az alábbi példában feltételezzük, hogy az "A" ujjlenyomat és a tulajdonos köznapi neve "B" kifejezés ugyanarra a tanúsítványra hivatkozik. 

  - kezdeti állapot: N0 = {P:{TP = A}, V:{TP = A}},... NK = {P:{TP = A}, V:{TP = A}} – a fürt nyugalmi állapotban van, minden csomópont közös konfigurációval rendelkezik, és az elsődleges Tanúsítvány ujjlenyomata
  - A frissítési tartomány (0) befejezése után: N0 = {P:{TP = A}, V:{TP = A, CN = B}},... NK = {P:{TP = A}, V:{TP = A}} – a UD0 csomópontjai bemutatják az A tanúsítványt, és elfogadják A (z) "A" vagy A "köznapi" névvel rendelkező tanúsítványokat is. minden más csomópont jelen van, és csak A tanúsítványt fogadja el
  - a legutóbbi frissítési tartomány befejezése után: N0 = {P:{TP = A}, V:{TP = A, CN = B}},... NK = {P:{TP = A}, V:{TP = A, CN = B}} – minden csomópont bemutat egy tanúsítványt, minden csomópont elfogadja vagy A (TP) vagy B (CN) tanúsítványt.

Ezen a ponton a következő frissítéssel folytathatja a megjelenítési szabályok módosítását:
  - a frissítési tartomány (0) befejezése után: N0 = {P:{CN = B}, V:{TP = A, CN = B}},... NK = {P:{TP = A}, V:{TP = A, CN = B}} – a UD0 csomópontjai a CN által talált B tanúsítványt fogják tartalmazni, és elfogadják A (z) "A" vagy A "köznapi" nevet tartalmazó tanúsítványokat. minden más csomópont jelen van, és csak az "A" tanúsítványt fogadja el, ujjlenyomat alapján kiválasztva
  - a legutóbbi frissítési tartomány befejezése után: N0 = {P:{CN = B}, V:{TP = A, CN = B}}... NK = {P:{CN = B}, V:{TP = A, CN = B}} – minden olyan csomópont, amely a CN által talált B tanúsítvány, minden csomópont elfogadja vagy A (TP) vagy B (CN) tanúsítványt.
    
A 2. fázis befejezése azt is jelzi, hogy a fürt átalakítása köznapi név alapú tanúsítványokra is vonatkozik; az ujjlenyomat-alapú érvényesítési deklarációk eltávolíthatók egy későbbi fürt frissítésében.

> [!NOTE]
> Az Azure Service Fabric-fürtökben a fent bemutatott munkafolyamatokat a Service Fabric erőforrás-szolgáltató hangolja össze. a fürt tulajdonosa továbbra is felelős a tanúsítványok fürtben való kihelyezésében a jelzett szabályok (megjelenítés vagy érvényesítés) alapján, és javasoljuk, hogy hajtsa végre a módosításokat több lépésben.

Egy külön cikkben egy Service Fabric-fürthöz tartozó tanúsítványok kezelésével és kiépítési témakörével foglalkozunk.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Hibaelhárítás és gyakran ismételt kérdések
A Service Fabric fürtök hitelesítéssel kapcsolatos problémáinak hibakeresése nem egyszerű, ezért a következő útmutatók és tippek segíthetnek. A vizsgálatok megkezdésének legegyszerűbb módja az Service Fabric eseménynaplók vizsgálata a fürt csomópontjain – nem feltétlenül csak a tüneteket mutató, de a csomópontok is, amelyek nem tudnak csatlakozni az egyik szomszédaihoz. Windows rendszeren a fontossági eseményeket általában az "alkalmazások és szolgáltatások Logs\Microsoft-ServiceFabric\Admin" vagy a "működési" csatornák alatt naplózza a rendszer. Esetenként hasznos lehet a [CAPI2 naplózásának engedélyezése](/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues), hogy további részleteket rögzítsen a tanúsítvány érvényesítésével, a CRL/CTL-ek lekérésével, stb. (ne felejtse el letiltani a Reprodukálási befejezése után.)

A hitelesítési problémákat tapasztaló fürtökben előforduló jellemző tünetek a következők: 
  - csomópontok leállítása/kerékpározás 
  - a csatlakozási kísérletek elutasítása
  - a csatlakozási kísérletek időtúllépést mutatnak

Előfordulhat, hogy az egyes tüneteket különböző problémák okozzák, és a kiváltó okok különböző megnyilvánulásokat is tartalmazhatnak. Ebben az esetben a tipikus problémák kis példáját fogjuk kilistázni, és javaslatokat teszünk a kijavítására. 

* A csomópontok képesek Exchange üzeneteket cserélni, de nem tudnak kapcsolatot létesíteni. A kapcsolódási kísérletek leállításának lehetséges okai a "nem egyező tanúsítvány" hibaüzenet – az egyik fél egy Service Fabric – Service Fabric kapcsolaton olyan tanúsítványt mutat be, amely nem felel meg a címzett ellenőrzési szabályainak. A következő hibák bármelyikével együtt lehet kísérni: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  További Diagnosztizálás/vizsgálat: a kapcsolódást megkísérlő összes csomóponton határozza meg, hogy melyik tanúsítvány jelenik meg. vizsgálja meg a tanúsítványt, és próbálja meg emulálni az ellenőrzési szabályokat (ellenőrizze az ujjlenyomatot vagy a köznapi név egyenlőségét, ellenőrizze a kiállítói ujjlenyomatai megfelelnek, ha meg van adva).

  Egy másik gyakori kísérő hibakód a következő lehet:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  Ebben az esetben a tanúsítványt köznapi név deklarálja, és a következők valamelyike érvényes:
    - a kibocsátók nincsenek rögzítve, és a főtanúsítvány nem megbízható, vagy
    - a kiállítók rögzítettek, de a deklaráció nem tartalmazza a tanúsítvány közvetlen kiállítójának ujjlenyomatát.

* Egy csomópont működik, de nem tud csatlakozni más csomópontokhoz; más csomópontok nem kapnak bejövő forgalmat a hibás csomóponttól. Ebben az esetben lehetséges, hogy a tanúsítvány betöltése sikertelen a helyi csomóponton. Keresse meg a következő hibákat:
  - a tanúsítvány nem található – gondoskodjon arról, hogy a megjelenítési szabályokban deklarált tanúsítványokat a LocalMachine\My (vagy a megadott) tanúsítványtárolójának tartalma feloldja. 
    A hiba lehetséges okai a következők lehetnek: 
      - érvénytelen karakterek szerepelnek az ujjlenyomat-deklarációban
      - a tanúsítvány nincs telepítve
      - a tanúsítvány lejárt
      - a common-name deklaráció tartalmazza a következő előtagot: "CN =".
      - a deklaráció megad egy helyettesítő karaktert, és nem tartalmaz pontos egyezést a tanúsítvány-tárolóban (deklaráció: CN = *. SajátTartomány. com, aktuális tanúsítvány: CN = Server. SajátTartomány. com)

  - ismeretlen hitelesítő adatok – a tanúsítványhoz tartozó hiányzó titkos kulcsot jelez, jellemzően a következő hibakóddal együtt: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    A megoldáshoz vizsgálja meg a titkos kulcs létezését; Győződjön meg arról, hogy a SFAdmins "READ | Execute" hozzáférése van megadva a titkos kulcshoz.

  - rossz szolgáltató típusa – a kriptográfiai új generációs (CNG) tanúsítvány ("Microsoft szoftveres kulcstároló-szolgáltató") jelzése; jelenleg a Service Fabric csak a CAPI1-tanúsítványokat támogatja. Jellemzően a következő hibakóddal kíséri:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    A CAPI1 létrehozásához hozza létre újra a fürtöt (például "Microsoft Enhanced RSA and AES kriptográfiai szolgáltató") szolgáltatót. A kriptográfiai szolgáltatókkal kapcsolatos további részletekért tekintse meg a [kriptográfiai szolgáltatók ismertetése](/windows/win32/seccertenroll/understanding-cryptographic-providers) című témakört.
