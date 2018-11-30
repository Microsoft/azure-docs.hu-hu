---
title: Biztonsági riasztások típus szerint az Azure Security Centerben | Microsoft Docs
description: Ez a cikk bemutatja az Azure Security Centerben elérhető biztonsági riasztások különböző fajtáit.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2018
ms.author: rkarlin
ms.openlocfilehash: 24c6487ee7ec7d8398f933e29ca51cc9e390f47f
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2018
ms.locfileid: "52633265"
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Az Azure Security Center biztonsági riasztásainak megismerése
Ez a cikk segít megismerni az Azure Security Centerben elérhető biztonsági riasztások különböző típusait, valamint a kapcsolódó elemzéseket. A riasztások és incidensek kezelésével kapcsolatos további információkért olvassa el a [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) című cikket.

A speciális észlelések beállításához frissítsen az Azure Security Center Standard verzióra. A 60 napos próbaverzió ingyenes. A frissítéshez a **Biztonsági szabályzat** beállításnál válassza ki a kívánt [tarifacsomagot](security-center-azure-policy.md). További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

> [!NOTE]
> A Security Centernek korlátozott előzetes verzióként kiadott új észlelései naplózott rekordokat használnak egy közös naplózási keretrendszerben a Linux rendszerű gépek rosszindulatú viselkedésének észlelésére. Amennyiben csatlakozni szeretne az előzetes verzióhoz, küldjön [nekünk](mailto:ASC_linuxdetections@microsoft.com) egy e-mailt, amely tartalmazza az előfizetés-azonosítóit.

## <a name="what-type-of-alerts-are-available"></a>Milyen típusú riasztások állnak rendelkezésre?
Az Azure Security Center különféle [észlelési funkciók](security-center-detection-capabilities.md) segítségével riasztja az ügyfeleket a környezetüket célzó lehetséges támadások esetén. Ezek a riasztások értékes információkat tartalmaznak arról, hogy mi váltotta ki a riasztást, valamint a támadás forrásáról és az általa célba vett erőforrásokról. A riasztásokban található információk eltérhetnek a fenyegetés észleléséhez használt elemzés típusától függően. Az incidensek további környezeti információkat is tartalmazhatnak, amelyek hasznosak lehetnek a fenyegetések vizsgálata során.  Ez a cikk a következő típusú riasztásokról nyújt információkat:

* Virtuális gép működésének elemzése (VMBA)
* Hálózatelemzés
* SQL Database- és SQL Data Warehouse-elemzés
* Környezeti információk

## <a name="virtual-machine-behavioral-analysis"></a>Virtuális gép működésének elemzése
Az Azure Security Center a működéselemzéssel azonosítja a feltört erőforrásokat a virtuális gépek eseménynaplóinak az elemzése alapján. Ilyenek például a folyamat-létrehozási események és a bejelentkezési események. Ezenkívül megvizsgálja az összefüggéseket más jelekkel, hogy alátámassza a nagy lépétkű kampányok bizonyítékait.

> [!NOTE]
> Ha részletes tájékoztatást szeretne kapni a Security Center észlelési funkcióinak működéséről, tekintse meg [az Azure Security Center észlelési funkcióit ismertető](security-center-detection-capabilities.md) cikket.


### <a name="event-analysis"></a>Esemény elemzése
A Security Center fejlett elemzési módszerekkel azonosítja a feltört erőforrásokat a virtuális gépek eseménynaplóinak elemzése alapján. Ilyenek például a folyamat-létrehozási események és a bejelentkezési események. Ezenkívül megvizsgálja az összefüggéseket más jelekkel, hogy alátámassza a nagy lépétkű kampányok bizonyítékait.

