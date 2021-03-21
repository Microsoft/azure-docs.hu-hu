---
title: 'Hálózati kapcsolat teljesítményének megoldása: Azure'
description: Ez az oldal egy szabványosított módszert biztosít az Azure hálózati kapcsolatok teljesítményének tesztelésére.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 01/07/2021
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 35e080e0fe45c18ad6a6d5392e0c78b116853c3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027468"
---
# <a name="troubleshooting-network-performance"></a>A hálózati teljesítmény hibaelhárítása
## <a name="overview"></a>Áttekintés
Az Azure stabil és gyors módszert biztosít a helyszíni hálózatról az Azure-hoz való kapcsolódásra. Az olyan megoldások segítségével, mint a helyek közötti VPN és az ExpressRoute, kisebb és nagyobb szervezetek is sikerrel futtathatják üzleti folyamataikat az Azure-ban. De mi történik, ha a teljesítmény nem felel meg a várt vagy a korábbi élménynek? Ez a cikk az adott környezet tesztelési és alapkonfigurációs módszerének szabványosítását segíti elő.

Megtudhatja, hogyan lehet egyszerűen és következetesen tesztelni a hálózati késést és a sávszélességet két gazdagép között. Az Azure-hálózat megkeresésének módjairól is tájékozódhat, így könnyebben elkülöníthetők a problémás pontok. A megjelenő PowerShell-parancsfájlnak és-eszközöknek két gazdagépet kell megkövetelniük a hálózaton (a tesztelt hivatkozás mindkét végén). Az egyik gazdagépnek Windows-kiszolgálónak vagy-asztalnak kell lennie, a másik pedig Windows vagy Linux. 

>[!NOTE]
>A hibaelhárítási módszer, a használt eszközök és módszerek személyes beállítások. Ez a dokumentum a gyakran elvégezhető megközelítést és eszközöket ismerteti. Az Ön megközelítése valószínűleg eltérő lesz, és a problémamegoldás különböző módszereinek egyike sem okoz gondot. Ha azonban nem rendelkezik létrehozott megközelítéssel, a dokumentum elsajátíthatja a saját módszereinek, eszközeinek és preferenciáinak a hálózati problémák megoldásához való létrehozásához szükséges útvonalat.
>
>

## <a name="network-components"></a>Hálózati összetevők
A hibaelhárításhoz való kiásás előtt beszéljünk néhány gyakori kifejezésről és összetevőről. Ez a vita biztosítja, hogy a végpontok közötti lánc minden olyan összetevőjére gondoljon, amely lehetővé teszi a kapcsolódást az Azure-ban.
![1][1]

A legmagasabb szinten leírom a három fő hálózati útválasztási tartományt;

- Az Azure-hálózat (a jobb oldali kék felhő)
- az Internet vagy a WAN (zöld felhő a központban)
- a vállalati hálózat (Peach Cloud a bal oldalon)

