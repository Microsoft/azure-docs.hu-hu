---
title: Az Azure AD Application Proxy-összekötők ismertetése | Microsoft Docs
description: Ismerje meg az Azure AD Application Proxy-összekötőket.
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
ms.openlocfilehash: f57d390ed71cb4e0e76972e02170afde7b13e4ae
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99253459"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Az Azure AD Application Proxy-összekötők ismertetése

Az összekötők teszik elérhetővé az Azure AD Application Proxy. Egyszerűen, egyszerűen üzembe helyezhetők és karbantarthatók, és rendkívül hatékonyak. Ez a cikk ismerteti, hogy milyen összekötők, hogyan működnek, és néhány javaslat a telepítés optimalizálására.

## <a name="what-is-an-application-proxy-connector"></a>Mi az alkalmazásproxy-összekötő?

Az összekötők olyan könnyű ügynökök, amelyek a helyszínen üzemelnek, és elősegítik a kimenő kapcsolódást az alkalmazásproxy szolgáltatáshoz. Az összekötőket olyan Windows-kiszolgálóra kell telepíteni, amely hozzáfér a háttérbeli alkalmazáshoz. Összekötő csoportokba rendezheti az összekötőket, és az egyes csoportokkal az egyes alkalmazások forgalmát is kezelheti. További információk az alkalmazásproxy és az alkalmazásproxy-architektúra teherbírást jelölő táblával megjelenítéséről: [Azure ad Application proxy használata helyszíni alkalmazások közzétételéhez távoli felhasználók](what-is-application-proxy.md#application-proxy-connectors) számára

## <a name="requirements-and-deployment"></a>Követelmények és központi telepítés

Az alkalmazásproxy sikeres telepítéséhez legalább egy összekötőre van szükség, de javasoljuk, hogy nagyobb rugalmasságot biztosítson. Telepítse az összekötőt egy Windows Server 2012 R2 vagy újabb rendszerű gépre. Az összekötőnek kommunikálnia kell az alkalmazásproxy szolgáltatással és a közzétenni kívánt helyszíni alkalmazásokkal.

### <a name="windows-server"></a>Windows Server
Szükség van egy Windows Server 2012 R2 vagy újabb rendszert futtató kiszolgálóra, amelyen telepítheti az alkalmazásproxy-összekötőt. A kiszolgálónak csatlakoznia kell az alkalmazásproxy-szolgáltatásokhoz az Azure-ban és a közzétenni kívánt helyszíni alkalmazásokhoz.

A Windows Servernek az alkalmazásproxy-összekötő telepítése előtt engedélyeznie kell a TLS 1,2-et. A TLS 1,2 engedélyezése a kiszolgálón:

1. Állítsa be a következő beállításkulcsokat:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Kiszolgáló újraindítása

Az összekötő-kiszolgáló hálózati követelményeivel kapcsolatos további információkért lásd: az [alkalmazásproxy első lépései és az összekötő telepítése](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Karbantartás

Az összekötők és a szolgáltatás gondoskodik a magas rendelkezésre állási feladatokról. A felhasználók dinamikusan hozzáadhatók vagy eltávolíthatók. Minden alkalommal, amikor egy új kérelem érkezik, a rendszer átirányítja az egyik jelenleg elérhető összekötőre. Ha egy összekötő átmenetileg nem érhető el, nem válaszol erre a forgalomra.

Az összekötők állapot nélküliek, és nem rendelkeznek konfigurációs adattal a gépen. Az egyetlen tárolt adattároló a szolgáltatás és a hitelesítési tanúsítvány csatlakoztatásának beállításai. Amikor csatlakoznak a szolgáltatáshoz, lekérik az összes szükséges konfigurációs adatmennyiséget, és néhány percenként frissítik.

Az összekötők azt is lekérdezik a kiszolgálót, hogy az összekötő újabb verziója van-e. Ha talál ilyet, az összekötők maguk frissítik magukat.

Az összekötőket az Eseménynapló és a teljesítményszámlálók segítségével figyelheti a gépen futó gépekről. Vagy megtekintheti az állapotukat a Azure Portal alkalmazásproxy lapján:

![Példa: Azure AD Application Proxy-összekötők](./media/application-proxy-connectors/app-proxy-connectors.png)

Nem kell manuálisan törölni a nem használt összekötőket. Amikor egy összekötő fut, aktív marad, ahogy csatlakozik a szolgáltatáshoz. A fel nem használt összekötők _inaktívként_ vannak megjelölve, és 10 napos inaktivitás után törlődnek. Ha el kívánja távolítani az összekötőt, akkor az összekötő szolgáltatást és a frissítési szolgáltatást is távolítsa el a kiszolgálóról. Indítsa újra a számítógépet a szolgáltatás teljes eltávolításához.

## <a name="automatic-updates"></a>Automatikus frissítések

Az Azure AD automatikus frissítéseket biztosít az összes telepített összekötőhöz. Amíg az alkalmazásproxy Connector Updater szolgáltatás fut, az összekötők automatikusan frissülnek. Ha nem látja a Connector Updater szolgáltatást a kiszolgálón, [újra kell telepítenie az összekötőt](application-proxy-add-on-premises-application.md) a frissítések beszerzéséhez.

Ha nem szeretné, hogy az automatikus frissítés megvárja az összekötőt, manuális frissítést végezhet. Nyissa meg az [összekötő letöltési lapját](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) azon a kiszolgálón, amelyen az összekötő található, és válassza a **Letöltés** lehetőséget. Ez a folyamat elindít egy frissítést a helyi összekötőhöz.

Több összekötővel rendelkező bérlők esetében az automatikus frissítés az egyes csoportokban egyszerre egy összekötőt céloz meg, hogy megakadályozza az állásidőt a környezetben.

A leállás akkor fordulhat elő, ha az összekötő a következőket frissíti:
  
- Csak egy összekötővel rendelkezik, javasoljuk, hogy telepítsen egy második összekötőt, és [hozzon létre egy összekötő csoportot](application-proxy-connector-groups.md). Ezzel elkerülhető az állásidő, és magasabb rendelkezésre állást biztosít.  
- A frissítés megkezdése után egy összekötő volt a tranzakció közepén. Bár a kezdeti tranzakció elvész, a böngészőnek automatikusan újra kell próbálkoznia a művelettel, vagy frissítheti a lapot. A kérés újraküldése után a rendszer átirányítja a forgalmat egy biztonsági mentési összekötőhöz.

A korábban kiadott verziókról és a benne foglalt változásokról további információt az [alkalmazásproxy verziójának kiadási előzményei](application-proxy-release-version-history.md)című témakörben talál.

## <a name="creating-connector-groups"></a>Összekötő-csoportok létrehozása

Az összekötő csoportok lehetővé teszik, hogy meghatározott összekötőket rendeljen hozzá bizonyos alkalmazásokhoz. Egyszerre több összekötőt is csoportosíthat, majd hozzárendelheti az egyes alkalmazásokat egy csoporthoz.

Az összekötő-csoportok megkönnyítik a nagyméretű telepítések kezelését. Emellett a különböző régiókban üzemeltetett alkalmazásokkal rendelkező bérlők számára is javítják a késést, mivel a csak helyi alkalmazások kiszolgálásához létrehozhat hely-alapú összekötő csoportokat.

További információ az összekötő-csoportokról: [alkalmazások közzététele különálló hálózatokon és helyszíneken összekötő csoportok használatával](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Kapacitástervezés

Fontos, hogy gondoskodjon arról, hogy elegendő kapacitást tervezzen az összekötők között a várt forgalmi mennyiség kezeléséhez. Javasoljuk, hogy minden összekötő-csoportnak legalább két összekötője legyen, hogy magas rendelkezésre állást és méretezést biztosítson. Ha a három összekötő optimális, akkor előfordulhat, hogy egy gépet bármikor kell kiszolgálni.

Általánosságban elmondható, hogy minél több felhasználó van, annál nagyobb a gép, amelyre szüksége lesz. Alább látható egy táblázat, amely felvázolja a kötetet, és a várható késést különböző gépek képesek kezelni. Vegye figyelembe, hogy a másodpercenkénti várt tranzakciók (TPS) alapján, mivel a használati minták eltérnek, és nem használhatók a betöltés előrejelzésére. A válaszok méretétől és a háttérbeli alkalmazások válaszideje – nagyobb válaszokra és lassabb válaszidő-ra is – a rendszer alacsonyabb TPS eredményez. Azt javasoljuk továbbá, hogy további gépek legyenek, hogy az elosztott terhelés a gépek között mindig bőséges puffert biztosítson. A további kapacitás garantálja, hogy magas rendelkezésre állást és rugalmasságot biztosítson.

|Cores|RAM|Várt késés (MS) – P99|Maximális TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200 *|

\* Ez a gép egyéni beállítást használt, hogy a .NET által ajánlott beállításokon felül néhány alapértelmezett kapcsolódási korlátot felemeljen. Javasoljuk, hogy futtasson egy tesztet az alapértelmezett beállításokkal, mielőtt felvette a kapcsolatot a támogatási szolgálattal, hogy megváltozzon a bérlőn a korlát.

> [!NOTE]
> A 4, 8 és 16 Magos gép közötti maximális TPS nem sok a különbség. A fő különbség a várt késés.
>
> Ez a táblázat egy összekötő várt teljesítményére koncentrál, amely a telepített számítógép típusától függ. Ez eltér az alkalmazásproxy szolgáltatás szabályozási korlátaitól, lásd: [szolgáltatás korlátai és korlátozásai](../enterprise-users/directory-service-limits-restrictions.md).

## <a name="security-and-networking"></a>Biztonság és hálózatkezelés

Az összekötők bárhonnan telepíthetők a hálózaton, amely lehetővé teszi a kérelmek küldését az alkalmazásproxy szolgáltatásba. Fontos, hogy az összekötőt futtató számítógép is hozzáférjen az alkalmazásaihoz. Az összekötők a vállalati hálózaton belül vagy a felhőben futó virtuális gépeken is telepíthetők. Az összekötők egy peremhálózati hálózaton, más néven vagy demilitarizált-zónában (DMZ) is futtathatók, de nem szükségesek, mert az összes forgalom kimenő, így a hálózat biztonságban marad.

Az összekötők csak kimenő kérelmeket küldenek. A rendszer elküldi a kimenő forgalmat az alkalmazásproxy szolgáltatásnak és a közzétett alkalmazásoknak. Nem kell megnyitnia a bejövő portokat, mert a forgalom mindkét módon a munkamenet létrehozása után áramlik. Emellett a tűzfalakon keresztül nem kell konfigurálnia a bejövő hozzáférést.

A kimenő tűzfalszabályok konfigurálásával kapcsolatos további információkért lásd: a [meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Teljesítmény és méretezhetőség

Az alkalmazásproxy szolgáltatás méretezése transzparens, de a méretezés az összekötők egyik tényezője. A maximális adatforgalom kezeléséhez elegendő összekötőre van szükség. Mivel az összekötők állapot nélküliek, nem befolyásolják a felhasználók vagy a munkamenetek számát. Ehelyett a kérelmek számának és a hasznos adatok méretének megfelelően reagálnak. A normál webes forgalom esetében egy átlagos gép másodpercenként több ezer kérést képes kezelni. A megadott kapacitás a gép pontos jellemzőitől függ.

Az összekötő teljesítményét a processzor és a hálózat köti. CPU-teljesítményre van szükség a TLS-titkosításhoz és-visszafejtéshez, míg a hálózatkezelés fontos az alkalmazások és az Azure online szolgáltatásának gyors eléréséhez.

Ezzel szemben a memória kisebb az összekötők hibája miatt. Az online szolgáltatás a feldolgozás és az összes nem hitelesített forgalom nagy részét gondoskodik. Minden, ami a felhőben végezhető, a felhőben végezhető el.

Ha bármilyen okból kifolyólag az összekötő vagy a gép elérhetetlenné válik, a forgalom egy másik összekötőhöz fog indulni a csoportban. Ez a rugalmasság azt is indokolja, hogy több összekötőt is ajánlunk.

Egy másik tényező, amely hatással van a teljesítményre, az összekötők közötti hálózatkezelés minősége, beleértve a következőket:

- **Online szolgáltatás**: az Azure-ban az alkalmazásproxy szolgáltatás lassú vagy nagy késleltetésű kapcsolatai befolyásolják az összekötő teljesítményét. A legjobb teljesítmény érdekében a szervezetet az Azure-hoz az expressz Route használatával kapcsolja össze. Ellenkező esetben a hálózati csapatnak biztosítania kell, hogy az Azure-kapcsolatok a lehető leghatékonyabban kezelhetők legyenek.
- **A háttérbeli alkalmazások**: bizonyos esetekben további proxyk vannak az összekötő és a háttérbeli alkalmazások között, amelyek lassú vagy meggátolják a kapcsolatokat. A forgatókönyv hibakereséséhez nyisson meg egy böngészőt az összekötő-kiszolgálóról, és próbálja meg elérni az alkalmazást. Ha az összekötőket az Azure-ban futtatja, de az alkalmazások a helyszínen vannak, előfordulhat, hogy a felhasználói élmény nem az, amit a felhasználók elvárnak.
- **Tartományvezérlők**: Ha az összekötők egyszeri bejelentkezést (SSO) hajtanak végre a Kerberos által korlátozott delegálás használatával, a kérésnek a háttérbe való küldése előtt kapcsolatba léphetnek a tartományvezérlőkkel. Az összekötők rendelkeznek a Kerberos-jegyek gyorsítótárával, de egy forgalmas környezetben a tartományvezérlők reagálása hatással lehet a teljesítményre. Ez a probléma gyakoribb az Azure-ban futó összekötők esetében, de a helyszíni tartományvezérlőkön kommunikál.

A hálózat optimalizálásával kapcsolatos további információkért tekintse meg a [hálózati topológia szempontjait a Azure Active Directory Application proxy használatakor](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Csatlakozás tartományhoz

Az összekötők olyan gépen is futtathatók, amely nincs tartományhoz csatlakoztatva. Ha azonban egyszeri bejelentkezésre (SSO) van szüksége az integrált Windows-hitelesítést (IWA) használó alkalmazásokhoz, a tartományhoz csatlakoztatott számítógépekre van szükség. Ebben az esetben az összekötő gépeket olyan tartományhoz kell csatlakoztatni, amely képes a felhasználók nevében [Kerberos](https://web.mit.edu/kerberos) által korlátozott delegálás végrehajtására a közzétett alkalmazásokhoz.

Az összekötők olyan erdők tartományához is csatlakozhatnak, amelyek részleges megbízhatósággal rendelkeznek, vagy írásvédett tartományvezérlők.

## <a name="connector-deployments-on-hardened-environments"></a>Összekötő üzembe helyezése megerősített környezetekben

Az összekötő üzembe helyezése általában egyszerű, és nem igényel speciális konfigurációt. Vannak azonban olyan egyedi feltételek, amelyeket figyelembe kell venni:

- A kimenő forgalmat korlátozó szervezeteknek [meg kell nyitniuk a szükséges portokat](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- Előfordulhat, hogy az FIPS-kompatibilis gépeknek módosítaniuk kell a konfigurációt, hogy az összekötő folyamatai tanúsítványokat állítanak elő és tárolnak.
- Azok a szervezetek, amelyek a hálózati kérelmeket kiállító folyamatok alapján zárolják a környezetet, gondoskodni kell arról, hogy mindkét összekötő-szolgáltatás engedélyezze a szükséges portok és IP-címek elérését.
- Bizonyos esetekben a kimenő továbbítási proxyk megszakítják a kétirányú Tanúsítványos hitelesítést, ami miatt a kommunikáció sikertelen lehet.

## <a name="connector-authentication"></a>Összekötő-hitelesítés

Biztonságos szolgáltatás biztosításához az összekötőknek hitelesíteniük kell magukat a szolgáltatás felé, és a szolgáltatásnak hitelesítenie kell magát az összekötő felé. Ez a hitelesítés ügyfél-és kiszolgálói tanúsítványokkal történik, ha az összekötők kezdeményezik a kapcsolódást. Így a rendszergazda felhasználónevét és jelszavát a rendszer nem tárolja az összekötő számítógépen.

A használt tanúsítványok az alkalmazásproxy szolgáltatásra vonatkoznak. A kezdeti regisztráció során jönnek létre, és az összekötők minden hónapban automatikusan megújítják őket.

Az első sikeres tanúsítvány megújítása után az Azure AD Application Proxy Connector Service (hálózati szolgáltatás) nem rendelkezik engedéllyel a régi tanúsítvány eltávolításához a helyi számítógép tárolójából. Ha a tanúsítvány lejárt, vagy a szolgáltatás nem használja többé, akkor azt biztonságosan törölheti.

A tanúsítvány megújításával kapcsolatos problémák elkerülése érdekében győződjön meg arról, hogy az összekötőtől a [dokumentált célhelyek](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) felé irányuló hálózati kommunikáció engedélyezve van.

Ha egy összekötő több hónapig nem csatlakozik a szolgáltatáshoz, előfordulhat, hogy a tanúsítványa elavult. Ebben az esetben távolítsa el, majd telepítse újra az összekötőt a regisztráció elindításához. A következő PowerShell-parancsokat futtathatja:

```
Import-module AppProxyPSModule
Register-AppProxyConnector -EnvironmentName "AzureCloud"
```

A Government esetében használja a következőt: `-EnvironmentName "AzureUSGovernment"` . További részletekért lásd: [ügynök telepítése a Azure Government felhőhöz](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud).

Ha többet szeretne megtudni a tanúsítvány ellenőrzéséről és a hibák elhárításáról, tekintse meg a [számítógép-és háttér-összetevők támogatása az alkalmazásproxy megbízhatósági tanúsítványának ellenőrzése](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate)című témakört.

## <a name="under-the-hood"></a>technikai részletek

Az összekötők a Windows Server webalkalmazás-proxyn alapulnak, így többek között ugyanazok a felügyeleti eszközök, mint a Windows-eseménynaplók

![Eseménynaplók kezelése a Eseménynapló](./media/application-proxy-connectors/event-view-window.png)

és Windows-teljesítményszámlálók.

![Teljesítményszámlálók hozzáadása az összekötőhöz a Teljesítményfigyelővel](./media/application-proxy-connectors/performance-monitor.png)

Az összekötők **rendszergazdai** és **munkamenet** -naplókkal is rendelkeznek. A **rendszergazdai** napló a legfontosabb eseményeket és azok hibáit tartalmazza. A **munkamenet** -napló tartalmazza az összes tranzakciót és a feldolgozás részleteit.

A naplók megjelenítéséhez nyissa meg **Eseménynapló** és lépjen az **alkalmazások és szolgáltatások naplóba** a  >  **Microsoft**  >  **AadApplicationProxy**-  >  **összekötő**. A **munkamenet** naplójának láthatóvá tételéhez válassza a **nézet** menü **elemzési és hibakeresési naplók megjelenítése** parancsát. A **munkamenet** -napló általában hibaelhárításhoz használatos, és alapértelmezés szerint le van tiltva. Lehetővé teszi, hogy megkezdse az események gyűjtését, és letiltja azt, ha már nincs rá szükség.

A szolgáltatás állapotát a szolgáltatások ablakban ellenőrizheti. Az összekötő két Windows-szolgáltatásból áll: a tényleges összekötőből és a updaterből. Mindkettőnek minden alkalommal futnia kell.

 ![Példa: szolgáltatások ablak, amelyen az Azure AD Services helyi](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Következő lépések

- [Alkalmazások közzététele különálló hálózatokon és helyszíneken összekötő csoportok használatával](application-proxy-connector-groups.md)
- [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
- [Alkalmazásproxy-és összekötő-hibák elhárítása](application-proxy-troubleshoot.md)
- [Az Azure AD Application Proxy-összekötő csendes telepítése](application-proxy-register-connector-powershell.md)
