---
title: Ajánlott Windowsos virtuális asztali biztonsági eljárások – Azure
description: Ajánlott eljárások a Windows rendszerű virtuális asztali környezet biztonságának megőrzéséhez.
author: heidilohr
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cfc980fdabdb9c6e7085088db12754243f133d89
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581389"
---
# <a name="security-best-practices"></a>Ajánlott biztonsági eljárások

A Windows Virtual Desktop egy felügyelt virtuális asztali szolgáltatás, amely számos biztonsági képességgel rendelkezik a szervezet biztonságának megőrzéséhez. A Windows rendszerű virtuális asztali környezetben a Microsoft az ügyfél nevében kezeli a szolgáltatások részeit. A szolgáltatás számos beépített speciális biztonsági funkcióval rendelkezik, például a fordított kapcsolat lehetőséggel, amely csökkenti annak kockázatát, hogy a Távoli asztalok bárhonnan elérhetők legyenek.

Ez a cikk azokat a további lépéseket ismerteti, amelyekkel rendszergazdaként megtarthatja ügyfelei Windows rendszerű virtuális asztali környezetének biztonságát.

## <a name="security-responsibilities"></a>Biztonsági felelősségek

Miben különböznek a Felhőbeli szolgáltatások a hagyományos helyszíni virtuális asztali infrastruktúrától (VDIs), hogyan kezelik a biztonsági feladatokat. Egy hagyományos helyszíni VDI esetében például az ügyfél felelős a biztonság minden aspektusa tekintetében. A legtöbb felhőalapú szolgáltatásban azonban ezeket a feladatokat megosztják az ügyfél és a vállalat között.

Ha a Windows rendszerű virtuális asztalt használja, fontos tisztában lennie azzal, hogy noha egyes összetevők már biztonságosak a környezetében, más területeket is konfigurálnia kell, hogy illeszkedjenek a szervezet biztonsági igényeihez.

A Windows rendszerű virtuális asztali környezetében a következő biztonsági követelményeknek kell megfelelnie:

| Biztonsági igények | Az ügyfél felelős erre? |
|---------------|:-------------------------:|
|Identitás|Yes|
|Felhasználói eszközök (mobil és PC)|Yes|
|Alkalmazás biztonsága|Yes|
|Munkamenet-gazda operációs rendszer|Yes|
|Központi telepítés konfigurálása|Yes|
|Hálózati vezérlők|Yes|
|Virtualizációs vezérlő síkja|No|
|Fizikai gazdagépek|No|
|Fizikai hálózat|No|
|Fizikai adatközpont|No|

Az ügyfél nem felelős a Microsoft által kezelt biztonsági követelményeknek.

## <a name="azure-security-best-practices"></a>Az Azure ajánlott biztonsági eljárásai

A Windows Virtual Desktop egy Azure-beli szolgáltatás. A Windows rendszerű virtuális asztali környezet biztonságának maximalizálása érdekében ügyeljen arra, hogy a környező Azure-infrastruktúrát és a felügyeleti gépet is biztonságossá tegye. Az infrastruktúra biztonságossá tételéhez gondolja át, hogy a Windows virtuális asztal hogyan illeszkedik a nagyobb Azure-ökoszisztémához. További információ az Azure-beli ökoszisztémáról: az [Azure biztonsági eljárásai és mintái](../security/fundamentals/best-practices-and-patterns.md).

Ez a szakasz az Azure-ökoszisztéma biztonságossá tételére vonatkozó ajánlott eljárásokat ismerteti.

### <a name="enable-azure-security-center"></a>Azure Security Center engedélyezése

Javasoljuk, hogy engedélyezze Azure Security Center Standard előfizetéseket, a virtuális gépeket, a kulcstartókat és a Storage-fiókokat.

A Azure Security Center Standard csomaggal a következőket teheti:

* Biztonsági rések kezelése.
* A megfelelőség felmérése közös keretrendszerek, például a PCI esetében.
* Erősítse meg a környezete általános biztonságát.

További információ: Azure- [előfizetése Security Center Standard](../security-center/security-center-get-started.md).

### <a name="improve-your-secure-score"></a>A biztonsági pontszám javítása

A biztonságos pontszám ajánlásokat és gyakorlati tanácsokat tartalmaz a teljes biztonság javítására. Ezek a javaslatok prioritást élveznek a legfontosabb elemek kiválasztásához, és a gyors javítási lehetőségek segítenek a lehetséges sebezhetőségek gyors megoldásában. Ezek az ajánlások az idő múlásával is frissülnek, és naprakészen tartanak a környezet biztonságának legjobb módjaival. További információ: [a biztonságos pontszám javítása Azure Security Centerban](../security-center/secure-score-security-controls.md).

