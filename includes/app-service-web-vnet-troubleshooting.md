---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: cec44bbabdb7d528c30a8d3396b819f2eb3c5386
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999439"
---
A szolgáltatás egyszerűen beállítható, de ez nem jelenti azt, hogy a probléma ingyenes lesz. Ha problémák merülnek fel a kívánt végpont elérésekor, néhány segédprogram segítségével tesztelheti a kapcsolatot az alkalmazás-konzolról. Két konzolt használhat. Az egyik a kudu-konzol, a másik pedig a Azure Portal konzolja. A kudu-konzol alkalmazásból való eléréséhez nyissa meg az **eszközök**  >  **kudu**. A Kudo-konzolt a következő helyen is elérheti: [sitename]. SCM. azurewebsites. net. A webhely betöltése után lépjen a **Debug konzol** lapra. Ha az alkalmazásból szeretné beolvasni a Azure Portal által üzemeltetett konzolt, lépjen az **eszközök**  >  **konzolra**.

#### <a name="tools"></a>Eszközök
Natív Windows-alkalmazásokban a **ping**, az **nslookup** és a **tracert** eszköz nem fog működni a konzolon a biztonsági korlátozások miatt ( [Egyéni Windows-tárolókban](../articles/app-service/quickstart-custom-container.md)működnek). Az üresség kitöltéséhez két különálló eszközt adnak hozzá. A DNS-funkciók teszteléséhez hozzáadott egy **nameresolver.exe** nevű eszközt. A szintaxis a következő:

```console
nameresolver.exe hostname [optional: DNS Server]
```

A nameresolver segítségével megtekintheti az alkalmazástól függő gazdagépeket. Így ellenőrizheti, hogy van-e hibásan konfigurálva a DNS-sel, vagy lehet, hogy nem fér hozzá a DNS-kiszolgálóhoz. Az alkalmazás által a-konzolon használt DNS-kiszolgáló az WEBSITE_DNS_SERVER és WEBSITE_DNS_ALT_SERVER környezeti változók megtekintésével tekinthető meg.

> [!NOTE]
> nameresolver.exe jelenleg nem működik az egyéni Windows-tárolókban.
>

A következő eszközzel tesztelheti a TCP-kapcsolatot egy gazdagéphez és a portok kombinációjára. Az eszköz neve **tcpping** , és a szintaxis a következő:

```console
tcpping.exe hostname [optional: port]
```

A **tcpping** segédprogrammal megtudhatja, hogy elérhető-e egy adott gazdagép és port. Ez csak akkor jeleníthető meg, ha egy alkalmazás figyeli a gazdagép és a port kombinációját, és van hálózati hozzáférése az alkalmazásból a megadott gazdagéphez és porthoz.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Virtuális hálózat által üzemeltetett erőforrásokhoz való hozzáférés hibakeresése
Számos dolog megakadályozhatja, hogy az alkalmazás elérjen egy adott gazdagépet és portot. A legtöbb esetben ez az egyik ilyen dolog:

* **A tűzfal éppen folyamatban van.** Ha tűzfallal rendelkezik, a TCP-időtúllépést is elérheti. Ebben az esetben a TCP-időkorlát 21 másodperc. A kapcsolat teszteléséhez használja a **tcpping** eszközt. A TCP-időtúllépéseket a tűzfalakon túl sok dolog okozhatja, de itt is elindítható.
* **A DNS nem érhető el.** DNS-kiszolgáló esetén a DNS-időtúllépés 3 másodperc. Ha két DNS-kiszolgálóval rendelkezik, az időtúllépés 6 másodperc. A nameresolver használatával ellenőrizze, hogy a DNS működik-e. Az nslookup nem használható, mert az nem használja a virtuális hálózat DNS-sel való konfigurálását. Ha nem érhető el, lehet, hogy tűzfallal vagy NSG blokkolja a hozzáférést a DNS-hez, vagy leállt.

Ha ezek az elemek nem válaszolnak a problémákra, először tekintse meg a következő dolgokat:

**Regionális VNet-integráció**
* A cél nem RFC1918-címet tartalmaz, és nincs WEBSITE_VNET_ROUTE_ALL beállítva 1-re?
* Van NSG blokkolása az integrációs alhálózatról?
* Ha az Azure ExpressRoute vagy egy VPN-en keresztül történik, a helyszíni átjáró úgy van konfigurálva, hogy az Azure-ba irányítsa a forgalmat? Ha a virtuális hálózatban található végpontok nem a helyszínen érhetők el, ellenőrizze az útvonalakat.
* Van elegendő engedélye a delegálás beállítására az integrációs alhálózaton? A regionális VNet integrációs konfigurációjában az integrációs alhálózat delegálása a Microsoft. Web/kiszolgálófarmok. A VNet integrációs KEZELŐFELÜLETe automatikusan delegálja az alhálózatot a Microsoft. Web/kiszolgálófarmok számára. Ha a fiókja nem rendelkezik megfelelő hálózati engedélyekkel a delegálás beállításához, szüksége lesz egy olyan személyre, aki az integrációs alhálózaton attribútumokat állíthat be az alhálózat delegálására. Az integrációs alhálózat manuális delegálásához nyissa meg az Azure Virtual Network alhálózat felhasználói felületét, és állítsa be a Microsoft. Web/kiszolgálófarmok delegálását.

