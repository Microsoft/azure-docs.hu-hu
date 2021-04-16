---
title: AMQP 1.0 Azure Service Bus és Event Hubs protokoll-útmutatóban | Microsoft Docs
description: Protokoll-útmutató az AMQP 1.0 kifejezésekhez és leíráshoz a Azure Service Bus és Event Hubs
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 8d346aeef74e1f67d3d525c061d40314ee5342aa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531012"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 Azure Service Bus és Event Hubs protokoll-útmutatóban

Az Advanced Message Queueing Protocol 1.0 egy szabványosított keretezési és átviteli protokoll aszinkron, biztonságos és megbízható üzenetek átviteléhez két fél között. Ez az üzenetkezelés és -Azure Service Bus elsődleges Azure Event Hubs.  

Az AMQP 1.0 annak a széles körű iparági együttműködésnek az eredménye, amely olyan középszolgáltatói együttműködést hozott létre, mint a Microsoft és a Red Hat, és számos üzenetküldő közvetítőfelhasználó, például a JP Morgan Morgan képviseli a pénzügyi szolgáltatási iparágat. Az AMQP protokoll és a bővítmény specifikációinak műszaki szabványosítási fóruma az ISO, és nemzetközi szabványként az ISO/IEC 19494:2014 szabványnak megfelelően hivatalos jóváhagyást ért el. 

## <a name="goals"></a>Célok

Ez a cikk összefoglalja az AMQP 1.0 üzenetkezelési specifikáció alapfogalmait és az [AZ AMQP](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) műszaki bizottság által kifejlesztett bővítményspecifikációkat, és elmagyarázza, hogyan valósítja meg és építi fel az Azure Service Bus ezeket a specifikációkat.

A cél minden olyan fejlesztő számára, aki bármely meglévő AMQP 1.0-ügyfél stacket használja bármely platformon, hogy az AMQP 1.0 Azure Service Bus keresztül kommunikálhat az Azure Service Bus-val.

