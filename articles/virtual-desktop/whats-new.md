---
title: Újdonságok a Windows Virtual Desktop? – Azure
description: Új funkciók és termékfrissítések a Windows Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 04/08/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 242dd2476783a3d1db6b74452d35e87c5cd189b6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516552"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Újdonságok a Windows Virtual Desktop?

Windows Virtual Desktop frissítéseket. Ebben a cikkben a következővel fog többet megtudni:

- A legújabb frissítések
- Új funkciók
- A meglévő funkciók fejlesztései
- Hibajavítások

Ezt a cikket havonta frissítjük. Mindenképpen gyakran ellenőrizze itt, hogy lépést tart-e az új frissítésekkel.

## <a name="client-updates"></a>Ügyfélfrissítések

Az alábbi cikkekből megtudhatja, hogyan frissülnek az ügyfelek a Windows Virtual Desktop és Távoli asztali szolgáltatások:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="windows-virtual-desktop-agent-updates"></a>Windows Virtual Desktop ügynök frissítései

A Windows Virtual Desktop ügynök havonta legalább egyszer frissül.

A következő módosult a Windows Virtual Desktop Agentben:

- 1.0.2990.800-as verzió: Ez a frissítés 2021. április 13-án jelent meg, és a következő módosításokat tartalmazza:
    - Ügynök hibaüzenetei frissítve.
    - Kivételt ad hozzá, amely megakadályozza, hogy nem Windows 7 rendszerű ügynököket telepítsen Windows 7 rendszerű virtuális gépekre.
    - Frissítette a szívverési szolgáltatás logikáját.
- 1.0.2944.1400-as verzió: Ez a frissítés 2021. április 7-én jelent meg, és a következő változásokat tartalmazza:
    - Az ügynökhibákra vonatkozó Windows Virtual Desktop ügynök hibaelhárítási útmutatójára mutató hivatkozásokat helyezett el az eseménynaplóban.
    - Egy további kivétel hozzáadva a jobb hibakezelés érdekében.
    - Hozzáadta a WVDAgentUrlTool.exe, amely lehetővé teszi az ügyfelek számára, hogy ellenőrizzék, mely szükséges URL-címeket tudják elérni.
- 1.0.2866.1500-as verzió: Ez a frissítés 2021. március 26-án jelent meg, és kijavítja a verem állapot-ellenőrzésével kapcsolatos problémát.
- 1.0.2800.2802-es verzió: Ez a frissítés 2021. március 10-én jelent meg, és általános fejlesztésekkel és hibajavításokkal rendelkezik.
- 1.0.2800.2800-as verzió: Ez a frissítés 2021. március 2-án jelent meg, és kijavított egy fordított kapcsolati problémát.
- 1.0.2800.2700-as verzió: Ez a frissítés 2021. február 10-én jelent meg, és általános fejlesztéseket és hibajavításokat tartalmaz.
- 1.0.2800.2700-as verzió: Ez a frissítés 2021. február 4-én jelent meg, és kijavított egy hozzáférés-megtagadási vezénylési problémát.

## <a name="fslogix-updates"></a>FSLogix-frissítések

Kíváncsi az FSLogix legújabb frissítésére? Tekintse meg [az FSLogix újdonságai részt.](/fslogix/whats-new)

## <a name="march-2021"></a>2021. március

2021 márciusában a következő változás történt:

### <a name="updates-to-the-azure-portal-ui-for-windows-virtual-desktop"></a>A Azure Portal felhasználói felületének frissítései Windows Virtual Desktop

A következő frissítéseket Windows Virtual Desktop a Azure Portal:

- Engedélyeztünk új rendelkezésre állási lehetőségeket (rendelkezésre állási csoport és zónák) a munkafolyamatokhoz gazdagépkészletek létrehozásához és virtuális gépek hozzáadásához.
- Kijavítottunk egy hibát, amely miatt a "Segítségre van szüksége" állapotú gazdagép nem elérhetőként jelent meg. Most a gazdagép mellett megjelenik egy figyelmeztető ikon.
- Engedélyezett az aktív munkamenetek rendezése.
- Mostantól üzeneteket küldhet vagy kijelentkeztethet adott felhasználóknak a gazdagép részletei lapon.
- Módosította a munkamenetek maximális korlátja mezőt.
- Hozzáadtunk egy szervezeti egység ellenőrzési útvonalát a munkafolyamathoz egy gazdagépkészlet létrehozásához.
- Most már használhatja a rendszerkép Windows 10 a személyes gazdagépkészlet létrehozásakor.