A diagramot a jobbról balra haladva tárgyaljuk, hogy röviden lássuk az egyes összetevőket:
 - **Virtuális gép** – a kiszolgálónak több hálózati adaptere is lehet. Győződjön meg arról, hogy a statikus útvonalak, az alapértelmezett útvonalak és az operációsrendszer-beállítások a forgalmat az Ön véleménye szerint küldik és fogadják. Emellett az egyes virtuális gépekhez tartozó SKU sávszélesség-korlátozást is tartalmaz. Ha kisebb VM SKU-t használ, a forgalmat a hálózati adapter számára elérhető sávszélesség korlátozza. Általában DS5v2 használok a teszteléshez (és a törlés után töröljük a pénzt) a megfelelő sávszélesség biztosításához a virtuális gépen.
 - **NIC** – győződjön meg arról, hogy ismeri a kérdéses hálózati adapterhez rendelt magánhálózati IP-címet.
 - **NIC NSG** – lehetséges, hogy a hálózati adapter szintjén bizonyos NSG vannak alkalmazva, hogy a NSG-szabálykészlet megfelelő legyen az átadni kívánt forgalomhoz. Tegyük fel például, hogy a iPerf, a 3389 for RDP vagy az SSH esetében a 5201-es portok nyitva vannak, hogy lehetővé tegyék a tesztelési forgalom továbbítását.
 - **VNet alhálózat** – a hálózati adapter egy adott alhálózathoz van rendelve, ellenőrizze, hogy az adott alhálózathoz melyik és milyen szabályok vannak társítva.
 - **ALHÁLÓZAT NSG** – csakúgy, mint a hálózati adapter, a NSG is alkalmazható az alhálózaton. Győződjön meg arról, hogy az átadni kívánt forgalom megfelel a NSG-szabálynak. (A hálózati adapterhez bejövő forgalom esetén az alhálózat NSG először a NIC-NSG alkalmazza. Ha a forgalom kimenő a virtuális gépről, akkor a hálózati adapter NSG először az alhálózat NSG alkalmazza.
 - **ALHÁLÓZAT UDR** – User-Defined útvonalak egy köztes ugrásra (például tűzfal vagy terheléselosztó) irányítják a forgalmat. Győződjön meg arról, hogy van-e UDR a forgalomhoz. Ha igen, tekintse át a helyét, és azt, hogy mi a következő ugrás a forgalomhoz. Előfordulhat például, hogy egy tűzfal átadja a forgalmat, és megtagadja a többi forgalmat ugyanahhoz a két gazdagéphez.
 - **Gateway subnet/NSG/UDR** – akárcsak a virtuálisgép-alhálózathoz, az átjáró alhálózatának NSG és UDR is lehet. Győződjön meg arról, hogy tudja, van-e ott, és milyen hatással vannak a forgalomra.
 - **VNet-átjáró (ExpressRoute)** – ha a társítás (ExpressRoute) vagy a VPN engedélyezve van, nincs sok olyan beállítás, amely hatással lehet a forgalmi útvonalakra vagy azokra. Ha egy VNet-átjáró több ExpressRoute-áramkörhöz vagy VPN-alagúthoz van csatlakoztatva, vegye figyelembe a kapcsolat súlyozási beállításait. A kapcsolatok súlyozása befolyásolja a kapcsolatok beállításait, és meghatározza a forgalom elérési útját.
 - **Útvonal-szűrő** (nem látható) – szükség van egy útvonal-szűrőre, ha a ExpressRoute-n keresztül használja a Microsoft-társat. Ha nem kap útvonalakat, ellenőrizze, hogy az útvonal szűrő konfigurálva van-e, és hogy megfelelően van-e alkalmazva az áramkörre.

Ezen a ponton a hivatkozás WAN-részén van. Ez az útválasztási tartomány lehet a szolgáltató, a vállalati WAN vagy az Internet. Számos ugrás, eszköz és vállalat vesz részt ezekkel a hivatkozásokkal, ami megnehezíti a hibakeresést. Először is ki kell zárnia az Azure-t és a vállalati hálózatokat, mielőtt a között megvizsgáljuk a komlót.

Az előző ábrán a bal szélen a vállalati hálózat. A vállalat méretétől függően ez az útválasztási tartomány lehet néhány hálózati eszköz, amely az Ön és a WAN, illetve több, a kampusz/vállalati hálózatban lévő eszközök között található.

A három különböző magas szintű hálózati környezet összetettsége miatt. Általában optimális a szélein, és meg kell mutatni, hogy hol van a teljesítmény, és hogy hol romlik. Ez a módszer segít azonosítani a három problémás útválasztási tartományt. Ezt követően az adott környezetre koncentrálhatja a hibaelhárítást.

## <a name="tools"></a>Eszközök
A legtöbb hálózati probléma az olyan alapszintű eszközökkel elemezhető és különíthető el, mint a ping és a traceroute. Ritka, hogy olyan eszközökkel kell kinéznie a csomagok elemzését, mint a Wireshark. 

A hibaelhárítással kapcsolatos segítség érdekében az Azure connectivity Toolkit (AzureCT) úgy lett kifejlesztve, hogy néhány ilyen eszközt egy egyszerű csomagban helyezzen el. A teljesítménytesztek, például a iPerf és a PSPing eszközök a hálózattal kapcsolatos információkat is biztosíthatnak. a iPerf egy általánosan használt eszköz az alapszintű előadások teszteléséhez, és meglehetősen könnyen használható. A PSPing egy, a SysInternals által fejlesztett pingelési eszköz. A PSPing az ICMP-t és a TCP-pingeket is elérheti egy távoli gazdagép eléréséhez. Mindkét eszköz könnyű, és a rendszer egyszerűen telepíti a fájlokat a gazdagép egy könyvtárába.

Ezek az eszközök és módszerek a telepíthető és használható PowerShell-modulba (AzureCT) vannak becsomagolva.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – az Azure connectivity Toolkit
A AzureCT PowerShell-modul két összetevő [rendelkezésre állásának tesztelését][Availability Doc] és a [teljesítmény tesztelését][Performance Doc]végzi. Ez a dokumentum csak a teljesítmény tesztelésével kapcsolatos, ezért a PowerShell-modulban a két hivatkozás teljesítményére vonatkozó parancsokra koncentrálhat.

Az eszközkészletet a teljesítmény-teszteléshez három alapvető lépéssel hajthatja végre. 1.) telepítse a PowerShell-modult, 2.) telepítse a támogató alkalmazásokat iPerf és PSPing 3) futtassa a teljesítménytesztet.