Az általános célú AMQP 1.0-vermek, például az [Apache Qpid Proton](https://qpid.apache.org/proton/index.html) vagy az [AMQP.NET Lite,](https://github.com/Azure/amqpnetlite)implementálják az AMQP 1.0 protokoll összes alapvető elemét, például a munkameneteket vagy a hivatkozásokat. Ezek az alapvető elemek néha egy magasabb szintű API-val vannak burkoltan; Az Apache Proton még kettőt is kínál: az imperatív Üzenetküldő API-t és a reaktív Reactor API-t.

A következő megbeszélés során feltételezzük, hogy az AMQP-kapcsolatok, munkamenetek és hivatkozások, valamint a képkockaátvitelek és a folyamatvezérlés kezelését a megfelelő verem (például Apache Proton-C) kezeli, és nem igényel sok figyelmet az alkalmazásfejlesztőktől. Absztrakt módon feltételezzük néhány API-primitív meglétét, például a kapcsolódási  képességet, valamint a küldő- és fogadó-absztrakciós objektumok valamilyen formáját, amelyek ezután valamilyen típusú és művelettel  `send()` is `receive()` rendelkezik.

A szolgáltatások speciális Azure Service Bus, például az üzenetböngészés vagy a munkamenetek kezelése során ezeket a funkciókat AMQP-kifejezésekkel mutatjuk be, de egy réteges pszeudo-implementációként is, amely ezen feltételezett API-absztrakción felül áll.

## <a name="what-is-amqp"></a>Mi az az AMQP?

Az AMQP egy keretezés és átviteli protokoll. A keretezés azt jelenti, hogy struktúrát biztosít a hálózati kapcsolat mindkét irányában áramló bináris adatfolyamok számára. A struktúra külön adatblokkokat (képkockákat) biztosít a csatlakoztatott felek közötti adatcseréhez.  Az átviteli képességek biztosítják, hogy a kommunikáló felek közös képet kapnak arról, hogy mikor kell átvitni a képkockákat, és mikor tekintendők befejezettnek.

A korábban az AMQP munkacsoport által létrehozott, néhány üzenetközvetítő által még használatban lévő lejárt piszkozatverzióktól eltérően a munkacsoport végső és szabványos AMQP 1.0 protokollja nem írja elő az üzenetközvetítők jelenlétét vagy az üzenetközvetítőn belüli entitások adott topológiáját.

A protokoll használható szimmetrikus, egyenrangú kommunikációhoz, az üzenetsorokat támogató üzenetközvetítők és a közzétételi/előfizetési entitások Azure Service Bus kommunikációhoz. Az üzenetkezelési infrastruktúrával való interakcióhoz is használható, ahol az interakciós minták eltérnek a normál üzenetsorok mintáitól, ahogyan az Azure Event Hubs. Az eseményközpontok üzenetsorként viselkednek, amikor eseményeket küldnek neki, de inkább soros tárolási szolgáltatásként viselkednek, amikor beolvassa az eseményeket; A egy szalagos meghajtóhoz hasonlít. Az ügyfél kivesz egy eltolást az elérhető adatfolyamba, majd az ettől az eltolástól a legújabb elérhetőre kézbesíti az összes eseményt.

Az AMQP 1.0 protokollt úgy tervezték, hogy bővíthető legyen, és további specifikációkat biztosít a képességeinek továbbfejlesztésében. Ezt a dokumentumban tárgyalt három bővítménysokaját szemlélteti. A meglévő HTTPS/WebSockets-infrastruktúrán keresztüli kommunikációhoz a natív AMQP TCP-portok konfigurálása nehézkes lehet. A kötési specifikáció határozza meg, hogyan lehet az AMQP-t WebSocket-ekkel rétegbe rétegeni. Az üzenetkezelési infrastruktúra kezelés céljából kérés/válasz módon történő használatához vagy speciális funkciók igényléséhez az AMQP felügyeleti specifikációja határozza meg a szükséges alapszintű interakciós primitíveket. Az összevont engedélyezési modell integrációjához az AMQP jogcímalapú biztonsági specifikáció határozza meg, hogyan társíthatja és újíthatja meg a hivatkozásokhoz társított engedélyezési jogkivonatokat.

## <a name="basic-amqp-scenarios"></a>Alapszintű AMQP-forgatókönyvek

Ez a szakasz az AMQP 1.0 és az Azure Service Bus alapszintű használatát ismerteti, beleértve a kapcsolatok, munkamenetek és hivatkozások létrehozását, valamint az üzenetek Service Bus-entitások, például üzenetsorok, témakörök és előfizetések közötti átvitelét.

Az AMQP működését a leg mérvadóbb forrás az [AMQP 1.0](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html)specifikációja, de a specifikáció úgy lett megírva, hogy pontosan a megvalósítást irányítsa, és ne a protokoll tanítása érdekében. Ez a szakasz az AMQP 1.0 használatának leírására Service Bus szükséges terminológiát ismerteti. Az AMQP átfogóbb bemutatásához, valamint az AMQP 1.0 szélesebb körű bemutatásához tekintse át ezt a [videós kurzust.][this video course]

### <a name="connections-and-sessions"></a>Kapcsolatok és munkamenetek

Az AMQP a kommunikáló programok *tárolóit hívja* meg; ezek *csomópontokat tartalmaznak,* amelyek a tárolókon belüli kommunikáló entitások. Az üzenetsorok ilyen csomópontok is lehetnek. Az AMQP lehetővé teszi a multiplexinget, így egyetlen kapcsolat használható a csomópontok közötti számos kommunikációs útvonalhoz; Egy alkalmazás ügyfél például egyidejűleg fogadhatja az egyik üzenetsort, és elküldheti egy másik üzenetsorba ugyanazon a hálózati kapcsolaton keresztül.

![A tárolók közötti munkameneteket és kapcsolatokat bemutató ábra.][1]

A hálózati kapcsolat így a tárolóhoz van horgonyozva. Ezt az ügyfélszerepkör tárolója kezdeményezi, amely kimenő TCP-szoftvercsatornás kapcsolatot létesít a fogadói szerepkörben található tárolóval, amely a bejövő TCP-kapcsolatokat figyeli és fogadja. A kapcsolati kézfogás magában foglalja a protokoll verziójának egyeztetését, a Transport Level Security (TLS/SSL) használatának deklarál vagy egyeztetését, valamint egy hitelesítési/engedélyezési kézfogást a KAPCSOLAT SASL-alapú hatókörében.

Azure Service Bus TLS-t kell használnia. Támogatja az 5671-es TCP-porton keresztüli kapcsolatokat, ahol a TCP-kapcsolat az AMQP protokoll kézfogásának megadása előtt először átfedi a TLS-t, és támogatja az 5672-es TCP-porton keresztüli kapcsolatokat is, ahol a kiszolgáló azonnal kötelezően frissíti a TLS-kapcsolatot az AMQP által előírt modell használatával. Az AMQP WebSockets kötés egy alagutat hoz létre a 443-as TCP-porton keresztül, amely ezután egyenértékű az AMQP 5671-kapcsolatokkal.

A kapcsolat és a TLS beállítása után a Service Bus KÉT SASL-mechanizmust kínál:

* SASL PLAIN gyakran használják a felhasználónév és jelszó hitelesítő adatainak a kiszolgálónak való átadásához. Service Bus nem rendelkezik fiókokkal, de közös hozzáférésű [biztonsági](service-bus-sas.md)szabályok () nevű szabályok, amelyek jogosultságokat osztják ki, és amelyek egy kulcshoz vannak társítva. A rendszer egy szabály nevét használja felhasználónévként, a kulcsot (base64 kódolású szövegként) pedig jelszóként. A kiválasztott szabályhoz társított jogosultságok szabályozzák a kapcsolaton engedélyezett műveleteket.
* A SASL ANONYMOUS az SASL-hitelesítés megkerülését jelenti, ha az ügyfél a később ismertetett jogcímalapú biztonsági (CBS) modellt szeretné használni. Ezzel a beállítással egy rövid ideig névtelenül létesíthet ügyfélkapcsolatot, amelynek során az ügyfél csak a CBS-végponttal kommunikálhat, és a CBS-kézfogásnak be kell fejeződnie.

Az átviteli kapcsolat létrejötte után a tárolók azt a maximális keretméretet deklarálják, amelyet hajlandók kezelni, és az üresjárati időtúllépés után egymás után megszakítják a kapcsolatot, ha nincs tevékenység a kapcsolatban.

Emellett azt is deklarálják, hogy hány párhuzamos csatorna támogatott. A csatorna egy egyirányú, kimenő virtuális átviteli útvonal a kapcsolat felett. A munkamenetek az összekapcsolt tárolók egyik csatornáját egy kétirányú kommunikációs útvonalon alkotják.

A munkamenetek ablakalapú folyamatvezérlési modellel vannak; egy munkamenet létrehozásakor mindegyik fél bejelentette, hogy hány képkockát hajlandó elfogadni a fogadási ablakban. Ahogy a felek képkockákat cserélnek, az átvitt képkockák kitöltik az ablakot, és az átvitel leáll, amikor az ablak megtelik, és amíg az ablak alaphelyzetbe nem áll vagy ki nem bővül a folyamat végrehajtásához *szükséges* művelettel (a *performative* a két fél között cserélt protokollszintű kézmozdulatok AMQP-kifejezése).

Ez az ablakalapú modell nagyjából hasonló az ablakalapú folyamatvezérlés TCP-fogalmához, de a szoftvercsatornán belüli munkamenet szintjén. A protokollnak az egyidejű munkamenetek engedélyezésére vonatkozó koncepciója azért létezik, hogy a magas prioritású forgalom a normál forgalmat túlküldje, például egy gyorsforgalmi expressz sávon.

Azure Service Bus jelenleg minden kapcsolathoz pontosan egy munkamenetet használ. A Service Bus maximális képkockaméret 262 144 bájt (256 K bájt) a Standard Service Bus számára. Prémium és prémium szintű Service Bus 1 048 576 (1 MB) Event Hubs. Service Bus nem ír elő konkrét munkamenetszintű szabályozási ablakokat, de a hivatkozásszintű folyamatvezérlés részeként rendszeresen alaphelyzetbe állítja az ablakot (lásd a következő [szakaszt).](#links)

A kapcsolatok, csatornák és munkamenetek időfokosak. Ha a mögöttes kapcsolat összeomlik, a kapcsolatokat, a TLS-alagutat, az SASL-hitelesítési környezetet és a munkameneteket újra kell összecsukni.

### <a name="amqp-outbound-port-requirements"></a>AmQP kimenő portra vonatkozó követelmények

Az AMQP-kapcsolatokat TCP protokollon használó ügyfelek számára az 5671-es és az 5672-es portot meg kell nyitni a helyi tűzfalon. Ezeken a portokon kívül szükség lehet további portok megnyitására is, ha az [EnableLinkRedirect](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect) szolgáltatás engedélyezve van. `EnableLinkRedirect` A egy új üzenetkezelési funkció, amely segít az egy ugrásos üzenetek fogadása közbeni kihagyásban, így növeli az átviteli sebességet. Az ügyfél közvetlenül a háttérszolgáltatással kommunikálna a 104XX porttartományon keresztül, ahogy az alábbi képen látható. 

![Célportok listája][4]

A .NET-ügyfél SocketException (Szoftvercsatorna-kivédés) hibát adna vissza ("A szoftvercsatornák elérésére tett kísérlet a hozzáférési engedélyei által tiltott módon történt"), ha a tűzfal blokkolja ezeket a portokat. A szolgáltatás letiltható a kapcsolati sztring beállításával, amely arra kényszeríti az ügyfeleket, hogy az 5671-es porton keresztül kommunikáljanak a távoli `EnableAmqpLinkRedirect=false` szolgáltatással.


### <a name="links"></a>Hivatkozások

Az AMQP hivatkozásokon keresztül továbbítja az üzeneteket. A hivatkozás egy munkameneten keresztül létrehozott kommunikációs útvonal, amely lehetővé teszi az üzenetek átvitelét egy irányban; az átadási állapot egyeztetése a csatlakoztatott felek közötti kapcsolaton és kétirányú kapcsolaton keresztül ért véget.

![Képernyőkép egy két tároló közötti kapcsolaton keresztüli munkamenetről.][2]

A hivatkozások bármikor és egy meglévő munkamenetben is létre lehet hozva egy tárolóval, ami miatt az AMQP eltér számos más protokolltól, például a HTTP-től és az MQTT-től, ahol az átvitelek és átviteli útvonalak kezdeményezése a szoftvercsatorna-kapcsolatot létrehozó fél kizárólagos jogosultsága.

A hivatkozás kezdeményező tároló arra kéri az ellenkező tárolót, hogy fogadja el a hivatkozást, és a küldő vagy a fogadó szerepkört választja. Ezért bármelyik tároló kezdeményezhet egyirányú vagy kétirányú kommunikációs útvonalakat, amelyek közül az utóbbi hivatkozáspárokként van modellve.

A hivatkozások neve és csomópontokhoz vannak társítva. Ahogy az elején is volt, a csomópontok a tárolón belüli kommunikáló entitások.

A Service Bus csomópontok közvetlenül egyenértékűek az üzenetsorokkal, témakörökvel, előfizetésekkel vagy az üzenetsorok vagy előfizetések holtponti alsorokkal. Az AMQP-ben használt csomópontnév ezért az entitás relatív neve az Service Bus névtéren belül. Ha az üzenetsor neve `myqueue` , akkor ez az AMQP-csomópont neve is. A témakör-előfizetés úgy követi a HTTP API-konvenciót, hogy egy  "előfizetések" erőforrás-gyűjteménybe rendezi őket, így a **mytopic** témakör előfizetési alhálózatának amQP-csomópontneve **mytopic/subscriptions/sub.**

A kapcsolat létrehozásához a csatlakozó ügyfélnek is helyi csomópontnevet kell használnia; Service Bus nem előíró a csomópontok neveivel kapcsolatban, és nem értelmezi azokat. Az AMQP 1.0-ügyfél vermek általában egy sémát használnak annak érdekében, hogy ezek a körívív csomópontnevek egyediek az ügyfél hatókörében.

### <a name="transfers"></a>Transzferek

A hivatkozás létrehozása után az üzenetek átvihetők a hivatkozáson keresztül. Az AMQP-ben az átvitel explicit protokoll-kézmozdulattal (az átviteli művelettel) van végrehajtva, amely egy üzenetet továbbít a küldőtől a fogadóhoz egy hivatkozáson keresztül.  Az átadás akkor fejeződik be, ha az átadás "megegyezésre kész", ami azt jelenti, hogy mindkét fél megosztottan érti az átadás kimenetelét.

![Diagram egy üzenet küldő és fogadó közötti átviteléről, valamint az eredményként kapott diszpozícióról.][3]

A legegyszerűbb esetben a küldő dönthet úgy, hogy előre meghatározott üzeneteket küld, ami azt jelenti, hogy az ügyfelet nem érdekli az eredmény, és a fogadó nem ad visszajelzést a művelet eredményéről. Ezt a módot a Service Bus AMQP protokollszinten támogatja, de egyik ügyfél API-ban sem teszi elérhetővé.

A szokásos eset az, hogy az üzenetek küldése nem egyértelmű, a fogadó pedig az elfogadást vagy az elutasítást jelzi a *diszpozíció* teljesítményéről. Elutasításra akkor kerül sor, ha a címzett bármilyen okból nem tudja elfogadni az üzenetet, és az elutasítási üzenet az okról tartalmaz információt, amely az AMQP által meghatározott hibastruktúra. Ha az üzeneteket a Service Bus belső hibái miatt utasítják el, a szolgáltatás további információkat ad vissza a struktúrán belül, amelyek segítségével diagnosztikai tippeket lehet adni a támogatási munkatársaknak, ha támogatási kéréseket küld. A hibákról a későbbiekben olvashat bővebben.

Az elutasítás egy különleges  formája a felszabadított állapot, amely azt jelzi, hogy a címzett nem rendelkezik technikai kifogásolással az átadással szemben, hanem nem érdeklődik az átadás elutasítása iránt. Ez az eset például akkor áll fenn, amikor egy üzenetet kézbesít egy Service Bus-ügyfélnek, és az ügyfél úgy dönt, hogy "felhagy" az üzenettel, mert nem tudja végrehajtani az üzenet feldolgozásával kapcsolatos munkát; az üzenetek kézbesítése nem hibás. Ennek az állapotnak egy változata a *módosított* állapot, amely lehetővé teszi az üzenet módosítását a felszabadításkor. Ezt az állapotot a Service Bus jelenleg nem használja.

Az AMQP 1.0 specifikáció egy *"received"* nevű további diszpozíciós állapotot határoz meg, amely kifejezetten segít a hivatkozás-helyreállítás kezelésében. A kapcsolat helyreállítása lehetővé teszi a hivatkozás és a függőben lévő kézbesítések helyreállítását egy új kapcsolat és munkamenet után, amikor az előző kapcsolat és munkamenet megszakadt.

Service Bus nem támogatja a hivatkozás-helyreállítást; Ha az ügyfél nem Service Bus függőben lévő üzenetátvitellel elveszíti a kapcsolatot az Service Bus-hez, akkor az üzenetátvitel megszakad, és az ügyfélnek újra kell csatlakoznia, újból létre kell hoznunk a hivatkozást, és újra meg kell próbálkoznunk az átvitellel.

Ezért az Service Bus és az Event Hubs támogatják az "legalább egyszer" történő átvitelt, ahol a küldő számára biztosítható az üzenet tárolása és elfogadottása, de nem támogatják az AMQP szintjén történő "pontosan egyszer" átvitelt, ahol a rendszer megkísérli helyreállítani a hivatkozást, és folytatja a kézbesítési állapot egyeztetését az üzenetátvitel duplikálásának elkerülése érdekében.

A lehetséges ismétlődő küldések kompenzálására a Service Bus támogatja a duplikált üzenetek észlelését választható szolgáltatásként az üzenetsorok és témakörök számára. A duplikált észlelés a felhasználó által megadott időablakban rögzíti az összes bejövő üzenet üzenet-azonosítóját, majd csendesen eldobja az azonos üzenet-azonosítóval küldött összes üzenetet ugyanabban az ablakban.

### <a name="flow-control"></a>Folyamatvezérlés

A korábban tárgyalt munkamenetszintű folyamatvezérlési modellen kívül minden hivatkozás saját folyamatvezérlési modellel rendelkezik. A munkamenetszintű folyamatvezérlés megvédi a tárolót attól, hogy egyszerre túl sok képkockát kell kezelnie, a kapcsolatszintű folyamvezérlés pedig az alkalmazásra helyezi a felelőst, hogy hány üzenetet szeretne kezelni egy hivatkozásból, és mikor.

![Képernyőkép egy naplóról, amely a forrást, a célt, a forrásportot, a célportot és a protokoll nevét mutatja. Az első sorban az 10401-es célport (0x28 A 1) fekete színben van felvázolva.][4]

Hivatkozáson az átvitel csak akkor történhet meg, ha a küldő elegendő *hivatkozási jóváírással rendelkezik.* A hivatkozási jóváírás egy számláló,  amelyet a fogadó a folyamat teljesítménymutatóját használva, amelynek hatóköre egy hivatkozásra terjed ki. Amikor a küldő kap egy hivatkozási kreditet, megpróbálja az üzenetek kézbesítése által felhasználhatja ezt a kreditet. Minden üzenetk kézbesítése 1-el szorra lecsiválja a fennmaradó hivatkozási krediteket. A hivatkozási kreditek használata után a kézbesítések leállnak.

Ha Service Bus a címzett szerepkörben van, azonnal bőséges hivatkozási kreditet biztosít a küldőnek, így az üzenetek azonnal elküldve lesznek. A hivatkozási kreditek használata során a Service Bus folyamat időnként teljesítő folyamatot küld *a* küldőnek a hivatkozás jóváírási egyenlegének frissítéséhez.

A küldői szerepkörben a Service Bus üzeneteket küld a függőben lévő hivatkozási kreditek használhatja fel.

Az API szintjén a "fogadási"  hívás az ügyfél által az Service Bus-nak küldött folyamat-teljesítményre utal, és a Service Bus felhasználja ezt a kreditet az üzenetsor első elérhető, feloldott üzenetének fogadása, zárolása és továbbítása által. Ha nincs azonnal elérhető üzenet kézbesítésre, az adott entitással létrehozott bármely kapcsolaton keresztüli fennálló jóváírások érkezési sorrendben rögzítve maradnak, és az üzenetek zárolva lesznek és át lesznek adva, amint elérhetővé válnak a fennálló kreditek használata érdekében.

Az üzenet zárolása akkor szabadul fel, amikor az átvitel  *elfogadott,* elutasított vagy kiadott terminál-államba *kerül.* Az üzenet el lesz távolítva Service Bus terminálállapot elfogadott *állapotából.* A rendszer a Service Bus, és a következő fogadóhoz továbbítja, amikor az átvitel eléri a többi állam bármelyikét. Service Bus automatikusan áthelyezi az üzenetet az entitás kézbesítő üzenetsorába, amikor az ismétlődő elutasítások vagy kiadások miatt eléri az entitáshoz engedélyezett maximális kézbesítési darabszámot.

Annak ellenére, hogy a Service Bus API-k jelenleg nem fedik fel közvetlenül ezt a lehetőséget, egy alacsonyabb szintű AMQP protokoll-ügyfél a link-credit modell használatával minden egyes fogadási kérelemhez egy egységnyi kredit kiadásának "lekéréses stílusú" interakcióját válthatja fel nagy számú hivatkozási kredit kiadásával, majd az üzenetek fogadásával, amint további interakciók nélkül válnak elérhetővé. A leküldés a [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) vagy [a MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) tulajdonságbeállításokkal támogatott. Ha nem nulla, az AMQP-ügyfél azt használja hivatkozási kreditként.

Ebben a kontextusban fontos megérteni, hogy az entitáson belüli üzenet zárolásának lejárati ideje akkor kezdődik, amikor az üzenetet az entitástól vették, nem pedig akkor, amikor az üzenet a vezetékre került. Ha az ügyfél arra utal, hogy készen áll az üzenetek fogadására hivatkozási kreditek kiállításával, akkor várhatóan aktívan leküldi az üzeneteket a hálózaton keresztül, és készen áll a kezelésükre. Ellenkező esetben előfordulhat, hogy az üzenet zárolása már az üzenet kézbesítése előtt lejárt. A link-credit folyamatvezérlés használatának közvetlenül tükröznie kell az azonnali készenlétet a fogadónak elküldött elérhető üzenetekre.

Összefoglalva, a következő szakaszok a különböző API-interakciók során a teljesítményre vonatkozó folyamat sematikus áttekintését biztosítják. Minden szakasz más logikai műveletet ír le. Ezen interakciók némelyike "lusta" lehet, ami azt jelenti, hogy csak szükség esetén végezhetők el. Előfordulhat, hogy az üzenetküldő létrehozása nem okoz hálózati interakciót, amíg az első üzenetet el nem küldi vagy nem kéri.

Az alábbi táblázatban lévő nyilak a folyamat végrehajtásához szükséges irányt mutatják.

#### <a name="create-message-receiver"></a>Üzenet fogadó létrehozása

| Ügyfél | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numerikus leíró},<br/>role=**receiver**,<br/>source={entitás neve},<br/>target={ügyfélkapcsolat azonosítója}<br/>) |Az ügyfél fogadóként csatolja az entitást |
| Service Bus válasza csatolja a hivatkozás végét |< – attach(<br/>name={link name},<br/>handle={numerikus leíró},<br/>role=**sender**,<br/>source={entitás neve},<br/>target={ügyfélkapcsolat azonosítója}<br/>) |

