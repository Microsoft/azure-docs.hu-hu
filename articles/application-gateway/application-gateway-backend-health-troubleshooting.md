---
title: Háttér-állapotproblémák elhárítása a Azure Application Gateway
description: A háttér-állapotproblémák hibaelhárítását ismerteti Azure Application Gateway
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: surmb
ms.openlocfilehash: 8664f9327af37345c7104c65b2521212669ae806
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786325"
---
<a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Háttér-állapotproblémák elhárítása a Application Gateway
==================================================

<a name="overview"></a>Áttekintés
--------

Alapértelmezés szerint a Azure Application Gateway ellenőrzi a háttérkiszolgálókat az állapotuk ellenőrzéséhez és annak ellenőrzéséhez, hogy készen állnak-e a kérések kiszolgálására. A felhasználók egyéni mintavételeket is létrehozhatnak, amelyek megemlítik az állomásnevet, a mintavételhez használt elérési utat és az elfogadható állapotkódokat Kifogástalanként. Ha a háttérkiszolgáló nem válaszol sikeresen, a Application Gateway nem megfelelőként jelöli meg a kiszolgálót, és leállítja a kérések kiszolgálóra való továbbítását. Miután a kiszolgáló sikeresen válaszol, a Application Gateway folytatja a kérelmek továbbítását.

### <a name="how-to-check-backend-health"></a>A háttér háttér állapotának ellenőrzése

