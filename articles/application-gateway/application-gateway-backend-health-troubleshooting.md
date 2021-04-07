---
title: A háttér állapotával kapcsolatos problémák elhárítása az Azure-ban Application Gateway
description: Leírja, hogyan lehet elhárítani az Azure-Application Gateway háttérbeli állapotával kapcsolatos problémákat
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 95b74e5fc6c5d2c09ff04b3f14e920ae675ab6e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592755"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>A Application Gateway háttérbeli állapotával kapcsolatos problémák elhárítása
==================================================

<a name="overview"></a>Áttekintés
--------

Alapértelmezés szerint az Azure Application Gateway mintavételek háttér-kiszolgálókon ellenőrzi az állapotukat, és ellenőrzi, hogy készen állnak-e a kérelmek kiszolgálására. A felhasználók létrehozhatnak egyéni mintavételeket is, amelyek megemlítik az állomásnév nevét, a mintavételhez szükséges elérési utat, valamint az állapot kódokat, amelyeket Kifogástalanként kell elfogadni. Minden esetben, ha a háttérrendszer-kiszolgáló nem válaszol sikeresen, Application Gateway megjelöli a kiszolgálót nem megfelelő állapotba, és leállítja a kérelmek továbbítását a kiszolgálónak. A kiszolgáló sikeres megválaszolása után Application Gateway folytatja a kérések továbbítását.

### <a name="how-to-check-backend-health"></a>A háttér állapotának ellenõrzése