## <a name="windows-virtual-desktop-security-best-practices"></a>Ajánlott Windowsos virtuális asztali biztonsági eljárások

A Windows virtuális asztal számos beépített biztonsági vezérlővel rendelkezik. Ebben a szakaszban megismerheti azokat a biztonsági vezérlőket, amelyekkel megtarthatja a felhasználók és az adatvédelem biztonságát.

### <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelése

A többtényezős hitelesítés megkövetelése a Windows virtuális asztal minden felhasználója és rendszergazdája számára növeli a teljes telepítés biztonságát. További információ: [Az Azure AD multi-Factor Authentication engedélyezése a Windows rendszerű virtuális asztali gépekhez](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Feltételes hozzáférés engedélyezése

A [feltételes hozzáférés](../active-directory/conditional-access/overview.md) engedélyezése lehetővé teszi a kockázatok kezelését, mielőtt hozzáférést biztosít a felhasználóknak a Windows rendszerű virtuális asztali környezethez. Annak eldöntéséhez, hogy mely felhasználók férhetnek hozzá a szolgáltatáshoz, javasoljuk, hogy vegye figyelembe, hogy ki a felhasználó, hogyan jelentkeznek be és milyen eszközön használják.

### <a name="collect-audit-logs"></a>Naplók összegyűjtése

A naplózás engedélyezésével megtekintheti a Windows rendszerű virtuális asztallal kapcsolatos felhasználói és rendszergazdai tevékenységeket. Néhány példa a legfontosabb naplókra:

-   [Azure-tevékenységnapló](../azure-monitor/essentials/activity-log.md)
-   [Azure Active Directory tevékenység naplója](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Munkamenet-gazdagépek](../azure-monitor/agents/agent-windows.md)
-   [Windows rendszerű virtuális asztali diagnosztikai napló](../virtual-desktop/diagnostics-log-analytics.md)
-   [Naplók Key Vault](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>RemoteApps használata

Üzembe helyezési modell kiválasztásakor megadhatja a távoli felhasználók számára a teljes virtuális asztalok elérését, vagy csak alkalmazásokat választhat. A távoli alkalmazások vagy RemoteApp-alkalmazások zökkenőmentes felhasználói élményt nyújtanak, mivel a felhasználó a virtuális asztalán futó alkalmazásokkal működik. A RemoteApp szolgáltatás csak akkor csökkenti a kockázatot, ha a felhasználó az alkalmazás által elérhetővé tett távoli gép egy részhalmazával dolgozik.

### <a name="monitor-usage-with-azure-monitor"></a>Használat figyelése Azure Monitor

A Windows rendszerű virtuális asztali szolgáltatások használatának és rendelkezésre állásának figyelése [Azure monitor](https://azure.microsoft.com/services/monitor/)használatával. Vegye fontolóra a [szolgáltatás-állapottal kapcsolatos riasztások](../service-health/alerts-activity-log-service-notifications-portal.md) létrehozását a Windows Virtual Desktop szolgáltatás számára, hogy értesítést kapjon, amikor valamilyen szolgáltatás hatással van egy eseményre.

## <a name="session-host-security-best-practices"></a>A munkamenet-gazdagép biztonsági eljárásai – ajánlott eljárások

A munkamenet-gazdagépek olyan virtuális gépek, amelyek az Azure-előfizetésben és a virtuális hálózaton belül futnak. A Windows rendszerű virtuális asztali környezet általános biztonsága a munkamenet-gazdagépekre helyezett biztonsági vezérlőktől függ. Ez a szakasz az ajánlott eljárásokat ismerteti a munkamenet-gazdagépek biztonságának megőrzése érdekében.

### <a name="enable-screen-capture-protection-preview"></a>Képernyőfelvétel-védelem engedélyezése (előzetes verzió)

A képernyőfelvételek védelme funkció megakadályozza a bizalmas adatok rögzítését az ügyfél-végpontokon. Ha engedélyezi ezt a funkciót, a rendszer automatikusan letiltja vagy elrejti a távoli tartalmat a képernyőképek és a képernyők megosztásában. A rendszer olyan kártékony szoftverekből is el fogja rejteni, amelyek folyamatosan rögzítik a képernyő tartalmát. Javasoljuk, hogy tiltsa le a vágólap-átirányítást, hogy megakadályozza a távoli tartalomnak a végpontokra való másolását a funkció használata közben.

Ezt a házirendet a gazdagép szintjén kell kikényszeríteni egy beállításkulcs konfigurálásával. A szabályzat engedélyezéséhez nyissa meg a PowerShellt, és állítsa be a **fEnableScreenCaptureProtection** beállításkulcsot a következő parancsmag futtatásával:

```powershell
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableScreenCaptureProtection /t REG_DWORD /d 1
```

Az új szolgáltatás tesztelése:

- Győződjön meg arról, hogy a gazdagép-készletek az ellenőrzési környezetben vannak kiépítve.
- Győződjön meg arról, hogy letöltötte és telepítette a Windows asztali ügyfelet (1.2.1526) vagy újabb verziót.

>[!NOTE]
>Az előzetes verzióban csak a Windows 10-es végpontok teljes asztali kapcsolatai támogatják ezt a funkciót.


### <a name="enable-endpoint-protection"></a>Endpoint Protection engedélyezése

Az ismert kártevő szoftverektől való üzembe helyezés védelme érdekében javasoljuk, hogy engedélyezze az Endpoint Protectiont az összes munkamenet-gazdagépen. Használhatja a Windows Defender víruskeresőt vagy egy harmadik féltől származó programot. További információ: a [Windows Defender víruskereső üzembe helyezési útmutatója VDI-környezetben](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus).

A VHD-fájlokat csatlakoztató FSLogix vagy más megoldások esetében ajánlott a VHD-fájlkiterjesztés kizárása.

### <a name="install-an-endpoint-detection-and-response-product"></a>Végpont-észlelési és-válasz termék telepítése

Javasoljuk, hogy egy végpont-észlelési és-gyorsreagáló (EDR) terméket telepítsen a speciális észlelési és reagálási képességek biztosításához. A [Azure Security Center](../security-center/security-center-services.md) engedélyező kiszolgálói operációs rendszerek esetén a EDR termék telepítése a Defender ATP-t telepíti. Ügyféloldali operációs rendszerek esetén a [DEFENDER ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) -t vagy egy harmadik féltől származó terméket is telepíthet ezekre a végpontokra.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>A veszélyforrások és a sebezhetőségek kezelésével kapcsolatos értékelések engedélyezése

Az operációs rendszerekben és alkalmazásokban létező szoftveres biztonsági rések azonosítása elengedhetetlen a környezet biztonságossá tételéhez. A Azure Security Center a kiszolgálói operációs rendszerekkel kapcsolatos sebezhetőségi felmérések segítségével azonosíthatja a problémás helyeket. Használhatja a Defender ATP-t is, amely fenyegetést és sebezhetőségi kezelést biztosít az asztali operációs rendszerekhez. A harmadik féltől származó termékeket is használhatja, ha úgy döntött, de javasoljuk, hogy a Azure Security Center és a Defender ATP szolgáltatást használja.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Javítási szoftverek biztonsági rései a környezetben

Miután azonosította a biztonsági rést, ki kell javítania. Ez a virtuális környezetekre is vonatkozik, amely magában foglalja a futó operációs rendszereket, a rajtuk telepített alkalmazásokat, valamint azokat a lemezképeket, amelyekről új gépeket hoz létre. Kövesse a gyártói javítások értesítési kommunikációját, és végezze el a javítások időben történő alkalmazását. Javasoljuk, hogy a kiinduló lemezképek havi javítását, hogy az újonnan telepített gépek a lehető legbiztonságosabbak legyenek.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Maximális inaktív idő és leválasztási házirendek létrehozása

Ha az inaktív erőforrások megőrzik a felhasználókat, és nem engedélyezik a hozzáférést, nem engedélyezett felhasználók férhetnek hozzá. Javasoljuk, hogy az időtúllépések egyensúlyba kerüljön a felhasználói hatékonyságot és az erőforrás-használatot. Az állapot nélküli alkalmazásokkal kommunikáló felhasználók esetében érdemesebb olyan agresszív házirendeket használni, amelyek kikapcsolják a gépeket, és megőrzik az erőforrásokat. A hosszú ideig futó alkalmazások leválasztása, amelyek továbbra is futnak, ha a felhasználó inaktív, például szimuláció vagy CAD-megjelenítés, megszakíthatja a felhasználó munkáját, és a számítógép újraindítását is megkövetelheti.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Képernyőfelvételek beállítása üresjárati munkamenetekhez

A nem kívánt rendszer-hozzáférés megakadályozható úgy, hogy a Windows rendszerű virtuális asztalt úgy konfigurálja, hogy a gép képernyőjét üresjárati idő alatt zárolja, és a zárolás feloldásához hitelesítést igényel.

### <a name="establish-tiered-admin-access"></a>Többplatformos rendszergazdai hozzáférés létrehozása

Javasoljuk, hogy ne adja meg a felhasználók rendszergazdai hozzáférését a virtuális asztalokhoz. Ha szoftvercsomagok szükségesek, javasoljuk, hogy tegye elérhetővé azokat a Configuration Management Utilities, például a Microsoft Endpoint Manager használatával. Többmunkamenetes környezetben javasoljuk, hogy ne telepítse közvetlenül a felhasználókat a szoftver telepítésére.

### <a name="consider-which-users-should-access-which-resources"></a>Gondolja át, hogy mely felhasználók férhetnek hozzá az erőforrásokhoz

Vegye fontolóra a munkamenetek gazdagépeit a meglévő asztali környezet kiterjesztéseként. Javasoljuk, hogy a hálózati erőforrásokhoz való hozzáférést ugyanúgy vezérelje, mint a környezet többi asztalához, például a hálózati szegmentálás és a szűrés használatával. Alapértelmezés szerint a munkamenet-gazdagépek csatlakozhatnak az interneten található erőforrásokhoz. Több módon korlátozhatja a forgalmat, beleértve a Azure Firewall, a hálózati virtuális berendezések vagy a proxyk használatát is. Ha korlátoznia kell a forgalmat, győződjön meg arról, hogy a megfelelő szabályokat adja hozzá, hogy a Windows virtuális asztal megfelelően működjön.

### <a name="manage-office-pro-plus-security"></a>Az Office Pro és a biztonság kezelése

A munkamenet-gazdagépek védelme mellett fontos, hogy a rajtuk futó alkalmazások is biztonságossá válnak. Az Office Pro Plus a munkamenet-gazdagépeken üzembe helyezett leggyakoribb alkalmazások egyike. Az Office-környezet biztonságának javítása érdekében javasoljuk, hogy a Microsoft 365 Enterprise-alkalmazások [biztonsági házirend-tanácsadóját](/DeployOffice/overview-of-security-policy-advisor) használja. Ez az eszköz azonosítja azokat a házirendeket, amelyek az üzemelő példányra alkalmazhatók a nagyobb biztonság érdekében. A Security Policy Advisor azt is javasolja, hogy milyen hatással vannak a házirendek a biztonságra és a hatékonyságra.

### <a name="other-security-tips-for-session-hosts"></a>További biztonsági tippek a munkamenet-gazdagépekhez

Az operációs rendszer képességeinek korlátozásával megerősítheti a munkamenet-gazdagépek biztonságát. Íme néhány dolog, amit tehet:

- Vezérelheti az eszközök átirányítását úgy, hogy átirányítja a meghajtókat, a nyomtatókat és az USB-eszközöket egy távoli asztali munkamenetben lévő felhasználó helyi eszközére. Javasoljuk, hogy értékelje ki a biztonsági követelményeket, és ellenőrizze, hogy a szolgáltatásokat le kell-e tiltani vagy sem.

- A Windows Intéző elérésének korlátozása a helyi és a távoli meghajtó-hozzárendelések elrejtésével. Ez megakadályozza, hogy a felhasználók a Rendszerkonfigurációval és a felhasználókkal kapcsolatos nemkívánatos információkat fedezzék fel.

- Kerülje a környezetében lévő munkamenet-gazdagépek közvetlen RDP-elérését. Ha az adminisztrációhoz vagy a hibaelhárításhoz közvetlen RDP-hozzáférésre van szüksége [, engedélyezze az igény szerinti](../security-center/security-center-just-in-time.md) hozzáférést a munkamenet-gazdagép lehetséges támadási felületének korlátozásához.

- A felhasználók számára korlátozott engedélyek megadása helyi és távoli fájlrendszerek eléréséhez. Az engedélyek korlátozásához győződjön meg arról, hogy a helyi és a távoli fájlrendszerek hozzáférés-vezérlési listát használnak a legalacsonyabb jogosultsággal. Így a felhasználók csak a szükséges igényekhez férhetnek hozzá, és nem módosíthatják és nem törölhetik a kritikus erőforrásokat.

- A nemkívánatos szoftverek futtatásának megakadályozása a munkamenet-gazdagépeken. A munkamenet-gazdagépek további biztonsága érdekében engedélyezheti az alkalmazások zárolását, így biztosíthatja, hogy csak az Ön által engedélyezett alkalmazások futhatnak a gazdagépen.

## <a name="next-steps"></a>Következő lépések

A többtényezős hitelesítés engedélyezéséről a [többtényezős hitelesítés beállítása](set-up-mfa.md)című témakörben olvashat bővebben.