* **A rendszer gyanús folyamat-végrehajtást észlelt**: A támadók gyakran próbálkoznak ártalmatlan folyamatnak álcázott kártékony kódok futtatásával. Ezek a riasztások jelzik, ha egy folyamat végrehajtására illik az alábbi jellemzők valamelyike:
    * A rendszer olyan folyamatot hajtott végre, amelyet közismerten ártó célokból szoktak használni. Az egyes parancsok ártalmatlannak jeleníthet meg, amíg a riasztás összességére vonatkozik az alábbi parancsok összesítést.
    * Egy folyamat végrehajtása ismeretlen helyről indult ki.
    * Egy folyamat végrehajtása olyan helyről indult ki, amelyen ismert gyanús fájlokkal osztozik.
    * Egy folyamat végrehajtása gyanús útvonalról történt.
    * Egy folyamat végrehajtásának a szokottól eltérő volt a kontextusa.
    * Egy folyamat végrehajtása egy szokatlan fiók használatával történt.
    * A rendszer gyanús kiterjesztésű folyamatot hajtott végre.
    * A rendszer gyanús kettős kiterjesztésű folyamatot hajtott végre.
    * A rendszer gyanús, a fájlnévben jobbról balra olvasott (RLO) karaktert tartalmazó folyamatot hajtott végre.
    * Egy folyamat, amelynek a neve hasonló, de más a gyakran futtatott folyamat lett végrehajtva
    * A rendszer egy ismert támadóeszközhöz kapcsolható névvel ellátott folyamatot hajtott végre.
    * A rendszer egy véletlenszerű névvel ellátott folyamatot hajtott végre.
    * A rendszer gyanús kiterjesztésű folyamatot hajtott végre.
    * A rendszer rejtett fájl futtatott.
    * A rendszer egy folyamatot egy másik, nem kapcsolódó folyamat alárendelt folyamataként hajtott végre.
    * Egy rendszerfolyamat szokatlan folyamatot hozott létre.
    * A Windows Update-szolgáltatás rendellenes folyamatot indított el.
    * Egy folyamat indítása szokatlan parancssorral történt. Ez azzal állhat kapcsolatban, amikor szabályszerű folyamatokat térítenek el kártékony tartalmak futtatása céljából.
    * Egy kísérlet történt a parancssorból egy mappa összes végrehajtható (*.exe) fájljának elindítására.
    * Egy folyamatot a PsExec segédprogram hajtott végre, amely folyamatok távoli futtatásához használható.
    * Az Apache Tomcat® fölérendelt végrehajtható fájlját (Tomcat#.exe) gyanús alárendelt folyamatok indítására használták, amelyek rosszindulatú parancsokat üzemeltethetnek vagy futtathatnak.
    * A Microsoft Windows „Programkompatibilitási segédet” (pcalua.exe) egy végrehajtható kód elindítására használták, amely akár rosszindulatú is lehet.
    * A rendszer gyanús sorozatos folyamatleállítást észlelt.
    * Az SVCHOST rendszerfolyamat végrehajtásának a szokottól eltérő volt a kontextusa.
    * Az SVCHOST rendszerfolyamat egy ritka szolgáltatáscsoportban lett végrehajtva.
    * A rendszer gyanús parancssort hajtott végre.
    * Egy PowerShell-szkript más ismert gyanús szkriptekkel közös jellemzőkkel rendelkezik.
    * A rendszer egy ismert rosszindulatú PowerShell PowerSploit-parancsmagot hajtott végre.
    * Egy beépített SQL-felhasználó olyan folyamatot hajtott végre, amelyet normális esetben nem tenne.
    * A rendszer egy Base-64 kódolású végrehajtható fájlt észlelt, amely arra utalhat, hogy egy támadó egy parancssorozat használatával menet közben létrehozott végrehajtható kód segítségével megkísérelte érvényteleníteni az észlelést.

* **RDP-erőforrás gyanús tevékenysége**: A támadók gyakran az RDP-hez hasonló nyitott felügyeleti portokat vesznek célba találgatásos támadásokkal. Ezek a riasztások gyanús távoli asztali bejelentkezési tevékenységre utalnak, amely a következőket jelentheti:
    * Kísérleteket tettek távoli asztali bejelentkezésre.
    * Kísérleteket tettek távoli asztali bejelentkezésre érvénytelen fiókokon keresztül.
    * Távoli asztali bejelentkezési kísérletekre került sor, amelyek némelyike tudtak sikeresen jelentkezzen be a gép.
* **SSH-erőforrás gyanús tevékenység**: A támadók gyakran az SSH-hoz hasonló nyitott felügyeleti portokat vesznek célba találgatásos támadásokkal. Ezek a riasztások gyanús SSH-bejelentkezési tevékenységre utalnak, amely a következőket jelentheti:
    * Sikertelen SSH-bejelentkezési kísérletekre került sor.
    * SSH-bejelentkezési kísérletekre került sor, amelyek egy része sikeres volt.
* **Gyanús WindowPosition beállításazonosító**: Ez a riasztás azt jelzi, hogy a WindowPosition beállításbeállításjegyzék-konfigurációjának módosítására tett kísérletre került sor, amely rejtett alkalmazásablakok jelenlétére utalhat az asztal nem látható területein.
* **Az AppLocker megkerülésére tett kísérlet**: Az AppLocker a Windowsban futó programok korlátozására használható, amellyel csökkenthető a rendszer kártevők általi fenyegetettsége. Ez a riasztás az AppLocker-korlátozások megkerülésére tett lehetséges kísérletet jelez, amely során megbízható (az AppLocker szabályzata által engedélyezett) végrehajtható fájlok használatával nem megbízható kód futtatását kísérelték meg.
* **Gyanús nevesítettcső-kommunikáció**: Ez a riasztás azt jelzi, hogy egy Windows-konzolparancs adatokat írt egy helyi nevesített csőre. A nevesített csöveket a támadók gyakran használják a rosszindulatú beágyazott elemek vezérlésére és a velük folytatott kommunikációra.
* **Végrehajtható fájl dekódolása a beépített certutil.exe eszköz használatával**: Ez a riasztás azt jelzi, hogy egy beépített rendszergazdai segédprogramot, a certutil.exe-t egy végrehajtható fájl dekódolására használták. A támadók gyakran használják különböző megbízható rendszergazdai eszközök funkcióit rosszindulatú műveletek elvégzésére. Például a certutil.exe vagy a hozzá hasonló eszközök rosszindulatú végrehajtható fájlok dekódolására használhatók, amelyeket aztán a rendszer végrehajt.
* **Egy eseménynapló törölve lett**: Ez a riasztás gyanús naplótörlési műveletet jelez, amelyet a támadók gyakran a tevékenységük nyomainak eltörlésére használnak.
* **IIS-naplófájlok letiltása és törlése**: Ez a riasztás az IIS-naplófájlok letiltását és/vagy törlését jelzi, amellyel a támadók gyakran a tevékenységük nyomait igyekeznek eltüntetni.
* **Gyanús fájltörlés**: Ez a riasztás gyanús fájltörlést jelez, amellyel egy esetleges támadó eltávolíthatja a rosszindulatú bináris fájlokra utaló bizonyítékokat.
* **Az összes fájl árnyékmásolata törölve lett**: Ez a riasztás azt jelzi, hogy az árnyékmásolatok törölve lettek.
* **Gyanús fájltörlési parancsok**: Ez a riasztás olyan systeminfo parancskombinációt jelez, amelyet gyakran behatolás utáni öntörlési tevékenységhez használnak.  Bár a *systeminfo.exe* megbízható Windows-eszköznek minősül, az egymás után kétszeri futtatása, majd ezt követően egy törlési parancs használata, ahogy ebben az esetben is történt, igen ritka.
* **Gyanús fióklétrehozás**: Ez a riasztás azt jelzi, hogy egy meglévő beépített rendszergazdai jogosultságú fiókhoz nagyon hasonló fiókot hoztak létre. Ezzel a technikával a támadók feltűnésmentesen hozhatnak létre rosszindulatú fiókokat.
* **Gyanús kötet-árnyékmásolati tevékenység**: Ez a riasztás árnyékmásolati törlési tevékenységet jelez az erőforráson. A kötet árnyékmásolata (Volume Shadow Copy, VSC) az adatok pillanatképeit tároló fontos összetevő. Ezt a tevékenységet, Zsarolóprogrammal társítva, de az is előfordulhat, hogy jogosult.
* **Windows beállításjegyzék adatmegőrzési metódusa**: Ez a riasztás egy végrehajtható kód megőrzésére tett kísérletet jelez a Windows beállításjegyzékben. A rosszindulatú folyamatok gyakran alkalmaznak ehhez hasonló technikákat, hogy a rendszer újraindítása után is megmaradjanak.
* **Gyanús új tűzfalszabály**: Ez a riasztás azt jelzi, hogy egy új tűzfalszabály lett hozzáadva a *netsh.exe* segítségével egy gyanús helyen található végrehajtható fájltól érkező forgalom engedélyezése érdekében.
* **Gyanús XCOPY-végrehajtások**: Ez a riasztás azt jelzi, hogy sikerült jelezze, hogy egyik gépet feltörték, és a kártevők használták XCOPY-végrehajtások egy sorozatát.
* **Jogi nyilatkozat jelenik meg a felhasználók bejelentkezésekor tiltási**: Ez a riasztás azt jelzi, hogy a beállításkulcsot, amely meghatározza, hogy jogi nyilatkozat módosítás megjelenik a felhasználók számára, amikor bejelentkeznek. Ezt a támadók gyakran alkalmazzák egy gazdagép feltörése után.
* **A rendszer kis- és nagybetűs karakterek rendellenes kombinációját észlelte a parancssorban**: Ez a riasztás kis- és nagybetűs karakterek kombinációjának használatát jelzi a parancssorban. Ezt a technikát a támadók a kis- és nagybetűket megkülönböztető vagy kivonatalapú gépi szabályok elől való elrejtőzésre használják.
* **Rejtjelezett parancssor**: Ez a riasztás azt jelzi, hogy a rendszer gyanús, rejtjelezésre utaló jeleket észlelt a parancssorban.
* **Több tartományi fiók lett lekérdezve**: A támadók gyakran kérdeznek le AD-tartományi fiókokat a felhasználók, tartományi rendszergazdai fiókok, tartományvezérlők és a tartományok közötti megbízhatósági kapcsolatok felderítése során. Ez a riasztás azt jelzi, hogy szokatlan mennyiségű, különböző tartományokban található fiókot kérdeztek le rövid időn belül.
* **Lehetséges helyi felderítési tevékenység**: Ez a riasztás azt jelzi, hogy végre lett hajtva felderítési tevékenységgel összefüggésbe hozható systeminfo parancsok kombinációját.  Bár a *systeminfo.exe* megbízható Windows-eszköznek minősül, egymás után kétszer igen ritkán futtatják.
* **Kulcslétrehozó végrehajtható fájl lehetséges futtatása**: Ez a riasztás olyan folyamat végrehajtását jelzi, amelynek neve egy kulcslétrehozó eszközre utal. Ezek az eszközök általában a szoftverlicencelési mechanizmusok kiiktatására szolgálnak, letöltésükkor azonban gyakran más rosszindulatú szoftverek is letöltődhetnek.
* **Gyanús végrehajtás a rundll32.exe használatával**: Ez a riasztás azt jelzi, hogy a rundll32.exe-t szokatlan névvel ellátott folyamat végrehajtására használták, amely megegyezik a támadók által a feltört gazdagépeken az implantátum első fázisának telepítése során alkalmazott elnevezési sémával.
* **A HTA és a PowerShell gyanús kombinációja**: Ez a riasztás azt jelzi, hogy egy Microsoft HTML-alkalmazásgazda (HTA) PowerShell-parancsokat indít el. Ezzel a technikával a támadók kártevő PowerShell-szkripteket indíthatnak.
* **Megváltozott egy beállításkulcs, amely kihasználható az UAC megkerülésére**: Ez a riasztás egy beállításkulcs megváltozását jelzi, amelyet a támadók gyakran arra használnak, hogy jogosultság nélküli hozzáférésük helyett (általános jogokkal rendelkező felhasználó) jogosultságokkal rendelkező (például rendszergazdai) hozzáféréshez jussanak egy feltört gazdagépen.
* **Gyanús tartománynév használata a parancssorban**: Ez a riasztás egy gyanús tartománynév használatát jelzi, amely bizonyíték lehet arra, hogy egy támadó kártékony eszközöket üzemeltet parancs- és vezérlési, valamint az adatok kiszűréséhez használt végpontokként.
* **Egy fiók több gazdagépen is létre lett hozva az elmúlt 24 órában**: Ez a riasztás azt jelzi, hogy ugyanazt a felhasználói fiókot több gazdagépen is megkísérelték létrehozni, amely bizonyíték lehet egy támadó oldalirányú mozgására a hálózaton, egy vagy több hálózati entitás feltörését követően.
* **Gyanús CACLS-használat a rendszer biztonsági szintjének csökkentése érdekében**: Ez a riasztás a hozzáférésvezérlés-módosítási lista (CACLS) módosítását jelzi. A támadók gyakran használják ezt a technikát arra, hogy teljes körű hozzáférést biztosítsanak a rendszer olyan bináris fájljai számára, mint az ftp.exe, a net.exe, a wscript.exe stb.
* **Gyanús Kerberos-aranyjegyes támadási paraméterek**: Ez a riasztás egy Kerberos-aranyjegyes támadásnak megfelelő paraméterekkel rendelkező parancssor végrehajtását jelzi. A feltört krbtgt kulcsokkal a támadók bármely felhasználó nevében eljárhatnak.
* **A WDigest UseLogonCredential beállításkulcs engedélyezése**: Ez a riasztás azt jelzi, hogy a beállításkulcs megváltozott-e, hogy a bejelentkezési hitelesítő adatait az LSA-memóriában majd memória begyűjtött is egyszerű szövegként kell tárolni.
* **A Telegram eszköz esetlegesen gyanús használata**: Ez a riasztás a Telegram nevű, ingyenes, felhőalapú azonnali üzenetküldő szolgáltatás telepítését jelzi, amelyet a támadók gyakran arra használnak, hogy rosszindulatú bináris fájlokat vigyenek át számítógépekre, mobiltelefonokra vagy táblagépekre.
* **Új ASEP létrehozása**: Ez a riasztás egy új ASEP (automatikus indításkiterjesztési pont) létrehozását jelzi, amely révén a parancssorban azonosított névvel ellátott folyamat automatikusan elindul, ezt pedig a támadók saját folyamataik megőrzésére használhatják.
* **Gyanús Set-ExecutionPolicy- és WinRM-módosítások**: Ez a riasztás olyan konfigurációmódosításokat jelez, amelyek a rosszindulatú ChinaChopper webshell használatával hozhatók összefüggésbe.
* **Kritikus szolgáltatások letiltása**: Ez a riasztás azt jelzi, hogy a „net.exe stop” parancsot a SharedAccesshez vagy a Windows Biztonsági központhoz hasonló kritikus szolgáltatások leállítására használták.
* **Az FTP -s kapcsolójának gyanús használata**: Ez a riasztás az FTP-k „-s” kapcsolójának használatát jelzi, amelyet kártevő programok használnak távoli FTP-kiszolgálókon való kapcsolódásra és további rosszindulatú bináris fájlok letöltésére.
* **A VBScript.Encode parancs gyanús végrehajtása**: Ez a riasztás a *VBScript.Encode* parancs végrehajtását jelzi, amely olvashatatlan szöveggé kódolja a szkripteket, így megnehezíti a felhasználóknak a kódok vizsgálatát.
* **VBScript HTTP-objektum lefoglalása**: Ez a riasztás azt jelzi, hogy a parancssorral egy VBScript-fájlt hoztak létre, amely rosszindulatú fájlok letöltésére használható.
* **Beragadó billentyűs támadás**: Ez a riasztás arra utalhat, hogy egy támadó megpróbálja kihasználni egy kisegítő lehetőség bináris fájlját (például a beragadó billentyűket, a képernyő-billentyűzetet, a narrátort), amellyel kiskaput biztosíthat magának a hozzáféréshez.
* **Petya zsarolóprogramra utaló jelek**: Ez a riasztás azt jelzi, hogy a rendszer a Petya zsarolóprogramra használatára utaló technikákat észlelt.
* **Az AMSI letiltására tett kísérlet**: Ez a riasztás a kártevőirtó vizsgálati felület (AMSI) letiltására tett kísérletet jelez, amellyel letiltható a kártevők észlelése.
* **Zsarolóprogramra utaló jelek**: Ez a riasztás olyan gyanús tevékenységet jelez, amely általában képernyőzároló és titkosító zsarolóprogramokkal hozható összefüggésbe.
* **Követőgyűjtemény gyanús kimeneti fájlja**: Ez a riasztás azt jelzi, hogy egy nyomkövetést (például egy hálózati tevékenységét) begyűjtöttek, és a kimenetét szokatlan fájltípusba továbbították.
* **Nagy kockázatú szoftver**: Ez a riasztás kártevők telepítésével összefüggésbe hozott szoftver telepítését jelzi. A támadók gyakran csomagolnak kártevőket a riasztásban szereplőhöz hasonló, alapvetően ártalmatlan eszközök mellé, majd csendes telepítéssel a háttérben telepítik őket.
* **Gyanús fájl létrehozása**: Ez a riasztás olyan folyamat létrehozását és végrehajtását jelzi, amelyet a támadók további kártevők letöltésére használnak a feltört gazdagépen, miután azon megnyitották egy adathalász dokumentum csatolmányát.
* **Gyanús hitelesítő adatok a parancssorban**: Ez a riasztás egy fájl futtatásához használt gyanús jelszót jelez. Ezt a módszert a támadók már bizonyítottan használták a Pirpi nevű kártevő futtatására.
* **Kártevő-átvivő lehetséges végrehajtása**: Ez a riasztás olyan fájlnevet jelez, amelyet a támadók kártevők telepítésére használtak.
* **Gyanús végrehajtás a rundll32.exe használatával**: Ez a riasztás azt jelzi, hogy a rundll32.exe-t egy notepad.exe vagy reg.exe végrehajtásához használták, amely egy támadók által használt folyamatbeszúrási módszernek felel meg.
* **Gyanús parancssori argumentumok**: Ez a riasztás gyanús parancssori argumentumokat jelez, amelyeket a HYDROGEN tevékenységi csoport által használt fordított felülettel (reverse shell) együtt használtak.
* **Gyanús hitelesítő adatok dokumentumokhoz**: Ez a riasztás egy gyanús, előre kiszámított, általános jelszókivonatot jelez, amelyet a rosszindulatú programok egy fájl végrehajtásához használnak.
* **Dinamikus PS-szkript konstrukció**: Ez a riasztás egy PowerShell-szkript dinamikus létrehozását jelzi. Ezt a módszert a támadók szkriptek fokozatos felépítésére használják az illetéktelen hálózati behatolást jelző (IDS) rendszerek megkerülése érdekében.
* **Metasploit mutatók**: Ez a riasztás a Metasploit-keretrendszer, amely számos funkciót és eszközt biztosít a kapcsolódó tevékenységet jelez.
* **Gyanús fióktevékenység**: Ez a riasztás egy géphez való csatlakozásra tett kísérletet jelez egy nemrégiben feltört fiók használatával.
* **Fióklétrehozás**: Ez a riasztás új fiók létrehozását jelzi a gépen.


### <a name="crash-analysis"></a>Összeomlás-elemzés


Az összeomlási memóriaképben található memória elemzése olyan speciálisan kifejlesztett kártevők észleléséhez használt módszer, amelyek ki tudják játszani a hagyományos biztonsági megoldásokat. A különféle formában előforduló kártevők megpróbálják megakadályozni, hogy a hagyományos vírusirtó termékek észleljék őket, ennek érdekében vagy soha nem írnak a lemezre, és nem titkosítják a lemezre írt szoftverösszetevőket. Ez a módszer megnehezíti a kártevők hagyományos kártevőirtó megoldásokkal történő észlelését. Azonban az ilyen kártevők memóriaelemzéssel felismerhetők, mivel a kártevőknek nyomot kell hagyniuk maguk után a memóriában ahhoz, hogy működni tudjanak.

Amikor a szoftver összeomlik, egy összeomlási memóriakép rögzíti a memória egy részét az összeomlás időpontjában. Az összeomlást okozhatja kártevő, általános alkalmazás vagy rendszerproblémák. Az összeomlási memóriaképben található memóriarész elemzésével a Security Center észlelni tudja az olyan technikákat, amelyekkel a kártevők kihasználják a szoftver biztonsági réseit, hozzáférnek a bizalmas adatokhoz, és a feltört gépen maradnak elrejtőzve. Mindez minimális hatással van a gazdagépek teljesítményére, mivel az elemzést a Security Center háttérrendszere végzi.

* **Kódinjektálás észlelhető**: A kódinjektálás olyan művelet, amely végrehajtható modulokat szúr be a futó folyamatokba vagy szálakba. Ezzel a technikával kártevő által adatok elérésére, elrejtésére vagy az Eltávolítás (például adatmegőrzés) megakadályozására szolgál. Ez a riasztás jelzi, hogy injektált modul szerepel az összeomlási memóriaképben. A megbízható szoftverfejlesztők esetenként nem ártó szándékkal hajtanak végre kódinjektálást, hanem például egy meglévő alkalmazás vagy az operációs rendszer egyik összetevőjének módosítása vagy bővítése érdekében. A kártékony és a nem kártékony injektált modulok megkülönböztetéséhez a Security Center ellenőrzi, hogy az injektált modulra illik-e a gyanús működés profilja. Az ellenőrzés eredménye a riasztás „SIGNATURE” mezőjében látható, és ettől függ a riasztás súlyossági szintje, a riasztás leírása és a hibaelhárítási művelet.
* **Gyanús kódszegmens**: A gyanús kódszegmensre figyelmeztető riasztás azt jelzi, hogy egy kódszegmens nem szabványos módszerrel lett lefoglalva, például reflektív injektálással vagy a process hollowingnak (hamis folyamat) nevezett technikával. A riasztás a jelentett kódszegmens további jellemzőit is feldolgozza, hogy kontextusba helyezze a képességeit és viselkedési mintáit.
* **Héjkód észlelhető**: A héjkód az a kártékony kód, amely azután fut le, hogy a kártevő a szoftver biztonsági rését kihasználva bejut a rendszerbe. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése olyan végrehajtható kódot talált, amely a kártékony kódokra jellemző működés jeleit mutatja. Bár előfordulhat, hogy nem rosszindulatú szoftverhez tartozik az adott működés, ez nem jellemző a szokásos szoftverfejlesztési gyakorlatban.
* **Moduleltérítés észlelhető**: A Windows dinamikus csatolású kódtárai (DLL) teszik lehetővé, hogy a szoftverek használják a Windows közös rendszerfunkcióit. DLL-eltérítés akkor történik, ha a kártevő megváltoztatja a DLL-betöltési sorrendet, hogy kártékony kódot töltsön be a memóriába, ahol tetszőleges kódot lehet végrehajtani. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése egy hasonló nevű modul betöltését észlelte két különböző elérési útról. Az egyik elérési út a Windows rendszer bináris rendszerfájljainak helyére mutat. A megbízható szoftverfejlesztők esetenként nem ártó szándékkal változtatják meg a DLL-ek betöltési sorrendjét, hanem például a Windows operációs rendszer eszközeinek biztosításához vagy bővítéséhez, illetve a Windows-alkalmazások bővítéséhez. A DLL-betöltési sorrend ártó szándékú és esetleg jóindulatú megváltoztatásának megkülönböztetéséhez a Security Center ellenőrzi, hogy a betöltött modulra illik-e a gyanús működés profilja.
* **Álcázásos Windows-modul észlelhető**: A kártevők felhasználhatják a Windows rendszerfájljainak (például: SVCHOST.EXE) vagy moduljainak (például: NTDLL.DLL) nevét arra, hogy elvegyüljenek, és elfedjék a szoftver kártékony jellegét a rendszergazdák elől. Ez a riasztás azt jelzi, hogy az összeomlási memóriaképfájl olyan modulokat tartalmaz, amelyek a Windows rendszermoduljainak nevét használják, de nem felelnek meg a jellemzően a Windows-modulokra vonatkozó feltételeknek. Az álcázásos modul lemezen lévő példányának elemzésével további adatokat kaphat a modul megbízható vagy kártékony jellegéről.
* **Módosított bináris rendszerfájl észlelhető**: A kártevők módosíthatják a rendszermag bináris fájljait, hogy magukat leplezve hozzáférhessenek az adatokhoz, vagy elrejtőzzenek a fertőzött rendszerben. Ez a riasztás azt jelzi, hogy az összeomlási memóriakép elemzése a Windows operációs rendszer módosított bináris rendszerfájljait észlelte a memóriában vagy a lemezen. A megbízható szoftverfejlesztők esetenként nem ártó szándékkal módosítják a memóriában lévő rendszermodulokat, hanem például elkerülő megoldásokhoz vagy az alkalmazások kompatibilitásához. A kártékony és a valószínűleg jóindulatú modulok megkülönböztetéséhez a Security Center ellenőrzi, hogy a betöltött modulra illik-e a gyanús működés profilja.

## <a name="network-analysis"></a>Hálózatelemzés
A Security Center hálózati fenyegetettség-észlelése úgy működik, hogy automatikusan összegyűjti a biztonsági információkat az Azure IPFIX (IP-folyamatadatok exportálása) forgalmából. A fenyegetések azonosításához elemzi ezeket az információkat, és gyakran megvizsgálja a különböző forrásokból származó adatok közötti összefüggéseket.

* **Lehetséges bejövő SQL találgatásos kísérletek**: A hálózati forgalomelemzés gyanús bejövő SQL-kommunikációt fedezett fel. Ez a tevékenység megfelel az SQL-kiszolgálók ellen indított találgatásos támadásoknak.
* **Gyanús, több forrásból származó bejövő RDP hálózati aktivitás**: A hálózati forgalomelemzés rendellenes, több forrásból származó bejövő RDP-kommunikációt (Remote Desktop Protocol) észlelt. A mintavételezett hálózati adatokban a géphez csatlakozó egyedi IP-címek fedezhetők fel, amely ebben a környezetben rendellenesnek számít. Ez a tevékenység az RDP-végpont több gazdagépről (Botnet) történő találgatásos támadására utalhat.
* **Gyanús bejövő RDP hálózati aktivitás**: A hálózati forgalomelemzés rendellenes, bejövő RDP-kommunikációt (Remote Desktop Protocol) észlelt. A mintavételezett hálózati adatokban a géphez csatlakozó nagy számú bejövő kapcsolat fedezhető fel, amely ebben a környezetben rendellenesnek számít. Ez a tevékenység az RDP-végpontra irányuló találgatásos támadási kísérletre utalhat.
* **Gyanús, több célra irányuló kimenő RDP hálózati aktivitás**: A hálózati forgalomelemzés rendellenes, több célra irányuló kimenő RDP-kommunikációt (Remote Desktop Protocol) észlelt. Ez a tevékenység arra utalhat, hogy a gépet feltörték, és az RDP-végpontokra irányuló találgatásos támadási kísérlethez használják. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.
* **Gyanús kimenő RDP hálózati aktivitás**: A hálózati forgalomelemzés rendellenes kimenő RDP-kommunikációt (Remote Desktop Protocol) észlelt. A mintavételezett hálózati adatokban a gépről induló nagy számú kimenő kapcsolat fedezhető fel, amely ebben a környezetben rendellenesnek számít. Ez a tevékenység arra utalhat, hogy a gépet feltörték, és az RDP-végpontokra irányuló találgatásos támadási kísérlethez használják. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.
* **Gyanús bejövő SSH hálózati aktivitás**: A hálózati forgalomelemzés rendellenes bejövő SSH-kommunikációt észlelt. A mintavételezett hálózati adatokban a géphez csatlakozó nagy számú bejövő kapcsolat fedezhető fel, amely ebben a környezetben rendellenesnek számít. Ez a tevékenység az SSH-végpontra irányuló találgatásos támadási kísérletre utalhat.
* **Gyanús, több helyről származó bejövő SSH-hálózati aktivitás**: A hálózati forgalomelemzés rendellenes, több helyről származó bejövő SSH-kommunikációt észlelt. A mintavételezett hálózati adatokban a géphez csatlakozó egyedi IP-címek fedezhetők fel, amely ebben a környezetben rendellenesnek számít. Ez a tevékenység az SSH-végpont több gazdagépről (Botnet) történő találgatásos támadására utalhat.
* **Gyanús kimenő SSH-hálózati aktivitás**: A hálózati forgalomelemzés rendellenes kimenő SSH-kommunikációt észlelt. A mintavételezett hálózati adatokban a gépről induló nagy számú kimenő kapcsolat fedezhető fel, amely ebben a környezetben rendellenesnek számít. Ez a tevékenység arra utalhat, hogy a gépet feltörték, és az SSH-végpontokra irányuló találgatásos támadási kísérlethez használják. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.
* **Gyanús, több célra irányuló kimenő SSH hálózati aktivitás**: A hálózati forgalomelemzés rendellenes, több célra irányuló kimenő SSH-kommunikációt észlelt. A mintavételezett hálózati adatokban egyedi IP-címek fedezhetők fel, amelyekhez a gép csatlakozik, ez pedig ebben a környezetben rendellenesnek számít. Ez a tevékenység arra utalhat, hogy a gépet feltörték, és az SSH-végpontokra irányuló találgatásos támadási kísérlethez használják. Megjegyzendő, hogy ez a tevékenység külső entitások számára rosszindulatúként sorolhatja be az Ön IP-címét is.
* **A rendszer egy kártevő géppel folytatott hálózati kommunikációt észlelt**: A hálózati forgalomelemzés eredményei azt mutatják, hogy a gép valószínűleg egy parancs- és vezérlőközponttal kommunikált.
* **A rendszer esetleges feltört gépet észlelt**: A hálózati forgalomelemzés kimenő tevékenységet észlelt, amely arra utalhat, hogy a hálózat egy botnet része. Az elemzés azokon az IP-címeken alapul, amelyekhez az erőforrás a DNS-rekordokkal együtt hozzáfért.


## <a name="sql-database-and-sql-data-warehouse-analysis"></a>SQL Database- és SQL Data Warehouse-elemzés

A Security Center erőforrás-elemzése a PaaS (szolgáltatásként üzemeltetett platform) szolgáltatásokra összpontosít, mint például az [Azure SQL Database fenyegetésészlelés](../sql-database/sql-database-threat-detection.md) és az Azure SQL Data Warehouse integrációja. Az SQL fenyegetésészlelés észleli a rendellenes tevékenységeket, amelyek az adatbázisok hozzáférésére vagy kihasználására irányuló szokatlan és potenciálisan rosszindulatú kísérletekre utalnak, és aktiválja a következő riasztásokat:

* **Biztonsági rés az SQL-injektálás számára**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás egy hibás SQL-utasítást hoz létre az adatbázisban. Ez az SQL-injektálási támadásokkal kihasználható biztonsági rést jelezhet. A hibás utasításokat két dolog okozhatja:
    * Egy hiba a hibás SQL-utasítást létrehozó alkalmazáskódban
    * Az alkalmazáskódok és tárolt eljárások nem ellenőrzik a felhasználói adatbevitelt a hibás SQL-utasítás létrehozásakor, amelyeket így SQL-injektálással ki lehet használni.
* **Potenciális SQL-injektálás**: Ez a riasztás akkor aktiválódik, ha egy alkalmazás SQL-injektálással szembeni ismert sebezhetőségét aktívan kihasználják. Ez azt jelenti, hogy a támadó megpróbál kártevő SQL-utasításokat injektálni a sebezhető alkalmazáskód vagy tárolt eljárások kihasználásával.
* **Hozzáférés szokatlan helyről**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan földrajzi helyről jelentkezett be az SQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
* **Hozzáférés szokatlan Azure-adatközpontból**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan Azure-adatközpontból jelentkezett be az SQL-kiszolgálóra, ami a kiszolgálón is látható volt a közelmúltban. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új Azure-, Power BI- vagy Azure SQL Query Editor-alkalmazást). Más esetekben a riasztás kártékony műveleteket észlel egy Azure-erőforrás vagy -szolgáltatás felől (egy korábbi alkalmazott vagy egy külső támadó részéről).
* **Hozzáférés szokatlan résztvevő részéről**: Ez a riasztás akkor aktiválódik, ha változás történik az SQL-kiszolgáló hozzáférési mintájában, amikor valaki egy szokatlan résztvevő (SQL-felhasználó) használatával jelentkezett be az SQL-kiszolgálóra. Bizonyos esetekben a riasztás jogszerű műveleteket észlel (egy új alkalmazást vagy fejlesztői karbantartást). Más esetekben a riasztás kártékony műveleteket észlel (egy korábbi alkalmazott vagy egy külső támadó részéről).
* **Hozzáférés potenciálisan kártékony alkalmazással**: Ez a riasztás akkor aktiválódik, ha valaki egy potenciálisan káros alkalmazást használ az adatbázis eléréséhez. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy gyakori támadóeszközökkel végrehajtott támadást észlel.
* **Találgatásos támadás SQL hitelesítő adatokkal**: Ez a riasztás akkor aktiválódik, ha rendellenesen magas a különböző hitelesítő adatok használatával történő sikertelen bejelentkezések száma. Bizonyos esetekben a riasztás behatolási teszteket észlel működés közben. Más esetekben a riasztás egy találgatásos támadást észlel.