#### <a name="create-message-sender"></a>Üzenetküldő létrehozása

| Ügyfél | Service Bus |
| --- | --- |
| --> attach(<br/>name={link name},<br/>handle={numerikus leíró},<br/>role=**sender**,<br/>source={ügyfélkapcsolat azonosítója},<br/>target={entitás neve}<br/>) |Nincs művelet |
| Nincs művelet |< – attach(<br/>name={hivatkozás neve},<br/>handle={numerikus leíró},<br/>role=**receiver**,<br/>source={ügyfélkapcsolat azonosítója},<br/>target={entitás neve}<br/>) |

#### <a name="create-message-sender-error"></a>Üzenetküldő létrehozása (hiba)

| Ügyfél | Service Bus |
| --- | --- |
| --> attach(<br/>name={hivatkozás neve},<br/>handle={numerikus leíró},<br/>role=**sender**,<br/>source={ügyfélkapcsolat azonosítója},<br/>target={entitás neve}<br/>) |Nincs művelet |
| Nincs művelet |< – attach(<br/>name={hivatkozás neve},<br/>handle={numerikus leíró},<br/>role=**receiver**,<br/>source=null,<br/>target=null<br/>)<br/><br/>< – detach(<br/>handle={numerikus leíró},<br/>closed=**true**,<br/>error={hibainformáció}<br/>) |