1. A PowerShell-modul telepítése

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Ez a parancs letölti a PowerShell-modult, és helyileg telepíti.

2. A támogató alkalmazások telepítése
    ```powershell
    Install-LinkPerformance
    ```
    Ez a AzureCT parancs telepíti a iPerf és a PSPing egy új "C:\ACTTools" könyvtárba, és megnyitja a Windows tűzfal portjait is, hogy engedélyezze az ICMP és a port 5201 (iPerf) forgalmat.

3. A teljesítményteszt futtatása

    Először a távoli gazdagépen telepítenie és futtatnia kell a iPerf kiszolgálói módban. Győződjön meg arról is, hogy a távoli gazdagép a 3389-as (RDP for Windows) vagy a 22 (SSH Linux rendszerhez) vagy a 5201-es porton keresztüli adatforgalom iPerf való figyelését figyeli. Ha a távoli gazdagép Windows, telepítse a AzureCT, és futtassa a Install-LinkPerformance parancsot. A parancs beállítja a iPerf-t, és a iPerf a kiszolgáló módban való indításához szükséges tűzfalszabályok. 
    
    Miután a távoli gép elkészült, nyissa meg a PowerShellt a helyi gépen, és indítsa el a tesztet:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Ez a parancs egyidejű terhelési és késési tesztek sorozatát futtatja a hálózati kapcsolat sávszélesség-kapacitásának és késésének becslése érdekében.

4. A tesztek kimenetének áttekintése

    A PowerShell kimeneti formátuma a következőhöz hasonlóan néz ki:

    ![4][4]

    Az összes iPerf-és PSPing-teszt részletes eredményeit a AzureCT-eszközök címtárának "C:\ACTTools." mappájában található egyedi szövegfájlokban található.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha a teljesítményteszt nem adja meg a várt eredményeket, akkor érdemes kitalálni, hogy miért kellene egy fokozatos lépésenkénti folyamatnak lennie. Az elérési úton található összetevők száma alapján a szisztematikus megközelítés gyorsabb megoldást biztosít, mint az ugrás. A hibaelhárítási módszerekkel többször is megakadályozhatja a szükségtelen tesztelést.

>[!NOTE]
>A forgatókönyv itt egy teljesítményproblémák, nem pedig kapcsolódási probléma. A lépések eltérőek lehetnek, ha a forgalom egyáltalán nem halad át.
>
>

