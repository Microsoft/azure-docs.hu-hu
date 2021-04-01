---
title: Munkamenet-affinitással kapcsolatos problémák elhárítása
titleSuffix: Azure Application Gateway
description: Ez a cikk az Azure-beli munkamenet-affinitási problémák elhárításával kapcsolatos tudnivalókat ismerteti Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 548bda36ed2b167c159d32a575b63ecbf10b16dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397569"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Az Azure Application Gateway munkamenet-affinitással kapcsolatos problémák elhárítása

Ismerje meg, hogyan diagnosztizálhatja és megoldhatja a munkamenet-affinitással kapcsolatos problémákat az Azure Application Gatewayban.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

A Cookie-alapú munkamenet-affinitás akkor hasznos, ha egy felhasználói munkamenetet egy adott kiszolgálón szeretne tartani. Az átjáróval kezelt cookie-k használatával az Application Gateway képes egy felhasználói munkamenet minden újabb forgalmát ugyanarra a kiszolgálóra irányítani feldolgozásra. Ez a funkció olyan esetekben lehet fontos, amelyekben egy felhasználói munkamenethez tartozó munkamenet-állapotot helyileg ment a rendszer a kiszolgálón.

## <a name="possible-problem-causes"></a>Lehetséges problémák okai

A cookie-alapú munkamenet-affinitás fenntartásával kapcsolatos probléma a következő fő okok miatt fordulhat elő:

- A "cookie-alapú affinitás" beállítás nincs engedélyezve
- Az alkalmazás nem tudja kezelni a cookie-alapú affinitást
- Az alkalmazás cookie-alapú affinitást használ, de továbbra is pattog a háttér-kiszolgálók között

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>Győződjön meg arról, hogy engedélyezve van-e a "cookie-alapú affinitás" beállítás

Időnként előfordulhat, hogy a munkamenet-affinitási probléma akkor fordul elő, ha elfelejti a "cookie-alapú affinitás" beállítás engedélyezését. Az alábbi utasításokat követve megállapíthatja, hogy engedélyezte-e a "cookie-alapú affinitás" beállítást a Azure Portal HTTP-beállítások lapján.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).

2. A **bal oldali navigációs** panelen kattintson a **minden erőforrás** elemre. Kattintson az Application Gateway nevére a minden erőforrás panelen. Ha a kiválasztott előfizetés már rendelkezik több erőforrással is, megadhatja az Application Gateway nevét a **szűrés név szerint..** . mezőbe.

