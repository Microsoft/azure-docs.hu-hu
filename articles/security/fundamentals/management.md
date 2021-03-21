---
title: A távfelügyelet biztonságának növelése az Azure-ban | Microsoft Docs
description: Ez a cikk a távfelügyeleti biztonság fokozásának lépéseit ismerteti Microsoft Azure környezetek, például a Cloud Services, a Virtual Machines és az egyéni alkalmazások felügyelete során.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 2431feba-3364-4a63-8e66-858926061dd3
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2020
ms.author: terrylan
ms.openlocfilehash: e9eabc73c244526f0ea15b9c72b5377545f662b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94844863"
---
# <a name="security-management-in-azure"></a>Biztonságkezelés az Azure-ban
Az Azure-előfizetők több eszközről kezelhetik felhőkörnyezeteiket, például felügyeleti munkaállomásokról, fejlesztői PC-kről, és olyan jogosult végfelhasználói eszközökről is, amelyek feladatspecifikus engedélyekkel rendelkeznek. Egyes esetekben a felügyeleti feladatkörök ellátását olyan webalapú konzolok használatával végzik, mint például az [Azure Portal](https://azure.microsoft.com/features/azure-portal/). Más esetekben az Azure-hoz való közvetlen kapcsolat létesíthető virtuális magánhálózatokon (VPN), terminálszolgáltatásokon, ügyfél-alkalmazásprotokollokon, vagy (szoftveresen) az Azure Service Management API-n (SMAPI) keresztül. Továbbá az ügyfél-végpontok lehetnek vagy tartományhoz csatlakoztatottak, vagy pedig elkülönítettek és felügyelet nélküliek, mint például a táblagépek vagy az okostelefonok.

A sokféle hozzáférési és kezelési képesség a lehetőségek széles tárházát biztosítja, ugyanakkor nagymértékű kockázatot is jelent a felhőkörnyezetek esetén. A felügyeleti műveletek kezelése, nyomon követése és naplózása nehézségekbe ütközhet. Ez a sokrétűség biztonsági fenyegetésekkel is járhat, mivel nem szabályozott hozzáférést tesz lehetővé a felhőszolgáltatások kezelésére használt ügyfélvégpontokhoz. Az általános vagy személyes munkaállomások fejlesztésre és infrastruktúra-kezelésre való használata olyan kiszámíthatatlan fenyegetési vektoroknak enged utat, mint például a webböngészés (pl. alapesetben megbízható weboldalak megfertőződése, ún. watering hole attack) vagy az e-mail (pl. pszichológiai manipuláció és adathalászat).

![Egy olyan diagram, amely a fenyegetés különböző módszereit mutatja be.](./media/management/typical-management-network-topology.png)

Az ilyen jellegű környezetben magasabb a támadások bekövetkeztének esélye, mert nehéz olyan biztonsági házirendeket és mechanizmusokat megalkotni, amelyekkel megfelelően felügyelhető az Azure-felületekhez (mint például a SMAPI-hoz) való hozzáférés a rendkívül változatos végpontokból.

### <a name="remote-management-threats"></a>Távfelügyelettel kapcsolatos fenyegetések
A támadók gyakran próbálkoznak emelt szintű hozzáférés megszerzésével a fiókok hitelesítő adatainak megkaparintásával, például próbálgatásos jelszavas támadáson, adathalászaton vagy kapcsolatok eltérítésén (credential harvesting) keresztül, vagy pedig úgy, hogy a felhasználókat trükk segítségével ártalmas kódnak teszik ki (webhelyekről automatikusan letöltődő kártevő vagy káros e-mail-mellékletek segítségével). Távfelügyelt felhőkörnyezetben nagyobb kockázattal járhat a fiókokhoz való illetéktelen hozzáférés, a bármikori és bárhonnani hozzáférhetőség miatt.

A fő rendszergazdai fiókok szigorú felügyelete mellett is kiaknázhatóak a biztonsági stratégia gyenge pontjai az alacsonyabb szintű felhasználói fiókokon keresztül. A megfelelő biztonsági képzés hiánya szintén vezethet biztonsági rések kialakulásához, a fiókadatok véletlen kiszivárogtatása útján.

Ha egy felhasználói munkaállomás felügyeleti feladatok elvégzésére is használatos, sok különböző ponton sérülhet a rendszer biztonsága. Ez bekövetkezhet például webböngészés, harmadik féltől származó és nyílt forrású eszközök használata, vagy trójait tartalmazó ártalmas dokumentum megnyitása során.

Általában a legtöbb, adatszivárgáshoz vezető, célzott támadás böngészők biztonsági réseinek kihasználásához, beépülő modulokhoz (mint a Flash, PDF vagy Java), vagy e-mailes adathalászathoz vezethető vissza az asztali számítógépek esetében. Ezek a gépek rendelkezhetnek rendszergazdai vagy szolgáltatásszintű engedélyekkel, hogy műveleti céllal hozzáférhessenek az élő kiszolgálókhoz vagy hálózati eszközökhöz, amikor fejlesztésre vagy más eszközök kezelésére használják őket.

### <a name="operational-security-fundamentals"></a>A működési biztonság alapjai
A biztonságosabb kezelés és működés érdekében minimalizálhatja egy ügyfél támadási felületét azzal, hogy csökkenti a lehetséges belépési pontok számát. Ez olyan rendszerbiztonsági alapelvekkel oldható meg, mint a „feladatkörök szétválasztása” és a „környezetek elkülönítése”.

Különítse el a bizalmas funkciókat egymástól annak érdekében, hogy egy adott szinten elkövetett hiba kisebb valószínűséggel vezethessen biztonsági réshez egy másik szinten. Angol nyelvű Példák:

* A rendszergazdai feladatokkal egy időben ne végezzen olyan tevékenységet, amely biztonsági réshez vezethet (például egy rendszergazda e-mailjében található kártevő átterjedhet egy infrastruktúra-kiszolgálóra).
* A bizalmas műveletek végzésére használt munkaállomásokat nem szabad magas kockázatú tevékenység (például webböngészés) céljából is használni.

Csökkentse a rendszer támadási felületét a szükségtelen szoftverek eltávolításával. Példa:

* Általános jellegű rendszergazdai, támogatási vagy fejlesztői munkaállomáson nincs szükség levelezőprogram vagy más irodai alkalmazás telepítésére, ha az eszköz fő felhasználási célja a felhőszolgáltatások kezelése.

Az infrastruktúra alkotóelemeihez rendszergazdai szintű hozzáféréssel rendelkező ügyfélrendszereket a lehető legszigorúbb biztonsági házirendnek kell alávetni, a biztonsági kockázat csökkentésének érdekében. Angol nyelvű Példák:

* A biztonsági házirendek tartalmazhatnak olyan csoportházirend-beállításokat, amelyek meggátolják az eszközön való nyílt internet-hozzáférést, valamint korlátozó tűzfal-beállításokat léptetnek érvénybe.
* Használjon IPsec alapú VPN-eket, ha közvetlen hozzáférésre van szükség.
* Állítson be különálló felügyeleti és fejlesztői Active Directory-tartományokat.
* Különítse el és szűrje a felügyeleti munkaállomások hálózati forgalmát.
* Használjon kártevőirtó szoftvert.
* Vezessen be többtényezős hitelesítést, hogy csökkentse a lopott fiókadatokkal történő hozzáférés esélyét.

A hozzáférési erőforrások konszolidálása és a felügyelet nélküli végpontok kiiktatása egyszerűsíti a felügyeleti feladatokat.

### <a name="providing-security-for-azure-remote-management"></a>Az Azure távfelügyelet biztonsági megoldásai
Az Azure biztonsági mechanizmusai segítséget nyújtanak a rendszergazdáknak a felhőszolgáltatások és virtuális gépek felügyeletében. Ezen mechanizmusok az alábbiak:

* Hitelesítés és [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).
* Figyelés és naplózás.
* Tanúsítványok és titkosított kommunikáció.
* Webes felügyeleti portál.
* Hálózaticsomag-szűrés.

Az ügyféloldali biztonsági konfiguráció, valamint egy felügyeleti átjáró adatközpontbeli elhelyezése lehetővé teszi a felhőalkalmazásokhoz és adatokhoz való rendszergazdai hozzáférés korlátozását és figyelését.

> [!NOTE]
> Egyes, ebben a cikkben olvasható javaslatok megnövekedett adat-, hálózati vagy számítási erőforrás-igénybevétellel járhatnak, és megnövelhetik a licencek vagy előfizetések költségeit.
>
>

## <a name="hardened-workstation-for-management"></a>Megerősített munkaállomás a felügyelethez
A munkaállomások megerősítésének célja az, hogy a rendszer működéséhez nélkülözhetetlen, kritikus funkciókon kívül minden mást ki lehessen iktatni, a lehető legkisebbre csökkentve a támadási felületet. A rendszer megerősítéséhez tartozik a telepített szolgáltatások és alkalmazások számának minimálisra csökkentése, az alkalmazásfuttatás korlátozása, a hálózati hozzáférés korlátozása csak a legszükségesebbekre, valamint a rendszer állandó naprakészen tartása. Továbbá a megerősített munkaállomás felügyeletre való használata elkülöníti a felügyeleti eszközöket és tevékenységeket a többi végfelhasználói feladattól.

Helyszíni vállalati környezeten belül csökkentheti a fizikai infrastruktúra támadási felületét dedikált felügyeleti hálózatok, beléptetőkártyás kiszolgálótermek és a hálózat védett területein üzemelő munkaállomások segítségével. Felhőalapú vagy hibrid informatikai modellek esetében bonyolultabb lehet a felügyeleti szolgáltatások biztonságának fenntartása, az informatikai erőforrásokhoz való fizikai hozzáférés hiánya miatt. A védelmi megoldások megvalósítása precíz szoftverkonfigurációt, biztonságközpontú folyamatokat és átfogó házirendeket követel meg.

Ha a felhőfelügyelethez – és az alkalmazásfejlesztéshez – egy zárolt munkaállomást használ a lehető legkevesebb szoftver telepítésével és a legalacsonyabb jogosultsági szintekkel, a távfelügyeleti és fejlesztői környezetek szabványosításával csökkentheti a biztonsági események bekövetkeztének kockázatát. A megerősített munkaállomás-konfiguráció segíthet megakadályozni a kritikus felhőerőforrások kezelésére használható felhasználói fiókokhoz való illetéktelen hozzáférést, a kártevők és a biztonsági rések kihasználói által leggyakrabban használt utak kiiktatásával. A [Windows AppLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd759117(v=ws.11)) és a Hyper-V technológia segítségével ellenőrizheti és elkülönítheti az ügyfélrendszer viselkedését. Ily módon csökkentheti a fenyegetéseket, beleértve az e-maillel és a webes böngészéssel járó veszélyeket.

A megerősített munkaállomásokon a rendszergazda általános jogú felhasználói fiókot használ (amely megakadályozza a rendszergazdai szintű kódvégrehajtást), és a társított alkalmazásokat engedélyezési lista szabályozza. A megerősített munkaállomás alapszintű alkotóelemei:

* Aktív vizsgálat és javítás. Telepítsen kártevőirtó szoftvert, rendszeresen végezzen vizsgálatot a biztonsági rések feltárása érdekében, és időben telepítse a legújabb biztonsági frissítéseket az összes munkaállomásra.
* Korlátozott funkciók. Távolítsa el az összes olyan alkalmazást, amelyre nincs szükség, és tiltsa le a szükségtelen (rendszerindításkor elinduló) szolgáltatásokat.
* A hálózat megerősítése. Alkalmazzon Windows tűzfal-szabályokat, hogy csak érvényes IP-címeket, portokat és az Azure-felügyelettel kapcsolatos URL-címeket engedélyezzen. Győződjön meg róla, hogy a munkaállomáshoz beérkező távoli kapcsolatok is le legyenek tiltva.
* Végrehajtási korlátozások. Csak előre megadott végrehajtható fájlokat engedélyezzen, amelyek szükségesek a kezeléshez („alapértelmezésként elutasítás”). A felhasználó alapesetben semmilyen program futtatására nem kap engedélyt, kivéve, ha az explicit módon meg van határozva az engedélyezési listában.
* Legkisebb jogosultság. A felügyeleti munkaállomások felhasználóinak ne legyen rendszergazdai jogosultsága magához a helyi géphez. Ez biztosítja, hogy ne változtathassák meg szándékosan vagy véletlenül a rendszer konfigurációját vagy a rendszerfájlokat.

Mindezt kényszerítheti [csoportházirend-objektumok](../../active-directory-domain-services/manage-group-policy.md) (GPO-k) használatával az Active Directory Domain Servicesben (AD DS), és ezeknek az összes felügyeleti fiókra való alkalmazásával a (helyi) felügyeleti tartományon keresztül.

### <a name="managing-services-applications-and-data"></a>A szolgáltatások, alkalmazások és adatok felügyelete
Az Azure felhőszolgáltatások konfigurálása lehetséges az Azure Portalon vagy SMAPI-n keresztül, a Windows PowerShell parancssori felületen, vagy olyan egyedi alkalmazás használatával, amely kihasználja ezeket a RESTful-felületeket. Ilyen mechanizmusokat használó szolgáltatások például az alábbiak: Azure Active Directory (Azure AD), Azure Storage, Azure Websites, Azure Virtual Network.

A virtuális gépek által telepített alkalmazások szükség szerint saját ügyféleszközöket és -felületeket biztosítanak. Ilyen a Microsoft Management Console (MMC), a vállalati felügyeleti konzolok (mint a Microsoft System Center vagy a Windows Intune), vagy más felügyeleti alkalmazások, például a Microsoft SQL Server Management Studio. Ezek az eszközök általában vállalati környezetben vagy az ügyfélhálózaton találhatóak meg. Függhetnek meghatározott hálózati protokolloktól (ilyen például a Remote Desktop Protocol (RDP)), amelyek közvetlen, állapotalapú kapcsolatokat igényelnek. Némelyik webes felülettel rendelkezhet, amelyet nem szabad nyilvánosan vagy az interneten keresztül elérhetővé tenni.

Az Azure-ban korlátozhatja az infrastruktúrához és a platformszolgáltatások kezeléséhez való hozzáférést [többtényezős hitelesítés](../../active-directory/authentication/concept-mfa-howitworks.md), [X.509 felügyeleti tanúsítványok](/archive/blogs/azuresecurity/certificate-management-in-azure-dos-and-donts) és tűzfalszabályok használatával. Az Azure Portal és a SMAPI a Transport Layer Security (TLS) protokoll használatát követeli meg. Azonban az Azure-ra telepített szolgáltatások és alkalmazások megkövetelik a megfelelő védelmi intézkedéseket, az adott alkalmazástól függően. Ezek a mechanizmusok gyakran egyszerűbben engedélyezhetőek egy szabványosított, megerősített munkaállomás-konfigurációval.

### <a name="management-gateway"></a>Felügyeleti átjáró
Beiktathat az Azure-környezetéhez csatlakoztatott, dedikált[távoli asztali átjáró](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560672(v=ws.10)) (RD-átjáró) kiszolgálót a helyszíni hálózatába, hogy központosíthasson minden rendszergazdai hozzáférést, és leegyszerűsíthesse a figyelést és a naplózást.

A távoli asztali átjáró egy házirendalapú RDP-proxyszolgáltatás, amely képes kikényszeríteni a biztonsági követelményeket. Az RD-átjáró Windows Server-hálózatvédelemmel (NAP) együtt alkalmazva segít biztosítani, hogy csak az Active Directory tartományi szolgáltatások (AD DS) csoportházirend-objektumai (GPO-k) által meghatározott biztonságiállapot-feltételeknek megfelelő ügyfelek csatlakozhatnak. Továbbá:

* Hozzon létre egy [Azure felügyeleti tanúsítványt](/previous-versions/azure/gg551722(v=azure.100)) az RD-átjárón, így biztosítva, hogy az legyen az egyetlen, az Azure Portalhoz való hozzáféréssel rendelkező állomás.
* Csatlakoztassa az RD-átjárót ugyanahhoz a [felügyeleti tartományhoz](/previous-versions/windows/it-pro/windows-2000-server/bb727085(v=technet.10)), amelyikhez a felügyeleti munkaállomások is kapcsolódnak. Ez akkor szükséges, ha helyek közötti IPsec VPN-t vagy ExpressRoute-ot használ az Azure AD felé egyirányú bizalmi kapcsolattal rendelkező tartományban, vagy ha összevonja a hitelesítő adatokat a helyszíni AD DS-példánya és az Azure AD között.
* Állítson be egy [ügyfélkapcsolat-engedélyezési házirendet](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753324(v=ws.11)) úgy, hogy az RD-átjáró ellenőrizze, érvényes-e (a tartományhoz csatlakozó-e) az ügyfélgép neve, és jogosult-e az Azure Portalhoz való hozzáférésre.
* Használjon IPsec-et az [Azure VPN-hez](https://azure.microsoft.com/documentation/services/vpn-gateway/) annak érdekében, hogy még jobban védje a felügyeleti adatforgalmat a lehallgatástól és a tokenlopástól. Ennek alternatívája lehet az elkülönített internetkapcsolat [Azure ExpressRoute-on](https://azure.microsoft.com/documentation/services/expressroute/) keresztül.
* Engedélyezze a többtényezős hitelesítést ( [Azure AD multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)) vagy intelligens kártyás hitelesítést olyan rendszergazdák számára, akik Rd-átjárón keresztül jelentkeznek be.
* Állítson be forrás [IP-címekre vonatkozó korlátozásokat](https://azure.microsoft.com/blog/2013/08/27/confirming-dynamic-ip-address-restrictions-in-windows-azure-web-sites/) vagy [hálózati biztonsági csoportokat](../../virtual-network/network-security-groups-overview.md) az Azure-ban a megengedett felügyeleti végpontok számának minimalizálása érdekében.

## <a name="security-guidelines"></a>Biztonsági irányelvek
A rendszergazdai munkaállomások felhőhasználatra való biztonságossá tétele általánosságban véve hasonlít bármilyen más, helyszíni munkaállomás esetében alkalmazott gyakorlathoz. Példa erre a minimalizált szerkezet és a korlátozó engedélyek. A felhőfelügyelet bizonyos egyedi aspektusai jobban hasonlítanak a távoli vagy izolált vállalatfelügyeletéihez. Ezek közé tartozik a hitelesítő adatok naplózása, a megnövelt biztonságú távelérés, valamint a fenyegetések észlelése és kezelése.

### <a name="authentication"></a>Hitelesítés
Használhat Azure bejelentkezési korlátozásokat az olyan forrás IP-címek korlátozására, amelyek jogosultak a felügyeleti eszközökhöz hozzáférni és naplózás-hozzáférési kérést küldeni. Annak érdekében, hogy az Azure azonosítani tudja a felügyeleti ügyfeleket (munkaállomásokat és/vagy alkalmazásokat), mindkét SMAPI konfigurálhatja (az ügyfél által fejlesztett eszközök, például a Windows PowerShell-parancsmagok segítségével), valamint az Azure Portal, hogy a TLS/SSL-tanúsítványok mellett telepíteni lehessen az ügyféloldali felügyeleti tanúsítványokat. Javasolt a többtényezős hitelesítés bevezetése a rendszergazdai hozzáférés esetében is.

Egyes Azure-ra telepített alkalmazások vagy szolgáltatások saját hitelesítési mechanizmusokkal rendelkezhetnek mind a végfelhasználói, mind a rendszergazdai hozzáféréshez, míg mások az Azure AD előnyeit használják ki. Attól függően, hogy hitelesítő adatokat von össze Active Directory összevonási szolgáltatások (AD FS) segítségével, címtár-szinkronizálást használ, vagy csak a felhőben kezeli a felhasználói fiókokat, a [Microsoft Identity Manager](/microsoft-identity-manager/) használata segít az identitás-életciklusok erőforrások közötti kezelésében.

### <a name="connectivity"></a>Kapcsolat
Az Azure virtuális hálózataihoz való ügyfélkapcsolatok biztonságossá tételének céljából több mechanizmus áll rendelkezésre. Ezen mechanizmusok közül kettő, a [helyek közötti VPN](https://channel9.msdn.com/series/Azure-Site-to-Site-VPN) (S2S) és a [pont-hely típusú VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) (P2S) lehetővé teszik az iparági szabványnak megfelelő IPsec (S2S) vagy a [Secure Socket Tunneling Protocol](/previous-versions/technet-magazine/cc162322(v=msdn.10)) (SSTP) (P2S) használatát titkosítás és alagútkezelés céljából. A nyilvánosan elérhető Azure-szolgáltatásokhoz (pl. Azure Portal) való csatlakozáshoz az Azure-nak HTTPS-kapcsolatra van szüksége.

Az olyan önálló megerősített munkaállomás esetében, amely nem RD-átjárón keresztül csatlakozik az Azure-hoz, javasolt, hogy SSTP alapú pont-hely típusú VPN-nel hozza létre az Azure Virtual Networkkel kialakított kezdeti kapcsolatot, majd az egyes virtuális gépekhez vezető RDP-kapcsolatokat a VPN-alagútból alakítsa ki.

### <a name="management-auditing-vs-policy-enforcement"></a>Felügyeleti naplózás vagy házirend kikényszerítése
A felügyeleti folyamatok biztonságossá tételére általában két megközelítés használatos: a naplózás és a házirendek kikényszerítése. A két módszer együttes használata átfogó ellenőrzést tesz lehetővé, de nem minden helyzetben lehetséges a kivitelezése. Továbbá mindkét megközelítés különböző szintű kockázattal, költséggel és a biztonsági felügyeletbe fektetett energiával jár, főképp a személyekbe és rendszerarchitektúrákba fektetett bizalom függvényében.

A megfigyelés és a naplózás biztosítják a felügyeleti tevékenység követésének és értelmezésének alapjait, de a keletkező adatmennyiség miatt nem mindig valósítható meg az összes művelet teljes részletességű naplózása. Ajánlott eljárás viszont a felügyeleti házirendek hatékonyságának naplózása.

A szigorú hozzáférés-vezérlést magában foglaló házirend-kikényszerítés olyan szoftveres mechanizmusokat léptet életbe, amelyek képesek felügyelni a rendszergazdák tevékenységét, és segít biztosítani, hogy azok minden lehetséges védelmi intézkedést betartsanak. A naplózás bizonyítékkal szolgál a kényszerítés működéséről, valamint feljegyzésekkel arról, ki és mit, melyik helyen és mikor hajtott végre. A naplózás egyúttal lehetővé teszi az információk feljegyzését és összehasonlítását azzal kapcsolatban, hogy betartják-e a rendszergazdák a házirendeket, valamint a tevékenységek bizonyítékait is rögzíti.

## <a name="client-configuration"></a>Ügyfél-konfiguráció
Háromféle elsődleges megerősített munkaállomás-konfigurációt ajánlunk. Legjobban a költség, használhatóság és hozzáférhetőség terén különböznek egymástól, de mind a három lehetőség hasonló biztonsági profillal rendelkezik. A következő táblázat egy rövid elemzést tartalmaz a három lehetőséggel járó előnyökről és kockázatokról. (Ne feledje, a „vállalati PC” általános jellegű asztali PC-konfigurációt jelent, amely minden tartományi felhasználó számítógépe esetében egyforma, szerepkörtől függetlenül.)

| Konfiguráció | Előnyök | Hátrányok |
| --- | --- | --- |
| Önálló, megerősített munkaállomás |Szigorúan ellenőrzött munkaállomás |A dedikált asztali gépek magasabb költsége |
| - | Az alkalmazások biztonsági réseinek kihasználásával kapcsolatos kisebb kockázat |Nagyobb energiabefektetést igénylő felügyelet |
| - | A feladatok egyértelmű elkülönítése | - |
| Vállalati PC mint virtuális gép |Alacsonyabb hardverköltségek | - |
| - | Szerepkör és alkalmazások elkülönítése | - |

Fontos, hogy a megerősített munkaállomás a gazda legyen, nem pedig a vendég, és semmi ne legyen a gazda operációs rendszer és a hardver között. A „tiszta forrás alapelv” (más néven „biztonságos eredet”) követése biztosítja, hogy a gazdagép a legjobban megerősített legyen. Egyéb esetben a megerősített munkaállomás (vendég) ki van téve a gazdarendszert érő támadásoknak.

Még jobban elkülönítheti a felügyeleti feladatköröket dedikált rendszerkép használatával minden egyes megerősített munkaállomáshoz, amely csak bizonyos Azure- és felhőalkalmazások felügyeletéhez rendelkezik a szükséges eszközökkel és engedélyekkel, valamint speciális helyi AD DS csoportházirend-objektumokkal is a szükséges feladatok ellátásához.

A helyszíni infrastruktúrával nem rendelkező informatikai környezetek esetében (ahol például a csoportházirend-objektumok számára nem lehetséges helyi AD DS-példányhoz történő hozzáférés, mert az összes kiszolgáló a felhőben található) az olyan szolgáltatások, mint a [Microsoft Intune](/mem/intune/) leegyszerűsíthetik a munkaállomás-konfigurációk kiépítését és fenntartását.

### <a name="stand-alone-hardened-workstation-for-management"></a>Önálló megerősített munkaállomás a felügyelethez
Önálló megerősített munkaállomás használatával a rendszergazdák rendelkeznek egy PC-vel vagy laptoppal, amelyet felügyeleti feladatok ellátására használnak, valamint egy másik, különálló PC-vel vagy laptoppal az egyéb feladatokhoz. Egy, az Azure-szolgáltatások kezelésére kijelölt munkaállomáson nincs szükség más alkalmazások telepítésére. Továbbá a [platformmegbízhatósági modulokat](/previous-versions/windows/it-pro/windows-vista/cc766159(v=ws.10)) (TPM) vagy hasonló hardverszintű kriptográfiás technológiát támogató munkaállomások használata segít az eszközök hitelesítésében és bizonyos típusú támadások megelőzésében. A TPM a rendszermeghajtó egész kötetes védelmét is képes támogatni a [BitLocker meghajtótitkosítás](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) használatával.

Az önálló megerősített munkaállomás-forgatókönyvben (lásd alább) a Windows tűzfal (vagy egy, nem a Microsoft által készített ügyféltűzfal) helyi példánya a bejövő kapcsolatok, mint például az RDP blokkolására van konfigurálva. A rendszergazda bejelentkezhet a megerősített munkaállomásra, és indíthat RDP-munkamenetet, amely az Azure Virtual Networkhöz való VPN-kapcsolat létesítése után kapcsolódik az Azure-hoz, de nem használhat RDP-t magához a megerősített munkaállomáshoz való kapcsolódáshoz vállalati PC-ről történő bejelentkezés révén.

![A különálló megerősített munkaállomás-forgatókönyvet bemutató diagram.](./media/management/stand-alone-hardened-workstation-topology.png)

### <a name="corporate-pc-as-virtual-machine"></a>Vállalati PC mint virtuális gép
Olyan esetekben, ahol az önálló megerősített munkaállomás megvalósítása költséges vagy nehézkes volna, ehelyett a megerősített munkaállomás üzemeltethet virtuális gépet a nem felügyeleti feladatok ellátására.

![Olyan diagram, amely a virtuális gépet üzemeltető megerősített munkaállomást jeleníti meg a nem rendszergazdai feladatok elvégzéséhez.](./media/management/hardened-workstation-enabled-with-hyper-v.png)

Annak érdekében, hogy elkerülje az ugyanannak a munkaállomásnak rendszerfelügyeletre és más napi munkafeladatok ellátására való használatából eredő lehetséges biztonsági kockázatokat, telepíthet Windows Hyper-V virtuális gépet a megerősített munkaállomásra. Ez a virtuális gép használható vállalati PC-ként. A vállalati PC-környezet így elkülönül a gazdagéptől, ami csökkenti annak támadási felületét, és elszigeteli a felhasználó napi tevékenységeit (például a levelezést) a bizalmas felügyeleti feladatoktól.

A vállalati PC virtuális gépe védett térben üzemel, és lehetővé teszi a felhasználói alkalmazások használatát. A gazdagép „tiszta forrás” marad, és kényszeríti a szigorú hálózati házirendek betartását a gyökér operációs rendszerben (például blokkolja a virtuális gépről történő RDP-hozzáférést).

## <a name="best-practices"></a>Ajánlott eljárások
Vegye figyelembe a következő irányelveket is az alkalmazások és adatok Azure-ban való kezelése során.

### <a name="dos-and-donts"></a>Ajánlott és nem ajánlott műveletek
Ne feltételezze, hogy ha egy munkaállomás zárolva lett, más gyakori biztonsági követelményeket nem kell teljesíteni. Az esetleges kockázat magasabb a megemelt hozzáférési szintek miatt, amelyekkel a rendszergazdai fiókok általában rendelkeznek. A kockázatokra való példákat és az elkerülésükre használatos biztonsági eljárásokat az alábbi táblázatban találja.

| Nem ajánlott | Ajánlott |
| --- | --- |
| Ne küldjön e-mail-hitelesítő adatokat rendszergazdai hozzáféréshez vagy egyéb titokhoz (például TLS/SSL vagy felügyeleti tanúsítványok). |Őrizze meg az adatok bizalmas mivoltát a fióknevek és jelszavak szóbeli közvetítésével (de ne tárolja őket hangpostán), végezze távolról az ügyfél-/kiszolgáló-tanúsítványok telepítését (titkosított munkameneten), védett hálózati megosztásról végezzen letöltéseket, illetve az adatokat személyesen, cserélhető adathordozókon tegye közzé. |
| - | Kezelje proaktívan a felügyeleti tanúsítvány-életciklusait. |
| Ne tároljon titkosítatlan vagy nem kivonatolt fiókjelszavakat alkalmazástárolókban (mint például táblázatokban, SharePoint-webhelyeken vagy fájlmegosztásokon). |Hozzon létre biztonságfelügyeleti alapelveket és rendszer-megerősítési házirendeket, és alkalmazza őket a fejlesztői környezetében. |
| - | Használjon [Enhanced Mitigation Experience Toolkit 5.5](https://technet.microsoft.com/security/jj653751) tanúsítványrögzítési szabályokat, hogy biztosítsa a megfelelő hozzáférést az Azure SSL-/TLS-helyekhez. |
| Ne ossza meg ugyanazt a fiókot vagy jelszót több rendszergazda között, és ne használja ugyanazt a jelszót több felhasználói fiókhoz vagy szolgáltatáshoz, különösképpen közösségi oldalakon és más nem felügyeleti tevékenységekhez. |Hozzon létre dedikált Microsoft-fiókot az Azure-előfizetése kezelésére – egy olyat, amelyet nem használ személyes e-mailes levelezésre. |
| Ne küldjön el e-mail-mellékletként konfigurációs fájlokat. |A konfigurációs fájlokat és profilokat megbízható helyről telepítse (például titkosított USB flash meghajtóról), nem pedig olyan biztonsági szempontból támadásoknak kitett mechanizmust igénybe véve, mint például az e-mail. |
| Ne használjon gyenge vagy egyszerű bejelentkezési jelszót. |Tartassa be az erős jelszavak használatára vonatkozó házirendeket, és alkalmazzon lejárati ciklusokat (első használatkori megváltoztatással), konzolhasználati időkorlátokat, valamint automatikus fiókzárolást. Használjon ügyféljelszó-kezelő rendszert, többtényezős hitelesítéssel a jelszótárolóhoz való hozzáféréshez. |
| Ne tegye interneten keresztül elérhetővé a felügyeleti portokat. |Zárolja az Azure-portokat és IP-címeket a felügyeleti hozzáférés korlátozása érdekében. További információkért tekintse meg az [Azure hálózati biztonság](https://download.microsoft.com/download/4/3/9/43902EC9-410E-4875-8800-0788BE146A3D/Windows%20Azure%20Network%20Security%20Whitepaper%20-%20FINAL.docx) című tanulmányt. |
| - | Használjon tűzfalat, VPN-t és NAP-ot minden felügyeleti kapcsolat esetében. |

## <a name="azure-operations"></a>Az Azure üzemeltetése
A Microsoftnál az Azure üzemeltetésének keretein belül az üzemeltetési mérnökök és a támogatási személyzet az Azure éles rendszereihez [virtuális géppel ellátott megerősített munkaállomás PC-ken](#stand-alone-hardened-workstation-for-management) keresztül férnek hozzá. A virtuális gépen keresztül lehetséges a vállalati hálózathoz és alkalmazásokhoz (például e-mail, intranet stb.) való hozzáférés. Minden felügyeleti munkaállomás rendelkezik TPM-mel, a gazda-rendszerindítási meghajtó BitLockerrel van titkosítva, és egy speciális szervezeti egységhez (OU) vannak csatlakoztatva a Microsoft elsődleges vállalati tartományában.

A rendszer-megerősítést a csoportházirendekkel kényszerítjük ki, központosított szoftverfrissítés mellett. Az eseménynaplókat (mint a biztonsági naplók és az AppLocker naplói) vizsgálati és elemzési célból begyűjtjük a felügyeleti munkaállomásokról, és egy központi helyen tároljuk őket.

Ezenkívül az Azure üzemi hálózatához való csatlakozás a Microsoft hálózatán található, kéttényezős hitelesítést igénylő, dedikált jump-box kiszolgálók segítségével történik.

## <a name="azure-security-checklist"></a>Azure biztonsági ellenőrzőlista
A rendszergazdák által a megerősített munkaállomásokon végezhető feladatok minimalizálása segít csökkenteni a támadási felületet a fejlesztői és felügyeleti környezetben. Használja a következő technológiákat a megerősített munkaállomása védelmének érdekében:

* Az IE megerősítése. Az Internet Explorer böngésző (illetve bármelyik böngésző) fontos belépési pont lehet az ártalmas kód számára, a külső szolgáltatókkal való hosszútávú kapcsolatok miatt. Tekintse át az ügyfélházirendjeit, és kényszerítse a védett üzemmódban futást. Tiltsa le a bővítményeket és a letöltéseket, és használjon [Microsoft SmartScreen](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj618329(v=ws.11)) szűrést. Győződjön meg róla, hogy a biztonsági figyelmeztetések mindig megjelennek. Használja ki az internetzónák előnyeit, és hozzon létre egy listát a megbízható webhelyekről, amelyekhez megerősítést állított be. Tiltson le minden más webhelyet és böngészőn belüli kódot, mint például az ActiveX és a Java használatát.
* Általános jogokkal rendelkező felhasználó. Az általános jogokkal rendelkező felhasználóként való üzemeltetés több előnnyel jár, melyek közül a legnagyobb, hogy így nehezebb a kártevőknek a rendszergazdai hitelesítő adatokat ellopni. Ezenkívül az általános jogokkal rendelkező felhasználói fiók nem rendelkezik emelt szintű jogosultságokkal a gyökér operációs rendszeren, és sok beállítási lehetőség és API alapértelmezés szerint le van tiltva.
* AppLocker. Az [AppLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619725(v=ws.10)) segítségével korlátozhatja a felhasználók által futtatható programokat és parancsfájlokat. Az AppLocker futtatható naplózási és kényszerítési módban. Alapértelmezett esetben az AppLockernek van egy engedélyezési szabálya, amely minden programkód futtatását engedélyezi az ügyfélen a rendszergazdai tokennel rendelkező felhasználóknak. Ez a szabály megakadályozza, hogy a rendszergazdák kizárják önmagukat, és csak emelt szintű tokenekre vonatkozik. Lásd még: Kódintegritás a Windows Server [központi biztonságának](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd348705(v=ws.10)) részeként.
* Kódaláírás. Az összes, rendszergazdák által használt eszköz és programkód kódaláírással való ellátása jól kezelhető mechanizmust biztosít alkalmazászárolási házirendek megvalósítására. A kivonatok nem követik arányosan a kód gyors változásait, és a fájlútvonalak biztonsági szintje nem magas. Ötvözze az AppLocker szabályait PowerShell [végrehajtási házirenddel](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10)), amely csak bizonyos előre megadott aláírt programkódok és parancsfájlok [végrehajtását](/powershell/module/microsoft.powershell.security/set-executionpolicy) engedélyezi.
* Csoportházirend. Hozzon létre globális felügyeleti házirendet, amely minden tartománybeli, felügyeletre használt munkaállomásra vonatkozik (minden más hozzáférést pedig tiltson le), valamint az ezen munkaállomásokon hitelesített összes felhasználói fiókra is.
* Megnövelt biztonságú kiépítés. Gondoskodjon a megerősített munkaállomások kiindulási rendszerképének védelméről, hogy jobban védekezhessen az illetéktelen módosítások ellen. Használjon titkosítást, elkülönítést és hasonló biztonsági intézkedéseket a rendszerképek, virtuális gépek és parancsfájlok tárolásánál, és korlátozza az ezekhez való hozzáférést (esetleg naplózható be- és kijelentkezési folyamat bevezetésével).
* Javítások. Tartson fent egy egységes buildet (vagy készítsen különböző rendszerképeket fejlesztéshez, üzemeltetéshez és egyéb felügyeleti feladatokhoz), rutinszerűen vizsgálja át a rendszert a változtatások és kártevők megtalálása érdekében, tartsa naprakészen a buildet, és csak akkor kapcsolja be a gépeket, amikor szükség van rájuk.
* Titkosítás. Győződjön meg róla, hogy a felügyeleti munkaállomásai rendelkeznek TPM-mel, a [titkosított fájlrendszer](/previous-versions/tn-archive/cc700811(v=technet.10))(EFS) és a BitLocker biztonságosabb üzembe helyezésének érdekében.
* Irányítás. Az AD DS csoportházirend-objektumok használatával ellenőrzés alatt tarthatja a rendszergazdák összes Windows-felületét, mint például a fájlmegosztást. Terjessze ki a naplózási és megfigyelési folyamatokat a felügyeleti munkaállomásokra. Kövessen nyomon minden rendszergazdai és fejlesztői hozzáférést és tevékenységet.

## <a name="summary"></a>Összefoglalás
A megerősített munkaállomás-konfiguráció Azure-felhőszolgáltatások, virtuális gépek és szolgáltatások felügyeletére való használata segíthet számos olyan kockázatok és fenyegetések elkerülésében, amelyek a kritikus informatikai infrastruktúrák távfelügyeletével járhatnak. Az Azure és a Windows is kínál a kommunikáció, hitelesítés és ügyfélviselkedés védelmére és ellenőrzésére alkalmas mechanizmusokat.

## <a name="next-steps"></a>Következő lépések
A következő források általánosabb információkat kínálnak az Azure-ról és a kapcsolódó Microsoft-szolgáltatásokról, az ebben a dokumentumban bemutatott konkrét elemek mellett:

* [Az emelt szintű hozzáférés biztonságossá tétele](/windows-server/identity/securing-privileged-access/securing-privileged-access) – itt technikai részleteket olvashat az Azure-felügyelethez használt biztonságos felügyeleti munkaállomás tervezéséről és kiépítéséről.
* [Microsoft biztonsági és adatkezelési központ](https://microsoft.com/en-us/trustcenter/cloudservices/azure) – ismerje meg az Azure platform olyan képességeit, amelyek megvédik az Azure-hálót és az Azure-on futó számítási feladatokat.
* [Microsoft Security Response Center](https://www.microsoft.com/msrc) – itt lehet bejelentést tenni a Microsoft biztonsági réseiről, beleértve az Azure-ral kapcsolatos problémákat (vagy pedig e-mailben, a [secure@microsoft.com](mailto:secure@microsoft.com) címen)