Először is megkérdőjelezheti a feltételezéseket. Ésszerű a várakozás? Ha például egy 1 GB/s ExpressRoute-áramkört és 100%-os késést tartalmaz. A TCP nagy késésű kapcsolatokon keresztüli teljesítményi jellemzői miatt nem ésszerű a teljes 1 GB-os adatforgalomra számítani. A teljesítményre vonatkozó feltételezésekről a [referenciák című szakaszban](#references) talál további információt.

Következő lépésként azt javasoljuk, hogy kezdjen az útválasztási tartományok között, és próbálja meg elkülöníteni a problémát egyetlen jelentős útválasztási tartományhoz. A vállalati hálózaton, a WAN-on vagy az Azure-hálózaton is elindítható. A felhasználók gyakran az elérési útban a "fekete dobozt" okolják. A fekete doboz hibáztatása azonban könnyen elvégezhető, ami jelentősen késleltetheti a felbontást. Különösen abban az esetben, ha a probléma olyan helyen található, amely a probléma megoldásához módosításokat hajthat végre. Győződjön meg arról, hogy a szolgáltató vagy ISP számára való átadás előtt gondoskodik a megfelelő gondosságról.

Miután azonosította a problémát tartalmazó fő útválasztási tartományt, létre kell hoznia egy diagramot a szóban forgó területről. A tábla, a Jegyzettömb vagy a Visio diagramjának rajzolásával módszeresen dolgozhat a probléma megoldásán, és elkülönítheti azt. Megtervezheti a tesztelési pontokat, és frissítheti a térképet úgy, hogy törli a területeket, vagy mélyebbre ássa a tesztelés folyamatát.

Most, hogy már rendelkezik egy diagrammal, megkezdheti a hálózat felosztását a szegmensekre, és leszűkítheti a problémát. Megtudhatja, hol működik és hol nem. Folytassa a tesztelési pontok áthelyezésével, hogy elkülönítse a jogsértő összetevőt.

Ne felejtse el megtekinteni az OSI-modell egyéb rétegeit is. A hálózatra és a 1-3-es rétegekre (fizikai, adatés hálózati rétegek) is könnyedén koncentrálhat, de a problémák az alkalmazási réteg 7. rétegében is felmerülhetnek. Tartsa meg a nyitott elmét, és ellenőrizze a feltételezéseket.

## <a name="advanced-expressroute-troubleshooting"></a>Speciális ExpressRoute-hibaelhárítás
Ha nem biztos abban, hogy a felhő széle valóban az, az Azure-összetevők elkülönítése kihívást jelenthet. A ExpressRoute használatakor a peremhálózat a Microsoft Enterprise Edge (MSEE) nevű hálózati összetevő. A **ExpressRoute használatakor** a MSEE az első kapcsolódási pont a Microsoft hálózatához, és az utolsó ugrás a Microsoft-hálózat elhagyásakor. Amikor létrehoz egy kapcsolati objektumot a VNet-átjáró és a ExpressRoute-áramkör között, valójában kapcsolatot létesít a MSEE. A MSEE felismerése az első vagy utolsó ugrásként attól függően, hogy a forgalom milyen irányba létfontosságú az Azure hálózati problémáinak elkülönítéséhez. Annak ismerete, hogy milyen irányba fog bizonyítani, hogy a probléma az Azure-ban, illetve a WAN-ban vagy a vállalati hálózaton található alsóbb rétegben van. 

![2][2]

>[!NOTE]
> Figyelje meg, hogy a MSEE nem az Azure-felhőben található. A ExpressRoute valójában a Microsoft-hálózat peremén van, valójában nem az Azure-ban. Miután kapcsolódott a ExpressRoute-hez egy MSEE, a Microsoft hálózatához csatlakozik, innen bármelyik felhőalapú szolgáltatáshoz, például a Microsoft 365hoz (Microsoft-partneri kapcsolattal) vagy az Azure-hoz (személyes és/vagy Microsoft-partneri kapcsolattal) is csatlakozhat.
>
>

Ha két virtuális hálózatok **ugyanahhoz** a ExpressRoute-áramkörhöz csatlakozik, elvégezheti az Azure-ban felmerülő problémák elkülönítésére szolgáló teszteket.
 
### <a name="test-plan"></a>Tesztelési terv
1. Futtassa a Get-LinkPerformance tesztet a VM1 és a VM2 között. Ez a teszt azt ismerteti, hogy a probléma helyi-e, vagy sem. Ha ez a teszt elfogadható késést és sávszélesség-eredményeket hoz létre, akkor a helyi VNet-hálózatot is érdemes lehet megjelölni.
2. Ha feltételezi, hogy a helyi VNet-forgalom jó, futtassa a Get-LinkPerformance tesztet a VM1 és a VM3 között. Ez a teszt a Microsoft hálózatán keresztül gyakorolja a kapcsolódást a MSEE, és visszatér az Azure-ba. Ha ez a teszt elfogadható késést és sávszélesség-eredményeket hoz létre, akkor az Azure-hálózatot is érdemes lehet megjelölni.
3. Ha az Azure-t kizárták, a vállalati hálózaton hasonló teszteket végezhet. Ha ez is jól működik, ideje, hogy együttműködjön a szolgáltatóval vagy az INTERNETSZOLGÁLTATÓval a WAN-kapcsolat diagnosztizálásához. Példa: futtassa ezt a tesztet két fiókirodában, illetve az íróasztal és egy adatközpont-kiszolgáló között. A teszteléstől függően olyan végpontokat talál, mint például a kiszolgálók és az ügyfélszámítógépek, amelyek ezt az elérési utat gyakorolhatják.

>[!IMPORTANT]
> Fontos, hogy minden egyes tesztnél megjelölje a teszt futtatásának időpontját, és az eredményeket egy közös helyen (például a OneNote vagy az Excel programban) jegyezze fel. Az egyes tesztelési műveleteknek azonos kimenettel kell rendelkezniük, így összehasonlíthatja az eredményül kapott adatokat a tesztek között, és az adatokat nem lehet "lyukakkal" ellátni. A több teszt közötti konzisztencia az elsődleges ok, amiért a AzureCT használja a hibaelhárításhoz. A Magic nem a jelenleg futtatott terhelési forgatókönyvek, hanem a *Magic* az a tény, hogy *egységes tesztet és adatkimenetet* kapok minden tesztből. Ha később azt tapasztalja, hogy a probléma szórványos, az idő rögzítése és az egységes adatkezelési idő is különösen hasznos. Legyen gondos az adatgyűjtés előtt, és elkerülheti a különböző forgatókönyvek újratesztelésének idejét (sok évvel ezelőtt megtanultam ezt a nehéz utat).
>
>

## <a name="the-problem-is-isolated-now-what"></a>A probléma el van különítve, most mi?
Minél többen elkülöníti a problémát, annál gyorsabban megtalálhatók a megoldás. Egy kis ideig elérheti azt a pontot, ahol nem mehet tovább a hibaelhárításhoz. Láthatja például, hogy az egész szolgáltatón keresztüli ugrások az Európa-on keresztül zajlanak, de az elérési út az egész Ázsiában marad. Ezen a ponton segítséget kell nyújtania valakinek. Az Ön által kért kérdés attól függ, hogy melyik útválasztási tartományhoz tartozik a probléma. Ha leszűkítheti egy adott összetevőre, amely még jobb lenne.

A vállalati hálózati problémák esetében az informatikai részleg vagy a szolgáltató segít az eszközök konfigurálásában vagy a hardverek javításában.

A WAN esetében a tesztelési eredmények megosztása a szolgáltatóval vagy az INTERNETSZOLGÁLTATÓval segít az első lépések megtételében. Ezzel a művelettel elkerülhető a már elvégzett munka duplikálása is. Ne sértse meg, ha saját maga szeretné ellenőrizni az eredményeket. A "bizalmi kapcsolat, de az ellenőrzés" jó választás, ha más személy által jelentett eredmények alapján végez hibakeresést.

Az Azure-ban, ha a problémát a lehető legrészletesebben elkülöníti, itt az ideje, hogy áttekintse az [Azure hálózati dokumentációját][Network Docs] , és ha szükséges, [Nyisson meg egy támogatási jegyet][Ticket Link]is.

## <a name="references"></a>Hivatkozások
### <a name="latencybandwidth-expectations"></a>Késés/sávszélesség várakozási ideje
>[!TIP]
> A tesztelt végpontok közötti földrajzi késés (mérföld vagy kilométer) messze a késés legnagyobb összetevője. A berendezések késése (fizikai és virtuális összetevők, ugrások száma stb.) esetében a földrajz a WAN-kapcsolatok kezelése során a teljes késés legnagyobb részét bizonyította. Azt is fontos megjegyezni, hogy a távolság a szál távolsága, nem az egyenes vonal vagy a közúti Térkép távolsága. Ez a távolság hihetetlenül nehéz bármilyen pontossággal. Ennek eredményeképpen általában egy városi távolsági számológépet használok az interneten, és tudja, hogy ez a módszer egy durván pontatlan mérték, de elég egy általános elvárást beállítani.
>
>

ExpressRoute-telepítőt kaptam a Washington állambeli Seattle-ben. Az alábbi táblázat a különböző Azure-helyszínekre való tesztelési késést és sávszélességet mutatja be. A teszt végén a földrajzi távolságot becsültük.

Teszt beállítása:
 - Egy Windows Server 2016 rendszert futtató fizikai kiszolgáló 10 GB/s hálózati adapterrel, amely egy ExpressRoute-áramkörhöz csatlakozik.
 - A 10Gbps prémium szintű ExpressRoute áramkör a privát társak beállításával azonosított helyen.
 - Egy Azure-VNet egy UltraPerformance-átjáróval a megadott régióban.
 - Egy Windows Server 2016 rendszert futtató DS5v2 virtuális gép a VNet. A virtuális gép nem csatlakozik a tartományhoz, és az alapértelmezett Azure-rendszerképből (optimalizálás vagy testreszabás nélkül) készült, és a AzureCT telepítve van.
 - Minden teszt a AzureCT Get-LinkPerformance parancsot használja 5 percenkénti terhelési teszttel a hat teszt futtatásához. Például:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Az egyes tesztek adatforgalmának terhelése a helyszíni fizikai kiszolgálóról (iPerf-ügyfél, Seattle-ből) történt, akár az Azure-beli virtuális gépen (iPerf-kiszolgáló a felsorolt Azure-régióban).
 - A "késés" oszlop adatait a nincs betöltési teszt (a TCP-késési teszt a iPerf futtatása nélkül) értékből származik.
 - A "maximális sávszélesség" oszlop adatait a 16 TCP-flow terhelési tesztje egy 1 MB méretű ablakméret alapján.

![3][3]

### <a name="latencybandwidth-results"></a>Késés/sávszélesség eredményei
>[!IMPORTANT]
> Ezek a számok csak általános referenciák. Számos tényező befolyásolja a késést, és noha ezek az értékek általában konzisztensek az idő múlásával, az Azure-ban vagy a szolgáltatói hálózaton belüli feltételek bármikor elküldhetik a forgalmat különböző elérési utakon keresztül, így a késés és a sávszélesség is hatással lehet. A változások hatása általában nem eredményez jelentős különbségeket.
>
>

| ExpressRoute<br/>Hely|Azure<br/>Region | Becsült<br/>Távolság (km) | Késés|1 munkamenet<br/>Sávszélesség | Maximum<br/>Sávszélesség |
| ------------------------------------------ | --------------------------- |  - | - | - | - |
| Seattle | USA 2. nyugati régiója        |    191 km |   5 MS | 262,0 MB/s |  3,74 Gbits/s |
| Seattle | USA nyugati régiója          |  1 094 km |  18 MS |  82,3 MB/s |  3,70 Gbits/s |
| Seattle | Az USA középső régiója       |  2 357 km |  40 MS |  38,8 MB/s |  2,55 Gbits/s |
| Seattle | USA déli középső régiója |  2 877 km |  51 MS |  30,6 MB/s |  2,49 Gbits/s |
| Seattle | USA északi középső régiója |  2 792 km |  55 MS |  27,7 MB/s |  2,19 Gbits/s |
| Seattle | USA 2. keleti régiója        |  3 769 km |  73 MS |  21,3 MB/s |  1,79 Gbits/s |
| Seattle | USA keleti régiója          |  3 699 km |  74 MS |  21,1 MB/s |  1,78 Gbits/s |
| Seattle | Kelet-Japán       |  7 705 km | 106 MS |  14,6 MB/s |  1,22 Gbits/s |
| Seattle | Az Egyesült Királyság déli régiója         |  7 708 km | 146 MS |  10,6 MB/s |   896 MB/s |
| Seattle | Nyugat-Európa      |  7 834 km | 153 MS |  10,2 MB/s |   761 MB/s |
| Seattle | Kelet-Ausztrália   | 12 484 km | 165 MS |   9,4 MB/s |   794 MB/s |
| Seattle | Délkelet-Ázsia   | 12 989 km | 170 MS |   9,2 MB/s |   756 MB/s |
| Seattle | Dél-Brazília *   | 10 930 km | 189 MS |   8,2 MB/s |   699 MB/s |
| Seattle | Dél-India      | 12 918 km | 202 MS |   7,7 MB/s |   634 MB/s |

\* A Brazília-re vonatkozó késés jó példa arra, hogy az egyenes vonal távolsága jelentősen eltér a szál futási helyétől. A várt késés a 160 MS környékén lenne, de valójában 189 MS. A késés különbsége úgy tűnik, hogy valahol hálózati problémát jelez. A valóság azonban az, hogy a szál nem a Brazíliában egyenes vonalban jelenik meg. Ezért további 1 000 km-t kell várnia, hogy a Seattle-ből Brazíliába utazzon.

## <a name="next-steps"></a>Következő lépések
1. Töltse le az Azure-kapcsolati eszközkészletet a GitHubról [https://aka.ms/AzCT][ACT]
2. Kövesse a [kapcsolati teljesítmény tesztelésének][Performance Doc] utasításait

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Azure hálózati összetevők"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute-hibaelhárítás"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Teljesítményfigyelő-tesztkörnyezet"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell-kimenet"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: ../index.yml
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT