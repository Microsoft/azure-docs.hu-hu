---
title: A meglévő helyszíni proxykiszolgálók és Azure Active Directory használata
description: Ismerteti, hogyan használható a meglévő helyszíni proxykiszolgálók a Azure Active Directory használatával.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/07/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: 09a257c4b80fd796ac4e1e8203f00857d2d95eaf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259117"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Meglévő helyszíni proxykiszolgálók használata

Ez a cikk bemutatja, hogyan konfigurálhatja a Azure Active Directory (Azure AD) alkalmazásproxy-összekötőket a kimenő proxykiszolgálók működéséhez. Olyan hálózati környezetű ügyfelek számára készült, amelyek meglévő proxykkal rendelkeznek.

Első lépésként tekintse meg a következő központi telepítési forgatókönyveket:

* Konfigurálja az összekötőket a helyszíni kimenő proxyk megkerüléséhez.
* Konfigurálja az összekötőket egy kimenő proxy használatára az Azure AD Application Proxy eléréséhez.
* Konfigurálás az összekötő és a háttérbeli alkalmazás közötti proxy használatával.

További információ az összekötők működéséről: az [Azure ad Application proxy-összekötők ismertetése](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Kimenő proxyk megkerülése

Az összekötők olyan operációsrendszer-összetevőkkel rendelkeznek, amelyek kimenő kérelmeket hajtanak végre. Ezek az összetevők automatikusan megpróbálnak megkeresni egy proxykiszolgálót a hálózaton a webproxy automatikus felderítése (WPAD) használatával.

Az operációs rendszer összetevői a WPAD. domainsuffix DNS-címkeresés végrehajtásával kísérlik meg a proxykiszolgáló megkeresését. Ha a keresés a DNS-ben oldódik fel, akkor a WPAD. dat IP-címére kell HTTP-kérelmet készíteni. Ez a kérelem a proxy konfigurációs parancsfájlja lesz a környezetben. Az összekötő ezt a parancsfájlt használja a kimenő proxykiszolgáló kiválasztásához. Előfordulhat azonban, hogy az összekötői forgalom továbbra sem halad át a proxyn szükséges további konfigurációs beállítások miatt.

Az összekötőt úgy is beállíthatja, hogy megkerülje a helyszíni proxyt, hogy az az Azure-szolgáltatásokhoz közvetlen kapcsolatot használjon. Ezt a megközelítést javasoljuk, ha a hálózati házirend lehetővé teszi a számára, mert az azt jelenti, hogy egy kisebb konfigurációt kell fenntartania.

Az összekötő kimenő proxy használatának letiltásához szerkessze a C:\Program Files\Microsoft HRE app proxy Connector\ApplicationProxyConnectorService.exe.config fájlt, és adja hozzá a *System.net* szakaszt a következő kódban:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Annak biztosítása érdekében, hogy a Connector Updater szolgáltatás a proxyt is megkerüljék, végezze el a ApplicationProxyConnectorUpdaterService.exe.config fájl hasonló módosítását. Ez a fájl a C:\Program Files\Microsoft HRE app proxy Connector Updater található.

Készítsen másolatot az eredeti fájlokról, ha az alapértelmezett. config fájlokra kell visszaállítania.

## <a name="use-the-outbound-proxy-server"></a>A kimenő proxykiszolgáló használata

Egyes környezetekben az összes kimenő forgalomnak a kimenő proxyn keresztül kell haladnia, kivétel nélkül. Ennek eredményeképpen a proxy mellőzése nem választható.

Az összekötő adatforgalmát beállíthatja úgy, hogy a kimenő proxyn Haladjon végig, ahogy az alábbi ábrán is látható:

 ![Az összekötő-forgalom konfigurálása egy kimenő proxyn keresztüli váltáshoz az Azure AD Application Proxy](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

A csak kimenő forgalom miatt nem kell konfigurálnia a tűzfalon keresztüli bejövő hozzáférést.

> [!NOTE]
> Az alkalmazásproxy nem támogatja más proxyk hitelesítését. Az összekötő/Updater hálózati szolgáltatás fiókjainak képesnek kell lenniük a proxyhoz való csatlakozásra anélkül, hogy a hitelesítésre kellene figyelni.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>1. lépés: az összekötő és a kapcsolódó szolgáltatások konfigurálása a kimenő proxyn való átugráshoz

Ha a WPAD engedélyezve van a környezetben, és megfelelően van konfigurálva, az összekötő automatikusan felfedi a kimenő proxykiszolgálót, és megpróbálja használni azt. Az összekötőt azonban explicit módon úgy konfigurálhatja, hogy átugorjon egy kimenő proxyn.

Ehhez szerkessze a C:\Program Files\Microsoft HRE app proxy Connector\ApplicationProxyConnectorService.exe.config fájlt, és adja hozzá a *System.net* szakaszt az ebben a kódban látható példában. Módosítsa a *ProxyServer: 8080* , hogy tükrözze a helyi proxykiszolgáló nevét vagy IP-címét, valamint azt a portot, amelyen a figyel. Az értéknek az előtag http://akkor is szerepelnie kell, ha IP-címet használ.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Ezután konfigurálja a Connector Updater szolgáltatást a proxy használatára úgy, hogy hasonló módosítást végez a C:\Program Files\Microsoft HRE app proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config fájlban.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>2. lépés: konfigurálja a proxyt úgy, hogy az összekötő és a kapcsolódó szolgáltatások forgalmát a

A kimenő proxyn négy szempontot kell figyelembe venni:

* Proxy kimenő szabályai
* Proxy hitelesítése
* Proxy portok
* TLS-ellenőrzés

#### <a name="proxy-outbound-rules"></a>Proxy kimenő szabályai

A következő URL-címek elérésének engedélyezése:

| URL-cím | Port |  Használat célja |
| --- | --- | --- |
| &ast;. msappproxy.net<br>&ast;. servicebus.windows.net | 443/HTTPS | Kommunikáció az összekötő és az alkalmazásproxy Cloud Service között |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP | Az összekötő ezeket az URL-címeket használja a tanúsítványok ellenőrzéséhez. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;. microsoftonline-p.com<br>&ast;. msauth.net<br>&ast;. msauthimages.net<br>&ast;. msecnd.net<br>&ast;. msftauth.net<br>&ast;. msftauthimages.net<br>&ast;. phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com | 443/HTTPS | Az összekötő ezeket az URL-címeket használja a regisztrációs folyamat során. |
| ctldl.windowsupdate.com | 80/HTTP | Az összekötő ezt az URL-címet használja a regisztrációs folyamat során. |

Ha a tűzfal vagy a proxy lehetővé teszi a DNS engedélyezési listája konfigurálását, akkor engedélyezheti a \* . msappproxy.net és a \* . servicebus.Windows.net kapcsolatait.

Ha a teljes tartománynév nem engedélyezhető, és az IP-címtartományok megadására van szükség, használja a következő beállításokat:

* Az összekötő kimenő hozzáférésének engedélyezése az összes célhelyre.
* Az összekötő kimenő hozzáférésének engedélyezése az összes Azure Datacenter IP-tartományhoz. Az Azure Datacenter IP-címtartományok listájának használatával kapcsolatos kihívás az, hogy hetente frissül. Egy olyan folyamatot kell létrehoznia, amely biztosítja, hogy a hozzáférési szabályok megfelelően frissüljenek. A konfigurációt csak az IP-címek egy részhalmaza használatával lehet megszakítani. A legújabb Azure-adatközpont IP-címtartományok letöltéséhez navigáljon az [https://download.microsoft.com](https://download.microsoft.com) "Azure IP-címtartományok és szolgáltatás-címkék" kifejezésre. Ügyeljen arra, hogy a megfelelő felhőt válassza ki. A nyilvános Felhőbeli IP-címtartományok például az "Azure IP-címtartományok és a szolgáltatás-címkék – nyilvános felhő" című részben találhatók. Az Egyesült Államok kormányzati felhője az "Azure IP-címtartományok és szolgáltatás-címkék – USA kormányzati felhő" kifejezésre való kereséssel érhető el.

#### <a name="proxy-authentication"></a>Proxy hitelesítése

A proxy hitelesítés jelenleg nem támogatott. A jelenlegi Javaslatunk lehetővé teszi, hogy az összekötő névtelen hozzáférést biztosítson az internetes célhelyekhez.

#### <a name="proxy-ports"></a>Proxy portok

Az összekötő a kimenő TLS-alapú kapcsolatokat a KAPCSOLÓDÁSi módszer használatával teszi elérhetővé. Ez a metódus lényegében egy alagutat állít be a kimenő proxyn keresztül. Konfigurálja a proxykiszolgálót a 443-es és a 80-es portok bújtatásának engedélyezéséhez.

> [!NOTE]
> Ha Service Bus a HTTPS-en keresztül fut, a 443-es portot használja. Alapértelmezés szerint azonban Service Bus megkísérli a közvetlen TCP-kapcsolatokat, és csak akkor térhet vissza a HTTPS-re, ha a közvetlen kapcsolat meghiúsul.

#### <a name="tls-inspection"></a>TLS-ellenőrzés

Ne használjon TLS-vizsgálatot az összekötő forgalmához, mert problémákat okoz az összekötő forgalmához. Az összekötő tanúsítvány használatával hitelesíti az alkalmazásproxy szolgáltatását, és a tanúsítvány elvész a TLS-vizsgálat során.

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>Konfigurálás proxy használatával az összekötő és a háttérrendszer alkalmazása között
Egyes környezetekben a háttérbeli alkalmazás felé irányuló kommunikációhoz a továbbítási proxy használata különleges követelmény lehet.
Ennek engedélyezéséhez kövesse a következő lépéseket:

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>1. lépés: adja hozzá a szükséges beállításazonosító értékét a kiszolgálóhoz
1. Ha engedélyezni szeretné az alapértelmezett proxy használatát, adja hozzá a következő beállításértéket (DWORD) `UseDefaultProxyForBackendRequests = 1` a "HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft AAD App Proxy Connector" mappában található összekötő-konfigurációs beállításkulcs-kulcshoz.

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>2. lépés: a proxykiszolgáló manuális konfigurálása a netsh parancs használatával
1.  Engedélyezze, hogy a csoportházirend legyen a számítógép proxybeállításait. Ez a következő címen található: Computer Computer \ Windows-összetevők \ Internet Explorer. Ezt úgy kell beállítani, hogy ezt a házirendet ne kelljen felhasználónkénti értékre beállítani.
2.  Futtassa a parancsot a `gpupdate /force` kiszolgálón, vagy indítsa újra a kiszolgálót, és győződjön meg arról, hogy a frissített csoportházirend-beállításokat használja.
3.  Nyisson meg egy rendszergazda jogú parancssort rendszergazdai jogosultságokkal, és írja be a parancsot `control inetcpl.cpl` .
4.  Konfigurálja a szükséges proxybeállításokat. 

Ezek a beállítások teszik, hogy az összekötő ugyanazt a továbbítási proxyt használja az Azure-hoz és a háttérbeli alkalmazáshoz való kommunikációhoz. Ha az összekötő és az Azure közötti kommunikációhoz nem szükséges a továbbítási proxy vagy más továbbítási proxy, beállíthatja, hogy a fájl módosítása ApplicationProxyConnectorService.exe.config a kimenő proxyk megkerülése vagy a kimenő proxykiszolgáló használata részben leírtak szerint.

> [!NOTE]
> Az internetes proxy különböző módon konfigurálható az operációs rendszeren. A NETSH WINHTTP használatával konfigurált proxybeállítások (Futtatás `NETSH WINHTTP SHOW PROXY` az ellenőrzéshez) felülbírálják a 2. lépésben konfigurált proxybeállításokat. 

Az összekötő-frissítési szolgáltatás a Machine proxyt is használja majd. Ez a viselkedés módosítható a ApplicationProxyConnectorUpdaterService.exe.config fájl módosításával.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Az összekötő-proxyval kapcsolatos problémák elhárítása és a szolgáltatás csatlakozási problémái

Most látnia kell a proxyn keresztül áramló összes forgalmat. Ha problémák merülnek fel, a következő hibaelhárítási információk segíthetnek.

Az összekötők kapcsolódási problémáinak azonosítására és elhárítására a legjobb módszer, ha az összekötő szolgáltatás elindítása közben hálózati rögzítést végez. Íme néhány gyors tipp a hálózati nyomkövetés rögzítéséről és szűréséről.

Az Ön által választott figyelési eszközt használhatja. A cikk értelmében a Microsoft Message Analyzert használtuk.

> [!NOTE]
> A [Microsoft Message Analyzer (MMA) szolgáltatást a rendszer kivonta](/openspecs/blog/ms-winintbloglp/dd98b93c-0a75-4eb0-b92e-e760c502394f) , és a letöltési csomagjait a Microsoft.com-webhelyekről eltávolította a 25 2019. novemberre.  Jelenleg nem érhető el a Microsoft Message Analyzer Microsoft-beli cseréje a fejlesztés során.  Hasonló funkciók esetén vegye fontolóra a külső hálózati protokollok elemző eszközét, például a Wireshark.

Az alábbi példák az üzenetsor-kezelőre vonatkoznak, de az alapelvek bármely Analysis Tool eszközre alkalmazhatók.

### <a name="take-a-capture-of-connector-traffic"></a>Összekötő-forgalom rögzítésének elvégzése

A kezdeti hibaelhárításhoz hajtsa végre a következő lépéseket:

1. A Services. msc ablakból állítsa le az Azure AD Application Proxy Connector szolgáltatást.

   ![Azure AD Application Proxy Connector szolgáltatás a Services. msc-ben](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Futtassa a Message Analyzert rendszergazdaként.
1. Válassza a **helyi Nyomkövetés indítása** lehetőséget.
1. Indítsa el az Azure AD Application Proxy Connector szolgáltatást.
1. Állítsa le a hálózati rögzítést.

   ![A képernyőfelvétel a hálózati rögzítés leállítása gombra mutat.](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Ellenőrizze, hogy az összekötő-forgalom megkerüli-e a kimenő proxykat

Ha az alkalmazásproxy-összekötőt úgy konfigurálta, hogy kihagyja a proxykiszolgálót, és közvetlenül kapcsolódjon az alkalmazásproxy szolgáltatáshoz, akkor a sikertelen TCP-kapcsolódási kísérletek hálózati rögzítését kell megkeresnie.

A próbálkozások azonosításához használja az üzenetsor-elemző szűrőt. Írja be `property.TCPSynRetransmit` a szűrőt a szűrő mezőbe, és kattintson az **alkalmaz** gombra.

Egy SYN-csomag az első, TCP-kapcsolat létesítéséhez küldött csomag. Ha a csomag nem ad vissza választ, a rendszer újrapróbálkozik a SYN-vel. Az előző szűrő használatával megtekintheti az újraküldött SYNs. Ezután megtekintheti, hogy ezek a SYNs megfelelnek-e az összekötővel kapcsolatos adatforgalomnak.

Ha arra számít, hogy az összekötő közvetlen kapcsolattal csatlakozik az Azure-szolgáltatásokhoz, az 443-as porton SynRetransmit válaszok jelzik, hogy van hálózati vagy tűzfallal kapcsolatos probléma.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Ellenőrizze, hogy az összekötő forgalma kimenő proxykat használ-e

Ha az alkalmazásproxy-összekötő forgalmát úgy konfigurálta, hogy a proxykiszolgálót átugorja, a sikertelen HTTPS-kapcsolatokat szeretné keresni a proxyhoz.

Ha szűrni szeretné a kapcsolódási kísérletek hálózati rögzítését, írja be az `(https.Request or https.Response) and tcp.port==8080` üzenet-elemző szűrőt, és cserélje le a 8080-et a proxy Service-portra. Válassza az **alkalmaz** lehetőséget a szűrő eredményeinek megtekintéséhez.

Az előző szűrő csak a HTTPs-kérelmeket és a proxy portra küldött válaszokat jeleníti meg. A proxykiszolgáló kommunikációját bemutató csatlakozási kérelmeket keresi. A művelet sikeressége után egy HTTP OK (200) választ kap.

Ha más válasz kódokat (például 407 vagy 502) lát, ez azt jelenti, hogy a proxy hitelesítést igényel, vagy más okból nem engedélyezi a forgalmat. Ezen a ponton a proxykiszolgálót támogató csapatot kell felvennie.

## <a name="next-steps"></a>Következő lépések

* [Az Azure AD Application Proxy-összekötők ismertetése](application-proxy-connectors.md)
* Ha problémája van az összekötők kapcsolódási problémáinak megoldásával, kérdezze meg a [Microsoft Q&a kérdéses oldalt Azure Active Directory](/answers/topics/azure-active-directory.html) vagy hozzon létre egy jegyet a támogatási csapatával.