### <a name="generation-2-images-and-trusted-launch"></a>2. generációs rendszerképek és megbízható indítás

A Azure Marketplace már rendelkezik 2. generációs rendszerképekkel Windows 10 Enterprise és Windows 10 Enterprise munkamenethez. Ezekkel a rendszerképekkel megbízható indítású virtuális gépeket használhat. További információ a 2. generációs virtuális gépekről: [Érdemes 1. vagy 2.](../virtual-machines/generation-2.md)generációs virtuális gépet létrehozni. A Megbízható indítású virtuális gépek Windows Virtual Desktop a [TechCo új bejegyzésében olvashat.](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-support-for-trusted-launch/m-p/2206170)

### <a name="fslogix-is-now-preinstalled-on-windows-10-enterprise-multi-session-images"></a>Az FSLogix most már előre Windows 10 Enterprise több munkamenetből álló rendszerképeken

Az ügyfelek visszajelzései alapján beállítottuk az Windows 10 Enterprise több munkamenetes rendszerkép új verzióját, amely már telepítve van az FSLogix nem konfigurált verziójával. Reméljük, hogy ez megkönnyíti a Windows Virtual Desktop üzembe helyezését.

### <a name="azure-monitor-for-windows-virtual-desktop-is-now-in-general-availability"></a>Azure Monitor a Windows Virtual Desktop mostantól általánosan elérhető

Azure Monitor a Windows Virtual Desktop mostantól általánosan elérhető a nyilvánosan. Ez a szolgáltatás egy automatizált szolgáltatás, amely figyeli az üzemelő példányokat, és lehetővé teszi, hogy egyetlen helyen tekintse meg az eseményeket, az állapotokat és a hibaelhárítási javaslatokat. További információért tekintse meg [dokumentációnkat,](azure-monitor.md) vagy tekintse meg [TechCo blogbejegyzésünket.](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-is-generally-available/m-p/2242861)

### <a name="march-2021-updates-for-teams-on-windows-virtual-desktop"></a>A Teams 2021. márciusi Windows Virtual Desktop

A következő frissítéseket készítettünk a Teamshez a Windows Virtual Desktop:

- Továbbfejlesztettük a hívások videóminőségét és a 2x2 módot.
- A cpu-kihasználtságot 5–10%-kal csökkentettük (a cpu-generációtól függően) a videófeldolgozás (XVP) hardveres kiszervezésével.
- A régebbi gépek mostantól az XVP és a hardver dekódolása segítségével zökkenőmentesen, 2x2 módban jelenítik meg a bejövő videóstreameket.
- Frissítettük a WebRTC vermet M74-ről M88-ra a jobb AV-szinkronizálási teljesítmény és kevesebb átmeneti probléma érdekében.
- A H264 kódolót OpenH264-re cseréljük (a Weben a Teamsben használt OSS-re), ami megnövelte a kimenő kamera videóminőségét.
- Március 30-án engedélyeztünk egy 2x2-es üzemmódot a Teams Server általános nyilvános számára. A 2x2 mód egyszerre legfeljebb négy bejövő videóstreamet mutat.

### <a name="start-vm-on-connect-public-preview"></a>Indítsa el a virtuális gépet a Connect public preview (Csatlakozás nyilvános előzetes verzió) funkcióval

