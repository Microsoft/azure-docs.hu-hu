---
title: Az Azure Application Gateway állapot-figyelésének áttekintése
description: Az Azure Application Gateway figyeli a háttér-készlet összes erőforrásának állapotát, és automatikusan eltávolítja a készletből a nem megfelelőnek ítélt erőforrásokat.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: f0e5a153efe26640e54f386600f07c7b3d4711d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89649070"
---
# <a name="application-gateway-health-monitoring-overview"></a>Application Gateway állapot figyelésének áttekintése

Az Azure Application Gateway alapértelmezés szerint a háttér-készlet összes erőforrásának állapotát figyeli, és automatikusan eltávolítja a készletből a nem megfelelőnek ítélt erőforrásokat. Az Application Gateway továbbra is figyeli a nem megfelelő állapotú példányokat, és ha elérhetővé válnak, és megfelelő választ adnak az állapotfelmérésre, akkor visszahelyezi őket a megfelelő állapotú háttérrendszeri készletbe. Alapértelmezés szerint az Application Gateway a háttérbeli HTTP-beállításokban meghatározott porttal küldi el az állapot-ellenőrzéseket. Az egyéni mintavételi portok egyéni állapotú mintavétel használatával konfigurálhatók.

Az állapot-mintavételhez használt forrás IP-cím Application Gateway a háttér-készlettől függ:
 
- Ha a háttér-készlet kiszolgálójának címe nyilvános végpont, akkor a forráscím az Application Gateway előtér-nyilvános IP-címe.
- Ha a háttér-készlet kiszolgálójának címe privát végpont, akkor a forrás IP-cím az Application Gateway alhálózatának magánhálózati IP-címéből származik.

![Application Gateway mintavételi példa][1]

Amellett, hogy az alapértelmezett állapot mintavételi figyelését is használja, testre is szabhatja az állapot-mintavételt az alkalmazás követelményeinek megfelelően. Ebben a cikkben az alapértelmezett és az egyéni állapot-mintavétel is szerepel.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Alapértelmezett állapot mintavétele

Az Application Gateway automatikusan beállítja az alapértelmezett állapot-mintavételt, ha nem állít be egyéni mintavételi konfigurációt. A figyelési viselkedés úgy működik, hogy HTTP GET kérelmet küld a háttér-készletben konfigurált IP-címekre vagy teljes tartománynévre. Az alapértelmezett mintavétel esetén, ha a háttérbeli http-beállítások HTTPS-re vannak konfigurálva, a mintavétel HTTPS-t használ a háttér-kiszolgálók állapotának teszteléséhez.

Például: úgy konfigurálhatja az Application Gatewayt, hogy az A, B és C háttér-kiszolgálókat használja a HTTP hálózati forgalom fogadásához a 80-es porton. Az alapértelmezett állapotfigyelő szolgáltatás 30 másodpercenként 30 másodpercenként teszteli a három kiszolgálót egy kifogástalan HTTP-válaszhoz, az egyes kérelmek esetében pedig 30 másodperces időkorlátot. Egy kifogástalan HTTP-válasz 200 és 399 közötti [állapotkódot](https://msdn.microsoft.com/library/aa287675.aspx) tartalmaz. Ebben az esetben az állapotfigyelő szolgáltatáshoz tartozó HTTP GET-kérelem úgy fog kinézni, mint http://127.0.0.1/ .

Ha az A kiszolgáló esetében nem sikerül az alapértelmezett mintavétel-ellenőrzés, az Application Gateway leállítja a kérések továbbítását erre a kiszolgálóra. Az alapértelmezett mintavétel továbbra is mindig 30 másodpercenként ellenőrzi a kiszolgálót. Ha az A kiszolgáló sikeresen válaszol egy alapértelmezett állapot-mintavételi kérelemre, az Application Gateway újra továbbítja a kéréseket a kiszolgálónak.

### <a name="default-health-probe-settings"></a>Alapértelmezett állapot mintavételi beállításai

| Mintavételi tulajdonság | Érték | Leírás |
| --- | --- | --- |
| Mintavételi URL-cím |\<protocol\>://127.0.0.1:\<port\>/ |A protokoll és a port örökölt a háttérbeli HTTP-beállításoktól, amelyekhez a mintavétel társítva van |
| Időköz |30 |Az az időtartam másodpercben, ameddig a következő állapotú mintavétel elküldése előtt várni kell.|
| Időtúllépés |30 |Az az időtartam másodpercben, ameddig az Application Gateway megvárja a mintavételi választ, mielőtt a mintavételt nem Kifogástalan állapotba kívánja megjelölni. Ha a mintavétel kifogástalan állapotot ad vissza, a megfelelő háttér azonnal kifogástalan állapotú lesz.|
| Nem kifogástalan állapot küszöbértéke |3 |Azt szabályozza, hogy hány mintavételt kell elküldeni, ha hiba történt a normál állapotú mintavétel során. A v1 SKU-ban a további állapot-mintavételek gyors egymásutánban lesznek elvégezve a háttér állapotának megállapításához, és ne várjon a mintavételi intervallumra. V2 SKU esetén az állapot-mintavétel megvárja az intervallumot. A háttér-kiszolgáló az egymást követő mintavételi hibák számának elérésekor a nem megfelelő állapotú küszöbértéket éri el. |

Az alapértelmezett mintavétel csak a következő helyen \<protocol\> jelenik \/ meg:/127.0.0.1: \<port\> az állapot meghatározásához. Ha az állapot-mintavételt úgy kell konfigurálnia, hogy az egyéni URL-címre váltson, vagy más beállításokat módosítson, egyéni mintavételt kell használnia. A HTTPS-tesztekkel kapcsolatos további információkért lásd: [a TLS-megszakítás áttekintése és a végpontok közötti TLS és a Application Gateway](ssl-overview.md#for-probe-traffic).

### <a name="probe-intervals"></a>Mintavételi időközök

Application Gateway mintavétel minden példánya egymástól független. Ugyanaz a mintavételi konfiguráció az egyes Application Gateway példányokra vonatkozik. Ha például a mintavételi konfiguráció 30 másodpercenként elküldi az állapot-mintavételt, és az Application Gateway két példányban van, akkor mindkét példány 30 másodpercenként elküldi az állapot-mintavételt.

Ha több figyelő is van, akkor minden figyelő a háttértől függetlenül ellenőrzi a hátteret. Ha például két figyelő ugyanarra a háttér-készletre mutat két különböző porton (két háttérbeli http-beállítással konfigurálva), akkor minden figyelő egymástól függetlenül ellenőrzi a hátteret. Ebben az esetben a két figyelő mindegyik Application Gateway-példánya két mintavételt biztosít. Ha ebben az esetben az Application Gateway két példányban szerepel, a háttérbeli virtuális gép a beállított mintavételi intervallumban négy mintavételt fog látni.

## <a name="custom-health-probe"></a>Egyéni állapot mintavétele

Az egyéni mintavételek lehetővé teszik, hogy részletesebben szabályozható legyen az állapot figyelése. Egyéni mintavételek használatakor beállíthatja az egyéni állomásnév, az URL-cím és a mintavételi időköz értékét, valamint azt, hogy hány sikertelen választ fogad el a rendszer, mielőtt a háttér-készlet példányát nem Kifogástalan állapotba állítja, stb.

### <a name="custom-health-probe-settings"></a>Egyéni állapot mintavételi beállításai

Az alábbi táblázat az egyéni állapotú mintavétel tulajdonságaira vonatkozó definíciókat tartalmazza.

| Mintavételi tulajdonság | Leírás |
| --- | --- |
| Név |A mintavétel neve. Ez a név azonosítja a mintavételt a háttérbeli HTTP-beállításokban, és hivatkozik rá. |
| Protokoll |A mintavétel küldéséhez használt protokoll. Ennek egyeznie kell az ahhoz a háttérbeli HTTP-beállításokban definiált protokollal, amelyhez hozzá van rendelve.|
| Gazdagép |Az az állomásnév, amellyel a mintavételt el lehet küldeni. A v1 SKU-ban ezt az értéket csak a mintavételi kérelem állomásneve fogja használni. A v2 SKU-ban a rendszer a gazdagép fejlécét és a SNI is használja. |
| Elérési út |A mintavétel relatív elérési útja. A "/" karakterrel kezdődő érvényes elérési út |
| Port |Ha meg van adva, a rendszer a célként megadott portot használja. Ellenkező esetben ugyanazt a portot használja, mint a hozzá társított HTTP-beállítások. Ez a tulajdonság csak a v2 SKU-ban érhető el
| Időköz |Mintavételi időköz másodpercben. Ez az érték a két egymást követő mintavétel közötti időtartam. |
| Időtúllépés |Mintavétel időtúllépése másodpercben. Ha nem érkezik érvényes válasz ezen az időkorláton belül, a mintavétel sikertelenként van megjelölve  |
| Nem kifogástalan állapot küszöbértéke |Újrapróbálkozások száma. A háttér-kiszolgáló akkor van megjelölve, ha az egymást követő mintavételi hibák száma eléri a nem kifogástalan állapotot. |

### <a name="probe-matching"></a>Mintavétel egyeztetése

Alapértelmezés szerint a 200 és 399 közötti állapotkódot biztosító HTTP (S) válaszok kifogástalannak számítanak. Az egyéni állapotú mintavételek emellett két megfelelő feltételt is támogatnak. Az egyeztetési feltételekkel opcionálisan módosítható az az alapértelmezett értelmezés, hogy mi teszi az egészséges választ.

Az alábbi feltételeknek megfelelő feltételek: 

- **Http-válasz állapotkód egyeztetése** – mintavétel egyeztetési feltétele a felhasználó által megadott http-válasz kód vagy válasz kód tartományának elfogadásához. A vesszővel elválasztott válaszokhoz tartozó állapotkódok vagy az állapotkód egy tartománya támogatott.
- **Http-válasz törzsének egyeztetése** – a mintavételi egyeztetési feltétel, amely a http-válasz törzsét keresi, és megegyezik egy felhasználó által megadott karakterlánccal. Az egyeztetés csak a felhasználó által megadott karakterlánc jelenlétét keresi a válasz törzsében, és nem teljes reguláris kifejezésnek felel meg.

Az egyeztetési feltételek a parancsmag használatával adhatók meg `New-AzApplicationGatewayProbeHealthResponseMatch` .

Például:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Miután megadta a megfeleltetési feltételeket, a PowerShellben található paraméterrel lehet csatolni a mintavételi konfigurációhoz `-Match` .

## <a name="nsg-considerations"></a>NSG szempontok

Engedélyeznie kell a bejövő internetes forgalmat a 65503-65534 **-as TCP** -portokon a Application Gateway v1 SKU-hoz, valamint a 65200-65535-es TCP-portokat a v2 SKU-hoz a célként megadott alhálózattal a **GatewayManager** szolgáltatás címkéjének megfelelően. Ez a porttartomány az Azure-infrastruktúra kommunikációja esetén szükséges.

Emellett a kimenő internetkapcsolatot nem lehet letiltani, a **AzureLoadBalancer** címkétől érkező bejövő forgalmat pedig engedélyezni kell.

További információ: [Application Gateway konfiguráció áttekintése](configuration-infrastructure.md#network-security-groups).

## <a name="next-steps"></a>Következő lépések
Miután megtanulta Application Gateway Health monitoringot, beállíthatja az [Egyéni állapot](application-gateway-create-probe-portal.md) -mintavételt a Azure Portalban vagy egy [Egyéni állapot](application-gateway-create-probe-ps.md) -mintavételt a PowerShell és a Azure Resource Manager üzembe helyezési modell használatával.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