#### <a name="close-message-receiversender"></a>Üzenet fogadójának/feladójának bezárása

| Ügyfél | Service Bus |
| --- | --- |
| --> detach(<br/>handle={numerikus leíró},<br/>closed=**true**<br/>) |Nincs művelet |
| Nincs művelet |< – detach(<br/>handle={numerikus leíró},<br/>closed=**true**<br/>) |

#### <a name="send-success"></a>Küldés (sikeres)

| Ügyfél | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numerikus leíró},<br/>delivery-tag={bináris leíró},<br/>áttért=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Nincs művelet |
| Nincs művelet |< - disposition(<br/>role=receiver,<br/>first={kézbesítési azonosító},<br/>last={kézbesítési azonosító},<br/>áttért=**true**,<br/>state=**elfogadva**<br/>) |

#### <a name="send-error"></a>Küldés (hiba)

| Ügyfél | Service Bus |
| --- | --- |
| --> transfer(<br/>delivery-id={numerikus leíró},<br/>delivery-tag={bináris leíró},<br/>áttért=**false**,,more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |Nincs művelet |
| Nincs művelet |< - disposition(<br/>role=receiver,<br/>first={kézbesítési azonosító},<br/>last={kézbesítési azonosító},<br/>áttért=**true**,<br/>state=**elutasítva**(<br/>error={hibainformáció}<br/>)<br/>) |

#### <a name="receive"></a>Fogadás

| Ügyfél | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=1<br/>) |Nincs művelet |
| Nincs művelet |< transfer(<br/>delivery-id={numerikus leíró},<br/>delivery-tag={binary handle},<br/>áttért=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=**receiver**,<br/>first={kézbesítési azonosító},<br/>last={kézbesítési azonosító},<br/>áttért=**true**,<br/>state=**elfogadva**<br/>) |Nincs művelet |