3. A **Beállítások** területen válassza a **http-beállítások** lapot.

   ![A képernyőképen a kiválasztott H t P-beállításokkal rendelkező beállítások láthatók.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. Kattintson a jobb oldalon található **appGatewayBackendHttpSettings** elemre annak megadásához, hogy **engedélyezve** van-e a cookie-alapú affinitáshoz.

   ![Képernyőfelvétel: az alkalmazás-átjáró inlcuidng, hogy ki van-e választva a cookie-alapú affinitás.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



Azt is megteheti, hogy a "**CookieBasedAffinity**" értéke "**backendHttpSettingsCollection**" értékre *van állítva* az alábbi módszerek egyikével:

- A [Get-AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) futtatása a PowerShellben
- A JSON-fájl áttekintése a Azure Resource Manager sablon használatával

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>Az alkalmazás nem tudja kezelni a cookie-alapú affinitást.

#### <a name="cause"></a>Ok

Az Application Gateway csak a munkamenet-alapú affinitást tudja végrehajtani egy cookie használatával.

#### <a name="workaround"></a>Áthidaló megoldás

Ha az alkalmazás nem tudja kezelni a cookie-alapú affinitást, akkor külső vagy belső Azure Load balancert vagy más külső gyártótól származó megoldást kell használnia.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>Az alkalmazás cookie-alapú affinitást használ, de továbbra is pattog a háttér-kiszolgálók között

#### <a name="symptom"></a>Hibajelenség

Engedélyezte a cookie-alapú affinitás beállítását, amikor a Application Gateway egy rövid név URL-címével éri el az Internet Explorerben, például: `http://website` a kérés továbbra is pattogó a háttér-kiszolgálók között.

A probléma azonosításához kövesse az alábbi utasításokat:

1. Vegyen fel egy web Debugger nyomkövetést az "ügyfélre", amely a Application Gateway mögött található alkalmazáshoz csatlakozik (ebben a példában a hegedűst használjuk).
    **Tipp** Ha nem tudja, hogyan használhatja a hegedűst, ellenőrizze a "**hálózati forgalom összegyűjtését és a web Debugger használatával történő elemzését**" lehetőséget a lap alján.

2. Ellenőrizze és elemezze a munkamenetek naplóit annak megállapításához, hogy az ügyfél által biztosított cookie-k rendelkeznek-e a ARRAffinity részleteivel. Ha nem találja a ARRAffinity részleteit (például "**ARRAffinity =** *ARRAffinityValue*") a cookie-készleten belül, az azt jelenti, hogy az ügyfél nem válaszol az arra szolgáló cookie-val, amelyet a Application Gateway biztosít.
    Például:

    ![Képernyőfelvétel: a munkamenet-napló egyetlen bejegyzéssel van kiemelve.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![A képernyőképen a H T T P-hez tartozó kérelmek fejlécei láthatók, beleértve a cookie-információkat is.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

Az alkalmazás továbbra is megpróbálja beállítani a cookie-t minden kérelemre, amíg a válasz nem lesz.

#### <a name="cause"></a>Ok

Ez a probléma azért fordul elő, mert az Internet Explorer és más böngészők esetleg nem tárolják vagy használják a cookie-t egy rövid név URL-címével.

#### <a name="resolution"></a>Feloldás

A probléma kijavításához az Application Gatewayhez FQDN-nel férjen hozzá. Használja például a [http://website.com](https://website.com/) vagy a [http://appgw.website.com](http://website.com/) .

## <a name="additional-logs-to-troubleshoot"></a>További hibakeresési naplók

További naplókat is gyűjthet, és elemezheti őket a cookie-alapú munkamenet-affinitással kapcsolatos problémák elhárítása érdekében.

### <a name="analyze-application-gateway-logs"></a>Application Gateway naplók elemzése

A Application Gateway naplók összegyűjtéséhez kövesse az alábbi utasításokat:

Naplózás engedélyezése az Azure Portalon

1. A [Azure Portal](https://portal.azure.com/)keresse meg az erőforrást, majd kattintson a **diagnosztikai naplók** elemre.

   Application Gateway esetében három napló érhető el: hozzáférési napló, Teljesítménynapló, tűzfal napló

2. Az adatok gyűjtésének megkezdéséhez kattintson **a diagnosztika bekapcsolása** elemre.

   ![A képernyőképen egy olyan Application Gateway látható, amelyen diagnosztikai naplók vannak kiválasztva.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. A **diagnosztika beállításai** panel a diagnosztikai naplók beállításait tartalmazza. Ebben a példában a Log Analytics tárolja a naplókat. A munkaterület beállításához kattintson a **log Analytics** alatt a **Konfigurálás** elemre. Eseményközpontot és tárfiókot is használhat a diagnosztikai naplók mentésére.

   ![Képernyőfelvétel: a diagnosztikai beállítások ablaktábla, amelyen Log Analytics a konfigurálás van kiválasztva.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. Erősítse meg a beállításokat, majd kattintson a **Mentés** gombra.

   ![Képernyőfelvétel: a diagnosztikai beállítások ablaktábla a kiválasztott mentéssel.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>A Application Gateway hozzáférési naplók megtekintése és elemzése

1. A Application Gateway erőforrás nézet alatti Azure Portal válassza a **figyelés** szakaszban a **diagnosztikai naplók** elemet.

   ![A képernyőfelvételen a kiválasztott diagnosztikai naplók FIGYELÉSe látható.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. A jobb oldalon válassza a "**ApplicationGatewayAccessLog**" elemet a legördülő listában a **naplózási kategóriák területen.**  

   ![A képernyőképen a naplók legördülő listája látható a kijelölt ApplicationGatewayAccessLog.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. A Application Gateway hozzáférési napló listában kattintson az elemezni és exportálni kívánt naplóra, majd exportálja a JSON-fájlt.

4. Konvertálja a 3. lépésben exportált JSON-fájlt CSV-fájlba, és megtekintheti őket az Excelben, Power BI vagy bármely más adatvizualizációs eszközben.

5. Győződjön meg a következő adatfájlokról:

- **Ügyfélip**– ez az ügyfél IP-címe a csatlakozó ügyfélről.
- **ClientPort** – ez a forrás port a kéréshez kapcsolódó ügyfélről.
- **RequestQuery** – ez a célkiszolgáló azt jelzi, hogy a kérés érkezett.
- **Kiszolgáló által átirányított**: a kérelem fogadására szolgáló háttérbeli készlet.
- **X-AzureApplicationGateway-log-ID**: a kérelemhez használt korrelációs azonosító. Felhasználható a háttér-kiszolgálók forgalmával kapcsolatos problémák elhárítására. Például: X-AzureApplicationGateway-CACHE-HIT = 0&SERVER-ROUTEd = 10.0.2.4.

  - **Server-status**: a háttérből Application Gateway fogadott http-válasz kódja.

  ![Képernyőfelvétel: a kiszolgáló állapota egyszerű szövegben, többnyire kitakarva, a clientPort és a kiszolgáló által irányított Kiemelt értékkel.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

Ha két elem jelenik meg ugyanabból a Ügyfélip és ügyfél-portból, és ugyanarra a háttér-kiszolgálóra érkezik, az azt jelenti, hogy a Application Gateway megfelelően van konfigurálva.

Ha két elem jelenik meg ugyanabból a Ügyfélip és az ügyfél-portból, és a rendszer elküldi őket a különböző háttér-kiszolgálókra, az azt jelenti, hogy a kérés a háttér-kiszolgálók között található, válassza az "**alkalmazás cookie-alapú affinitás használata, de a háttér-kiszolgálók között még mindig pattogás**" lehetőséget, hogy hárítsa el.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>A HTTP-vagy HTTPS-forgalom rögzítése és elemzése a webes hibakereső használatával

A webes hibakeresési eszközök, például a Hegedűs, a webalkalmazások hibakeresését segítik az Internet és a tesztelési számítógépek közötti hálózati forgalom rögzítésével. Ezek az eszközök lehetővé teszik a bejövő és kimenő adatvizsgálatok megtekintését, ahogy a böngésző megkapja/elküldi azokat. Hegedűs, ebben a példában a HTTP-visszajátszás lehetőséggel rendelkezik, amely segítséget nyújt a webalkalmazásokkal kapcsolatos ügyféloldali problémák megoldásában, különösen a probléma hitelesítése során.

Használja az Ön által választott webes hibakeresőt. Ebben a példában a hegedűst fogjuk használni a http-vagy https-forgalom rögzítéséhez és elemzéséhez, kövesse az alábbi utasításokat:

1. A Hegedűs eszköz letöltése a következő címen: <https://www.telerik.com/download/fiddler> .

    > [!NOTE]
    > Válassza a Fiddler4 lehetőséget, ha a rögzítési számítógép .NET 4-es szervizcsomaggal rendelkezik. Ellenkező esetben válassza a Fiddler2 lehetőséget.

2. A telepítéshez kattintson a jobb gombbal a telepítő végrehajtható fájlra, majd a Futtatás rendszergazdaként parancsra.

    ![Képernyőfelvétel: a Hegedűs eszköz telepítőprogramja egy környezetfüggő menüvel, amelyen a futtató rendszergazda van kiválasztva.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. A Hegedűs megnyitásakor automatikusan el kell kezdenie a forgalom rögzítését (figyelje meg a rögzítést a bal alsó sarokban). Nyomja meg az F12 billentyűt a forgalom rögzítésének indításához vagy leállításához.

    ![Képernyőfelvétel: a Hegedűs webes hibakeresője a rögzítési mutató kiemelésével.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. Legvalószínűbb, hogy a rendszer visszafejti a https-forgalmat, és engedélyezheti a https-titkosítást a **Tools**  >  **Hegedűs beállításainak** kiválasztásával, és a " **https-forgalom visszafejtése**" jelölőnégyzet bejelölésével.

    ![A képernyőképen a megjelenő, H T T P-vel rendelkező Hegedűs lehetőségei láthatók, és visszafejti a kiválasztott HTTPS-forgalmat](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. A probléma reprodukálása előtt távolítsa el a korábbi nem kapcsolódó munkameneteket, és kattintson az  **X** (ikon) > az **összes eltávolítása** gombra a következő képernyőképen: 

    ![A képernyőképen a kiválasztott X ikon látható, amely megjeleníti az összes eltávolítása lehetőséget.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. A probléma reprodukálása után mentse a fájlt véleményezésre, **és válassza a**  >    >  **minden munkamenet mentése..**. lehetőséget. 

    ![A képernyőfelvételen a minden munkamenet mentése lehetőség be van jelölve.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. Ellenőrizze és elemezze a munkamenetek naplóit a probléma okának meghatározásához.

    Példák:

- **Példa:** Megtalálhatja azt a munkamenet-naplót, amelyet a kérelem elküld az ügyféltől, és a Application Gateway nyilvános IP-címére kerül, a részletek megtekintéséhez kattintson erre a naplóra.  A jobb oldalon az alsó mezőben lévő adat a Application Gateway, amelyet a rendszer az ügyfélnek ad vissza. Válassza a "RAW" fület, és állapítsa meg, hogy az ügyfél kap-e "**Set-Cookie: ARRAffinity =** *ARRAffinityValue*". Ha nincs cookie, a munkamenet-affinitás nincs beállítva, vagy a Application Gateway nem alkalmazza a cookie-t az ügyfélre.

   > [!NOTE]
   > Ez a ARRAffinity érték a cookie-azonosító, amelyet az ügyfél egy adott háttér-kiszolgálónak való elküldésekor Application Gateway állít be.

   ![A képernyőképen egy olyan naplóbejegyzés látható, amelyben a Set-Cookie érték van kiemelve.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **B példa:** A következő munkamenet-naplót, amelyet az előző követ, az ügyfél visszaválaszol a Application Gatewayra, amelyik beállította a ARRAAFFINITY. Ha a ARRAffinity cookie-azonosítója megegyezik, a csomagot a korábban használt háttér-kiszolgálónak kell elküldeni. Tekintse át a http-kommunikáció következő néhány sorát, és ellenőrizze, hogy módosul-e az ügyfél ARRAffinity-cookie-je.

   ![A képernyőképen egy példa látható egy, a cookie-t kiemelő naplóbejegyzés részleteire.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> Ugyanahhoz a kommunikációs munkamenethez a cookie-nak nem szabad megváltoztatnia. Jelölje be a jobb oldalon található felső négyzetet, és válassza a "cookie-k" fület, és ellenőrizze, hogy az ügyfél használja-e a cookie-t, majd küldje vissza a Application Gateway. Ha nem, az ügyfél böngészője nem tartja és nem használja a cookie-t a beszélgetésekhez. Időnként előfordulhat, hogy az ügyfél hazudik.

 

## <a name="next-steps"></a>Következő lépések

Ha a fenti lépések nem oldják meg a problémát, nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/).