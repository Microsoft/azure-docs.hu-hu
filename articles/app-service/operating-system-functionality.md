---
title: Operációs rendszer funkcionalitása
description: Ismerkedjen meg a Windows Azure App Service operációs rendszerbeli funkcióival. Ismerje meg, hogy az alkalmazás milyen típusú fájl-, hálózati és beállításjegyzék-hozzáférést kap.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: 949e408544e25cb55622cf2a1b1d2dddb92350a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96001507"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Operációs rendszer funkciójának Azure App Service
Ez a cikk a [Azure app Serviceon](./overview.md)futó összes Windows-alkalmazás számára elérhető általános alapkonfiguráció operációs rendszer funkcióit ismerteti. Ez a funkció magában foglalja a fájl-, hálózat-és beállításjegyzék-hozzáférést, valamint a diagnosztikai naplókat és eseményeket. 

> [!NOTE] 
> A App Service [linuxos alkalmazásai](overview.md#app-service-on-linux) saját tárolókban futnak. A gazdagép operációs rendszeréhez való hozzáférés nem engedélyezett, a tárolóhoz rendszergazdai hozzáférése van. Hasonlóképpen, a [Windows-tárolókban futó alkalmazások](quickstart-custom-container.md?pivots=container-windows)esetében rendszergazdai hozzáféréssel rendelkezik a tárolóhoz, de nem fér hozzá a gazdagép operációs rendszeréhez. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>App Service csomag szintjei
App Service a több-bérlős üzemeltetési környezetben futtatja az alkalmazásokat. Az **ingyenes** és a **közös** szinten üzembe helyezett alkalmazások megosztott virtuális gépeken futnak munkavégző folyamatokban, míg a **standard** és a **prémium** csomagokban üzembe helyezett alkalmazások kifejezetten az egyetlen ügyfélhez társított alkalmazások számára dedikált virtuális gépeken futnak.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Mivel App Service támogatja a különböző rétegek közötti zökkenőmentes skálázást, a App Service alkalmazások számára kényszerített biztonsági beállítások változatlanok maradnak. Ez biztosítja, hogy az alkalmazások ne legyenek hirtelen eltérően viselkednek, nem várt módon, amikor App Service megtervezni az egyik rétegről a másikra való váltást.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Fejlesztési keretrendszerek
App Service díjszabási szintjei szabályozzák az alkalmazások számára elérhető számítási erőforrások (CPU, lemezes tárolás, memória és hálózati forgalom) mennyiségét. Az alkalmazások számára elérhető keretrendszer-funkcionalitás szélessége azonban a skálázási szintektől függetlenül változatlan marad.

App Service számos fejlesztési keretrendszert támogat, többek között a ASP.NET, a klasszikus ASP, a node.js, a PHP és a Python alkalmazást, amelyek mindegyike az IIS-en belül fut. A biztonsági konfiguráció egyszerűsítése és normalizálása érdekében App Service alkalmazások általában a különböző fejlesztési keretrendszereket futtatják az alapértelmezett beállításokkal. Az alkalmazások konfigurálásának egyik módja az, hogy testre szabhatja az API Surface területét és funkcióit az egyes fejlesztési keretrendszerekhez. A App Service ehelyett inkább általános megközelítést tesz lehetővé azáltal, hogy az alkalmazás fejlesztői keretrendszere alapján az operációs rendszer működésének általános alapkonfigurációját is felhasználja.

A következő részekben App Service alkalmazások számára elérhető általános operációsrendszer-funkciók összegzése látható.

<a id="FileAccess"></a>

## <a name="file-access"></a>Fájl-hozzáférés
Különböző meghajtók léteznek App Serviceon belül, beleértve a helyi meghajtókat és a hálózati meghajtókat.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Helyi meghajtók
A App Service egy olyan szolgáltatás, amely az Azure Pásti (platform as Service) infrastruktúrájának tetején fut. Ennek eredményeképpen a virtuális géphez csatolt helyi meghajtók ugyanazok a meghajtók, amelyek az Azure-ban futó feldolgozói szerepkörök számára is elérhetők. Ide tartoznak az alábbiak:

- Operációs rendszer meghajtója (a D:\ meghajtó
- Olyan alkalmazás-meghajtó, amely kizárólag App Service által használt Azure Package cspkg-fájlokat tartalmaz (és nem érhető el az ügyfelek számára)
- "User" meghajtó (a C:\ meghajtó), amelynek mérete a virtuális gép méretétől függően változik. 

Fontos, hogy az alkalmazás növekedésével figyelje a lemez kihasználtságát. Ha elérte a lemezkvóta elérését, az hatással lehet az alkalmazására. Például: 

- Az alkalmazás olyan hibát okozhat, amely nem elegendő helyet jelez a lemezen.
- Előfordulhat, hogy a kudu-konzolra való tallózáskor lemezhibák jelenhetnek meg.
- Az Azure DevOps vagy a Visual studióból történő üzembe helyezés sikertelen lehet `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)` .
- Az alkalmazás teljesítménye csökkenhet.

<a id="NetworkDrives"></a>

### <a name="network-drives-unc-shares"></a>Hálózati meghajtók (UNC-megosztások)
App Service egyik egyedi aspektusa, amely az alkalmazások üzembe helyezését és karbantartását teszi egyértelművé, hogy az összes felhasználói tartalmat egy UNC-megosztáson tárolja. Ez a modell jól mutat a különböző elosztott terhelésű kiszolgálókkal rendelkező helyszíni webüzemeltetési környezetek által használt tartalom tárolására. 

App Service belül az egyes adatközpontokban létrehozott UNC-megosztások száma. Az egyes adatközpontokban lévő összes ügyfél felhasználói tartalmának százalékos aránya az egyes UNC-megosztásokhoz van lefoglalva. Emellett az egyetlen ügyfél előfizetéséhez tartozó összes fájl tartalma mindig ugyanarra az UNC-megosztásra kerül. 

Az Azure-szolgáltatások működése miatt az UNC-megosztás üzemeltetéséhez felelős adott virtuális gép idővel változhat. Garantáljuk, hogy az UNC-megosztásokat a különböző virtuális gépek csatlakoztatják, ahogy azokat a normál Azure-műveletek során felhasználják. Emiatt az alkalmazások soha nem hajtanak végre olyan, nehezen kódolt feltételezéseket, amelyekkel az UNC-fájl elérési útjában lévő információk stabilak maradnak az idő múlásával. Ehelyett a App Service által biztosított kényelmes *faux* Absolute Path **D:\home\site** kell használniuk. Ez a faux abszolút elérési út egy olyan hordozható, alkalmazás-és felhasználói agnosztikus módszert biztosít, amely az egyik saját alkalmazására hivatkozik. A **D:\home\site** használatával az egyik átviheti a megosztott fájlokat az alkalmazásból az alkalmazásba anélkül, hogy új abszolút elérési utat kellene konfigurálnia az egyes átvitelekhez.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Az alkalmazáshoz megadott fájl-hozzáférési típusok
Minden ügyfél előfizetése fenntartott címtár-struktúrával rendelkezik egy adott UNC-megosztáson az adatközponton belül. Egy ügyfél több alkalmazást is létrehozhat egy adott adatközponton belül, így az egyetlen ügyfél-előfizetéshez tartozó összes címtár ugyanazon az UNC-megosztáson jön létre. A megosztás tartalmazhat olyan címtárakat is, mint például a tartalom, a hiba és a diagnosztikai naplók, valamint a verziókövetés által létrehozott alkalmazás korábbi verziói. Ahogy az a várt módon, az ügyfél alkalmazás-könyvtárai olvasási és írási hozzáféréshez érhetők el futásidőben az alkalmazás kódjával.

Az alkalmazást futtató virtuális géphez csatlakoztatott helyi meghajtókon App Service a C:\ egy darab tárterületet foglal le. az alkalmazás-specifikus ideiglenes helyi tárterület meghajtója. Bár az alkalmazások teljes írási/olvasási hozzáféréssel rendelkeznek a saját ideiglenes helyi tárolójához, a tárolót valóban nem közvetlenül az alkalmazás kódjához kívánja használni. Ehelyett a cél az, hogy ideiglenes file Storage-t biztosítson az IIS és a webalkalmazási keretrendszerek számára. A App Service az egyes alkalmazások számára elérhető ideiglenes helyi tárterületet is korlátozza, így megakadályozva, hogy az egyes alkalmazások túlzott mennyiségű helyi tárterületet is igénybe vehetnek.

Két példa arra, hogy a App Service hogyan használja az ideiglenes helyi tárolást az ideiglenes ASP.NET-fájlok és az IIS-hez tömörített fájlok könyvtára számára. A ASP.NET-fordítási rendszer az "ideiglenes ASP.NET fájlok" könyvtárat használja ideiglenes fordítási gyorsítótárként. Az IIS az "IIS ideiglenes tömörített fájlok" könyvtárat használja a tömörített válasz kimenetének tárolására. Mindkét fájltípust (valamint másokat) a App Service az alkalmazáson belüli ideiglenes helyi tárterületre rendeli hozzá. Ez az újramegfeleltetés biztosítja, hogy a funkciók a várt módon folytatódnak.

A App Serviceban lévő alkalmazások véletlenszerűen egyedi, alacsony jogosultsági szintű munkavégző folyamat identitásának nevezett "alkalmazáskészlet-identitás" néven futnak, amely az itt olvasható: [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities) . Az alkalmazás kódja ezt az identitást használja az operációs rendszer meghajtójának alapszintű írásvédett eléréséhez (a D:\ meghajtó). Ez azt jelenti, hogy az alkalmazás kódja listázhatja az általános címtár-struktúrákat, és beolvashatja az operációs rendszer meghajtóján található általános fájlokat Bár ez némileg szélesebb körű hozzáférésnek tűnhet, ugyanazok a könyvtárak és fájlok érhetők el, amikor feldolgozói szerepkört helyez üzembe egy Azure által üzemeltetett szolgáltatásban, és beolvassa a meghajtó tartalmát. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Fájlhoz való hozzáférés több példány között
A kezdőkönyvtár tartalmaz egy alkalmazás tartalmát, és az alkalmazás kódja írhat. Ha egy alkalmazás több példányon fut, a kezdőkönyvtár az összes példány között meg van osztva, így minden példány ugyanazt a könyvtárat látja. Ha például egy alkalmazás feltölti a feltöltött fájlokat a kezdőkönyvtárba, a fájlok azonnal elérhetők lesznek az összes példány számára. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Hálózati hozzáférés
Az alkalmazás kódja TCP/IP-és UDP-alapú protokollok használatával kimenő hálózati kapcsolatokat hajthat végre a külső szolgáltatásokat közzétevő internetes elérhető végpontok számára. Az alkalmazások ezeket a protokollokat használhatják az Azure-ban lévő szolgáltatásokhoz való csatlakozásra, például HTTPS-kapcsolatok létrehozásával SQL Databasehoz.

Az alkalmazások számára korlátozott a lehetőség, hogy egy helyi visszacsatolási kapcsolatot hozzon létre, és egy alkalmazás figyelje a helyi visszacsatolási szoftvercsatornát. Ez a funkció elsősorban olyan alkalmazások engedélyezésére szolgál, amelyek a helyi visszacsatolási szoftvercsatornák használatát figyelik a funkcióik részeként. Minden alkalmazás "privát" visszacsatolási kapcsolatban látja a következőt:. Az "A" alkalmazás nem tud figyelni a "B" alkalmazás által létesített helyi visszacsatolási szoftvercsatornára.

A nevesített csöveket a folyamaton kívüli kommunikációs (IPC) mechanizmusok is támogatják, amelyek együttesen futtatnak egy alkalmazást. Az IIS FastCGI-modul például a nevesített csövekre támaszkodik, hogy összehangolja a PHP-oldalakat futtató egyes folyamatokat.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Kódfuttatást, folyamatokat és memóriát
Ahogy azt korábban említettük, az alkalmazások az alacsony jogosultságú munkavégző folyamatokon belül futnak egy véletlenszerű alkalmazáskészlet-identitás használatával. Az alkalmazás kódja hozzáfér a munkavégző folyamathoz társított memóriához, valamint a CGI-folyamatok vagy más alkalmazások által elindított alárendelt folyamatokhoz is. Azonban az egyik alkalmazás nem fér hozzá egy másik alkalmazáshoz tartozó memóriához vagy adatszolgáltatáshoz, még akkor is, ha ugyanazon a virtuális gépen van.

Az alkalmazások futtathatnak támogatott webfejlesztési keretrendszerek által írt parancsfájlokat vagy lapokat. App Service a webes keretrendszer beállításait nem konfigurálja több korlátozott üzemmódra. A App Service futó ASP.NET-alkalmazások például a "teljes" megbízhatósági kapcsolaton keresztül futnak, és nem sokkal korlátozott megbízhatósági módra. A webes keretrendszerek, beleértve a klasszikus ASP és a ASP.NET is, meghívhatják a folyamaton kívüli COM-összetevőket (de nem a feldolgozott COM-összetevőket), például az ADO (ActiveX Data Objects) szolgáltatást, amelyek alapértelmezés szerint regisztrálva vannak a Windows operációs rendszeren.

Az alkalmazások tetszőleges programkódot kaphatnak és futtathatnak. Lehetővé teszi, hogy az alkalmazások olyan műveleteket végezzenek, mint a parancs-rendszerhéj vagy egy PowerShell-szkript futtatása. Azonban bár tetszőleges kód és folyamat is elindítható egy alkalmazásból, a végrehajtható programok és parancsfájlok továbbra is a szülő alkalmazáskészlethez megadott jogosultságokra korlátozódnak. Egy alkalmazás például olyan végrehajtható fájlt kaphat, amely egy kimenő HTTP-hívást kezdeményez, de ugyanezen végrehajtható fájl nem próbálkozhat a virtuális gép IP-címének lekötésével a hálózati adapterről. A kimenő hálózati hívások engedélyezése alacsony jogosultsági szintű programkódot tesz lehetővé, de a virtuális gépek hálózati beállításainak újrakonfigurálására tett kísérlethez rendszergazdai jogosultságok szükségesek.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnosztikai naplók és események
A naplózási adatok egy másik adathalmaz, amelyet egyes alkalmazások megpróbálnak elérni. A App Service-ban futó kód számára elérhető naplózási információk az alkalmazás által könnyen elérhető diagnosztikai és naplózási információkat tartalmaznak. 

Az aktív alkalmazások által létrehozott W3C HTTP-naplók például az alkalmazáshoz létrehozott hálózati megosztási helyen, vagy a blob Storage-ban elérhető naplófájlokban érhetők el, ha az ügyfél W3C-naplózást állított be a tárolóba. Az utóbbi lehetőség lehetővé teszi, hogy nagy mennyiségű naplót gyűjtsön anélkül, hogy meghaladják a hálózati megosztáshoz társított fájl tárolási korlátait.

Hasonló módon, a .NET-alkalmazásokból származó valós idejű diagnosztikai információk a .NET-nyomkövetési és diagnosztikai infrastruktúra használatával is naplózhatók, és lehetőség van arra, hogy a nyomkövetési adatokat az alkalmazás hálózati megosztására vagy egy blob Storage-helyre írja.

Az alkalmazások számára nem elérhető diagnosztikai naplózási és nyomkövetési területek a Windows ETW eseményei és az általános Windows-eseménynaplók (például a rendszer, az alkalmazás és a biztonsági eseménynaplók). Mivel a ETW-nyomkövetési adatok (a megfelelő ACL-ekkel együtt) a teljes gépen elérhetők, a ETW-események olvasási és írási hozzáférése le van tiltva. A fejlesztők megtekinthetik, hogy az API-hívások olvasási és írási ETW, valamint a Windows-eseménynaplók gyakran működnek, de ennek oka, hogy App Service "szimulálják" a hívásokat úgy, hogy azok sikeresnek jelenjenek meg. A valóságban az alkalmazás kódjának nincs hozzáférése ehhez az eseményhez.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Beállításjegyzék-hozzáférés
Az alkalmazások csak olvasási hozzáféréssel rendelkeznek a virtuális gép beállításjegyzékéhez, amelyen futnak. A gyakorlatban ez olyan beállításkulcsokat jelent, amely lehetővé teszi, hogy az alkalmazások csak olvasási hozzáférést engedélyezzenek a helyi felhasználók csoport számára. A beállításjegyzék egyik területe, amely jelenleg nem támogatott olvasási vagy írási hozzáférés esetén, a HKEY \_ aktuális \_ felhasználói struktúrája.

A beállításjegyzékhez való írási hozzáférés le van tiltva, beleértve a felhasználónkénti beállításkulcsok elérését is. Az alkalmazás szemszögéből az írási hozzáférés a beállításjegyzékhez soha nem támaszkodhat az Azure-környezetben, mivel az alkalmazások a különböző virtuális gépeken telepíthetők át (és nem). Az egyetlen alkalmazástól függő, az alkalmazáson belüli, az App Service UNC-megosztásokon tárolt alkalmazások közötti tartalmi könyvtár szerkezete. 

## <a name="remote-desktop-access"></a>Távoli asztali hozzáférés

App Service nem biztosít távoli asztali hozzáférést a virtuálisgép-példányokhoz.

## <a name="more-information"></a>További információ

[Azure app Service sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) – a app Service végrehajtási környezetével kapcsolatos legfrissebb információk. Ezt az oldalt közvetlenül a App Service fejlesztői csapat tartja karban.