#### <a name="multi-message-receive"></a>Több üzenet fogadása

| Ügyfél | Service Bus |
| --- | --- |
| --> flow(<br/>link-credit=3<br/>) |Nincs művelet |
| Nincs művelet |< transfer(<br/>delivery-id={numerikus leíró},<br/>delivery-tag={binary handle},<br/>áttért=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Nincs művelet |< transfer(<br/>delivery-id={numerikus leíró+1},<br/>delivery-tag={bináris leíró},<br/>áttért=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| Nincs művelet |< transfer(<br/>delivery-id={numerikus leíró+2},<br/>delivery-tag={bináris leíró},<br/>áttért=**false**,<br/>more=**false**,<br/>state=**null**,<br/>resume=**false**<br/>) |
| --> disposition(<br/>role=receiver,<br/>first={kézbesítési azonosító},<br/>last={kézbesítési azonosító+2},<br/>áttért=**true**,<br/>state=**elfogadva**<br/>) |Nincs művelet |

### <a name="messages"></a>Üzenetek

A következő szakaszok ismertetik, hogy a szabványos AMQP-üzenetszakaszok mely tulajdonságait használja Service Bus és hogyan vannak leképezve a Service Bus API-készletre.

Az alkalmazás által definiálni szükséges minden tulajdonságot le kell leképezni az AMQP `application-properties` térképére.

#### <a name="header"></a>fejléc

| Mezőnév | Használat | API neve |
| --- | --- | --- |
| Tartós |- |- |
| Prioritás |- |- |
| ttl |Az üzenethez való idő |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| first-acquirer |- |- |
| kézbesítések száma |- |[DeliveryCount (Kézbesítés száma)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Mezőnév | Használat | API neve |
| --- | --- | --- |
| üzenetazonosító |Az üzenet alkalmazás által definiált, szabad űrlapos azonosítója. Duplikált elemek észlelésére használatos. |[MessageId (Üzenetazonosító)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| felhasználói azonosító |Alkalmazás által definiált felhasználói azonosító, amelyet nem a Service Bus. |Nem érhető el a Service Bus API-n keresztül. |
| felhasználóként a(z) |Alkalmazás által definiált célazonosító, amelyet nem a Service Bus. |[Ide:](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| tárgy |Alkalmazás által definiált üzenetcélú azonosító, amelyet nem a Service Bus. |[Címke](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| reply-to |Alkalmazás által definiált válaszútvonal-jelző, amelyet nem a Service Bus. |[ReplyTo (Válasz válasza)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| korrelációs azonosító |Alkalmazás által definiált korrelációs azonosító, amelyet nem a Service Bus. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-type (tartalomtípus) |A törzs alkalmazás által definiált tartalomtípus-jelzője, amelyet nem a Service Bus. |[ContentType (Tartalomtípus)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| content-encoding |A törzs alkalmazás által definiált tartalomkódolási jelzője, amelyet nem a Service Bus. |Nem érhető el a Service Bus API-n keresztül. |
| abszolút lejárati idő |Deklarál, hogy az üzenet mely abszolút azonnali lejárati idejekor jár le. A bemenet figyelmen kívül hagyva (a fejléc TTL-e), mérvadó a kimenetben. |[ExpiresAtUtc (Lejárati idő)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| létrehozás ideje |Deklarálja, hogy az üzenet mikor jött létre. Nem használja a Service Bus |Nem érhető el a Service Bus API-n keresztül. |
| group-id (csoportazonosító) |Egy kapcsolódó üzenetkészlet alkalmazás által meghatározott azonosítója. A munkamenetekhez Service Bus használható. |[Munkamenet](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| csoportsorozat |A munkameneten belüli üzenet relatív sorszámát azonosító számláló. A rendszer figyelmen kívül hagyja Service Bus. |Nem érhető el a Service Bus API-n keresztül. |
| reply-to-group-id |- |[ReplyToSessionId (Válaszazonosító)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>Üzenetjegyzetek

Vannak más Service Bus-üzenettulajdonságok is, amelyek nem részei az AMQP-üzenettulajdonságoknak, és az üzenetben a következőként vannak `MessageAnnotations` átkedve.

| Jegyzetleképozási kulcs | Használat | API neve |
| --- | --- | --- |
| x-opt-scheduled-enqueue-time | Deklarálta, hogy az üzenet mikor jelenjen meg az entitáson |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc) |
| x-opt-partition-key | Alkalmazás által definiált kulcs, amely azt határozza meg, hogy az üzenet melyik partícióba megjelenik. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey) |
| x-opt-via-partition-key | Alkalmazás által meghatározott partíciókulcs-érték, ha egy tranzakcióval üzeneteket küld egy átviteli üzenetsoron keresztül. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey) |
| x-opt-enqueued-time | A szolgáltatás által megadott UTC-idő, amely az üzenetbesorolás tényleges idejét mutatja. Bemenet figyelmen kívül hagyva. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) |
| x-opt-sequence-number | Egy üzenethez hozzárendelt, szolgáltatás által definiált egyedi szám. | [SequenceNumber (Sorrendszám)](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) |
| x-opt-offset | Az üzenet szolgáltatás által megadott sorrendben megadott sorszáma. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber) |
| x-opt-locked-until | Szolgáltatás által definiált. Az a dátum és idő, amely előtt az üzenet zárolva lesz az üzenetsorban/előfizetésben. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc) |
| x-opt-deadletter-source | Szolgáltatás által definiált. Ha az üzenet a nem szöveges üzenetsorból érkezik, az eredeti üzenet forrása lesz. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource) |

### <a name="transaction-capability"></a>Tranzakciós képesség

Egy tranzakció két vagy több műveletet kapcsol össze egyetlen végrehajtási hatókörbe. Természeténél fogva az ilyen tranzakcióknak biztosítaniuk kell, hogy egy adott műveletcsoporthoz tartozó összes művelet együtt sikeres vagy sikertelen legyen.
A műveletek egy azonosító alapján vannak `txn-id` csoportosítva.

Tranzakciós interakciókhoz az ügyfél egy műveletként működik, amely a csoportosított `transaction controller` műveleteket vezérli. Service Bus Szolgáltatás a szolgáltatásként működik, és a kérésének megfelelő munkát `transactional resource` `transaction controller` végez.

Az ügyfél és a szolgáltatás a ügyfél által létrehozott kapcsolaton keresztül `control link` kommunikál. A vezérlő a vezérlő hivatkozásán keresztül küldi a és a üzenetet a tranzakciók lefoglalása és befejezése érdekében (ezek nem a tranzakciós munka `declare` `discharge` demarkálását jelölik). A tényleges küldés/fogadás nem ezen a hivatkozáson történik. A kért tranzakciós műveleteket a rendszer explicit módon azonosítja a kívánt értékekkel, ezért a kapcsolat bármely `txn-id` hivatkozásán előfordulhatnak. Ha a vezérlőkapcsolat le van zárva, amíg léteznek nem lezárt tranzakciók, akkor a rendszer azonnal visszaállítja az összes ilyen tranzakciót, és a további tranzakciós feladatokra tett kísérletek sikertelenek lesznek. A vezérlőhivatkozáson található üzeneteket nem szabad előre rendezni.

Minden kapcsolatnak saját vezérlési hivatkozást kell kezdeményezni, hogy el tudja kezdeni és el tudja vetni a tranzakciókat. A szolgáltatás meghatároz egy speciális célt, amely a következőként működik: `coordinator` . Az ügyfél/vezérlő létrehoz egy vezérlőkapcsolatot a célhoz. A vezérlőkapcsolat egy entitáson kívül esik, azaz ugyanaz a vezérlőkapcsolat több entitás tranzakcióinak kezdeményezésére és kezdeményezésére is használható.

#### <a name="starting-a-transaction"></a>Tranzakció indítása

A tranzakciós munka megkezdése. A vezérlőnek a `txn-id` koordinátortól kell beszereznie egy et. Ezt egy típusüzenet `declare` küldésvel teszi meg. Ha a deklaráció sikeres, a koordinátor egy diszpozíciós eredménnyel válaszol, amely magában hordozza a hozzárendelt `txn-id` et.

| Ügyfél (vezérlő) | Irány | Service Bus (koordinátor) |
| :--- | :---: | :--- |
| attach(<br/>name={hivatkozás neve},<br/>... ,<br/>role=**sender**,<br/>target=**koordinátor**<br/>) | ------> |  |
|  | <------ | attach(<br/>name={link name},<br/>... ,<br/>target=Coordinator()<br/>) |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (**Declare()**)}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=**Deklarált**(<br/>**txn-id**={tranzakcióazonosító}<br/>))|

#### <a name="discharging-a-transaction"></a>Tranzakció kimerezése

Az adatkezelő a tranzakciós munka zárásához üzenetet küld a `discharge` koordinátornak. A vezérlő jelzi, hogy végleges szeretné véglegesni vagy vissza kívánja-e fordni a tranzakciós munkát úgy, hogy be jelöli a jelölőt `fail` a törzsben. Ha a koordinátor nem tudja befejezni a szavazást, a rendszer elutasítja az üzenetet, és az eredmény a következőt hordozja: `transaction-error` .

> Megjegyzés: a fail=true egy tranzakció visszaállítását, a fail=false pedig Véglegesítést jelenti.

| Ügyfél (vezérlő) | Irány | Service Bus (koordinátor) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={tranzakcióazonosító}<br/>))|
| | . . . <br/>Tranzakciós munka<br/>egyéb hivatkozásokon<br/> . . . |
| transfer(<br/>delivery-id=57, ...)<br/>{ AmqpValue (<br/>**Foga(txn-id=0, <br/> fail=false)**)}| ------> |  |
| | <------ | disposition( <br/> first=57, last=57, <br/>state=**Accepted()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Üzenet küldése tranzakcióban

Minden tranzakciós munka a txn-id azonosítót magában vevő tranzakciós kézbesítési `transactional-state` állapotmal történik. Üzenetek küldése esetén a tranzakciós állapotot az üzenet átviteli kerete továbbítja. 

| Ügyfél (vezérlő) | Irány | Service Bus (koordinátor) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={tranzakcióazonosító}<br/>))|
| transfer(<br/>handle=1,<br/>delivery-id=1, <br/>**state= <br/> TransactionalState( <br/> txn-id=0)**)<br/>{ hasznos tartalom }| ------> |  |
| | <------ | disposition( <br/> first=1, last=1, <br/>state=**TransactionalState( <br/> txn-id=0, <br/> outcome=Accepted() ))**|

