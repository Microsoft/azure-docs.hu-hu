---
title: A Windows rendszerű virtuális asztal újdonságai – Azure
description: Új szolgáltatások és Termékfrissítések a Windows rendszerű virtuális asztali gépekhez.
author: Heidilohr
ms.topic: overview
ms.date: 02/23/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: lizross
ms.custom: references_regions
ms.openlocfilehash: c0b4ad17c5aefe29a313b9eaf75fc87ce4d88720
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499883"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>A Windows rendszerű virtuális asztal újdonságai

A Windows rendszerű virtuális asztali frissítések rendszeres időközönként. Ebből a cikkből megismerheti a következőket:

- A legújabb frissítések
- Új funkciók
- A meglévő funkciók fejlesztése
- Hibajavítások

Ez a cikk havonta frissül. Ügyeljen arra, hogy az új frissítések megtartása érdekében gyakran térjen vissza ide.

## <a name="client-updates"></a>Ügyfelek frissítései

Tekintse meg ezeket a cikkeket a Windows rendszerű virtuális asztali és Távoli asztali szolgáltatások-ügyfelek frissítéseinek megismeréséhez:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="fslogix-updates"></a>FSLogix-frissítések

Kíváncsi a FSLogix legújabb frissítéseire? Tekintse [meg a FSLogix újdonságait](/fslogix/whats-new).

## <a name="february-2021"></a>2021. február

A következőképpen módosult a február 2021.

### <a name="portal-experience"></a>Portal-felület

Az alábbi módokon javítottuk a Azure Portal élményt:

- Tömeges kiürítési mód a gazdagépeken a munkamenet-gazdagép rács lapján. 
- A MSIX-alkalmazás csatolása mostantól elérhető nyilvános előzetes verzióként.
- Rögzített gazdagép-készlet – áttekintési információ sötét üzemmódhoz.

### <a name="eu-metadata-storage-now-in-public-preview"></a>Nyilvános előzetes verzióban elérhető az EU metaadat-tárolója

Most az Európa (EU) Földrajzának nyilvános előzetesét üzemeltetjük tárolási lehetőségként a Windows rendszerű virtuális asztali szolgáltatás metaadatainak számára. Az ügyfelek a szolgáltatásbeli objektumaik létrehozásakor választhatnak Nyugat-vagy Észak-Európában. A gazdagépekhez tartozó szolgáltatási objektumokat és metaadatokat az egyes régiókkal társított Azure-földrajz fogja tárolni. További információért olvassa el [a nyilvános előzetes verziót bejelentő blogbejegyzést](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939).

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>Csapatok a Windows rendszerű virtuális asztali beépülő modul frissítésein

Továbbfejlesztettük a videohívás minőségét a Windows rendszerű virtuális asztali beépülő modulban a leggyakrabban jelentett problémák megoldásával, például ha a képernyő hirtelen sötét vagy a videó és hang deszinkronizálva lesz. Ezek a tökéletesítések az egyvideós nézet teljesítményét növelhetik az aktív hangszórók váltásával. Olyan problémát is kijavítunk, amelyben a speciális karaktereket tartalmazó hardvereszközök nem voltak elérhetők a csapatokban.

## <a name="january-2021"></a>2021. január

A következők módosultak a januári 2021-ben:

### <a name="new-windows-virtual-desktop-offer"></a>Új Windowsos virtuális asztali ajánlat