## <a name="contextual-information"></a>Környezeti információk
Vizsgálatok során az elemzőknek további háttér-információra van szükségük ahhoz, hogy megállapíthassák a fenyegetés jellegét és elháríthassák veszélyt.  Ha például hálózati anomáliát észlelnek, de nincsenek tisztában azzal, hogy mi történik a hálózaton vagy a célzott erőforráson, rendkívül nehéz meghatározni a következő lépést. Vizsgálatot végző, a biztonsági incidensek összetevőket, kapcsolódó események és a vizsgáló kapcsolatos tartalmazhat. A további elérhető információk eltérhetnek az észlelt fenyegetés típusától, valamint a környezet konfigurációjától függően, és nem minden biztonsági incidens esetében állnak rendelkezésre.

Ha van további elérhető információ, akkor a biztonsági incidensben, a riasztások listája alatt látható. Ez többek között az alábbi információkat tartalmazhatja:

- Naplótörlési események
- PNP-eszköz csatlakoztatása ismeretlen eszközről
- Riasztások, amelyek nem hasznosítható
- Új fiók létrehozása
- Fájl dekódolása a certutil eszközzel

![Szokatlan hozzáférés miatti riasztás](./media/security-center-alerts-type/security-center-alerts-type-fig20.png)


## <a name="next-steps"></a>További lépések
Ebben dokumentumban megismerhette a Security Center különböző típusú biztonsági riasztásait. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági incidensek kezelése az Azure Security Centerben](security-center-incident.md)
* [Az Azure Security Center észlelési képességei](security-center-detection-capabilities.md)
* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](security-center-planning-and-operations-guide.md)
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md): Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