Az új gazdagépkészlet-beállítás, a Virtuális gép létrehozása csatlakozáskor, mostantól nyilvános előzetes verzióban érhető el. Ezzel a beállítással bármikor bekapcsolhatja a virtuális gépeket, amikor szüksége van rájuk. Ha meg szeretné menteni a költségeket, fel kell szabadította a virtuális gépeket a virtuális gép Azure Compute konfigurálásával. További információért tekintse meg [blogbejegyzésünket](https://aka.ms/wvdstartvmonconnect) és [dokumentációunkat.](start-virtual-machine-connect.md)

### <a name="windows-virtual-desktop-specialty-certification"></a>Windows Virtual Desktop különleges tanúsítvány

Kiadtunk egy bétaverziót az AZ-140 vizsga számára, amelyből bizonyíthatja az Azure-beli Windows Virtual Desktop szakértelmét. További tudnivalókért tekintse meg [TechCo új bejegyzésünket.](https://techcommunity.microsoft.com/t5/microsoft-learn-blog/beta-exam-prove-your-expertise-in-windows-virtual-desktop-on/ba-p/2147107)

## <a name="february-2021"></a>2021. február

A következő változott 2021 februárjában.

### <a name="portal-experience"></a>Portal-felület

A következő módokon javítottuk Azure Portal felhasználói élményt:

- Tömeges kiürítési mód gazdagépen a munkamenetgazda rács lapján. 
- Az MSIX-alkalmazás csatolása mostantól elérhető a nyilvános előzetes verzióban.
- Ki lett javítva a gazdagépkészlet áttekintési információja a sötét módhoz.

### <a name="eu-metadata-storage-now-in-public-preview"></a>Az EU metaadatainak tárolása mostantól nyilvános előzetes verzióban érhető el

Az európai (EU-) földrajzi hely nyilvános előzetes verziója mostantól a szolgáltatási metaadatok tárolási lehetőségeként érhető el a Windows Virtual Desktop. Az ügyfelek a szolgáltatásobjektumaik létrehozásakor választhatnak Nyugat- vagy Észak-Európa között. A gazdagépkészletek szolgáltatásobjektumai és metaadatai az egyes régiókhoz társított Azure földrajzi helyen lesznek tárolva. További információért olvassa el a nyilvános előzetes kiadást [bejelentő blogbejegyzésünket.](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939)

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>A Teams a Windows Virtual Desktop beépülő modul frissítéseit

Továbbfejlesztettük a videóhívások minőségét a Windows Virtual Desktop beépülő modulon a leggyakrabban jelentett problémák kezelésével, például hogy mikor sötétedik a képernyő, vagy mikor lesz hirtelen sötét a videó és a hangszinkronizálva. Ezeknek a fejlesztéseknek növelniük kell az egy videós nézet teljesítményét az aktív beszélőváltással. Kijavítottunk egy problémát is, amely miatt a speciális karakterekkel nem elérhető hardvereszközök nem voltak elérhetők a Teamsben.

## <a name="january-2021"></a>2021. január

A 2021. januári változás a következő:

### <a name="new-windows-virtual-desktop-offer"></a>Új Windows Virtual Desktop ajánlat

Az új ügyfelek akár 90 napig 30%-kal is megtakarítják Windows Virtual Desktop D- és Bs-sorozatú virtuális gépek számítási költségeit a natív Microsoft-megoldás használata esetén. Ezt az ajánlatot 2021. március 31. előtt Azure Portal beválthatja a következőben: . További információt a Windows Virtual Desktop [oldalon olvashat.](https://azure.microsoft.com/services/virtual-desktop/offer/)

### <a name="networksecuritygrouprules-value-change"></a>networkSecurityGroupRules értékváltozás 

A Azure Resource Manager sablonban módosította a networkSecurityGroupRules alapértelmezett értékét objektumról tömbre. Ez megakadályozza a hibákat, ha a managedDisks-customimagevm.jsa networkSecurityGroupRules értékének megadása nélkül használja. Ez nem volt kompatibilitást hozó változás, és visszamenőlegesen kompatibilis.

### <a name="fslogix-hotfix-update"></a>FSLogix gyorsjavítás frissítése

Megjelent az FSLogix 2009-es HF_01-es verziója (2.9.7654.46150) az előző kiadásban (2.9.7621.30127) problémák megoldása érdekében. Javasoljuk, hogy a lehető leghamarabb állítsa le az előző verzió használatát, és frissítse az FSLogixot.

További információkért tekintse meg az Újdonságok az [FSLogixban részt](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150)a kibocsátási megjegyzésekben.

### <a name="azure-portal-experience-improvements"></a>Azure Portal felhasználói élmény fejlesztései

A következő fejlesztéseket javítottuk a Azure Portal felhasználói élményben:

- Most már közvetlenül hozzáadhatja a helyi virtuális gép rendszergazdai hitelesítő adatait ahelyett, hogy egy, a tartományhoz való csatlakozáshoz szükséges hitelesítő Active Directory létrehozott helyi fiókot adna hozzá.
- A felhasználók mostantól egyéni és csoport-hozzárendeléseket is listába sorolnak külön lapokon az egyes felhasználók és csoportok számára.
- Az Windows Virtual Desktop Agent verziószáma mostantól látható a gazdagépkészletek virtuális gépek áttekintésében.
- Tömeges törlés hozzáadva a gazdagépkészletek és alkalmazáscsoportok számára.
- Mostantól engedélyezheti vagy letilthatja a kiürítési módot több munkamenetgazda számára egy gazdagépkészletben.
- A nyilvános IP-cím mező el lett távolítva a virtuális gép részleteit tartalmazó lapról.

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>Windows Virtual Desktop ügynök hibaelhárítása

Nemrég beállítottuk a [Windows Virtual Desktop Agent hibaelhárítási](troubleshoot-agent.md) útmutatóját, amely segítséget ad a gyakori problémákkal szembesülő ügyfeleknek.

### <a name="microsoft-defender-for-endpoint-integration"></a>Microsoft Defender végpontintegrációhoz

Általánosan elérhető a Microsoft Defender végpontintegrációja. Ez a funkció ugyanazt a Windows Virtual Desktop teszi lehetővé a virtuális gépek vizsgálatát, mint a helyi Windows 10 gépeken. Ha több munkamenetet használ, Windows 10 Enterprise Microsoft Defender for Endpoint akár 50 egyidejű felhasználói kapcsolatot is támogat, így Windows 10 Enterprise több munkamenetes munkamenet költségmegtakarítása és Windows 10 Enterprise Microsoft Defender végponttal kapcsolatos megbízhatósága is elérhető. További információt a [blogbejegyzésben talál.](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712)

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Az Azure biztonsági alapkonfigurációja Windows Virtual Desktop

Nemrég közzétettünk egy cikket [az Azure](security-baseline.md) biztonsági alapkonfigurációról Windows Virtual Desktop, amelyre fel szeretnénk hívni a figyelmet. Ezek az irányelvek az Azure biztonsági teljesítményteszt 2.0-s verziójának alkalmazásával kapcsolatos információkat tartalmaznak Windows Virtual Desktop. Az Azure biztonsági teljesítményteszt ismerteti azokat a beállításokat és eljárásokat, amelyek használatát javasoljuk az Azure-beli felhőalapú megoldások biztonságossá való beállítása érdekében.

## <a name="december-2020"></a>2020. december

2020 decemberében a következő változott: 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Azure Monitor a Windows Virtual Desktop

A nyilvános előzetes verzió Azure Monitor a Windows Virtual Desktop elérhető. Ez az új funkció egy nagy teljesítményű irányítópultot tartalmaz, amely Azure Monitor-munkafüzetek alapján segít az informatikai szakembereknek megérteni a Windows Virtual Desktop környezetüket. További [részletekért tekintse](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587) meg blogunkban a bejelentést. 

### <a name="azure-resource-manager-template-change"></a>Azure Resource Manager módosítása 

A legújabb frissítésben eltávolítottuk az összes nyilvános IP-cím paramétert a Azure Resource Manager gazdagépkészletek létrehozásához és üzembe Azure Resource Manager sablonból. Javasoljuk, hogy az üzemelő példány biztonságossá Windows Virtual Desktop nyilvános IP-Windows Virtual Desktop használatát. Ha az üzemelő példány nyilvános IP-kre támaszkodott, újra kell konfigurálnia, hogy privát IP-eket használjon, különben az üzembe helyezés nem fog megfelelően működni.

### <a name="msix-app-attach-public-preview"></a>MSIX-alkalmazás csatolása nyilvános előzetes verzióhoz 

Az MSIX-alkalmazás csatolása egy másik szolgáltatás, amely ebben a hónapban kezdte meg a nyilvános előzetes verzió kiadását. Az MSIX app attach egy olyan szolgáltatás, amely dinamikusan mutatja be az MSIX-alkalmazásokat a Windows Virtual Desktop Munkamenetgazda virtuális gépei számára. További [részletekért tekintse](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231) meg blogunkban a bejelentést. 

### <a name="screen-capture-protection"></a>Képernyőfelvétel-védelem 

Ebben a hónapban a képernyőfelvétel-védelem nyilvános előzetes kiadásának kezdetét is jelölte. Ezzel a funkcióval megakadályozhatja, hogy bizalmas adatokat rögzítettek az ügyfélvégponton. Képernyőfelvétel-védelemmel próbálja ki ezt az [oldalt:](https://aka.ms/WVDScreenCaptureProtection).  

### <a name="built-in-roles"></a>Beépített szerepkörök

Új beépített szerepköröket adtunk hozzá a rendszergazdai Windows Virtual Desktop számára. További információ: [Beépített](rbac.md)szerepkörök a Windows Virtual Desktop. 

### <a name="application-group-limit-increase"></a>Alkalmazáscsoport korlátnövekedése

A bérlőnkénti alapértelmezett alkalmazáscsoport-korlátot Azure Active Directory 200 csoportra.

### <a name="client-updates-for-december-2020"></a>2020. decemberi ügyfélfrissítések

A következő ügyfelek új verzióit adták ki: 

- Android
- macOS
- Windows

További információ az ügyfélfrissítésekkel kapcsolatban: [Ügyfélfrissítések.](whats-new.md#client-updates)

## <a name="november-2020"></a>2020. november

### <a name="azure-portal-experience"></a>Azure Portal felhasználói élmény

Két hibát kijavítottunk a felhasználói Azure Portal felhasználói élményben:

- Az asztali alkalmazás rövid nevét már nem írja felül a "Virtuális gép hozzáadása" munkafolyamat.
- A munkamenetgazda lap betöltődik, ha a munkamenetgazda-gazdagépek méretezési készletek részei.

### <a name="fslogix-client-version-2009"></a>FSLogix-ügyfél, 2009-es verzió 

Az FSLogix-ügyfél új verzióját számos javítással és fejlesztéssel adták ki. További információt a [blogbejegyzésünkben talál.](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix)

### <a name="rdp-shortpath-public-preview"></a>RDP – Rövid távú nyilvános előzetes verzió

Az RDP shortpath közvetlen kapcsolatot biztosít a Windows Virtual Desktop munkamenetgazda számára pont–hely és hely–hely VPN-ek és ExpressRoute használatával. Emellett bevezeti az URCP átviteli protokollt is. Az RDP Shortpath úgy lett kialakítva, hogy csökkentse a késést és a hálózati ugrásokat a felhasználói élmény javítása érdekében. További információ: [RDP Windows Virtual Desktop.](shortpath.md)

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization, 2.0.1-es verzió

A parancsmagok 2.0.1-es verzióját Windows Virtual Desktop ki. Ez a frissítés olyan parancsmagokat tartalmaz, amelyek segítségével kezelheti az MSIX-alkalmazás csatolását. Az új verziót a [PowerShell-galériából töltheti le.](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1)

### <a name="azure-advisor-updates"></a>Azure Advisor frissítései

Azure Advisor új javaslat áll a Windows Virtual Desktop közelségi útmutatására, valamint egy új javaslat a teljesítmény mélyreható, elosztott terhelésű gazdagépkészletek optimalizálására. További információt az [Azure webhelyén talál.](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/)

## <a name="october-2020"></a>2020. október

2020 októberében a következő változott:

### <a name="improved-performance"></a>Jobb teljesítmény

- A következő Azure-beli földrajzi területek csatlakozási késésének csökkentésével optimalizáltuk a teljesítményt:
    - Svájc
    - Kanada

Most már a [Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) használatával megbecsülheti a felhasználói élmény minőségét ezeken a területeken.

### <a name="azure-government-cloud-availability"></a>Azure Government felhő rendelkezésre állása

A Azure Government Cloud általánosan elérhető. További információt a [blogbejegyzésben talál.](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/)

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Windows Virtual Desktop Azure Portal frissítések

Néhány frissítést készítettünk a Windows Virtual Desktop Azure Portal:

- Kijavítottunk egy resourceID-hibát, amely megakadályozta, hogy a felhasználók megnyitják a "Munkamenetek" lapot.
- Egyszerűsítette a felhasználói felületet a "Munkamenet-gazdagépek" lapon.
- Ki van javítva az "Alapértelmezések", "Használhatóság" és "Alapértelmezett beállítások visszaállítása" beállítás az RDP-tulajdonságok alatt.
- Az "Eltávolítás" és a "Törlés" függvények konzisztensek az összes lapon.
- A portál mostantól ellenőrzi az alkalmazásneveket az "Alkalmazás hozzáadása" munkafolyamatban.
- Kijavítottunk egy hibát, amely miatt a munkamenetgazda exportálási adatai nem igazodtak az oszlopokhoz.
- Kijavítottunk egy hibát, amely miatt a portál nem tudta lekérni a felhasználói munkameneteket.
- Kijavítottunk egy hibát a munkamenetgazda lekérésében, amely akkor történt, amikor a virtuális gépet egy másik erőforráscsoportban hozták létre.
- Frissült a "Munkamenetgazda" lap az aktív és a leválasztott munkamenetek listához.
- Az "Alkalmazások" lap már oldalakat is tartalmaz.
- Kijavítottunk egy hibát, amely miatt a "parancssor szükséges" szöveg nem megfelelően jelent meg az "Alkalmazáslista" lapon.
- Kijavítottunk egy hibát, amely akkor jelent meg, amikor a portál nem tudott gazdagépkészleteket vagy virtuális gépeket üzembe helyezni a Shared Image Gallery.

### <a name="client-updates-for-october-2020"></a>2020. októberi ügyfélfrissítések

Az ügyfelek új verzióit adták ki. További információért tekintse meg ezeket a cikkeket:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

További információ a többi ügyfélről: [Ügyfélfrissítések.](#client-updates)

## <a name="september-2020"></a>2020. szeptember

2020 szeptemberében a következő változott:

- A következő Azure-beli földrajzi területek csatlakozási késésének csökkentésével optimalizáltuk a teljesítményt:
    - Németország
    - Dél-Afrika (csak ellenőrzési környezetek esetén)

Most már a [Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) használatával megbecsülheti a felhasználói élmény minőségét ezeken a területeken.

- A Windows asztali ügyfél 1.2.1364-es verzióját adták ki a Windows Virtual Desktop. Ebben a frissítésben a következő módosításokat végrehajtotta:
    - Kijavítottunk egy hibát, amely miatt az egyszeri bejelentkezés (SSO) nem működött Windows 7 rendszeren.
    - Kijavítottunk egy problémát, amely miatt az ügyfél megszakadt, amikor egy Teams médiaoptimalizálást engedélyező felhasználó megpróbált Teams-értekezletet hívni vagy csatlakozni, miközben egy másik alkalmazás kizárólagos módban nyitott egy hangstreamet.
    - Kijavítottunk egy hibát, amely miatt a Teams nem enumerálta a hang- vagy videoeszközöket, amikor engedélyezve volt a Teams médiaoptimalizálása.
    - Hozzá van adva egy "Segítségre van szükségem a beállításokkal?" az asztali beállítások oldalára mutató hivatkozás.
    - Ki lett javítva az "Előfizetés" gomb kontrasztos sötét témák használata esetén történt problémája.
    
- A felhasználóink hatalmas segítségének köszönhetően kijavítottunk két kritikus problémát a Microsoft Store Távoli asztal ügyfélhez. Továbbra is áttekintjük a visszajelzéseket, és kijavítjuk a problémákat, mivel az ügyfél szakaszos kiadását világszerte több felhasználóra bővítjük.
    
- Hozzáadtunk egy új funkciót, amellyel módosíthatja a virtuális gép helyét, rendszerképét, erőforráscsoportját, előtagnevét és hálózati konfigurációját a virtuális gépek üzembe helyezéshez való hozzáadásának munkafolyamatának részeként a Azure Portal.

- Az it-szakemberek mostantól a Microsoft Azure Active Directory használatával Windows 10 Enterprise virtuális gépeket is Endpoint Manager. További információt a [blogbejegyzésben talál.](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048)

## <a name="august-2020"></a>2020. augusztus

2020 augusztusában a következő változott:

- Javítottunk a teljesítményen, hogy csökkentsük a kapcsolat késését a következő Azure-régiókban: 

    - Egyesült Királyság
    - Franciaország
    - Norvégia
    - Dél-Korea

   A Experience [Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) segítségével általánosan átveheti, hogy ezek a módosítások milyen hatással lesznek a felhasználókra.

- A Microsoft Store Távoli asztal Client (v10.2.1522+) mostantól általánosan elérhető! A Microsoft Store Távoli asztal Client ezen verziója kompatibilis a Windows Virtual Desktop. Frissített felhasználói felületi folyamatokat is bevezettünk a jobb felhasználói élmény érdekében. Ez a frissítés a fluent tervezést, a világos és a sötét módot, valamint számos egyéb izgalmas változást tartalmaz. Át is írtuk az ügyfelet, hogy ugyanazt a mögöttes RDP-motort használja, mint az iOS-, macOS- és Android-ügyfelek. Ez lehetővé teszi, hogy minden platformon gyorsabban biztosítsunk új funkciókat. [Töltse le az ügyfelet,](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) és próbálja ki!

- Kijavítottunk egy hibát a Teams Asztali ügyfélben (1.3.00.21759-es verzió), amelyben az ügyfél csak az UTC időzónát mutatta be a csevegésben, a csatornákon és a naptárban. A frissített ügyfél most már a távoli munkamenet időzónát jeleníti meg.

- Azure Advisor már a Windows Virtual Desktop. Ha a Windows Virtual Desktop keresztül fér hozzá a Azure Portal, javaslatokat láthat a saját Windows Virtual Desktop optimalizálására. További információ: [Azure Advisor.](azure-advisor.md)

- Az Azure CLI mostantól támogatja Windows Virtual Desktop ( ) szolgáltatásokat, amelyek segítségével automatizálhatja a `az desktopvirtualization` Windows Virtual Desktop üzembe helyezését. A [bővítményparancsok listájáért tekintse](/cli/azure/ext/desktopvirtualization/) meg az asztalvirtualizálást.

- Frissítettük az üzembe helyezési sablonokat, hogy teljes mértékben kompatibilisek a Windows Virtual Desktop Azure Resource Manager felületekkel. A sablonokat a [GitHubon találja.](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates)

- A Windows Virtual Desktop US Gov portál nyilvános előzetes verzióban érhető el. További információért tekintse meg a [bejelentést.](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/)

## <a name="july-2020"></a>2020. július  

Júliusban vált általános Windows Virtual Desktop az Azure Resource Management-integrációval való integrációra.

Az új kiadás a következőt módosította: 

- A "2019. őszi kiadás" mostantól "Windows Virtual Desktop (klasszikus), míg a "2020. tavaszi kiadás" már csak "Windows Virtual Desktop" További információt ebben a [blogbejegyzésben talál.](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/) 

Az új funkciókkal kapcsolatos további információkért tekintse meg ezt [a blogbejegyzést.](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245) 

### <a name="autoscaling-tool-update"></a>Automatikus skálázás eszköz frissítése

Általánosan elérhető az automatikus skálázás eszköz előzetes verziójának legújabb verziója. Ez az eszköz egy Azure Automation-fiók és az Azure Logic App használatával automatikusan leállít és újraindítja a munkamenetgazda virtuális gépeit (VM-eket) a gazdagépkészleten belül, csökkentve az infrastruktúra költségeit. További információ: [Munkamenetgazda-gazdagépek méretezése a Azure Automation.](set-up-scaling-script.md)

### <a name="azure-portal"></a>Azure Portal

Most már a következő lépéseket használhatja a Azure Portal a Windows Virtual Desktop: 

- Felhasználók közvetlen hozzárendelése személyes asztali munkamenetgazdékhoz  
- A gazdagépkészletek érvényesítési környezetének módosítása 

### <a name="diagnostics"></a>Diagnosztika

Új előre összeállított lekérdezéseket adtunk ki a Log Analytics-munkaterülethez. A lekérdezések eléréséhez válassza a Naplók **lehetőséget,** majd a **Kategória alatt** válassza **a** Windows Virtual Desktop. További információ: [A Log Analytics használata a diagnosztikai funkcióhoz.](diagnostics-log-analytics.md)

### <a name="update-for-remote-desktop-client-for-android"></a>Frissítés Távoli asztal Androidhoz készült ügyfélhez

Az [Távoli asztal Androidhoz készült ügyfél mostantól](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) támogatja a Windows Virtual Desktop kapcsolatokat. A 10.0.7-es verziótól kezdve az Android-ügyfél új felhasználói felületet biztosít a jobb felhasználói élmény érdekében. Az ügyfél az Android-eszközök Microsoft Authenticator is integrálható a feltételes hozzáférés engedélyezéséhez, amikor Windows Virtual Desktop munkaterületre.  

Az ügyfél korábbi Távoli asztal neve mostantól "Távoli asztal 8". Az ügyfél korábbi verziójában meglévő kapcsolatok zökkenőmentesen át lesznek adva az új ügyfélnek. Az új ügyfelet ugyanazra a mögöttes RDP-alapmotorra írják át, mint az iOS- és macOS-ügyfelek, így minden platformon gyorsabban oszlhatók meg az új funkciók. 

### <a name="teams-update"></a>Teams-frissítés

Továbbfejlesztjük a Microsoft Teams for Windows Virtual Desktop. Ami a legfontosabb, Windows Virtual Desktop mostantól támogatja a hang- és videóoptimalizálást a Windows asztali ügyfélhez. Az átirányítás azáltal javítja a késést, hogy közvetlen útvonalakat hoz létre a felhasználók között, amikor hang- vagy videóhívásokat és -értekezleteket használnak. A kisebb távolság kevesebb ugrást jelent, ami a hívások megjelenését és zökkenőmentesebb hangját teszi lehetővé. További információ: [A Teams használata a Windows Virtual Desktop.](teams-on-wvd.md)

## <a name="june-2020"></a>2020. június

A múlt hónapban bemutattunk egy Windows Virtual Desktop előzetes Azure Resource Manager integrációval. Ez a frissítés rengeteg izgalmas új funkciót tartalmaz, amelyekről szívesen szívesen segítünk. A jelenlegi verzió újdonsága a Windows Virtual Desktop.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Windows Virtual Desktop már integrálva van a Azure Resource Manager

Windows Virtual Desktop már integrálva van a Azure Resource Manager. A legújabb frissítésben az összes Windows Virtual Desktop erőforrás Azure Resource Manager meg. Ez a frissítés az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) is integrálva van. További [információ: Mi Azure Resource Manager?](../azure-resource-manager/management/overview.md)

A változás a következőt teszi önért:

- Windows Virtual Desktop már integrálva van a Azure Portal. Ez azt jelenti, hogy mindent közvetlenül a portálon kezelhet, nincs szükség PowerShellre, webalkalmazásra vagy harmadik féltől származó eszközökre. Első lépésekért tekintse meg a Gazdagépkészlet létrehozása [a](create-host-pools-azure-marketplace.md)következővel: Azure Portal.

- A frissítés előtt csak az egyes felhasználók számára lehetett RemoteApps- és Asztali alkalmazásokat közzétenni. A Azure Resource Manager most már közzéteheti az erőforrásokat a Azure Active Directory számára.

- Az alkalmazás korábbi Windows Virtual Desktop négy beépített rendszergazdai szerepköre volt, amelyek hozzárendelhetőek egy bérlőhöz vagy gazdagépkészlethez. Ezek a szerepkörök mostantól az [Azure szerepköralapú hozzáférés-vezérlésében (Azure RBAC) vannak.](../role-based-access-control/overview.md) Ezeket a szerepköröket minden Windows Virtual Desktop Azure Resource Manager objektumra alkalmazhatja, így teljes, gazdag delegálás modellel rendelkezik.

- Ebben a frissítésben már nem kell ismételten futtatnia a Azure Marketplace vagy a GitHub-sablont egy gazdagépkészlet kibontásához. A gazdagépkészlet kibontásához elég a gazdagépkészletet a Azure Portal és a **+** Hozzáadás lehetőséget választani további munkamenetgazdék üzembe helyezéséhez.

- A gazdagépkészlet üzembe helyezése mostantól teljesen integrálva van az [Azure Shared Image Gallery.](../virtual-machines/shared-image-galleries.md) Shared Image Gallery egy különálló Azure-szolgáltatás, amely a virtuális gép (VM) rendszerkép-definícióit tárolja, beleértve a rendszerkép verziószámozását is. A globális replikációval átmásolhatja és elküldheti a rendszerképeket más Azure-régiókba helyi üzembe helyezésre.

- A korábban a PowerShellen vagy a Diagnostics Service-webalkalmazáson keresztül végzett figyelési függvények mostantól átkerültek a Log Analyticsbe a Azure Portal. Most már két lehetőség is van a jelentések megjelenítésére. Kusto-lekérdezéseket futtathat, és a Munkafüzetek használatával vizualizációs jelentéseket hozhat létre.

- A továbbiakban nem kell megadnia a Azure Active Directory (Azure AD) beleegyezését a Windows Virtual Desktop. Ebben a frissítésben az Azure-előfizetés Azure AD-bérlője hitelesíti a felhasználókat, és Azure RBAC-vezérlőket biztosít a rendszergazdák számára.

### <a name="powershell-support"></a>PowerShell-támogatás

Ezzel a frissítéssel új AzWvd-parancsmagokat adtunk hozzá Azure PowerShell Az modulhoz. Ezt az új modult támogatja a PowerShell Core, amely .NET Core-on fut.

A modul telepítéséhez kövesse A [PowerShell-modul](powershell-module.md)beállítása az Windows Virtual Desktop.

Az elérhető parancsok listáját az [AzWvd PowerShell-referenciában is láthatja.](/powershell/module/az.desktopvirtualization/#desktopvirtualization)

Az új funkciókkal kapcsolatos további információkért tekintse meg [a blogbejegyzésünket.](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245)

### <a name="additional-gateways"></a>További átjárók

Új átjárófürtöt adtunk hozzá Dél-Afrikai Köztársaságban a kapcsolat késésének csökkentése érdekében.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams a Windows Virtual Desktop (előzetes verzió)

A Microsoft Teams for Windows Virtual Desktop. Ami a legfontosabb, Windows Virtual Desktop már támogatja a hívások hang- és vizuális átirányítását. Az átirányítás azáltal javítja a késést, hogy közvetlen útvonalakat hoz létre a felhasználók között, amikor hang- vagy videó használatával hívják meg őket. A kisebb távolság kevesebb ugrást jelent, ami a hívások megjelenését és zökkenőmentesebb hangját teszi lehetővé.

További információt a [blogbejegyzésben talál.](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)

## <a name="next-steps"></a>Következő lépések

A jövőbeli tervekről a következő [ütemtervben Microsoft 365 Windows Virtual Desktop olvashat.](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop)