**Átjáró – szükséges VNet-integráció**
* A pont – hely címtartomány az RFC 1918-tartományokban (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Megjelenik az átjáró a portálon? Ha az átjáró nem működik, hozza létre a biztonsági mentést.
* A tanúsítványok szinkronként jelennek meg, vagy azt gyanítja, hogy a hálózati konfiguráció megváltozott?  Ha a tanúsítványok nincsenek szinkronban, vagy ha azt gyanítja, hogy a ASP nem szinkronizált virtuális hálózati konfiguráció módosítása történt, válassza a **szinkronizálás hálózat** lehetőséget.
* Ha a VPN-en keresztül történik, a helyszíni átjáró úgy van konfigurálva, hogy a forgalmat az Azure-ba irányítsa? Ha a virtuális hálózatban található végpontok nem a helyszínen érhetők el, ellenőrizze az útvonalakat.
* Olyan párhuzamos átjárót próbál használni, amely támogatja mind a pont-hely, mind a ExpressRoute? Az együttélési átjárók nem támogatottak a VNet-integrációval.

A hálózati problémák hibakeresése kihívást jelent, mivel nem tudja megtekinteni, hogy mi blokkolja a hozzáférést egy adott gazdagéphez: Port kombináció. Bizonyos okok a következők:

* Rendelkezik egy tűzfallal a gazdagépen, amely megakadályozza, hogy hozzáférjen az alkalmazás portjához a pont – hely IP-tartományból. Az alhálózatok átlépéséhez gyakran nyilvános hozzáférésre van szükség.
* A célként megadott gazdagép nem működik.
* Az alkalmazás nem érhető el.
* Nem megfelelő IP-címet vagy állomásnevet adott meg.
* Az alkalmazás a várttól eltérő portot figyel. A folyamat AZONOSÍTÓját megtekintheti a figyelő porton a "netstat-Aon" paranccsal a végponti gazdagépen.
* A hálózati biztonsági csoportok úgy vannak konfigurálva, hogy megakadályozzák az alkalmazás gazdagépének és portjának elérését a pont – hely IP-tartományból.

Nem tudja, milyen címet használ ténylegesen az alkalmazás. Az integrációs alhálózat vagy a pont – hely címtartomány bármely címe lehet, ezért a teljes címtartományból engedélyeznie kell a hozzáférést.

További hibakeresési lépések a következők:

* Kapcsolódjon a virtuális hálózatban található virtuális GÉPHEZ, és próbálja meg elérni az erőforrás-gazdagépet: innen a port. A TCP-hozzáférés teszteléséhez használja a következő PowerShell **-parancsot: test-NETCONNECTION**. A szintaxis a következő:

```powershell
test-netconnection hostname [optional: -Port]
```

* Egy alkalmazás üzembe helyezése egy virtuális gépen, valamint a gazdagép és a port elérésének tesztelése a konzolról az **tcpping** használatával.

#### <a name="on-premises-resources"></a>Helyszíni erőforrások ####

Ha az alkalmazás nem tud helyszíni erőforrást elérni, ellenőrizze, hogy elérhető-e az erőforrás a virtuális hálózatról. Használja a **test-NETCONNECTION** PowerShell-parancsot a TCP-hozzáférés ellenőrzéséhez. Ha a virtuális gép nem tudja elérni a helyszíni erőforrást, előfordulhat, hogy a VPN-vagy ExpressRoute-kapcsolat nem megfelelően van konfigurálva.

Ha a virtuális hálózat által üzemeltetett virtuális gép elérheti a helyszíni rendszerét, de az alkalmazás nem tudja, az ok a következő okok egyike lehet:

* Az útvonalak nincsenek konfigurálva az alhálózat vagy a pont – hely címtartomány között a helyszíni átjárón.
* A hálózati biztonsági csoportok blokkolja a pont – hely IP-címtartomány elérését.
* A helyszíni tűzfalak blokkolja a pont – hely IP-címtartomány forgalmát.
* Nem RFC 1918-címeket próbál elérni a regionális VNet-integrációs szolgáltatás használatával.