A háttérbeli készlet állapotának ellenőrzését a Azure Portal **háttér állapota** lapján végezheti el. A [Azure PowerShell](/powershell/module/az.network/get-azapplicationgatewaybackendhealth), a [CLI](/cli/azure/network/application-gateway#az-network-application-gateway-show-backend-health)vagy a [REST API](/rest/api/application-gateway/applicationgateways/backendhealth)is használható.

Az alábbi módszerek bármelyikével lekért állapot a következők egyike lehet:

- Kifogástalan

- Nem kifogástalan

- Ismeretlen

Ha a kiszolgáló háttér-állapotának állapota Kifogástalan, az azt jelenti, hogy Application Gateway továbbítja a kéréseket a kiszolgálónak. Ha azonban egy háttér-készlet összes kiszolgálójának háttér-állapota nem kifogástalan vagy ismeretlen, akkor problémák léphetnek fel, amikor megpróbál hozzáférni az alkalmazásokhoz. Ez a cikk az egyes megjelenített hibák tüneteit, okát és megoldásait ismerteti.

<a name="backend-health-status-unhealthy"></a>Háttér állapotának állapota: sérült
-------------------------------

Ha a háttér állapot állapota nem megfelelő, a portál nézet a következő képernyőképre fog hasonlítani:

![Application Gateway háttér állapota – nem megfelelő állapotú](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Ha Azure PowerShell, CLI vagy Azure REST API lekérdezést használ, a következőhöz hasonló választ kap:
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```
Miután a háttérbeli készlet összes kiszolgálójára vonatkozóan egy nem kifogástalan állapotú háttér-kiszolgáló állapota jelenik meg, a rendszer nem továbbítja a kérelmeket a kiszolgálókra, és Application Gateway "502 hibás átjáró" hibát ad vissza a kérelmező ügyfélnek. A probléma megoldásához keresse meg a **háttér állapota** lap **részletek** oszlopát.

A **részletek** oszlopban megjelenő üzenet részletesebb információkat nyújt a problémáról, és ezek alapján megkezdheti a probléma elhárítását.

> [!NOTE]
> Az alapértelmezett mintavételi kérelmet \<protocol\> ://127.0.0.1:/. formátumban küldi el a rendszer \<port\> . Például http://127.0.0.1:80 egy http-mintavétel esetén a 80-es porton. Csak a 200 és 399 közötti HTTP-állapotkódok kifogástalannak tekintendők. A protokoll és a célport a HTTP-beállításoktól örökölt. Ha azt Application Gateway szeretné, hogy a mintavétel egy másik protokollon, állomásnéven vagy elérési úton történjen, és egy másik állapotkódot ismer fel Kifogástalan állapotba, állítson be egy egyéni mintavételt, és társítsa a HTTP-beállításokhoz.

<a name="error-messages"></a>Hibaüzenetek
------------------------
#### <a name="backend-server-timeout"></a>Háttér-kiszolgáló időtúllépése

**Üzenet:** A háttér által az Application Gateway s Health mintavételre való reagáláshoz szükséges idő meghaladja a mintavételi \' beállítás időtúllépési küszöbértékét.

**OK:** Miután Application Gateway elküld egy HTTP (S) mintavételi kérelmet a háttér-kiszolgálónak, megvárja a háttér-kiszolgáló válaszát egy konfigurált időszakra. Ha a háttérrendszer-kiszolgáló nem válaszol a beállított időtartamon belül (az időtúllépési érték), akkor azt a rendszer nem megfelelőként jelöli meg, amíg újra nem válaszol a beállított időtúllépési időszakon belül.

**Megoldás:** Győződjön meg arról, hogy a háttér-kiszolgáló vagy az alkalmazás miért nem válaszol a beállított időkorláton belül, és tekintse meg az alkalmazás függőségeit is. Például megtudhatja, hogy az adatbázis rendelkezik-e olyan problémával, amely a válasz késleltetését kiváltó lehet. Ha ismeri az alkalmazás viselkedését, és csak az időtúllépési érték után válaszol, növelje az időtúllépési értéket az egyéni mintavételi beállítások alapján. Az időtúllépési érték módosításához egyéni mintavétel szükséges. Az egyéni mintavétel konfigurálásával kapcsolatos információkért [tekintse meg a dokumentációs oldalt](./application-gateway-create-probe-portal.md).

Az időtúllépési érték növeléséhez kövesse az alábbi lépéseket:

1.  Közvetlenül elérheti a háttér-kiszolgálót, és megvizsgálhatja, hogy a kiszolgáló milyen időpontban válaszol az adott oldalon. Bármilyen eszközt használhat a háttér-kiszolgáló eléréséhez, beleértve a fejlesztői eszközöket használó böngészőt is.

1.  Miután kitalálta az alkalmazás válaszához szükséges időt, válassza az **állapot** -mintavételek lapot, majd válassza ki a http-beállításokhoz társított mintavételt.

1.  Adja meg az időtúllépési értéket, amely nagyobb, mint az alkalmazás válaszideje (másodpercben).

1.  Mentse az egyéni mintavételi beállításokat, és győződjön meg arról, hogy a háttér állapota most kifogástalan állapotú-e.

#### <a name="dns-resolution-error"></a>DNS-feloldási hiba

**Üzenet:** A Application Gateway nem tudott mintavételt létrehozni ehhez a háttérhöz. Ez általában akkor fordul elő, ha a háttérrendszer teljes tartományneve nem lett megfelelően megadva. 

**OK:** Ha a háttér-készlet IP-cím/FQDN vagy App Service típusú, Application Gateway a tartománynévrendszer (DNS) (egyéni vagy Azure alapértelmezett) által megadott teljes tartománynév IP-címére lesz feloldva, és megpróbál csatlakozni a kiszolgálóhoz a HTTP-beállításokban említett TCP-porton. Ha azonban ez az üzenet jelenik meg, azt javasolja, hogy Application Gateway nem tudta sikeresen feloldani a megadott teljes tartománynév IP-címét.

**Megoldás:**

1.  Ellenőrizze, hogy a háttér-készletben megadott teljes tartománynév helyes-e, és hogy a nyilvános tartomány-e, majd próbálja meg feloldani a helyi gépről.

1.  Ha feloldja az IP-címet, akkor előfordulhat, hogy a virtuális hálózat DNS-konfigurációja hibás.

1.  Győződjön meg arról, hogy a virtuális hálózat egyéni DNS-kiszolgálóval van konfigurálva. Ha igen, ellenőrizze a DNS-kiszolgálót arról, hogy miért nem oldható fel a megadott FQDN IP-címére.

1.  Ha az Azure alapértelmezett DNS-t használja, egyeztessen a tartománynév-regisztrálóval arról, hogy megfelelő-e A rekord vagy CNAME rekord leképezése.

1.  Ha a tartomány magán-vagy belső, próbálja meg feloldani az azonos virtuális hálózatban lévő virtuális gépről. Ha meg tudja oldani, indítsa újra Application Gateway és ellenőrizze újra. Application Gateway újraindításához [le kell állítania](/powershell/module/azurerm.network/stop-azurermapplicationgateway) és [el kell indítania](/powershell/module/azurerm.network/start-azurermapplicationgateway) a kapcsolódó erőforrásokban leírt PowerShell-parancsokat.

#### <a name="tcp-connect-error"></a>TCP-csatlakozási hiba

**Üzenet:** Application Gateway nem tudott csatlakozni a háttérhez.
Győződjön meg arról, hogy a háttér válaszol a mintavételhez használt porton.
Győződjön meg arról is, hogy a NSG/UDR/tűzfal blokkolja-e a háttér IP-címét és portját.

**OK:** A DNS-feloldási fázis után Application Gateway megpróbál csatlakozni a háttér-kiszolgálóhoz a HTTP-beállításokban konfigurált TCP-porton. Ha Application Gateway nem tud TCP-munkamenetet létesíteni a megadott porton, akkor a mintavétel nem megfelelőként van megjelölve az üzenettel.

**Megoldás:** Ha ezt a hibaüzenetet kapja, kövesse az alábbi lépéseket:

1.  A böngésző vagy a PowerShell használatával győződjön meg arról, hogy tud-e csatlakozni a háttér-kiszolgálóhoz a HTTP-beállításokban említett porton. Futtassa például a következő parancsot: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Ha a megadott port nem a kívánt port, a háttér-kiszolgálóhoz való csatlakozáshoz adja meg a Application Gateway megfelelő portszámot.

1.  Ha a helyi gépről nem tud kapcsolatot létesíteni a porttal, akkor:

    a.  Keresse meg a háttér-kiszolgáló hálózati adapterének és alhálózatának hálózati biztonsági csoport (NSG) beállításait, valamint azt, hogy engedélyezett-e a bejövő kapcsolatok a konfigurált porton. Ha nem, hozzon létre egy új szabályt a kapcsolatok engedélyezéséhez. A NSG-szabályok létrehozásával kapcsolatos további információkért [tekintse meg a dokumentációs oldalt](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules).

    b.  Győződjön meg arról, hogy a Application Gateway alhálózat NSG beállításai engedélyezik-e a kimenő nyilvános és magánhálózati forgalmat, így a kapcsolatok elhelyezhetők. A NSG-szabályok létrehozásával kapcsolatos további információkért tekintse meg a 3a. lépésben megadott dokumentum oldalát.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  A Application Gateway és a háttérrendszer-kiszolgáló alhálózatának felhasználói útvonalak (UDR) beállításainak megadásával bármely útválasztási rendellenesség esetén. Győződjön meg arról, hogy a UDR nem irányítja át a forgalmat a háttér-alhálózatból. Keressen például útvonalakat a hálózati virtuális berendezésekhez, vagy az alapértelmezett útvonalakat az Azure ExpressRoute és/vagy VPN-en keresztül hirdetett Application Gateway alhálózatra.

    d.  A hálózati adapter érvényes útvonalait és szabályait a következő PowerShell-parancsokkal tekintheti meg:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Ha nem talál problémát a NSG vagy a UDR kapcsolatban, ellenőrizze a háttér-kiszolgálót az alkalmazásokkal kapcsolatos problémákról, amelyek megakadályozzák, hogy az ügyfelek TCP-munkamenetet hozzanak létre a konfigurált portokon. Néhány dolog, amit érdemes ellenőriznie:

    a.  Nyisson meg egy parancssort (Win + R- \> cmd), írja be a parancsot `netstat` , majd válassza az ENTER billentyűt.

    b.  Győződjön meg arról, hogy a kiszolgáló figyeli-e a konfigurált portot. Például:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Ha nem figyeli a konfigurált portot, ellenőrizze a webkiszolgáló beállításait. Például: site kötések az IIS-ben, az NGINX-kiszolgáló blokkolása és a virtuális gazdagép Apache-ban.

    d.  Ellenőrizze az operációs rendszer tűzfalának beállításait, és győződjön meg arról, hogy a portra irányuló bejövő forgalom engedélyezett.

#### <a name="http-status-code-mismatch"></a>HTTP-állapotkód eltérése

**Üzenet:** A háttérbeli \' s http-válasz állapotkód nem felelt meg a mintavételi beállításnak. Várt: {HTTPStatusCode0} érkezett: {HTTPStatusCode1}.

**OK:** A TCP-kapcsolatok létrehozása és a TLS-kézfogás elvégzése után (ha a TLS engedélyezve van), Application Gateway a mintavételt HTTP GET kérelemként fogja elküldeni a háttér-kiszolgálónak. A fentiekben leírtak szerint az alapértelmezett mintavétel \<protocol\> :://127.0.0.1: \<port\> /, és a Rage 200-ben a 399-as kifogástalan állapot-kódokat veszi figyelembe. Ha a kiszolgáló más állapotkódot ad vissza, az üzenet nem megfelelőként lesz megjelölve.

**Megoldás:** A háttér-kiszolgáló válaszának kódjától függően a következő lépéseket hajthatja végre. Néhány gyakori állapotkód itt látható:

| **Hiba** | **Műveletek** |
| --- | --- |
| Nem egyező mintavételi állapotkód: fogadott 401 | Győződjön meg arról, hogy a háttér-kiszolgáló hitelesítést igényel. Application Gateway mintavétel nem tud hitelesítő adatokat továbbítani a hitelesítéshez. Engedélyezze a \" HTTP 401-et a mintavételi \" állapotkód-egyeztetésben vagy a mintavételben olyan elérési útra, ahol a kiszolgálónak nincs szükség hitelesítésre. | |
| Nem egyező mintavételi állapotkód: fogadott 403 | Hozzáférés megtiltva. Győződjön meg arról, hogy az elérési úthoz való hozzáférés engedélyezett-e a háttér-kiszolgálón. | |
| Nem egyező mintavételi állapotkód: fogadott 404 | A lap nem található. Győződjön meg arról, hogy az állomásnév elérési útja elérhető-e a háttér-kiszolgálón. Módosítsa az állomásnév vagy az elérési út paramétert egy elérhető értékre. | |
| Nem egyező mintavételi állapotkód: fogadott 405 | A Application Gateway mintavételi kérelmei a HTTP GET metódust használják. Győződjön meg arról, hogy a kiszolgáló engedélyezi ezt a metódust. | |
| Nem egyező mintavételi állapotkód: fogadott 500 | Belső kiszolgálóhiba. Győződjön meg arról, hogy a backend-kiszolgáló állapota és a szolgáltatások futnak-e. | |
| Nem egyező mintavételi állapotkód: fogadott 503 | A szolgáltatás nem érhető el. Győződjön meg arról, hogy a backend-kiszolgáló állapota és a szolgáltatások futnak-e. | |

Vagy ha úgy gondolja, hogy a válasz legitim, és azt szeretné, hogy a Application Gateway a többi állapotkódot is Kifogástalan állapotba fogadja, létrehozhat egy egyéni mintavételt. Ez a megközelítés olyan helyzetekben hasznos, amikor a háttérbeli webhelynek hitelesítést kell használnia. Mivel a mintavételi kérelmek nem rendelkeznek felhasználói hitelesítő adatokkal, sikertelenek lesznek, és a háttér-kiszolgáló HTTP 401 állapotkódot ad vissza.

Egyéni mintavétel létrehozásához kövesse [az alábbi lépéseket](./application-gateway-create-probe-portal.md).

#### <a name="http-response-body-mismatch"></a>HTTP-válasz törzsének eltérése

**Üzenet:** A háttérbeli \' s http-válasz törzse nem felelt meg a mintavételi beállításnak. A kapott válasz törzse nem tartalmaz {string}.

**OK:** Egyéni mintavétel létrehozásakor lehetősége van a háttér-kiszolgáló megfelelő állapotának megjelölésére a válasz törzsében szereplő karakterláncok egyeztetésével. Beállíthatja például, hogy Application Gateway fogadja el a "jogosulatlan" karakterláncot az egyeztetéshez. Ha a háttér-kiszolgáló válasza a mintavételi kérelemhez nem **engedélyezett** karakterláncot tartalmaz, akkor kifogástalanként lesz megjelölve. Ellenkező esetben ez az üzenet nem megfelelőként lesz megjelölve.

**Megoldás:** A probléma megoldásához kövesse az alábbi lépéseket:

1.  A háttér-kiszolgálót helyileg vagy egy, a mintavételi útvonalon található ügyfélszámítógépen keresztül érheti el, majd ellenőrzi a válasz törzsét.

1.  Ellenőrizze, hogy a Application Gateway egyéni mintavételi konfigurációban szereplő válasz törzse megfelel-e a konfigurált beállításoknak.

1.  Ha nem egyeznek, módosítsa a mintavételi konfigurációt úgy, hogy az a megfelelő karakterlánc-értéket fogadja el.

További információ a [Application Gateway mintavétel egyeztetéséről](./application-gateway-probe-overview.md#probe-matching).

>[!NOTE]
> A TLS-hez kapcsolódó hibaüzenetek esetében a SNI viselkedésének és a v1 és v2 SKU közötti különbségek megismeréséhez tekintse meg a [TLS – áttekintés](ssl-overview.md) lapot.


#### <a name="backend-server-certificate-invalid-ca"></a>A háttér-kiszolgáló tanúsítványa érvénytelen CA

**Üzenet:** A háttérrendszer által használt kiszolgálói tanúsítványt nem a jól ismert hitelesítésszolgáltató (CA) írta alá. Engedélyezze a háttérrendszer használatát a Application Gateway a háttér által használt kiszolgálói tanúsítvány főtanúsítványának feltöltésével.

**OK:** Application Gateway v2 végpontok közötti SSL használatához a háttér-kiszolgáló tanúsítványát ellenőrizni kell annak érdekében, hogy a kiszolgáló kifogástalan legyen.
A TLS/SSL-tanúsítvány megbízhatósága érdekében a háttér-kiszolgáló tanúsítványát olyan HITELESÍTÉSSZOLGÁLTATÓnak kell kiállítania, amely a Application Gateway megbízható tárolójában szerepel. Ha a tanúsítványt nem megbízható HITELESÍTÉSSZOLGÁLTATÓ állította ki (például önaláírt tanúsítvány használata esetén), a felhasználóknak fel kell tölteniük a kiállító tanúsítványát Application Gateway.

**Megoldás:** Kövesse az alábbi lépéseket a megbízható főtanúsítvány Application Gateway való exportálásához és feltöltéséhez. (Ezek a lépések a Windows-ügyfelekre vonatkoznak.)

1.  Jelentkezzen be arra a gépre, amelyen az alkalmazás üzemeltetve van.

1.  Válassza a Win + R lehetőséget, vagy kattintson a jobb gombbal a **Start** gombra, majd válassza a **Futtatás** lehetőséget.

1.  Adja meg `certmgr.msc` és válassza az ENTER billentyűt. A Tanúsítványkezelőt a **Start** menüben is megkeresheti.

1.  Keresse meg a tanúsítványt, általában a-ben `\Certificates - Current User\\Personal\\Certificates\` , majd nyissa meg.

1.  Válassza ki a főtanúsítványt, majd válassza a **Tanúsítvány megtekintése** lehetőséget.

1.  A tanúsítvány tulajdonságai között válassza a **részletek** lapot.

1.  A **részletek** lapon válassza a **Másolás fájlba** lehetőséget, és mentse a fájlt a Base-64 kódolású X. 509 (. CER) formátum.

1.  Nyissa meg a Application Gateway HTTP- **Beállítások** lapot a Azure Portal.

1. Nyissa meg a HTTP-beállításokat, válassza a **tanúsítvány hozzáadása** lehetőséget, és keresse meg az imént mentett tanúsítványfájl-fájlt.

1. A HTTP-beállítások mentéséhez kattintson a **Mentés** gombra.

Azt is megteheti, hogy a főtanúsítványt egy ügyfélgépről exportálja, ha a böngészőből közvetlenül fér hozzá a kiszolgálóhoz (Application Gateway megkerülése), és a főtanúsítványt a böngészőből exportálja.

További információ a megbízható főtanúsítványok kibontásáról és feltöltéséről a Application Gatewayban: [megbízható főtanúsítvány exportálása (v2 SKU)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).

#### <a name="trusted-root-certificate-mismatch"></a>Nem megfelelő a megbízható főtanúsítvány

**Üzenet:** A háttérrendszer által használt kiszolgálói tanúsítvány főtanúsítványa nem felel meg az Application Gateway számára hozzáadott megbízható főtanúsítványnak. Győződjön meg arról, hogy a megfelelő főtanúsítványt adja hozzá a háttér engedélyezési.

**OK:** Application Gateway v2 végpontok közötti SSL használatához a háttér-kiszolgáló tanúsítványát ellenőrizni kell annak érdekében, hogy a kiszolgáló kifogástalan legyen.
A TLS/SSL-tanúsítvány megbízhatósága érdekében a háttér-kiszolgáló tanúsítványát olyan HITELESÍTÉSSZOLGÁLTATÓnak kell kiállítania, amely a Application Gateway megbízható tárolójában szerepel. Ha a tanúsítványt nem megbízható HITELESÍTÉSSZOLGÁLTATÓ állította ki (például önaláírt tanúsítványt használt), a felhasználóknak fel kell tölteniük a kiállító tanúsítványát Application Gateway.

A Application Gateway HTTP-beállításokba feltöltött tanúsítványnak meg kell egyeznie a háttér-kiszolgáló tanúsítványának főtanúsítványával.

**Megoldás:** Ha ezt a hibaüzenetet kapja, a Application Gatewayba feltöltött tanúsítvány és a háttér-kiszolgálóra feltöltött egyik nem egyezik.

Az előző módszer 1-11. lépéseit követve töltse fel a megfelelő megbízható főtanúsítványt Application Gatewayra.

További információ a megbízható főtanúsítványok kibontásáról és feltöltéséről a Application Gatewayban: [megbízható főtanúsítvány exportálása (v2 SKU)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku).
> [!NOTE]
> Ez a hiba akkor is megjelenhet, ha a háttér-kiszolgáló nem cseréli ki a tanúsítvány teljes láncát, beleértve a > köztes (ha alkalmazható), a TLS-kézfogás során > levélben szereplő gyökeret. Annak ellenőrzéséhez, hogy az OpenSSL-parancsokat bármely ügyfélről felhasználhatja, és a Application Gateway mintavétel konfigurált beállításaival csatlakozhat a háttér-kiszolgálóhoz.

Például:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Ha a kimenet nem jeleníti meg a visszaadott tanúsítvány teljes láncát, a teljes lánctal újra exportálja a tanúsítványt, beleértve a főtanúsítványt is. Konfigurálja a tanúsítványt a háttér-kiszolgálón. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

#### <a name="backend-certificate-invalid-common-name-cn"></a>A háttér-tanúsítvány érvénytelen köznapi neve (CN)

**Üzenet:** A háttérbeli tanúsítvány köznapi neve (CN) nem egyezik meg a mintavétel állomásneve.

**OK:** Application Gateway ellenőrzi, hogy a háttérbeli HTTP-beállításokban megadott állomásnév megegyezik-e a háttér-kiszolgáló TLS/SSL-tanúsítványa által bemutatott CN-névvel. Ez Standard_v2 és WAF_v2 SKU (v2) viselkedés. A standard és a WAF SKU (v1) Kiszolgálónév jelzése (SNI) a háttérbeli készlet címe teljes tartományneveként van beállítva. A SNI viselkedésével és a v1 és v2 SKU közötti különbségekkel kapcsolatos további információkért lásd: [a TLS-lezárás és a végpontok közötti TLS-kapcsolat áttekintése Application Gateway](ssl-overview.md).

Ha a v2 SKU-ban alapértelmezett mintavétel van (az egyéni mintavétel nincs konfigurálva és társítva), a SNI a HTTP-beállításokban említett állomásnév alapján lesz beállítva. Ha a HTTP-beállításokban az "állomásnév kiválasztása a háttérbeli címről" szó szerepel, a háttér-címkészlet érvényes teljes tartománynevet tartalmaz, ez a beállítás lesz alkalmazva.

Ha a HTTP-beállításokhoz egyéni mintavétel van társítva, a SNI az egyéni mintavételi konfigurációban említett állomásnév alapján lesz beállítva. Ha az egyéni mintavételben az **állomásnév a háttérbeli http-beállítások közül** van kiválasztva, a SNI a http-beállításokban említett állomásnév alapján lesz beállítva.

Ha a **kiválasztott állomásnév a háttérbeli címről** beállítás van megadva a http-beállításokban, a háttérbeli címkészlet érvényes teljes tartománynevet kell tartalmaznia.

Ha ezt a hibaüzenetet kapja, a háttér-tanúsítvány CN-je nem egyezik meg az egyéni mintavételben vagy a HTTP-beállításokban konfigurált állomásnévvel (ha a kiválasztó **állomásnév a háttérbeli http-beállítások közül** ki van jelölve). Ha alapértelmezett mintavételt használ, az állomásnév **127.0.0.1**-ként lesz beállítva. Ha ez nem a kívánt érték, hozzon létre egy egyéni mintavételt, és társítsa a HTTP-beállításokhoz.

**Megoldás:**

A probléma megoldásához kövesse az alábbi lépéseket.

Windows esetén:

1.  Jelentkezzen be arra a gépre, amelyen az alkalmazás üzemeltetve van.

1.  Válassza a Win + R lehetőséget, vagy kattintson a jobb gombbal a **Start** gombra, és válassza a **Futtatás** lehetőséget.

1.  Adja meg a **certmgr. msc parancsot** , majd kattintson az ENTER gombra. A Tanúsítványkezelőt a **Start** menüben is megkeresheti.

1.  Keresse meg a tanúsítványt (általában a-ben `\Certificates - Current User\\Personal\\Certificates` ), majd nyissa meg a tanúsítványt.

1.  A **részletek** lapon keresse meg a tanúsítvány **tárgyát**.

1.  Ellenőrizze a tanúsítvány CN-tartalmát a részletek közül, és adja meg ugyanezt az egyéni mintavétel állomásnév mezőjében vagy a HTTP-beállításokban (ha a kiválasztó **állomásnév a háttérbeli http-beállítások közül** ki van jelölve). Ha ez nem a webhely kívánt állomásneve, be kell szereznie egy tanúsítványt az adott tartományhoz, vagy meg kell adnia a helyes állomásnevet az egyéni mintavétel vagy a HTTP-beállítás konfigurációjában.

Linux rendszerhez OpenSSL használatával:

1.  Futtassa ezt a parancsot az OpenSSL-ben:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  A megjelenített tulajdonságok közül keresse meg a tanúsítvány CN-t, és adja meg ugyanezt a http-beállítások állomásnév mezőjében. Ha ez nem a webhely kívánt állomásneve, be kell szereznie egy tanúsítványt az adott tartományhoz, vagy meg kell adnia a helyes állomásnevet az egyéni mintavétel vagy a HTTP-beállítás konfigurációjában.

#### <a name="backend-certificate-is-invalid"></a>A háttér-tanúsítvány érvénytelen

**Üzenet:** A háttér-tanúsítvány érvénytelen. Az aktuális dátum nem a \" tanúsítvány érvényességi \" ideje és a \" dátumtartomány érvényességi \" tartománya.

**OK:** Minden tanúsítványhoz tartozik egy érvényességi tartomány, és a HTTPS-kapcsolat nem lesz biztonságos, kivéve, ha a kiszolgáló TLS/SSL-tanúsítványa érvényes. Az aktuális adatoknak **érvényes** és **érvényes** tartományon belül kell lenniük. Ha nem, akkor a tanúsítvány érvénytelennek minősül, és egy biztonsági problémát hoz létre, amelyben Application Gateway megjelöli a háttér-kiszolgálót sérültként.

**Megoldás:** Ha a TLS/SSL-tanúsítvány lejárt, újítsa meg a tanúsítványt a szállítóval, és frissítse a kiszolgáló beállításait az új tanúsítvánnyal. Ha ez egy önaláírt tanúsítvány, érvényes tanúsítványt kell előállítania, és fel kell töltenie a főtanúsítványt a Application Gateway HTTP-beállításokba. Ehhez kövesse az alábbi lépéseket:

1.  Nyissa meg Application Gateway HTTP-beállításait a portálon.

1.  Válassza ki a lejárt tanúsítványt tartalmazó beállítást, válassza a **tanúsítvány hozzáadása** lehetőséget, és nyissa meg az új tanúsítványfájl.

1.  Távolítsa el a régi tanúsítványt a tanúsítvány melletti **delete (Törlés** ) ikon használatával, majd válassza a **Mentés** lehetőséget.

#### <a name="certificate-verification-failed"></a>A tanúsítvány ellenőrzése nem sikerült

**Üzenet:** Nem lehetett ellenőrizni a háttér-tanúsítvány érvényességét. Az ok megállapításához ellenőrizze az OpenSSL diagnosztikát a következő hibakóddal társított üzenethez: {errorCode}

**OK:** Ez a hiba akkor fordul elő, ha Application Gateway nem tudja ellenőrizni a tanúsítvány érvényességét.

**Megoldás:** A probléma megoldásához ellenőrizze, hogy a kiszolgálón a tanúsítvány megfelelően lett-e létrehozva. Például az [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) használatával ellenőrizheti a tanúsítványt és annak tulajdonságait, majd megpróbálhatja újból feltölteni a tanúsítványt a Application Gateway http-beállításokba.

<a name="backend-health-status-unknown"></a>Háttér állapotának állapota: ismeretlen
-------------------------------
Ha a háttér állapota ismeretlenként jelenik meg, a portál nézet a következő képernyőképre fog hasonlítani:

![Application Gateway háttér állapota – ismeretlen](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Ez a viselkedés a következő okok közül egyet vagy többet okozhat:

1.  Az Application Gateway alhálózat NSG blokkolja a 65503-65534 (v1 SKU) vagy 65200-65535 (v2 SKU) portok bejövő hozzáférését az "internetről".
1.  Az Application Gateway alhálózat UDR az alapértelmezett útvonalra (0.0.0.0/0) van beállítva, és a következő ugrás nincs "Internet" értékre megadva.
1.  Az alapértelmezett útvonalat a ExpressRoute/VPN-kapcsolat a BGP-en keresztül egy virtuális hálózattal meghirdeti.
1.  Az egyéni DNS-kiszolgáló olyan virtuális hálózaton van konfigurálva, amely nem tudja feloldani a nyilvános tartományneveket.
1.  Az Application Gateway állapota Nem megfelelő.

**Megoldás:**

1.  Győződjön meg arról, hogy a NSG blokkolja-e az **internetről** a 65503-65534-as (v1 SKU) vagy a 65200-65535 (v2 SKU) portokhoz való hozzáférést:

    a.  Az Application Gateway **Áttekintés** lapon válassza a **Virtual Network/alhálózat** hivatkozást.

    b.  A virtuális hálózat **alhálózatok** lapján válassza ki azt az alhálózatot, ahol a Application Gateway telepítve lett.

    c.  Győződjön meg arról, hogy van-e NSG konfigurálva.

    d.  Ha be van állítva egy NSG, keresse meg a NSG-erőforrást a **Keresés** lapon vagy az **összes erőforrás** területen.

    e.  A **Bejövő szabályok** szakaszban adjon hozzá egy bejövő szabályt, amely lehetővé teszi, hogy a 65503-65534 **-as célport** -tartomány v1 SKU vagy 65200-65535 v2 SKU legyen a **forrásként** , vagy az **Internet**.

    f.  Válassza a **Mentés** lehetőséget, és ellenőrizze, hogy a háttér állapota Kifogástalan-e. Azt is megteheti, hogy a [PowerShell vagy a parancssori](../virtual-network/manage-network-security-group.md)felület használatával végezhető el.

1.  Győződjön meg arról, hogy a UDR rendelkezik-e alapértelmezett útvonallal (0.0.0.0/0), és a következő ugrás nincs beállítva **internetként**:
    
    a.  Az alhálózat meghatározásához kövesse az 1a és az 1b lépést.

    b.  Győződjön meg arról, hogy van-e konfigurált UDR. Ha van, keresse meg az erőforrást a keresősáv vagy az **összes erőforrás** területen.

    c.  Győződjön meg arról, hogy vannak-e alapértelmezett útvonalak (0.0.0.0/0) a következő ugrással, amely nincs beállítva **internetként**. Ha a beállítás **virtuális készülék** vagy **Virtual Network átjáró**, meg kell győződnie arról, hogy a virtuális berendezés vagy a helyszíni eszköz a csomag módosítása nélkül képes az internetre irányítani a csomagot.

    d.  Ellenkező esetben módosítsa a következő ugrást az **internetre**, válassza a **Mentés** lehetőséget, és ellenőrizze a háttér állapotát.

1.  A virtuális hálózat ExpressRoute/VPN-kapcsolata által meghirdetett alapértelmezett útvonal BGP-en keresztül:

    a.  Ha ExpressRoute/VPN-kapcsolattal rendelkezik a virtuális hálózathoz BGP-en keresztül, és ha az alapértelmezett útvonalat hirdeti, meg kell győződnie arról, hogy a csomagot az internetre irányítja át a módosítás nélkül. A Application Gateway-portálon a **kapcsolatok hibakeresése** lehetőség használatával ellenőrizheti.

    b.  Válassza ki manuálisan a célhelyet bármely, az internetre irányítható IP-cím, például a 1.1.1.1 esetében. Adja meg a cél portját bármit, és ellenőrizze a kapcsolatot.

    c.  Ha a következő ugrás a virtuális hálózati átjáró, lehet, hogy a ExpressRoute vagy VPN-en keresztül meghirdetett alapértelmezett útvonal.

1.  Ha a virtuális hálózaton konfigurálva van egy egyéni DNS-kiszolgáló, ellenőrizze, hogy a kiszolgáló (vagy kiszolgálók) képes-e a nyilvános tartományok feloldására. Előfordulhat, hogy a nyilvános tartománynevek feloldásához olyan helyzetekben van szükség, amikor a Application Gatewaynak olyan külső tartományokhoz kell csatlakozniuk, mint például az OCSP-kiszolgálók, vagy ellenőriznie kell a tanúsítvány visszavonási állapotát.

1.  Annak ellenőrzéséhez, hogy a Application Gateway állapota Kifogástalan-e, nyissa meg a portál **Resource Health** lehetőségét, és ellenőrizze, hogy az állapot **kifogástalan**-e. Ha **sérült** vagy **csökkentett teljesítményű** állapotot lát, [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/options/).

<a name="next-steps"></a>Következő lépések
----------

További információ a [Application Gateway diagnosztika és naplózás](./application-gateway-diagnostics.md).
