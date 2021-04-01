---
title: Az Azure Traffic Manager csökkentett teljesítményű állapotának elhárítása
description: A Traffic Manager-profilok hibakeresése, ha csökkentett teljesítményű állapotot mutat.
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: duau
ms.openlocfilehash: b76eab5771d724e4f0ec56b7d5acd5cf5f91edc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183455"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Az Azure Traffic Manager csökkentett teljesítményének hibaelhárítása

Ez a cikk a csökkentett teljesítményű állapotot mutató Azure Traffic Manager-profilok hibaelhárítását ismerteti. Az Azure-Traffic Manager csökkentett teljesítményének elhárításának első lépéseként engedélyezze a naplózást.  További információért tekintse meg az [erőforrás-naplók engedélyezése](./traffic-manager-diagnostic-logs.md) című témakört. Ebben az esetben vegye figyelembe, hogy konfigurált egy Traffic Manager profilt, amely a cloudapp.net üzemeltetett szolgáltatásaira mutat. Ha a Traffic Manager állapota **csökkentett teljesítményű** , akkor a rendszer egy vagy több végpont állapotát **csökkentheti:**

![csökkentett teljesítményű végpont állapota](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Ha a Traffic Manager állapota **inaktív** állapotot jelez, akkor előfordulhat, hogy mindkét végpont **le lesz tiltva**:

![Inaktív Traffic Manager állapota](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Traffic Manager mintavételek ismertetése

* Traffic Manager úgy véli, hogy egy végpont csak ONLINE állapotba kerül, ha a mintavétel HTTP 200-választ kap vissza a mintavételi útvonalról. Ha az alkalmazás bármely más HTTP-hibakódot ad vissza, akkor az adott hibakódot a Traffic Manager profiljának [várt állapotkód-tartományához](./traffic-manager-monitoring.md#configure-endpoint-monitoring) kell hozzáadnia.
* A 30x átirányítási válasza meghiúsul, hacsak nem adta meg ezt érvényes válaszként a Traffic Manager profiljának [várt állapotkód-tartományában](./traffic-manager-monitoring.md#configure-endpoint-monitoring) . Traffic Manager nem ellenőrzi az átirányítás célját.
* HTTPs-próbák esetén a rendszer figyelmen kívül hagyja a tanúsítvány hibáit.
* A mintavételi útvonal tényleges tartalma nem számít, feltéve, hogy a 200-as értéket adja vissza. Egy olyan statikus tartalom URL-címe, mint például a "/favicon.ico", egy gyakori módszer. Előfordulhat, hogy a dinamikus tartalom, például az ASP-lapok nem mindig adnak vissza 200-et, még akkor is, ha az alkalmazás kifogástalan állapotban van.
* Ajánlott eljárás a mintavételi útvonal beállítása olyan értékre, amely elegendő logikával rendelkezik annak megállapításához, hogy a hely fel vagy le van-e állítva. Az előző példában a "/favicon.ico" elérési út beállításával csak azt teszteli, hogy w3wp.exe válaszol-e. Ez a mintavétel nem utalhat arra, hogy a webalkalmazás kifogástalan állapotú. A jobb lehetőség egy olyan elérési út beállítása, mint például a "/Probe.aspx", amely a hely állapotának meghatározására szolgáló logikát tartalmaz. Használhat például teljesítményszámlálók használatával a CPU-kihasználtságot, vagy mérhetővé teheti a sikertelen kérések számát. Vagy megkísérelheti elérni az adatbázis-erőforrásokat vagy a munkamenet-állapotot, hogy meggyőződjön arról, hogy a webalkalmazás működik.
* Ha a profilban lévő összes végpontot csökkenti, akkor Traffic Manager az összes végpontot Kifogástalan állapotba kezeli, és az összes végpontra irányítja a forgalmat. Ez a viselkedés biztosítja, hogy a szondázás mechanizmussal kapcsolatos problémák ne eredményezik a szolgáltatás teljes kimaradását.

## <a name="troubleshooting"></a>Hibaelhárítás

A mintavételi hibák hibaelhárításához olyan eszközre van szükség, amely megjeleníti a mintavételi URL-címről visszaadott HTTP-állapotkódot. Számos eszköz áll rendelkezésre, amelyek a nyers HTTP-választ mutatják be.

* [Fiddler](https://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Emellett a HTTP-válaszok megtekintéséhez használhatja az F12 hibakereső eszközök hálózat lapját az Internet Explorerben.

Ebben a példában a mintavételi URL-címről érkező választ szeretnénk látni: http: \/ /watestsdp2008r2.cloudapp.net:80/Probe. A következő PowerShell-példa szemlélteti a problémát.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Példa a kimenetre:

```output
StatusCode StatusDescription
---------- -----------------
        301 Moved Permanently
```

Figyelje meg, hogy átirányítási választ kaptunk. Ahogy korábban már említettük, a 200-től eltérő StatusCode nem számít. Traffic Manager a végpont állapotát offline értékre módosítja. A probléma megoldásához tekintse meg a webhely konfigurációját, és győződjön meg arról, hogy a megfelelő StatusCode visszatérhet a mintavételi útvonalról. Konfigurálja újra az Traffic Manager mintavételt úgy, hogy az egy 200-as értéket visszaadó elérési útra mutasson.

Ha a mintavétel a HTTPS protokollt használja, előfordulhat, hogy le kell tiltania a tanúsítvány-ellenőrzést, hogy elkerülje az SSL/TLS-hibákat a tesztelés során. A következő PowerShell-utasítások letiltják a tanúsítvány érvényesítését az aktuális PowerShell-munkamenethez:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Következő lépések

[Tudnivalók a Traffic Manager forgalom-útválasztási módszerekről](traffic-manager-routing-methods.md)

[Mi az Traffic Manager?](traffic-manager-overview.md)

[Felhőszolgáltatások](/previous-versions/azure/jj155995(v=azure.100))

[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operations on Traffic Manager (A Traffic Managerrel végezhető műveletek) (REST API-referencia)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Azure Traffic Manager-parancsmagok][1]

[1]: /powershell/module/az.trafficmanager