Az új ügyfelek 30 százalékot takarítanak meg a Windows rendszerű virtuális asztali számítási költségeknél a D sorozat és a BS sorozatú virtuális gépek számára, akár 90 napig, a natív Microsoft-megoldás használatakor. Ezt az ajánlatot a Azure Portal 2021. március 31. előtt is beválthatja. További információt a [Windows rendszerű virtuális asztali ajánlatról szóló oldalon](https://azure.microsoft.com/services/virtual-desktop/offer/)olvashat.

### <a name="networksecuritygrouprules-value-change"></a>networkSecurityGroupRules értékének változása 

A Azure Resource Manager beágyazott sablonban a networkSecurityGroupRules alapértelmezett értékét módosították egy objektumból egy tömbre. Ez megakadályozza az esetleges hibákat, ha a managedDisks-customimagevm.js-t használja a networkSecurityGroupRules értékének megadása nélkül. Ez nem módosult, és visszamenőlegesen kompatibilis.

### <a name="fslogix-hotfix-update"></a>FSLogix gyorsjavítás frissítése

Megjelent a FSLogix, a 2009-es verzió HF_01 (2.9.7654.46150) az előző kiadásban felmerülő problémák megoldásához (2.9.7621.30127). Javasoljuk, hogy a lehető leghamarabb állítsa le az előző verziót, és frissítse a FSLogix.

További információkért tekintse [meg a FSLogix újdonságai](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150)című cikk kibocsátási megjegyzéseit.

### <a name="azure-portal-experience-improvements"></a>Azure Portal a tapasztalatok fejlesztése

A Azure Portal élményét a következő továbbfejlesztettük:

- Mostantól közvetlenül is hozzáadhat helyi virtuálisgép-rendszergazdai hitelesítő adatokat ahelyett, hogy hozzá kellene adnia egy helyi fiókot, amely a Active Directory tartományhoz való csatlakozás fiókjának hitelesítő adataival lett létrehozva.
- A felhasználók mostantól külön lapokon is felsorolják az egyéni és a csoportos hozzárendeléseket az egyes felhasználók és csoportok számára.
- A Windows rendszerű virtuális asztali ügynök verziószáma már látható a gazdagép-készletek virtuális gépek áttekintésében.
- Tömeges törlés lett hozzáadva a gazdagép-készletekhez és az alkalmazás-csoportokhoz.
- Mostantól engedélyezheti vagy letilthatja a kiürítési módot több munkamenet-gazdagép számára a gazdagép-készletekben.
- Eltávolította a nyilvános IP-címet a virtuális gép részletei lapon.

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>Windows rendszerű virtuális asztali ügynök hibaelhárítása

Nemrég állítottuk be a [Windows rendszerű virtuális asztali ügynök hibaelhárítási útmutatóját](troubleshoot-agent.md) , amely segítséget nyújt az olyan ügyfelek számára, akik gyakori problémákat észleltek.

### <a name="microsoft-defender-for-endpoint-integration"></a>Microsoft Defender az Endpoint Integration szolgáltatáshoz

Mostantól általánosan elérhető a Microsoft Defender az Endpoint Integration szolgáltatáshoz. Ez a funkció lehetővé teszi, hogy a Windows rendszerű virtuális asztali gépek ugyanazzal a vizsgálattal szolgálják a helyi Windows 10-es gépeket. Ha a Windows 10 Enterprise több munkamenetet használja, a Microsoft Defender for Endpoint akár 50 egyidejű felhasználói kapcsolatot is támogat, így megtakaríthatja a Windows 10 Enterprise többmunkamenetes és a Microsoft Defender for Endpoint megbízhatóságát. További információkért tekintse meg a [blogbejegyzésben](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712).

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Azure biztonsági alapkonfiguráció a Windows rendszerű virtuális asztali gépekhez

Nemrég közzétettünk egy cikket a Windows rendszerű virtuális asztali környezethez készült [Azure biztonsági alaptervről](security-baseline.md) , amelyről fel szeretné hívni a figyelmét. Ezek az irányelvek az Azure biztonsági teljesítményteszt 2,0-es verzióról a Windows rendszerű virtuális asztalra való alkalmazásával kapcsolatos információkat tartalmaznak. Az Azure biztonsági teljesítményteszt ismerteti azokat a beállításokat és gyakorlatokat, amelyeket az Azure-beli felhőalapú megoldások biztonságossá tételéhez használ.

## <a name="december-2020"></a>2020. december

A következőképpen módosult a 2020 decemberében: 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Azure Monitor a Windows rendszerű virtuális asztalhoz

A Windows rendszerű virtuális asztali Azure Monitor nyilvános előzetes verziója már elérhető. Ez az új szolgáltatás olyan robusztus irányítópultot tartalmaz, amely Azure Monitor munkafüzetekre épül, így az informatikai szakemberek megismerhetik a Windows rendszerű virtuális asztali környezeteket. További részletekért tekintse [meg a blogon bejelentést](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587) . 

### <a name="azure-resource-manager-template-change"></a>Azure Resource Manager sablon módosítása 

A legújabb frissítés során a rendszer eltávolította az összes nyilvános IP-cím paramétert a Azure Resource Manager sablonból a gazdagépek létrehozásához és üzembe helyezéséhez. Javasoljuk, hogy ne használjon nyilvános IP-címeket a Windows rendszerű virtuális asztali környezet számára a központi telepítés biztonságának megőrzése érdekében. Ha a központi telepítés nyilvános IP-címekre támaszkodik, akkor újra kell konfigurálnia a magánhálózati IP-címek használatára, ellenkező esetben a telepítés nem fog megfelelően működni.

### <a name="msix-app-attach-public-preview"></a>Nyilvános előzetes MSIX alkalmazás 

A MSIX-alkalmazás csatolása egy másik szolgáltatás, amely ebben a hónapban megkezdte a nyilvános előzetes verziót. A MSIX-alkalmazás csatolása egy olyan szolgáltatás, amely dinamikusan mutatja be a MSIX-alkalmazásokat a Windows rendszerű virtuális asztali munkamenetgazda-alapú virtuális gépek számára. További részletekért tekintse [meg a blogon bejelentést](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231) . 

### <a name="screen-capture-protection"></a>Képernyőfelvételek védelme 

Ebben a hónapban a képernyőfelvételek védelméhez a nyilvános előzetes verzió elején is meg lett jelölve. Ezzel a szolgáltatással megakadályozhatja a bizalmas adatok rögzítését az ügyfél-végpontokon. A képernyőfelvételek védelmének megadásához nyissa meg [ezt a lapot](https://aka.ms/WVDScreenCaptureProtection).  

### <a name="built-in-roles"></a>Beépített szerepkörök

Új beépített szerepköröket adtunk hozzá a Windows virtuális asztal rendszergazdai engedélyeihez. További információ: [beépített szerepkörök a Windows rendszerű virtuális asztali gépekhez](rbac.md). 

### <a name="application-group-limit-increase"></a>Application Group-korlát növekedése

Megnövelte az alapértelmezett alkalmazáscsoport-korlátot Azure Active Directory bérlőről 200 csoportra.

### <a name="client-updates-for-december-2020"></a>Ügyfelek frissítései december 2020

A következő ügyfelek új verzióit adtuk ki: 

- Android
- macOS
- Windows

Az ügyfelek frissítéseivel kapcsolatos további információkért lásd: [ügyfelek frissítései](whats-new.md#client-updates).

## <a name="november-2020"></a>2020. november

### <a name="azure-portal-experience"></a>Azure Portali élmény

Két hibát javítottunk a Azure Portal felhasználói élményben:

- Az asztali alkalmazás rövid neve már nem íródik felül a "virtuális gép hozzáadása" munkafolyamatban.
- A munkamenet-gazdagép lap ekkor betöltődik, ha a munkamenet-gazdagépek a méretezési csoportok részét képezik.

### <a name="fslogix-client-version-2009"></a>FSLogix-ügyfél, 2009-es verzió 

Megjelent a FSLogix-ügyfél új verziója számos javítással és javítással. További információt a [blogbejegyzésben](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix)talál.

### <a name="rdp-shortpath-public-preview"></a>RDP Shortpath nyilvános előzetes verziója

Az RDP-Shortpath pont – hely és helyek közötti VPN-EK és ExpressRoute segítségével közvetlen kapcsolatot vezet be a Windows rendszerű virtuális asztali munkamenetgazda-munkamenethez. Emellett bemutatja a URCP átviteli protokollt is. Az RDP-Shortpath célja, hogy csökkentse a késést és a hálózati ugrásokat a felhasználói élmény javítása érdekében. További információ: [Windows Virtual Desktop RDP Shortpath](shortpath.md).

### <a name="azdesktopvirtualization-version-201"></a>Az az. DesktopVirtualization, 2.0.1-es verzió

Megjelent a Windows rendszerű virtuális asztali parancsmagok 2.0.1-es verziója. Ez a frissítés olyan parancsmagokat tartalmaz, amelyek lehetővé teszik a MSIX-alkalmazás csatolásának kezelését. Az új verziót [a PowerShell-galériában](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1)töltheti le.

### <a name="azure-advisor-updates"></a>Azure Advisor frissítések

Azure Advisor most már rendelkezik egy új javaslattal a Windows virtuális asztal közelségi útmutatójában, valamint egy új ajánlást a teljesítmény optimalizálásához az első terheléselosztású gazdagép-készletekben. További információ [Az Azure-webhelyről](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/).

## <a name="october-2020"></a>2020. október

A következők módosultak a 2020 októberében:

### <a name="improved-performance"></a>Jobb teljesítmény

- A következő Azure-földrajzi területeken a kapcsolatok késésének csökkentésével optimalizáltuk a teljesítményt:
    - Svájc
    - Kanada

Most már használhatja a [Experience kalkulátort](https://azure.microsoft.com/services/virtual-desktop/assessment/) a felhasználói élmény minőségének becsléséhez ezekben a területeken.

### <a name="azure-government-cloud-availability"></a>A felhő rendelkezésre állásának Azure Government

A Azure Government-felhő már általánosan elérhető. További információt a [blogbejegyzésben](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/)talál.

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Windows rendszerű virtuális asztali Azure Portal frissítései

Készítettünk néhány frissítést a Windows rendszerű virtuális asztali Azure Portal:

- Kijavítva egy resourceID hiba, amely megakadályozta, hogy a felhasználók megnyissák a "munkamenetek" lapot.
- Egyszerűsítheti a felhasználói felületet a "munkamenet-gazdagépek" lapon.
- Az RDP-tulajdonságok területen az "Alapértelmezések", a "használhatóság" és az "alapértékek visszaállítása" beállítások lettek kijavítva.
- A "Remove" és a "Delete" függvények az összes lapon konzisztensek.
- A portál mostantól érvényesíti az alkalmazások nevét az "alkalmazás hozzáadása" munkafolyamatban.
- Kijavítva a probléma, hogy a munkamenet-gazdagép exportálási adatértékei nem voltak igazítva az oszlopokban.
- Kijavított egy hibát, amelyben a portál nem tudta beolvasni a felhasználói munkameneteket.
- Kijavítottuk a munkamenet-gazdagép lekérésének hibáját, amely akkor történt, amikor a virtuális gép egy másik erőforráscsoporthoz lett létrehozva.
- Frissítette a "munkamenet-gazdagép" fület az aktív és a Leválasztott munkamenetek listázásához.
- Az "alkalmazások" lapon már vannak lapok.
- Kijavított egy hibát, amelyben a "parancssor szükséges" szöveg nem jelenik meg megfelelően az "alkalmazások listája" lapon.
- Javítva a problémát, ha a portál nem tudta üzembe helyezni a gazdagép-készleteket vagy virtuális gépeket, miközben a megosztott képgyűjtemény német nyelvű verzióját használja.

### <a name="client-updates-for-october-2020"></a>Ügyfelek frissítései október 2020

Megjelent az ügyfelek új verziói. További információt a következő cikkekben talál:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

További információ a többi ügyfélről: [ügyfelek frissítései](#client-updates).

## <a name="september-2020"></a>2020. szeptember

A következőképpen módosult a 2020 szeptemberében:

- A következő Azure-földrajzi területeken a kapcsolatok késésének csökkentésével optimalizáltuk a teljesítményt:
    - Németország
    - Dél-Afrika (csak ellenőrzési környezetek esetén)

Most már használhatja a [Experience kalkulátort](https://azure.microsoft.com/services/virtual-desktop/assessment/) a felhasználói élmény minőségének becsléséhez ezekben a területeken.

- Megjelent a Windows rendszerű virtuális asztali Windows asztali ügyfél 1.2.1364 verziója. Ebben a frissítésben a következő módosításokat hajtottuk végre:
    - Kijavított egy problémát, amelyben az egyszeri bejelentkezés (SSO) nem működött a Windows 7 rendszeren.
    - Kijavított egy problémát, amely miatt az ügyfél leválasztott egy olyan felhasználót, aki engedélyezte a csapatok számára a média optimalizálását, vagy csatlakozhat egy Teams-értekezlethez, míg egy másik alkalmazás kizárólagos módban nyitott meg egy hangadatfolyamot.
    - Kijavított egy problémát, amelyben a csapatok nem észleltek hang-és video-eszközöket, amikor engedélyezve lett a csapatok multimédia-optimalizálása.
    - "Segítségre van szüksége a beállításokhoz?" az asztali beállítások lapra mutató hivatkozás.
    - A nagy kontrasztú sötét témák használatakor megjelenő "subscribe" (előfizetés) gombra vonatkozó probléma kijavítva.
    
- A felhasználók óriási segítségének köszönhetően javítottunk két kritikus problémát a Microsoft Store Távoli asztal-ügyfél számára. Folytatjuk a visszajelzések áttekintését és a problémák megoldását, mivel az ügyfél szakaszos kiadását bővítjük világszerte több felhasználó számára.
    
- Új funkciót adtunk hozzá, amely lehetővé teszi a virtuális gép helyének, rendszerképének, erőforráscsoportának, előtagjának és hálózati konfigurációjának módosítását a munkafolyamatok részeként, hogy a virtuális gépet hozzáadja a központi telepítéshez a Azure Portal.

- Az informatikai szakemberek mostantól a Microsoft Endpoint Manager használatával kezelhetik a hibrid Azure Active Directory csatlakoztatott Windows 10 Enterprise rendszerű virtuális gépeket. További információt [a blogbejegyzésben](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048)talál.

## <a name="august-2020"></a>2020. augusztus

A következőképpen módosult a 2020 augusztusában:

- Továbbfejlesztettük a teljesítményt a kapcsolódási késés csökkentése érdekében a következő Azure-régiókban: 

    - Egyesült Királyság
    - Franciaország
    - Norvégia
    - Dél-Korea

   Az [Experience kalkulátor](https://azure.microsoft.com/services/virtual-desktop/assessment/) használatával általános képet kaphat arról, hogy ezek a változások milyen hatással lesznek a felhasználókra.

- A Microsoft Store Távoli asztal ügyfél (v 10.2.1522 +) mostantól általánosan elérhető! A Microsoft Store Távoli asztal ügyfél ezen verziója kompatibilis a Windows Virtual Desktop szolgáltatással. A felhasználói élmény fokozása céljából a frissített felhasználói FELÜLETi folyamatokat is bevezetjük. Ez a frissítés magában foglalja a folyékony kialakítást, a világos és a sötét üzemmódot, valamint számos más izgalmas változást. A-ügyfelet az iOS-, macOS-és Android-ügyfelekhez is ugyanezen alapul szolgáló Remote Desktop Protocol (RDP) motor használatára is átírta. Ez lehetővé teszi, hogy az összes platformon gyorsabban elérhetővé tegyük az új funkciókat. [Töltse le az ügyfelet](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) , és próbálja ki!

- Kijavítottunk egy problémát a csapatok asztali ügyfelének (1.3.00.21759-verzió), ahol az ügyfél csak az UTC időzónát mutatta a csevegésben, a csatornákban és a naptárban. A frissített ügyfél most már megjeleníti a távoli munkamenet időzónáját.

- Azure Advisor mostantól a Windows rendszerű virtuális asztal része. Ha a Azure Portalon keresztül fér hozzá a Windows rendszerű virtuális asztalhoz, a Windows rendszerű virtuális asztali környezet optimalizálására vonatkozó javaslatok láthatók. További információ: [Azure Advisor](azure-advisor.md).

- Az Azure CLI mostantól támogatja a Windows rendszerű virtuális asztal () használatát a `az desktopvirtualization` Windows rendszerű virtuális asztali környezetek automatizálásának elősegítése érdekében. Tekintse meg a [desktopvirtualization](/cli/azure/ext/desktopvirtualization/) listáját a kiterjesztési parancsok listájához.

- Frissítettük a központi telepítési sablonokat, hogy teljes mértékben kompatibilisek legyenek a Windows rendszerű virtuális asztali Azure Resource Manager felületekkel. A sablonok a [githubon](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates)találhatók.

- A Windows rendszerű virtuális asztali US Gov portál már nyilvános előzetes verzióban érhető el. További információkért tekintse meg [a bejelentést](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/).

## <a name="july-2020"></a>2020. július  

Július volt, amikor általánosan elérhetővé vált a Windows virtuális asztal az Azure Resource Management-integrációval.

Az új kiadás változása: 

- A "Fall 2019 Release" néven ismert "Windows virtuális asztal (klasszikus)", míg a "Spring 2020 kiadás" most csak "Windows virtuális asztal". További információkért tekintse meg [ezt a blogbejegyzést](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Ha többet szeretne megtudni az új funkciókról, tekintse meg [ezt a blogbejegyzést](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="autoscaling-tool-update"></a>Automatikus skálázási eszköz frissítése

Az előzetes verzióban már általánosan elérhető az automatikus skálázási eszköz legújabb verziója. Ez az eszköz egy Azure Automation-fiók és az Azure Logic App használatával automatikusan leállítja és újraindítja a munkamenetgazda virtuális gépeket a gazdagépen belül, így csökkentve az infrastrukturális költségeket. További információ: [Azure Automation használatával méretezhető munkamenet-gazdagépek](set-up-scaling-script.md).

### <a name="azure-portal"></a>Azure Portal

Mostantól a következő műveleteket végezheti el a Windows rendszerű virtuális asztali Azure Portalban: 

- Felhasználók közvetlen kiosztása a személyes asztali munkamenet-gazdagépekhez  
- Alkalmazáskészletek érvényesítési környezete beállításának módosítása 

### <a name="diagnostics"></a>Diagnosztika

Megjelent néhány új előre elkészített lekérdezés a Log Analytics munkaterülethez. A lekérdezések eléréséhez nyissa meg a **naplók** elemet, és a **Kategória** területen válassza a **Windows virtuális asztal** lehetőséget. További információ [a diagnosztikai szolgáltatás log Analytics használatáról](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Az Android rendszerhez készült Távoli asztal-ügyfél frissítése

Az [Androidhoz készült Távoli asztal-ügyfél](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) mostantól támogatja a Windows rendszerű virtuális asztali kapcsolatokat. A 10.0.7 verziótól kezdődően az Android-ügyfél egy új felhasználói FELÜLETET kínál a jobb felhasználói élmény érdekében. Az ügyfél a Windows rendszerű virtuális asztali munkaterületekre való feliratkozáskor az Android-eszközökön Microsoft Authenticator is integrálva van.  

A Távoli asztal-ügyfél előző verziója már "Távoli asztal 8" néven is ismert. Az ügyfél korábbi verziójában meglévő kapcsolatok zökkenőmentesen lesznek továbbítva az új ügyfélhez. Az új ügyfél ugyanarra a mögöttes RDP alapmotorra lett írva, mint az iOS-és macOS-ügyfelek, az új funkciók gyorsabb kiadása minden platformon. 

### <a name="teams-update"></a>Csapatok frissítése

Javítottuk a Microsoft Teams for Windows virtuális asztali verzióját. Ami a legfontosabb, hogy a Windows rendszerű virtuális asztal mostantól támogatja a Windows asztali ügyfél hang-és video-optimalizálását. Az átirányítás javítja a késést azáltal, hogy közvetlen elérési utakat hoz létre a felhasználók között, ha hang-vagy videohívásokat használ a hívások és értekezletek A kisebb távolság kevesebb ugrást jelent, így a hívások megjelenése és a hang gördülékenyebb. További információt a [Windows rendszerű virtuális asztal használatáról szóló csapatokban](teams-on-wvd.md)olvashat.

## <a name="june-2020"></a>2020. június

A múlt hónapban bevezetjük a Windows rendszerű virtuális asztalt az előzetes verzióban Azure Resource Manager integrációval. Ez a frissítés számos izgalmas új funkciót kínál, melyekről szívesen tájékoztatjuk Önt. A Windows rendszerű virtuális asztal ezen verziójának újdonságai.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>A Windows rendszerű virtuális asztal már integrálva van Azure Resource Manager

A Windows rendszerű virtuális asztal már integrálva van Azure Resource Managerba. A legújabb frissítés során a Windows rendszerű virtuális asztali objektumok mostantól Azure Resource Manager erőforrásai. Ez a frissítés az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) is integrálva van. További információ: [What is Azure Resource Manager?](../azure-resource-manager/management/overview.md) .

Ezt a változást a következőképpen teheti meg:

- A Windows rendszerű virtuális asztal már integrálva van a Azure Portal. Ez azt jelenti, hogy közvetlenül a portálon, a PowerShell, a webalkalmazások és a harmadik féltől származó eszközök nélkül is kezelhet mindent. Az első lépésekhez tekintse meg a [Host Pool létrehozása a Azure Portal](create-host-pools-azure-marketplace.md)használatával című oktatóanyagot.

- A frissítés előtt csak a RemoteApp-és asztali számítógépeket teheti közzé az egyes felhasználók számára. A Azure Resource Manager segítségével mostantól Azure Active Directory csoportokba teheti közzé az erőforrásokat.

- A Windows rendszerű virtuális asztal korábbi verziója négy beépített rendszergazdai szerepkörrel rendelkezik, amelyeket hozzárendelhet egy bérlőhöz vagy egy gazdagéphez. Ezek a szerepkörök már [Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC)](../role-based-access-control/overview.md)rendelkeznek. Ezeket a szerepköröket minden Windows rendszerű virtuális asztali Azure Resource Manager objektumra alkalmazhatja, amely lehetővé teszi, hogy teljes körű, sokoldalú delegálási modellel rendelkezzen.

- Ebben a frissítésben már nincs szükség az Azure Marketplace vagy a GitHub-sablon ismételt futtatására a gazdagépek kibővítéséhez. A gazdagépek kibővítéséhez mindössze a Azure Portalban kell megnyitnia a gazdagépet, majd a **+ Hozzáadás** lehetőségre kattintva további munkamenet-gazdagépeket telepíthet.

- A gazdagép-készlet üzembe helyezése mostantól teljes mértékben integrálva van az [Azure megosztott rendszerkép](../virtual-machines/shared-image-galleries.md)-katalógusával. A megosztott képkatalógus egy különálló Azure-szolgáltatás, amely a virtuális gép (VM) képdefinícióit tárolja, beleértve a rendszerkép verziószámozását is. A globális replikálás használatával a rendszerképeket más Azure-régiókba is másolhatja és elküldheti a helyi telepítéshez.

- A PowerShellen vagy a diagnosztikai szolgáltatás webalkalmazásán keresztül elvégzendő figyelési függvények mostantól a Azure Portal Log Analyticsba kerültek. Mostantól két lehetőség közül választhat a jelentések megjelenítéséhez. Kusto-lekérdezéseket futtathat, és a munkafüzetek használatával vizuális jelentéseket hozhat létre.

- A Windows virtuális asztal használatához már nem szükséges Azure Active Directory (Azure AD) beleegyeznie. Ebben a frissítésben az Azure AD-bérlő az Azure-előfizetésében hitelesíti a felhasználókat, és biztosítja az Azure RBAC-vezérlőket a rendszergazdák számára.

### <a name="powershell-support"></a>PowerShell-támogatás

Új AzWvd-parancsmagokat adtunk hozzá a Azure PowerShell az modulhoz ezzel a frissítéssel. Ez az új modul támogatott a PowerShell Core-ban, amely a .NET Core-on fut.

A modul telepítéséhez kövesse a [PowerShell-modul beállítása a Windows rendszerű virtuális asztalhoz](powershell-module.md)című témakör utasításait.

Az elérhető parancsok listáját a [AzWvd PowerShell-referenciában](/powershell/module/az.desktopvirtualization/#desktopvirtualization)is megtekintheti.

Az új funkciókkal kapcsolatos további információkért tekintse meg a [blogbejegyzését](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>További átjárók

Új átjáró-fürtöt adtunk hozzá Dél-Afrikában a kapcsolatok késésének csökkentése érdekében.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams a Windows Virtual Desktopban (előzetes verzió)

Javítottunk a Microsoft Teams for Windows Virtual Desktop szolgáltatásban. Ami a legfontosabb, hogy a Windows virtuális asztal mostantól támogatja a hívások hang-és vizualizációs átirányítását. Az átirányítás úgy javítja a késést, hogy a felhasználók között közvetlen elérési utakat hoz létre a hang vagy a videó használatával. A kisebb távolság kevesebb ugrást jelent, így a hívások megjelenése és a hang gördülékenyebb.

További információt [a blogbejegyzésben](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/)talál.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a jövőbeli tervekkel a [Microsoft 365 Windows rendszerű virtuális asztali menetrendben](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).
