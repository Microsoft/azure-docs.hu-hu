---
title: Az Azure AD alkalmazásproxy összekötőinek | Microsoft Docs
description: Ismerje meg az Azure AD alkalmazásproxy összekötőit.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b54e3cb3b4ce7eb8f541755df75e8d6a22eb7c2
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575309"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Az Azure AD-alkalmazásproxy összekötőinek ismertetése

Az Azure AD-t összekötők teszik alkalmazásproxy teszi lehetővé. Egyszerűek, könnyen üzembe helyezhetők és karbantarthatóak, és rendkívül hatékonyak. Ez a cikk ismerteti, mik azok az összekötők, hogyan működnek, és javaslatokat ad az üzemelő példány optimalizálására.

## <a name="what-is-an-application-proxy-connector"></a>Mi az alkalmazásproxy összekötő?

Az összekötők a helyszínen található egyszerűsített ügynökök, amelyek megkönnyítik a kimenő kapcsolatot a alkalmazásproxy szolgáltatással. Az összekötőket olyan Windows Serverre kell telepíteni, amely hozzáféréssel rendelkezik a háttéralkalmazáshoz. Az összekötőket összekötőcsoportokba rendezheti úgy, hogy minden csoport adott alkalmazások forgalmát kezeli. További információ az alkalmazásproxyról és az alkalmazásproxy architektúrája diagramos ábrázolásról: [Using Azure AD alkalmazásproxy to publish on-premises apps for remote users](what-is-application-proxy.md#application-proxy-connectors) (Az Azure AD alkalmazásproxy használata helyszíni alkalmazások közzétételére távoli felhasználók számára)

## <a name="requirements-and-deployment"></a>Követelmények és üzembe helyezés

A alkalmazásproxy üzembe helyezéséhez legalább egy összekötőre van szükség, de a nagyobb rugalmasság érdekében kettő vagy több ajánlott. Telepítse az összekötőt egy Windows Server 2012 R2 vagy újabb rendszert futtató gépre. Az összekötőnek kommunikálnia kell a alkalmazásproxy szolgáltatással és a közzétett helyszíni alkalmazásokkal.

### <a name="windows-server"></a>Windows Server
Szüksége lesz egy Windows Server 2012 R2 vagy újabb rendszert futtató kiszolgálóra, amelyre telepítheti az alkalmazásproxy összekötőt. A kiszolgálónak csatlakoznia kell az Azure alkalmazásproxy szolgáltatásokhoz és a közzétevő helyszíni alkalmazásokhoz.

A windowsos kiszolgálónak engedélyeznie kell a TLS 1.2-t az alkalmazásproxy telepítése előtt. A TLS 1.2 engedélyezése a kiszolgálón:

1. Állítsa be a következő beállításkulcsokat:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Kiszolgáló újraindítása

További információ az összekötő-kiszolgáló hálózati követelményeiről: Első lépések a alkalmazásproxy [és összekötő telepítése.](application-proxy-add-on-premises-application.md)

## <a name="maintenance"></a>Karbantartás

Az összekötők és a szolgáltatás gondoskodnak az összes magas rendelkezésre állású feladatról. Dinamikusan hozzáadhatók vagy eltávolíthatók. Minden új kérés érkezésekor a rendszer az aktuálisan elérhető összekötők egyikéhez irányít át. Ha egy összekötő átmenetileg nem érhető el, nem válaszol erre a forgalomra.

Az összekötők állapot nélküliek, és nincsenek konfigurációs adataik a gépen. Az egyetlen tárolt adat a szolgáltatás és a hitelesítési tanúsítvány csatlakoztatásának beállításai. Amikor csatlakoznak a szolgáltatáshoz, leküldik az összes szükséges konfigurációs adatot, és néhány percenként frissítik őket.

Az összekötők a kiszolgálótól is lekérdezik, hogy elérhető-e az összekötő újabb verziója. Ha talál egyet, az összekötők frissülnek.

Az eseménynapló és a teljesítményszámlálók segítségével az összekötőket a gépen is figyelheti. Vagy megtekintheti azok állapotát a alkalmazásproxy lap Azure Portal:

![Példa: Azure AD alkalmazásproxy összekötők](./media/application-proxy-connectors/app-proxy-connectors.png)

A nem használt összekötőket nem kell manuálisan törölnie. Ha egy összekötő fut, aktív marad a szolgáltatáshoz való csatlakozáskor. A nem használt összekötők inaktívként vannak _megjelölve,_ és 10 nap inaktivitás után törlődnek. Ha azonban el szeretne távolítani egy összekötőt, távolítsa el az Összekötő szolgáltatást és az Updater szolgáltatást is a kiszolgálóról. Indítsa újra a számítógépet a szolgáltatás teljes eltávolításához.

## <a name="automatic-updates"></a>Automatikus frissítések

Az Azure AD automatikus frissítéseket biztosít az összes üzembe helyezett összekötőhöz. Amíg a alkalmazásproxy Connector Updater szolgáltatás fut, az összekötők automatikusan frissülnek a legújabb fő [összekötő-kiadással.](application-proxy-faq.yml#why-is-my-connector-still-using-an-older-version-and-not-auto-upgraded-to-latest-version-) Ha nem látja a Connector Updater szolgáltatást a kiszolgálón, újra [](application-proxy-add-on-premises-application.md) kell telepítenie az összekötőt a frissítésekhez.

Ha nem szeretne megvárni, amíg egy automatikus frissítés jöjjön az összekötőhöz, manuális frissítést is lehet használni. Kattintson az [összekötő letöltési oldalára](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) a kiszolgálón, ahol az összekötő található, és válassza a **Letöltés lehetőséget.** Ez a folyamat egy frissítést kezd a helyi összekötőhöz.

A több összekötővel rendelkező bérlők esetén az automatikus frissítések minden csoportban egyszerre egy-egy összekötőt céloznak meg a környezet állásideje elkerülése érdekében.

A következő esetben tapasztalhat állásidőt, amikor az összekötő frissül:
  
- Javasoljuk, hogy csak egy összekötővel rendelkezik, és hozzon létre egy [összekötőcsoportot.](application-proxy-connector-groups.md) Ez elkerüli az állásidőt, és magasabb rendelkezésre állást biztosít.  
- Az összekötő egy tranzakció közepén volt a frissítés elkezdődött. Bár a kezdeti tranzakció elveszett, a böngészőnek automatikusan újra kell próbálkoznia a művelettel, vagy frissítheti az oldalt. Ha a kérést a rendszer visszakérte, a forgalom egy biztonsági mentési összekötőre lesz irányítva.

A korábban kiadott verziókról és azok módosításairól további információt a [alkalmazásproxy- verziók kiadási előzményeiben található.](application-proxy-release-version-history.md)

## <a name="creating-connector-groups"></a>Összekötőcsoportok létrehozása

Az összekötőcsoportok lehetővé teszik adott összekötők hozzárendelését adott alkalmazások kiszolgálására. Több összekötőt csoportosíthat, majd az egyes alkalmazásokat hozzárendelheti egy csoporthoz.

Az összekötőcsoportok megkönnyítik a nagy méretű üzemelő példányok kezelését. Emellett javítják a késést azon bérlők esetében, amelyek különböző régiókban üzemeltetett alkalmazásokat üzemeltetnek, mivel helyalapú összekötőcsoportokat hozhat létre, amelyek csak helyi alkalmazásokat szolgálnak ki.

További információ az összekötőcsoportokról: Alkalmazások közzététele külön hálózatokon és helyeken [összekötőcsoportokkal.](application-proxy-connector-groups.md)

## <a name="capacity-planning"></a>Kapacitástervezés

Fontos, hogy az összekötők között elegendő kapacitást tervezve legyen a várt forgalom mennyiségének kezeléséhez. Javasoljuk, hogy minden összekötőcsoport legalább két összekötővel rendelkezik a magas rendelkezésre állás és a méretezés érdekében. A három összekötő optimális arra az esetre, ha bármikor szervizelnünk kell egy gépet.

Általában minél több felhasználója van, annál nagyobb gépre lesz szüksége. Az alábbi táblázat ismerteti a különböző gépek által kezelhető kötetet és várható késést. Vegye figyelembe, hogy nem a felhasználó, hanem a várt másodpercenkénti tranzakciók (TPS) alapján működik, mivel a használati minták eltérőek, és nem használhatók a terhelés előrejelzésére. A válaszok mérete és a háttéralkalmazás válaszidejétől függően is lesz néhány különbség. A nagyobb válaszméret és a lassabb válaszidők alacsonyabb Max TPS-t eredményeznek. Javasoljuk továbbá, hogy további gépeket is biztosítsunk, hogy a gépek közötti elosztott terhelés mindig elegendő puffert biztosít. A további kapacitás biztosítja a magas rendelkezésre állást és rugalmasságot.

|Cores|RAM|Várt késés (MS)-P99|Maximális TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Ez a gép egy egyéni beállítással megemelt néhány alapértelmezett kapcsolati korlátot a .NET által javasolt beállításokon túl. Javasoljuk, hogy futtason egy tesztet az alapértelmezett beállításokkal, mielőtt kapcsolatba lép az ügyfélszolgálattal, hogy megváltozta a bérlőre vonatkozó korlátot.

> [!NOTE]
> A maximális TPS-ben nincs sok különbség a 4, 8 és 16 magos gépek között. Ezek között a fő különbség a várt késés.
>
> Ez a táblázat az összekötők várt teljesítményére is összpontosít az alapján, hogy milyen típusú gépre van telepítve. Ez elkülönül a alkalmazásproxy szolgáltatás szabályozási korlátaitól, lásd: [Szolgáltatáskorlátok és -korlátozások.](../enterprise-users/directory-service-limits-restrictions.md)

## <a name="security-and-networking"></a>Biztonság és hálózat

Az összekötők bárhol telepíthetők a hálózaton, ami lehetővé teszi számukra, hogy kéréseket küldjenek a alkalmazásproxy szolgáltatásnak. Ami fontos, hogy az összekötőt futtató számítógép is hozzáfér az alkalmazásokhoz. Az összekötőket a vállalati hálózaton belül vagy egy felhőben futó virtuális gépre is telepítheti. Az összekötők egy szegélyhálózaton, más néven demilitarizált zónában (DMZ) futnak, de ez nem szükséges, mivel minden forgalom kimenő, így a hálózat biztonságos marad.

Az összekötők csak kimenő kérelmeket küldenek. A kimenő forgalom a alkalmazásproxy szolgáltatásnak és a közzétett alkalmazásoknak lesz elküldve. Nem kell megnyitnia a bejövő portokat, mert a forgalom mindkét módon áramlik a munkamenet létrejötte után. Emellett nem kell konfigurálnia a bejövő hozzáférést a tűzfalakon keresztül.

A kimenő tűzfalszabályok konfigurálásával kapcsolatos további információkért lásd: Meglévő helyszíni [proxykiszolgálók használata.](application-proxy-configure-connectors-with-proxy-servers.md)

## <a name="performance-and-scalability"></a>Teljesítmény és méretezhetőség

Az alkalmazásproxy skálázható, de a méretezés az összekötők egyik tényezője. A csúcsforgalom kezeléséhez elegendő összekötőre van szükség. Mivel az összekötők állapot nélküliek, a felhasználók vagy munkamenetek száma nem befolyásolja őket. Ehelyett válaszolnak a kérések számára és a hasznos tartalom méretére. Standard webes forgalom esetén egy átlagos gép másodpercenként néhány ezer kérést képes kezelni. Az adott kapacitás a gép pontos jellemzőitől függ.

Az összekötő teljesítményét a processzor és a hálózat. A TLS-titkosításhoz és -visszafejtéshez processzorteljesítményre van szükség, míg a hálózat fontos az alkalmazásokhoz és az Azure online szolgáltatásához való gyors kapcsolódáshoz.

Ezzel szemben a memória kevésbé jelent problémát az összekötők számára. Az online szolgáltatás gondoskodik a feldolgozás nagy áról és az összes nem hitelesített forgalomról. A felhőben minden, amit el lehet tenni, a felhőben történik.

Ha az összekötő vagy gép valamilyen okból elérhetetlenné válik, a forgalom a csoport egy másik összekötőjához fog átsziváni. Ez a rugalmasság az oka annak is, hogy több összekötő használatát javasoljuk.

Egy másik tényező, amely hatással van a teljesítményre, az összekötők közötti hálózat minősége, beleértve a következőket:

- **Online szolgáltatás:** Az Azure-beli alkalmazásproxy szolgáltatás lassú vagy nagy késésű kapcsolatai befolyásolják az összekötő teljesítményét. A legjobb teljesítmény érdekében az Express Route használatával csatlakoztassa a szervezetet az Azure-hoz. Ellenkező esetben gondoskodjon róla, hogy a hálózati csapat a lehető leghatékonyabban kezelje az Azure-hoz való csatlakozásokat.
- **Háttéralkalmazások:** Bizonyos esetekben további proxyk is vannak az összekötő és a háttéralkalmazások között, amelyek lelassítják vagy megakadályozzák a kapcsolatokat. A forgatókönyv elhárításához nyisson meg egy böngészőt az összekötő-kiszolgálóról, és próbálja meg elérni az alkalmazást. Ha az Azure-ban futtatja az összekötőket, de az alkalmazások helyszíniek, előfordulhat, hogy a felhasználói élmény nem az, amit a felhasználók elvárnak.
- **A tartományvezérlők:** Ha az összekötők egyszeri bejelentkezést (SSO) végeznek a Kerberos által korlátozott delegálással, akkor a kérés háttérnek való elküldése előtt kapcsolatba lépnek a tartományvezérlővel. Az összekötők Kerberos-jegyeket gyorsítótáraznak, de foglalt környezetben a tartományvezérlők válaszképessége hatással lehet a teljesítményre. Ez a probléma gyakoribb az Azure-ban futó, de helyszíni tartományvezérlővel kommunikáló összekötők esetén.

A hálózat optimalizálásával kapcsolatos további információkért lásd: Hálózati [topológiával](application-proxy-network-topology.md)kapcsolatos szempontok a Azure Active Directory alkalmazásproxy.

## <a name="domain-joining"></a>Tartományhoz való csatlakozás

Az összekötők olyan gépeken futnak, amelyek nem csatlakoznak tartományhoz. Ha azonban egyszeri bejelentkezést (SSO) szeretne használni az integrált Windows-hitelesítés (IWA) alkalmazást használják, tartományhoz csatlakozott gépre van szüksége. Ebben az esetben az összekötő gépeket olyan tartományhoz kell csatlakoztatni, amely képes [Kerberos](https://web.mit.edu/kerberos) által korlátozott delegálást végrehajtani a közzétett alkalmazások felhasználóinak nevében.

Az összekötők részleges megbízhatóságú erdők tartományaihoz vagy csak olvasható tartományvezérlőkhöz is csatlakoztathatóak.

## <a name="connector-deployments-on-hardened-environments"></a>Összekötők üzembe helyezése a megkétesedő környezetekben

Az összekötők üzembe helyezése általában egyszerű, és nem igényel különleges konfigurációt. Van azonban néhány egyedi feltétel, amit figyelembe kell venni:

- A kimenő forgalmat korlátozó szervezeteknek meg kell [nyitniuk a szükséges portokat.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)
- Előfordulhat, hogy a FIPS-kompatibilis gépeknek módosítaniuk kell a konfigurációjukat, hogy az összekötő folyamatai tanúsítványt hozzanak létre és tároljanak.
- Azok a szervezetek, amelyek a hálózati kéréseket kitató folyamatok alapján zárolják a környezetüket, meg kell győződni arról, hogy mindkét összekötő-szolgáltatás számára engedélyezve van az összes szükséges port és IP-port elérése.
- Bizonyos esetekben a kimenő továbbítási proxyk megszakítják a két irányú tanúsítványhitelesítést, és a kommunikáció meghiúsulhat.

## <a name="connector-authentication"></a>Összekötő hitelesítése

A biztonságos szolgáltatás biztosítása érdekében az összekötőknek hitelesítést kell végezniük a szolgáltatás felé, a szolgáltatásnak pedig hitelesítenie kell magát az összekötő felé. Ez a hitelesítés ügyfél- és kiszolgálótanúsítványok használatával történik, amikor az összekötők kezdeményezik a kapcsolatot. Így a rendszergazda felhasználónevét és jelszavát a rendszer nem tárolja az összekötőt kezelő számítógépen.

A használt tanúsítványok a szolgáltatáshoz alkalmazásproxy vonatkoznak. Ezeket a rendszer a kezdeti regisztráció során hozta létre, és az összekötők néhány hónaponként automatikusan megújítják őket.

A tanúsítvány első sikeres megújítása után az Azure AD alkalmazásproxy Connector szolgáltatás (hálózati szolgáltatás) nem rendelkezik engedéllyel a régi tanúsítvány eltávolításához a helyi gép tárolójában. Ha a tanúsítvány lejárt, vagy a szolgáltatás már nem használja, biztonságosan törölheti.

A tanúsítvány megújításával kapcsolatos problémák elkerülése érdekében győződjön meg arról, hogy az összekötő és a dokumentált cél közötti hálózati kommunikáció [engedélyezve](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) van.

Ha egy összekötő több hónapja nem csatlakozik a szolgáltatáshoz, előfordulhat, hogy a tanúsítványai elavultak. Ebben az esetben távolítsa el és telepítse újra az összekötőt a regisztráció aktiválása miatt. A következő PowerShell-parancsokat futtathatja:

```
Import-module AppProxyPSModule
Register-AppProxyConnector -EnvironmentName "AzureCloud"
```

A kormányzati felhasználáshoz használja a `-EnvironmentName "AzureUSGovernment"` et. További részletekért lásd: Install Agent for the Azure Government Cloud (Ügynök telepítése a [Azure Government Cloudhoz).](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud)

A tanúsítvány ellenőrzésével és a problémák elhárításával kapcsolatos további információkért lásd: Verify Machine and backend components support for alkalmazásproxy trust certificate (A megbízható tanúsítványok számítógép- és [háttérösszetevőinek](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate)támogatása).

## <a name="under-the-hood"></a>technikai részletek

Az összekötők a Windows Server Web alkalmazásproxy alapulnak, így a legtöbb ugyanazokkal a felügyeleti eszközökkel, köztük a Windows-eseménynaplókkal is rendelkezik

![Eseménynaplók kezelése a Eseménynapló](./media/application-proxy-connectors/event-view-window.png)

és Windows-teljesítményszámlálók.

![Számlálók hozzáadása az összekötőhöz a Teljesítményfigyelővel](./media/application-proxy-connectors/performance-monitor.png)

Az összekötők rendszergazdai **és munkamenet-naplókkal** **is** rendelkezik. A **rendszergazdai** napló tartalmazza a fő eseményeket és azok hibáit. A **munkamenet-napló** tartalmazza az összes tranzakciót és azok feldolgozási részleteit.

A naplók megtekintéséhez nyissa meg a **Eseménynapló,** és nyissa meg az **Alkalmazás-** és szolgáltatásnaplók  >  **Microsoft**  >  **AadApplicationProxy-összekötőt.**  >   A **munkamenetnapló láthatóvá** tenni a View (Nézet) **menü** **Show Analytic and Debug Logs**(Naplók megjelenítése és hibakeresése) parancsát. A **munkamenetnaplót** általában hibaelhárításra használják, és alapértelmezés szerint le van tiltva. Engedélyezze az események gyűjtését és letiltását, ha már nincs rá szükség.

A szolgáltatás állapotát a Szolgáltatások ablakban vizsgálhatja meg. Az összekötő két Windows-szolgáltatásból áll: a tényleges összekötőből és az updaterből. Mindkettőnek mindig futnia kell.

 ![Példa: Szolgáltatások ablak az Azure AD-szolgáltatások helyi megjelenítésével](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Következő lépések

- [Alkalmazások közzététele külön hálózatokon és helyeken összekötőcsoportok használatával](application-proxy-connector-groups.md)
- [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
- [A alkalmazásproxy és összekötő hibáinak elhárítása](application-proxy-troubleshoot.md)
- [Az Azure AD alkalmazásproxy-összekötő csendes telepítése](application-proxy-register-connector-powershell.md)