#### <a name="disposing-a-message-in-a-transaction"></a>Üzenet felfedése egy tranzakcióban

Az üzenetek eloszlása olyan műveleteket tartalmaz, mint a `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer` . Ahhoz, hogy ezeket a műveleteket egy tranzakción belül hajtsa végre, adja át a `transactional-state` et a diszpozícióval együtt.

| Ügyfél (vezérlő) | Irány | Service Bus (koordinátor) |
| :--- | :---: | :--- |
| transfer(<br/>delivery-id=0, ...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | disposition( <br/> first=0, last=0, <br/>state=Declared(<br/>txn-id={tranzakcióazonosító}<br/>))|
| | <------ |transfer(<br/>handle=2,<br/>delivery-id=11, <br/>state=null)<br/>{ hasznos tartalom }|  
| disposition( <br/> first=11, last=11, <br/>state=**TransactionalState( <br/> txn-id=0, <br/> outcome=Accepted() ))**| ------> |


## <a name="advanced-service-bus-capabilities"></a>Speciális Service Bus képességek

Ez a szakasz az AMQP Azure Service Bus kiegészítő vázlatán alapuló, az AMQP műszaki bizottságának fejlesztés alatt álló speciális funkcióit tartalmazza. Service Bus a vázlatok legújabb verzióit valósítja meg, és változásokat vezet be, amikor ezek a vázlatok elérik a szabványos állapotot.

> [!NOTE]
> Service Bus üzenetkezelés speciális műveletei kérés-válasz mintán keresztül támogatottak. A műveletek részleteit a következő cikkben, az [AMQP 1.0 Service Bus: request-response-based operations (kérés-válasz-alapú műveletek)című cikkben olvashatja.](service-bus-amqp-request-response.md)
> 
> 

### <a name="amqp-management"></a>AMQP-felügyelet

Az AMQP felügyeleti specifikáció a cikkben tárgyalt első vázlatos bővítmény. Ez a specifikáció az AMQP protokollra rétegzett protokollkészletet határozza meg, amelyek lehetővé teszik az üzenetkezelési infrastruktúrával való felügyeleti interakciót az AMQP-on keresztül. A specifikáció olyan általános műveleteket határoz meg,  mint például a *létrehozás,* *olvasás,* frissítés *és* törlés az üzenetküldési infrastruktúrán belüli entitások és lekérdezési műveletek egy halmazának kezeléséhez.

Ezekhez a kézmozdulathoz szükség van egy kérés/válasz interakcióra az ügyfél és az üzenetkezelési infrastruktúra között, ezért a specifikáció határozza meg, hogyan modellelhető ez az interakciós minta az AMQP-ben: az ügyfél csatlakozik az üzenetkezelési infrastruktúrához, munkamenetet kezdeményez, majd létrehoz egy hivatkozáspárt. Az egyik hivatkozáson az ügyfél küldőként, a másikon fogadóként működik, így létrehoz egy hivatkozáspárt, amely kétirányú csatornaként is működik.

| Logikai művelet | Ügyfél | Service Bus |
| --- | --- | --- |
| Kérés válaszának elérési útja |--> attach(<br/>name={*hivatkozás neve*},<br/>handle={*numerikus leíró*},<br/>role=**sender**,<br/>source=**null**,<br/>target="myentity/$management"<br/>) |Nincs művelet |
| Kérés válaszának elérési útja |Nincs művelet |\<-- attach(<br/>name={*hivatkozás neve*},<br/>handle={*numerikus leíró*},<br/>role=**receiver**,<br/>source=null,<br/>target="myentity"<br/>) |
| Kérés válaszának elérési útja |--> attach(<br/>name={*hivatkozás neve*},<br/>handle={*numerikus leíró*},<br/>role=**receiver**,<br/>source="myentity/$management",<br/>target="myclient$id"<br/>) | |
| Kérés válaszának elérési útja |Nincs művelet |\<-- attach(<br/>name={*hivatkozás neve*},<br/>handle={*numerikus leíró*},<br/>role=**sender**,<br/>source="myentity",<br/>target="myclient$id"<br/>) |

Ennek a hivatkozáspárnak a megvalósítása egyszerű: a kérés egy, az üzenetkezelési infrastruktúrán belüli entitásnak küldött üzenet, amely megérti ezt a mintát. Ebben a kérelemüzenetben a properties (tulajdonságok) szakaszban található  *reply-to (válasz-válasz)* mező annak a hivatkozásnak a célazonosítója, amelyre a választ küldeni kell.  A kezelő entitás feldolgozza a kérést, majd kézbesíti a választ azon a hivatkozáson keresztül, amelynek célazonosítója megegyezik a jelzett *válasz-válasz azonosítóval.* 

A minta nyilvánvalóan megköveteli, hogy az ügyféltároló és a válasz célhelyhez létrehozott ügyfél által létrehozott azonosító egyedi legyen az összes ügyfélen, és biztonsági okokból szintén nehezen megjósolható.

A felügyeleti protokollhoz és az összes többi protokollhoz használt üzenetcserék, amelyek ugyanazt a mintát használják, alkalmazásszinten történnek; nem határoznak meg új AMQP protokollszintű kézmozdulatokat. Ez szándékos, hogy az alkalmazások azonnal kihasználják ezeket a bővítményeket a megfelelő AMQP 1.0-vermekkel.

Service Bus jelenleg nem valósítja meg a felügyeleti specifikáció egyik alapvető funkcióját sem, de a felügyeleti specifikáció által meghatározott kérés/válasz minta alapvető a jogcímalapú biztonsági funkció és a következő szakaszokban tárgyalt fejlett képességek szinte mindegyikéhez:

### <a name="claims-based-authorization"></a>Jogcímalapú engedélyezés

Az AMQP jogcímalapú hitelesítés (CBS) specifikációs vázlata a felügyeleti specifikáció kérés-válasz mintára épül, és általánosított modellt ír le az összevont biztonsági jogkivonatok AMQP-val való használatára.

A bevezetésben tárgyalt AMQP alapértelmezett biztonsági modellje a SASL-alapú, és integrálható az AMQP kapcsolati kézfogással. Az SASL használatának előnye, hogy olyan olyan kitehető modellt biztosít, amelyhez definiálva van egy mechanizmuskészlet, amelyből az SASL-t hivatalosan használó protokollok előnyösek. Ezek között a mechanizmusok "EGYSZERŰek" a felhasználónevek és jelszavak átviteléhez, az "EXTERNAL" a TLS-szintű biztonsághoz való kötéshez, az "ANONYMOUS" a kifejezett hitelesítés/engedélyezés hiányának kifejezésére, valamint számos további mechanizmus, amelyek lehetővé teszik a hitelesítési és/vagy engedélyezési hitelesítő adatok vagy jogkivonatok átadását.

Az AMQP SASL-integrációjának két hátránya van:

* Minden hitelesítő adat és jogkivonat hatóköre a kapcsolatra terjed ki. Előfordulhat, hogy az üzenetkezelési infrastruktúra entitásonként szeretne különböző hozzáférés-vezérlést biztosítani; Például lehetővé teszi, hogy egy jogkivonat bearerje elküldje az A üzenetsorba, de a B üzenetsorba ne. Ha az engedélyezési környezet a kapcsolatra van kikötve, nem lehet egyetlen kapcsolatot használni, és mégis különböző hozzáférési jogkivonatokat használni az A üzenetsorhoz és a B üzenetsorhoz.
* A hozzáférési jogkivonatok általában csak korlátozott ideig érvényesek. Ez az érvényesség megköveteli, hogy a felhasználó rendszeres időközönként újra igényelje a jogkivonatokat, és lehetőséget biztosít a jogkivonat-kibocsátónak, hogy visszautasítsa a friss jogkivonat kiadását, ha a felhasználó hozzáférési engedélyei módosulnak. Az AMQP-kapcsolatok hosszú ideig is lehetnek. Az SASL-modell csak a csatlakozáskor ad lehetőséget a jogkivonat beállításának, ami azt jelenti, hogy az üzenetkezelési infrastruktúrának le kell bontani az ügyfelet a jogkivonat lejártakor, vagy el kell fogadnia annak kockázatát, hogy a hozzáférési jogokat ideiglenesen visszavonták az ügyféllel való folyamatos kommunikáció engedélyezésével.

Az Service Bus által megvalósított AMQP CBS-specifikáció elegáns áthidaló megoldást kínál mindkét problémára: Lehetővé teszi, hogy az ügyfél hozzáférési jogkivonatokat társítson az egyes csomópontokkal, és frissítse ezeket a jogkivonatokat a lejáratuk előtt, az üzenetfolyam megszakítása nélkül.

A CBS egy virtuális felügyeleti csomópontot határoz meg $cbs *nevű* virtuális felügyeleti csomópontot, amelyet az üzenetkezelési infrastruktúra biztosít. A felügyeleti csomópont fogadja a jogkivonatokat az üzenetkezelési infrastruktúra többi csomópontja nevében.

A protokoll kézmozdulat a felügyeleti specifikációban meghatározott kérés-válasz csere. Ez azt jelenti, hogy az  ügyfél létrehoz egy pár hivatkozást a $cbs-csomóponttal, majd továbbít egy kérést a kimenő hivatkozáson, majd megvárja a választ a bejövő hivatkozáson.

A kérésüzenet az alábbi alkalmazástulajdonságokat tartalmazza:

| Kulcs | Választható | Érték típusa | Érték tartalma |
| --- | --- | --- | --- |
| művelet |Nem |sztring |**put-token** |
| típus |Nem |sztring |A beírt jogkivonat típusa. |
| name |Nem |sztring |A "célközönség", amelyre a jogkivonat vonatkozik. |
| Lejárati |Igen |időbélyeg |A jogkivonat lejárati ideje. |

A *name tulajdonság* azonosítja azt az entitást, amelyhez a jogkivonatot társítja. Ebben Service Bus ez az üzenetsor vagy témakör/előfizetés elérési útja. A *type tulajdonság* azonosítja a jogkivonat típusát:

| Jogkivonat típusa | Jogkivonat leírása | Törzs típusa | Jegyzetek |
| --- | --- | --- | --- |
| Jwt |JSON-webtoken (JWT) |AMQP-érték (sztring) | |
| servicebus.windows.net:sastoken |Service Bus SAS-jogkivonat használata |AMQP-érték (sztring) |- |

Jogkivonatok ferenciajogok. Service Bus három alapvető jogról van szó: a "Küldés" engedélyezi a küldést, a "Listen" (Figyelés) engedélyezi a fogadást, a "Kezelés" pedig lehetővé teszi az entitások kezelését. Service Bus SAS-jogkivonatok a névtéren vagy entitáson konfigurált szabályokra vonatkoznak, és ezek a szabályok jogosultságokkal vannak konfigurálva. Ha a jogkivonatot a szabályhoz társított kulccsal írja alá, a jogkivonat így a megfelelő jogosultságokat fejezi ki. A *put-tokent* használó entitáshoz társított jogkivonat lehetővé teszi a csatlakoztatott ügyfél számára, hogy jogkivonati jogosultságok szerint kommunikálja az entitást. Egy hivatkozáshoz, ahol az ügyfél átveszi a *küldő* szerepkört, a "Küldés" jog szükséges; ha a fogadó *szerepkört veszi* át, a "Figyel" jog szükséges.

A válaszüzenet az alábbi *alkalmazástulajdonság-értékeket* tartalmazza

| Kulcs | Választható | Érték típusa | Érték tartalma |
| --- | --- | --- | --- |
| állapotkód |Nem |int |HTTP-válaszkód **[RFC2616]**. |
| status-description (állapot leírása) |Igen |sztring |Az állapot leírása. |

Az ügyfél többször is *hívhatja a put-tokent* az üzenetkezelési infrastruktúra bármely entitása esetében. A jogkivonatok hatóköre az aktuális ügyfélre terjed ki, és az aktuális kapcsolatra van horgonyozva, ami azt jelenti, hogy a kiszolgáló eldobja a megőrzott jogkivonatokat, amikor a kapcsolat megszakad.

A jelenlegi Service Bus implementáció csak a CBS-t engedélyezi a "ANONYMOUS" SASL-metódussal együtt. Az SSL/TLS-kapcsolatnak mindig léteznie kell az SASL-kézfogás előtt.

Ezért a kiválasztott AMQP 1.0-ügyfélnek támogatnia kell a ANONYMOUS mechanizmust. A névtelen hozzáférés azt jelenti, hogy a kezdeti kapcsolati kézfogás, beleértve a kezdeti munkamenet létrehozását is, anélkül történik, Service Bus tudná, ki hoz létre kapcsolatot.

Miután létrejött a kapcsolat és a munkamenet,  csak a hivatkozások csatolása $cbs csomópontra és a *put-token* kérelem küldése engedélyezett műveletek. Egy érvényes jogkivonatot sikeresen be kell állítani egy *put-token* kérelem használatával egy entitáscsomóponthoz a kapcsolat létrejötte után 20 másodpercen belül, ellenkező esetben a kapcsolat el lett Service Bus.

Ezt követően az ügyfél felelős a jogkivonat lejáratának nyomon követéséért. Amikor egy jogkivonat lejár, Service Bus a rendszer azonnal eldobja a megfelelő entitáshoz tartozó kapcsolat összes hivatkozását. A probléma elkerülése érdekében az ügyfél bármikor lecserélheti a csomópont jogkivonatát egy újra a virtuális *$cbs* felügyeleti csomóponton ugyanazon *put-token* kézmozdulattal, és anélkül, hogy akadályozná a különböző hivatkozásokon áthaladó hasznos adatforgalmat.

### <a name="send-via-functionality"></a>Küldési funkció

[A Send-via /Transfer Sender](service-bus-transactions.md#transfers-and-send-via) egy olyan funkció, amely lehetővé teszi, hogy a Service Bus egy adott üzenetet továbbküld egy célentitásnak egy másik entitáson keresztül. Ez a funkció egyetlen tranzakció entitásai közötti műveletek végrehajtásához használható.

Ezzel a funkcióval létrehoz egy küldőt, és létrehozza a `via-entity` hivatkozását. A hivatkozás létrehozása során további információk átadása szükséges a hivatkozáson található üzenetek/átvitelek valódi célhelyének létrehozására. Ha a csatolás sikeres volt, a hivatkozáson küldött összes  üzenetet a rendszer automatikusan továbbítja a célentitásnak *a-entitáson keresztül.* 

> Megjegyzés: A hivatkozás létrehozása előtt a hitelesítést *entitáson és* célentitáson keresztül is végre kell hajtanunk. 

| Ügyfél | Irány | Service Bus |
| :--- | :---: | :--- |
| attach(<br/>name={link name},<br/>role=sender,<br/>source={ügyfélkapcsolat azonosítója},<br/>target=**{via-entity}**,<br/>**properties=map [( <br/> com.microsoft:transfer-destination-address= <br/> {destination-entity} )]** ) | ------> | |
| | <------ | attach(<br/>name={hivatkozás neve},<br/>role=receiver,<br/>source={ügyfélkapcsolat azonosítója},<br/>target={via-entity},<br/>properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )] ) |

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az AMQP-ről, látogasson el a következő hivatkozásokra:

* [Service Bus AMQP áttekintése]
* [AMQP 1.0-támogatás particionált Service Bus üzenetsorok és témakörök számára]
* [AMQP Service Bus Windows Server rendszerben]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Service Bus AMQP áttekintése]: service-bus-amqp-overview.md
[AMQP 1.0-támogatás particionált Service Bus üzenetsorok és témakörök számára]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)