A háttérkészlet állapotának ellenőrzéshez használhatja a  Háttér állapota lapot a Azure Portal. Vagy használhatja a [Azure PowerShell,](/powershell/module/az.network/get-azapplicationgatewaybackendhealth) [a CLI](/cli/azure/network/application-gateway#az_network_application_gateway_show_backend_health)vagy a [REST API.](/rest/api/application-gateway/applicationgateways/backendhealth)

A fenti módszerek bármelyike által lekért állapot a következők bármelyike lehet:

- Kifogástalan

- Nem kifogástalan

- Ismeretlen

Ha egy kiszolgáló háttérállapota Kifogástalan, az azt jelenti, hogy a Application Gateway továbbítja a kéréseket a kiszolgálónak. Ha azonban a háttérkészletben található összes kiszolgáló háttér-állapota Nem megfelelő vagy ismeretlen, problémákba ütközhet, amikor alkalmazásokat próbál meg elérni. Ez a cikk az egyes bemutatott hibák tüneteit, okát és megoldását ismerteti.

<a name="backend-health-status-unhealthy"></a>Háttér állapota: Nem egészségügyi állapot
-------------------------------

Ha a háttér állapota Nem megfelelő, a portálnézet az alábbi képernyőképen láthatóhoz hasonló:

![Application Gateway állapota – Nem egészségügyi](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

Vagy ha egy lekérdezést, Azure PowerShell CLI-t vagy Azure REST API-lekérdezést használ, az alábbihoz hasonló választ kap:
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
Miután nem megfelelő állapotú háttérkiszolgálót kap a háttérkészletben található összes kiszolgálóhoz, a kérelmeket a rendszer nem továbbítja a kiszolgálókra, és az Application Gateway "502 Hibás átjáró" hibát ad vissza a kérelmező ügyfélnek. A probléma elhárításához tekintse meg a **Háttér** állapota lap **Részletek oszlopát.**

A Részletek oszlopban  megjelenő üzenet részletesebb információkat nyújt a problémáról, és ezek alapján elkezdheti elhárítani a problémát.

> [!NOTE]
> Az alapértelmezett mintavételi kérelmet a rendszer a következő formátumban küldi \<protocol\> el: ://127.0.0.1: \<port\> /. Például egy http://127.0.0.1:80 HTTP-mintavételhez a 80-as porton. Csak a 200–399-es HTTP-állapotkódok számítanak kifogástalannak. A protokoll és a célport a HTTP-beállításokból öröklődik. Ha azt szeretné Application Gateway más protokollon, állomásnévn vagy elérési úton szeretne mintavételt használni, és egy másik állapotkódot Kifogástalan állapotkódként szeretne felismerni, állítson be egy egyéni mintavételt, és társítsa azt a HTTP-beállításokkal.

<a name="error-messages"></a>Hibaüzenetek
------------------------
#### <a name="backend-server-timeout"></a>Háttérkiszolgáló időtúllépése

**Üzenet:** A háttérkiszolgálónak az Application Gateway állapot-mintavételére való válaszadáshoz szükséges ideje nagyobb, mint a mintavételi beállítás időtúllépési \' küszöbértéke.

**Ok:** Miután Application Gateway HTTP(S) mintavételi kérelmet küld a háttérkiszolgálónak, megvárja a háttérkiszolgáló válaszát a beállított időtartamra. Ha a háttérkiszolgáló nem válaszol a konfigurált időszakon belül (az időtúllépési érték), akkor nem megfelelőként lesz megjelölve, amíg nem válaszol újra a konfigurált időkorláton belül.

**Megoldás:** Ellenőrizze, hogy a háttérkiszolgáló vagy az alkalmazás miért nem válaszol a konfigurált időkorláton belül, és ellenőrizze az alkalmazás függőségeit is. Ellenőrizze például, hogy az adatbázis rendelkezik-e olyan problémákkal, amelyek késleltetik a válaszadást. Ha ismeri az alkalmazás viselkedését, és csak az időtúllépési érték után kell válaszolnia, növelje az időtúllépési értéket az egyéni mintavételi beállításokból. Az időtúllépés értékének módosítása egyéni mintavételt is kell, hogy legyen. Az egyéni mintavétel konfigurálásával kapcsolatos információkért tekintse meg a [dokumentációs oldalt.](./application-gateway-create-probe-portal.md)

Az időtúllépés értékének növeléséhez kövesse az alábbi lépéseket:

1.  Közvetlenül a háttérkiszolgálóhoz fér hozzá, és ellenőrizze, hogy a kiszolgálónak az adott oldalon mikor kell válaszolnia. Bármilyen eszközzel hozzáférhet a háttérkiszolgálóhoz, beleértve a fejlesztői eszközök böngészőjét is.

1.  Miután kitalálta az alkalmazás válaszához szükséges időt,  válassza az Állapot-mintavételek lapot, majd válassza ki a HTTP-beállításokhoz társított mintavételt.

1.  Adja meg az alkalmazás válaszidenél nagyobb időtúllépési értékét másodpercben.

1.  Mentse az egyéni mintavételi beállításokat, és ellenőrizze, hogy a háttér háttér állapota Kifogástalan most.

#### <a name="dns-resolution-error"></a>DNS-feloldási hiba

**Üzenet:** Application Gateway nem tudott mintavételt létrehozni ehhez a háttérhez. Ez általában akkor fordul elő, ha a háttérrendszer teljes tartományneve nem lett megfelelően megadva. 

**Ok:** Ha a háttérkészlet IP-cím/teljes tartománynév vagy App Service típusú, a Application Gateway feloldja a tartománynévrendszeren (DNS) keresztül megadott teljes tartománynév IP-címét (egyéni vagy Azure alapértelmezett), és megpróbál csatlakozni a kiszolgálóhoz a HTTP-beállítások között említett TCP-porton. Ha azonban ez az üzenet megjelenik, azt javasolja, Application Gateway a megadott teljes tartomány neve IP-címe nem oldható fel sikeresen.

**Megoldás:**

1.  Ellenőrizze, hogy a háttérkészletben megadott teljes tartománynév helyes-e, és hogy nyilvános tartomány-e, majd próbálja meg feloldani a helyi gépről.

1.  Ha meg tudja oldani az IP-címet, akkor a virtuális hálózat DNS-konfigurációjában hiba lehet.

1.  Ellenőrizze, hogy a virtuális hálózat egyéni DNS-kiszolgálóval van-e konfigurálva. Ha igen, ellenőrizze a DNS-kiszolgálón, hogy miért nem tud feloldani a megadott teljes tartománynév IP-címére.

1.  Ha az Azure alapértelmezett DNS-t használja, ellenőrizze a tartománynév-regisztrálónál, hogy befejeződött-e a megfelelő A vagy CNAME rekordleképezés.

1.  Ha a tartomány privát vagy belső, próbálja meg feloldani egy ugyanazon a virtuális hálózaton található virtuális gépről. Ha meg tudja oldani, indítsa újra a Application Gateway és ellenőrizze újra. A Application Gateway újraindításához le kell [](/powershell/module/azurerm.network/stop-azurermapplicationgateway) állítania, majd el kell indítania az indítást az ezekben a csatolt erőforrásokban leírt PowerShell-parancsokkal. [](/powershell/module/azurerm.network/start-azurermapplicationgateway)

#### <a name="tcp-connect-error"></a>TCP-kapcsolódási hiba

**Üzenet:** Application Gateway nem tudott csatlakozni a háttérhez.
Ellenőrizze, hogy a háttér válaszol-e a mintavételhez használt porton.
Azt is ellenőrizze, hogy valamelyik NSG/UDR/tűzfal blokkolja-e a háttérhálózat IP-címének és portjának hozzáférését

**Ok:** A DNS-feloldási fázis után a Application Gateway megpróbál csatlakozni a háttérkiszolgálóhoz a HTTP-beállításokban konfigurált TCP-porton. Ha Application Gateway tcp-munkamenetet a megadott porton, a mintavétel nem megfelelőként lesz megjelölve ezzel az üzenettel.

**Megoldás:** Ha ezt a hibaüzenetet kapja, kövesse az alábbi lépéseket:

1.  Ellenőrizze, hogy tud-e csatlakozni a háttérkiszolgálóhoz a HTTP-beállítások között említett porton egy böngésző vagy a PowerShell használatával. Futtassa például a következő parancsot: `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  Ha az említett port nem a kívánt port, adja meg a megfelelő portszámot, Application Gateway a háttérkiszolgálóhoz való csatlakozáshoz

1.  Ha a helyi gépről sem tud csatlakozni a porton, akkor:

    a.  Ellenőrizze a háttérkiszolgáló hálózati adapterének és alhálózatának hálózati biztonsági csoport (NSG) beállításait, és hogy engedélyezve vannak-e a konfigurált porthoz bejövő kapcsolatok. Ha nem, hozzon létre egy új szabályt, amely engedélyezi a kapcsolatokat. Az NSG-szabályok létrehozásáról a [dokumentációs oldalon tájékozódhat.](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules)

    b.  Ellenőrizze, hogy a hálózati alhálózat NSG-beállításai Application Gateway engedélyezik-e a kimenő nyilvános és privát forgalmat a kapcsolat létesítése érdekében. A 3a lépésben megadott dokumentumlapon további információt olvashat az NSG-szabályok létrehozásáról.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    c.  Ellenőrizze a felhasználó által megadott útvonalak (UDR) beállításait a Application Gateway és a háttérkiszolgáló alhálózatán, hogy vannak-e útválasztási anomáliák. Győződjön meg arról, hogy az UDR nem irányítja a forgalmat a háttérbeli alhálózatról. Ellenőrizze például a hálózati virtuális berendezésekhez vagy az alhálózathoz Application Gateway és/vagy VPN-en keresztül meghirdetett Azure ExpressRoute útvonalakat.

    d.  A hálózati adapterek hatályos útvonalának és szabályainak ellenőrzéséhez használhatja a következő PowerShell-parancsokat:
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  Ha nem talál problémát az NSG-vel vagy az UDR-rel kapcsolatban, ellenőrizze a háttérkiszolgálón, hogy vannak-e olyan alkalmazásokkal kapcsolatos problémák, amelyek megakadályozzák, hogy az ügyfelek TCP-munkamenetet létesítsen a konfigurált portokon. Néhány dolog, amit ellenőrizni kell:

    a.  Nyisson meg egy parancssort (Win+R – cmd), írja be a \> `netstat` parancsot, majd válassza az Enter billentyűt.

    b.  Ellenőrizze, hogy a kiszolgáló figyel-e a konfigurált porton. Például:
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    c.  Ha nem figyel a konfigurált porton, ellenőrizze a webkiszolgáló beállításait. Például: helykötések az IIS-ban, kiszolgálóblokk az NGINX-ban és virtuális gazdagép az Apache-ban.

    d.  Ellenőrizze az operációs rendszer tűzfalának beállításait, és győződjön meg arról, hogy a portra érkező bejövő forgalom engedélyezve van.

#### <a name="http-status-code-mismatch"></a>HTTP-állapotkód eltérése

**Üzenet:** A háttér háttér http-válaszának \' állapotkódja nem felelt meg a mintavételi beállításnak. Expected:{HTTPStatusCode0} Received:{HTTPStatusCode1}.

**Ok:** Miután létrejött a TCP-kapcsolat, és megtörtént a TLS-kézfogás (ha a TLS engedélyezve van), a Application Gateway HTTP GET kérésként elküldi a mintavételt a háttérkiszolgálónak. Ahogy korábban említettük, az alapértelmezett mintavétel \<protocol\> a ://127.0.0.1: /, és a 200 és 399 között található válaszállapot-kódokat Kifogástalannak \<port\> tekinti. Ha a kiszolgáló bármilyen más állapotkódot ad vissza, az ezzel az üzenettel Nem megfelelő állapotúként lesz megjelölve.

**Megoldás:** A háttérkiszolgáló válaszkódja alapján a következő lépéseket használhatja. Néhány gyakori állapotkód itt található:

| **Hiba** | **Műveletek** |
| --- | --- |
| Mintavételi állapotkód eltérése: Fogadott 401 | Ellenőrizze, hogy a háttérkiszolgáló hitelesítést igényel-e. Application Gateway mintavételek nem tudnak hitelesítő adatokat átadni a hitelesítéshez. Engedélyezze a \" HTTP 401-et egy mintavételi állapotkódban, vagy a mintavételt egy olyan elérési útra, ahol a kiszolgáló nem \" igényel hitelesítést. |
| Mintavételi állapotkód eltérése: Fogadott hibakód: 403 | Hozzáférés tiltott. Ellenőrizze, hogy az elérési úthoz való hozzáférés engedélyezve van-e a háttérkiszolgálón. |
| A mintavételi állapotkód eltérése: 404 érkezett | Az oldal nem található. Ellenőrizze, hogy a gazdanév elérési útja elérhető-e a háttérkiszolgálón. Módosítsa a gazdagépnév vagy az elérési út paraméterét egy elérhető értékre. |
| A mintavételi állapotkód eltérése: 405 érkezett | Az alkalmazás mintavételi kérései Application Gateway HTTP GET metódust használják. Ellenőrizze, hogy a kiszolgáló engedélyezi-e ezt a metódust. |
| A mintavételi állapotkód eltérése: 500 érkezett | Belső kiszolgálóhiba. Ellenőrizze a háttérkiszolgáló állapotát és azt, hogy futnak-e a szolgáltatások. |
| A mintavételi állapotkód eltérése: 503 érkezett | A szolgáltatás nem érhető el. Ellenőrizze a háttérkiszolgáló állapotát és azt, hogy futnak-e a szolgáltatások. |

Vagy ha úgy gondolja, hogy a válasz megbízható, és Application Gateway szeretne elfogadni más állapotkódokat Kifogástalan állapotkódként, létrehozhat egy egyéni mintavételt. Ez a megközelítés olyan helyzetekben hasznos, amikor a háttér-webhely hitelesítést igényel. Mivel a mintavételi kérések nem tartalmaznak felhasználói hitelesítő adatokat, sikertelenek lesznek, és a háttérkiszolgáló EGY HTTP 401-es állapotkódot ad vissza.

Egyéni mintavétel létrehozásához kövesse az [alábbi lépéseket.](./application-gateway-create-probe-portal.md)

#### <a name="http-response-body-mismatch"></a>A HTTP-válasz törzse nem egyező

**Üzenet:** A háttér háttér \' http-válaszának törzse nem felelt meg a mintavételi beállításnak. A kapott válasz törzse nem tartalmazza a következő sztringet: {string}.

**Ok:** Amikor egyéni mintavételt hoz létre, lehetősége van a háttérkiszolgálót Kifogástalan állapotúként megjelölni a válasz törzséből származó sztring egyeztetésével. Beállíthatja például, hogy Application Gateway "jogosulatlan" sztringként egyező sztringként fogadja el. Ha a mintavételi kérés háttérkiszolgálói válasza tartalmazza a nem engedélyezett sztringet, akkor kifogástalan állapotúként lesz megjelölve. Ellenkező esetben ezzel az üzenettel Nem túl jónak jelöli a rendszer.

**Megoldás:** A probléma megoldásához kövesse az alábbi lépéseket:

1.  A háttérkiszolgálót helyileg vagy egy ügyfélszámítógépről érik el a mintavételi útvonalon, és ellenőrizze a válasz törzsét.

1.  Ellenőrizze, hogy a válasz törzse Application Gateway egyéni mintavételi konfiguráció megfelel-e a konfiguráltnak.

1.  Ha nem egyeznek, módosítsa a mintavételi konfigurációt úgy, hogy az a megfelelő sztringértéket fogadja el.

További információ a Application Gateway [egyeztetésről.](./application-gateway-probe-overview.md#probe-matching)

>[!NOTE]
> A TLS-sel kapcsolatos összes hibaüzenetért, ha többet szeretne megtudni az SNI viselkedéséről és a v1 és v2 termékváltozat közötti különbségekről, tekintse meg a [TLS áttekintő oldalát.](ssl-overview.md)


#### <a name="backend-server-certificate-invalid-ca"></a>A háttérkiszolgáló tanúsítványa érvénytelen hitelesítésszolgáltató

**Üzenet:** A háttérkiszolgáló által használt kiszolgálói tanúsítványt nem egy jól ismert hitelesítésszolgáltató (CA) írta alá. Engedélyezze a háttérkiszolgálón Application Gateway a háttérkiszolgáló által használt kiszolgálótanúsítvány főtanúsítványának feltöltésével.

**Ok:** A v2-vel Application Gateway végpontok között használt SSL megköveteli a háttérkiszolgáló tanúsítványának ellenőrzését, hogy a kiszolgáló kifogástalannak minősül.
Ahhoz, hogy egy TLS/SSL-tanúsítvány megbízható legyen, a háttérkiszolgáló tanúsítványát olyan hitelesítésszolgáltatónak kell kiállítania, amely szerepel a megbízható tanúsítványtárolóban Application Gateway. Ha a tanúsítványt nem megbízható hitelesítésszolgáltató bocsátotta ki (például ha önaírt tanúsítványt használtak), a felhasználóknak fel kell töltenie a kiállító tanúsítványát a Application Gateway.

**Megoldás:** Az alábbi lépéseket követve exportálhatja és töltheti fel a megbízható főtanúsítványt a Application Gateway. (Ezek a lépések Windows-ügyfelekre vannak behozva.)

1.  Jelentkezzen be arra a gépre, ahol az alkalmazást üzemelteti.

1.  Válassza a Win+R lehetőséget, vagy kattintson a jobb gombbal a **Start** gombra, majd válassza a **Futtatás lehetőséget.**

1.  Írja be `certmgr.msc` az billentyűt, majd válassza az Enter billentyűt. A Start menüben a Tanúsítványkezelőre is **rákereshet.**

1.  Keresse meg a tanúsítványt, általában `\Certificates - Current User\\Personal\\Certificates\` a fájlban, és nyissa meg.

1.  Válassza ki a főtanúsítványt, majd válassza a **Tanúsítvány megtekintése lehetőséget.**

1.  A Tanúsítvány tulajdonságai lapon válassza a **Részletek** lapot.

1.  A Részletek **lapon** válassza  a Másolás fájlba lehetőséget, és mentse a fájlt a Base-64 kódolású X.509 -be (. CER) formátumban.

1.  Nyissa meg Application Gateway **http-beállítások lapot** a Azure Portal.

1. Nyissa meg a HTTP-beállításokat, válassza a **Tanúsítvány hozzáadása** lehetőséget, és keresse meg az előbb mentett tanúsítványfájlt.

1. A **HTTP-beállítások** mentéséhez válassza a Mentés lehetőséget.

Másik lehetőségként exportálhatja a főtanúsítványt egy ügyfélszámítógépről úgy, hogy közvetlenül hozzáfér a kiszolgálóhoz (a Application Gateway megkerülése mellett), és exportálja a főtanúsítványt a böngészőből.

A megbízható főtanúsítványok megbízható főtanúsítványok Application Gateway és feltöltésével kapcsolatos további információkért lásd: Megbízható főtanúsítvány exportálása [(v2 termékváltozat esetén).](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)

#### <a name="trusted-root-certificate-mismatch"></a>A megbízható főtanúsítványok nem egyezőek

**Üzenet:** A háttérkiszolgáló által használt kiszolgálótanúsítvány főtanúsítványa nem egyezik az alkalmazásátjáróhoz hozzáadott megbízható főtanúsítvánnyal. Győződjön meg arról, hogy a megfelelő főtanúsítványt adja hozzá a háttérbe való felvételhez.

**Ok:** A v2-vel Application Gateway teljes Application Gateway szükséges a háttérkiszolgáló tanúsítványának ellenőrzése ahhoz, hogy a kiszolgáló kifogástalan állapotúnak minősül.
Ahhoz, hogy egy TLS/SSL-tanúsítvány megbízható legyen, a háttérkiszolgáló tanúsítványát egy olyan hitelesítésszolgáltatónak kell kiállítania, amely a tanúsítvány megbízható tárolójában Application Gateway. Ha a tanúsítványt nem megbízható hitelesítésszolgáltató bocsátotta ki (például önaírt tanúsítványt használt), a felhasználóknak fel kell töltenie a kiállító tanúsítványát a Application Gateway.

A HTTP-beállításokba feltöltött Application Gateway meg kell egyeznie a háttérkiszolgáló-tanúsítvány főtanúsítványának.

**Megoldás:** Ha ezt a hibaüzenetet kapja, eltérés van az Application Gateway-be feltöltött és a háttérkiszolgálóra feltöltött tanúsítvány között.

Az előző módszer 1–11. lépéseit követve töltse fel a megfelelő megbízható főtanúsítványt a Application Gateway.

A megbízható főtanúsítványok megbízható főtanúsítványok Application Gateway és feltöltésével kapcsolatos további információkért lásd: Megbízható főtanúsítvány exportálása [(v2 termékváltozat esetén).](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)
> [!NOTE]
> Ez a hiba akkor is előfordulhat, ha a háttérkiszolgáló nem cseréli le a tanúsítvány teljes láncát, beleértve a Legfelső szintű > Köztes (ha van) és a > Levélt a TLS-kézfogás során. Az ellenőrzéshez bármely ügyfél OpenSSL-parancsát használhatja, és csatlakozhat a háttérkiszolgálóhoz az adatbázis-mintavételben konfigurált Application Gateway használatával.

Például:
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```
Ha a kimenet nem mutatja a visszaadott tanúsítvány teljes láncát, exportálja újra a tanúsítványt a teljes lánccal együtt, beleértve a főtanúsítványt is. Konfigurálja ezt a tanúsítványt a háttérkiszolgálón. 

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

#### <a name="backend-certificate-invalid-common-name-cn"></a>A háttér tanúsítványa érvénytelen köznév (CN)

**Üzenet:** A háttér tanúsítvány közneve (CN) nem egyezik a mintavétel állomásfejlécének nevével.

**Ok:** Application Gateway ellenőrzi, hogy a háttérkiszolgáló HTTP-beállításaiban megadott állomásnév megegyezik-e a háttérkiszolgáló TLS-/SSL-tanúsítványa által bemutatott CN- nevével. Ez a Standard_v2 és WAF_v2 (V2) viselkedése. A Standard és a WAF termékváltozat (v1) Kiszolgálónév jelzése (SNI) A háttérkészlet címében található teljes tartomány neve. Az SNI viselkedésével és a v1 és v2 termékváltozat közötti különbségekkel kapcsolatos további információkért lásd: [Overview of TLS termination and end](ssl-overview.md)to end TLS with Application Gateway .

Ha a v2 termékváltozatban van alapértelmezett mintavétel (nincs egyéni mintavétel konfigurálva és társítva), az SNI a HTTP-beállításokban említett gazdagépnév alapján lesz beállítva. Ha a HTTP-beállítások között szerepel a "Gazdagépnév beállítása a háttércímből" beállítás, ahol a háttércímkészlet érvényes teljes tartománynevet tartalmaz, ez a beállítás lesz alkalmazva.

Ha a HTTP-beállításokhoz egyéni mintavétel van társítva, az SNI az egyéni mintavételi konfigurációban említett gazdagépnév alapján lesz beállítva. Ha az **egyéni** mintavételben a Gazdagépnév kiválasztása a háttér HTTP-beállításokból lehetőséget választja, az SNI a HTTP-beállításokban említett gazdagépnév alapján lesz beállítva.

Ha **a Gazdagépnév beállítása a** háttércímből beállítás be van állítva a HTTP-beállítások között, a háttércímkészletnek érvényes teljes tartománynevet kell tartalmaznia.

Ha ezt a hibaüzenetet kapja, a háttér tanúsítványának CN-címe nem egyezik meg az egyéni mintavételben vagy a HTTP-beállításokban konfigurált gazdagépnévvel (ha a Gazdagépnév kiválasztása a háttérből **HTTP-beállítások** lehetőséget választotta). Ha alapértelmezett mintavételt használ, az állomásnév **127.0.0.1 lesz.** Ha ez nem egy kívánt érték, hozzon létre egy egyéni mintavételt, és társítsa azt a HTTP-beállításokkal.

**Megoldás:**

A probléma megoldásához kövesse az alábbi lépéseket.

Windows esetén:

1.  Jelentkezzen be arra a gépre, ahol az alkalmazást üzemelteti.

1.  Válassza a Win+R lehetőséget, vagy kattintson a jobb **gombbal a Start gombra,** és válassza a **Futtatás lehetőséget.**

1.  Írja be **a certmgr.msc, majd az** Enter billentyűt. A Start menüben a Tanúsítványkezelőre is **rákereshet.**

1.  Keresse meg a tanúsítványt (általában `\Certificates - Current User\\Personal\\Certificates` a fájlban), és nyissa meg a tanúsítványt.

1.  A Részletek **lapon** ellenőrizze a tanúsítvány **tulajdonosát.**

1.  Ellenőrizze a tanúsítvány CN-ét a részletek között, és adja meg ugyanezt az egyéni mintavétel gazdagépnév mezőjében vagy a HTTP-beállításokban (ha a **Gazdagépnév** kiválasztása a háttér http-beállításokból beállítás be van jelölve). Ha nem ez a webhely kívánt állomásneve, akkor be kell szereznie egy tanúsítványt a tartományhoz, vagy meg kell adnia a megfelelő gazdagépnevet az egyéni mintavétel vagy a HTTP-beállítás konfigurációjában.

OpenSSL-t használó Linux esetén:

1.  Futtassa ezt a parancsot az OpenSSL-ban:
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  A megjelenített tulajdonságok között keresse meg a tanúsítvány CN-ját, és adja meg ugyanezt a HTTP-beállítások gazdagépnév mezőjében. Ha nem ez a webhely kívánt állomásneve, akkor be kell szereznie a tartomány tanúsítványát, vagy meg kell adnia a megfelelő gazdagépnevet az egyéni mintavétel vagy a HTTP-beállítás konfigurációjában.

#### <a name="backend-certificate-is-invalid"></a>A háttér tanúsítvány érvénytelen

**Üzenet:** A háttér tanúsítványa érvénytelen. Az aktuális dátum nem esik a tanúsítvány \" Érvényesség és \" Érvényesség \" dátumtartományán \" belülre.

**Ok:** Minden tanúsítvány érvényességi időtartammal rendelkezik, és a HTTPS-kapcsolat csak akkor lesz biztonságos, ha a kiszolgáló TLS-/SSL-tanúsítványa érvényes. Az aktuális adatoknak  az érvényes és a **tartományon belül kell lennie.** Ha nem, a tanúsítvány érvénytelennek minősül, és ez olyan biztonsági problémát fog létrehozni, amely Application Gateway a háttérkiszolgálót Nem megfelelőként jelöli meg.

**Megoldás:** Ha a TLS-/SSL-tanúsítvány lejárt, újítsa meg a tanúsítványt a gyártónál, és frissítse a kiszolgáló beállításait az új tanúsítvánnyal. Ha ez egy önaírt tanúsítvány, létre kell hoznia egy érvényes tanúsítványt, és fel kell töltenie a főtanúsítványt a Application Gateway HTTP-beállításokba. Ehhez kövesse az alábbi lépéseket:

1.  Nyissa meg Application Gateway HTTP-beállításokat a portálon.

1.  Válassza ki azt a beállítást, amely a lejárt tanúsítvánnyal rendelkezik, válassza a **Tanúsítvány** hozzáadása lehetőséget, és nyissa meg az új tanúsítványfájlt.

1.  Távolítsa el a régi tanúsítványt a tanúsítvány melletti **Törlés** ikonnal, majd válassza a **Mentés lehetőséget.**

#### <a name="certificate-verification-failed"></a>A tanúsítvány ellenőrzése nem sikerült

**Üzenet:** A háttér-tanúsítvány érvényességét nem sikerült ellenőrizni. Az ok kiderítését az OpenSSL diagnosztikában az {errorCode} hibakóddal társított üzenetért tekintse meg.

**Ok:** Ez a hiba akkor Application Gateway nem tudja ellenőrizni a tanúsítvány érvényességét.

**Megoldás:** A probléma megoldásához ellenőrizze, hogy a tanúsítvány megfelelően létrejött-e a kiszolgálón. Az [OpenSSL](https://www.openssl.org/docs/man1.0.2/man1/verify.html) használatával például ellenőrizheti a tanúsítványt és annak tulajdonságait, majd megpróbálhatja újból betölteni a tanúsítványt a Application Gateway HTTP-beállításokba.

<a name="backend-health-status-unknown"></a>Háttér állapota: ismeretlen
-------------------------------
Ha a háttér állapota Ismeretlen, a portálnézet az alábbi képernyőképen láthatóhoz hasonló lesz:

![Application Gateway háttér állapota – Ismeretlen](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

Ez a viselkedés a következő okok közül egy vagy több miatt fordulhat elő:

1.  Az Application Gateway-alhálózat NSG-ja blokkolja a 65503–65534-es (v1 termékváltozat) vagy a 65200–65535-ös (v2 termékváltozat) portok bejövő hozzáférését az "Internet" portokról.
1.  Az UDR Application Gateway alapértelmezett útvonalra van beállítva (0.0.0.0/0), és a következő ugrás nincs megadva "Internet" értékként.
1.  Az alapértelmezett útvonalat egy ExpressRoute-/VPN-kapcsolat hirdeti meg egy virtuális hálózathoz BGP-en keresztül.
1.  Az egyéni DNS-kiszolgáló olyan virtuális hálózaton van konfigurálva, amely nem tudja feloldani a nyilvános tartományneveket.
1.  Az Application Gateway állapota Nem megfelelő.

**Megoldás:**

1.  Ellenőrizze, hogy az NSG blokkolja-e a 65503-65534 (v1 termékváltozat) vagy a 65200-65535 (v2 SKU) portok internetes **hozzáférését:**

    a.  A Application Gateway **lapon** válassza a **Virtual Network/Alhálózat hivatkozást.**

    b.  A **virtuális hálózat Alhálózatok** lapján válassza ki azt az alhálózatot, Application Gateway üzembe lett stb.

    c.  Ellenőrizze, hogy van-e NSG konfigurálva.

    d.  Ha egy NSG konfigurálva van, keresse meg az adott NSG-erőforrást a **Keresés** lapon vagy a **Minden erőforrás alatt.**

    e.  A **Bejövő szabályok** szakaszban adjon hozzá egy bejövő szabályt, amely engedélyezi a 65503–65534-es célporttartományt az 1-es termékváltozathoz vagy a 65200–65535 v2 termékváltozathoz, ha a Forrás beállítása **Bármely** vagy  **Internetes.**

    f.  Válassza **a Mentés** lehetőséget, és ellenőrizze, hogy kifogástalan állapotúként tudja-e megtekinteni a háttért. Másik lehetőségként ezt a [PowerShell/parancssori felület használatával is használhatja.](../virtual-network/manage-network-security-group.md)

1.  Ellenőrizze, hogy az UDR rendelkezik-e alapértelmezett útvonalsal (0.0.0.0/0), és hogy a következő ugrás nem **internetes:**
    
    a.  Kövesse az 1a és az 1b lépést az alhálózat meghatározásához.

    b.  Ellenőrizze, hogy van-e konfigurálva UDR. Ha van, keresse meg az erőforrást a keresősávon, vagy a **Minden erőforrás alatt.**

    c.  Ellenőrizze, hogy vannak-e alapértelmezett útvonalak (0.0.0.0/0) úgy, hogy a következő ugrás nincs **internetként beállítva.** Ha a beállítás **virtuális** berendezés vagy Virtual Network **Gateway,** meg kell győződnie arról, hogy a virtuális berendezés vagy a helyszíni eszköz megfelelően vissza tudja-e irányíthatja a csomagot az internetes célhelyre a csomag módosítása nélkül.

    d.  Ellenkező esetben módosítsa a következő ugrást **internetre,** válassza a **Mentés** lehetőséget, és ellenőrizze a háttér állapotát.

1.  Alapértelmezett útvonal, amelyet az ExpressRoute/VPN-kapcsolat hirdet meg a virtuális hálózathoz BGP-en keresztül:

    a.  Ha ExpressRoute-/VPN-kapcsolattal rendelkezik a virtuális hálózathoz BGP-en keresztül, és egy alapértelmezett útvonalat hirdet meg, meg kell győződnie arról, hogy a csomag vissza van irányítva az internetes célhelyre annak módosítása nélkül. Ezt a kapcsolat hibaelhárítása lehetőséggel **ellenőrizheti** a Application Gateway portálon.

    b.  Válassza ki manuálisan a célhelyet bármilyen internetre átirányítható IP-címként, például 1.1.1.1. Állítsa be a célportot bármiként, és ellenőrizze a kapcsolatot.

    c.  Ha a következő ugrás egy virtuális hálózati átjáró, lehet, hogy egy alapértelmezett útvonal van meghirdetve ExpressRoute- vagy VPN-kapcsolaton keresztül.

1.  Ha egyéni DNS-kiszolgáló van konfigurálva a virtuális hálózaton, ellenőrizze, hogy a kiszolgáló (vagy kiszolgálók) fel tudja-e oldani a nyilvános tartományokat. A nyilvános tartománynévfeloldás olyan esetekben lehet szükséges, Application Gateway a rendszernek külső tartományokat kell elérnie, például OCSP-kiszolgálókat, vagy ellenőriznie kell a tanúsítvány visszavonási állapotát.

1.  Annak ellenőrzéséhez, Application Gateway kifogástalan állapotú-e  és fut-e, Resource Health portálon válassza a Következő lehetőséget, és ellenőrizze, hogy kifogástalan **állapotú-e.** Ha nem **megfelelő** vagy csökkentett  teljesítményű állapotot lát, lépjen kapcsolatba [az ügyfélszolgálattal.](https://azure.microsoft.com/support/options/)

<a name="next-steps"></a>Következő lépések
----------

További információ a [Application Gateway és naplózásról.](./application-gateway-diagnostics.md)
