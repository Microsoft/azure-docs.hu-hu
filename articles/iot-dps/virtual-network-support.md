---
title: Az Azure IoT Device kiépítési szolgáltatás (DPS) támogatása virtuális hálózatokhoz
description: Virtuális hálózatok kapcsolódási mintájának használata az Azure IoT Device kiépítési szolgáltatással (DPS)
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: f1409a931195d236b2729e629e4603c606137593
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94959781"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>Azure IoT Hub Device Provisioning Service (DPS) virtuális hálózatok támogatása

Ez a cikk bemutatja a virtuális hálózat (VNET) kapcsolódási mintáját a IoT-eszközöknek a DPS használatával történő üzembe helyezéséhez. Ez a minta privát kapcsolatot biztosít az eszközök, a DPS és az IoT hub között az ügyfél saját Azure-VNET belül. 

A legtöbb forgatókönyvben, ahol a DPS konfigurálva van egy VNET, a IoT Hub ugyanazon a VNET is konfigurálva lesz. Az IoT-hubok VNET támogatásával és konfigurálásával kapcsolatos további információkért lásd: [IoT hub virtuális hálózat támogatása](../iot-hub/virtual-network-support.md).



## <a name="introduction"></a>Bevezetés

Alapértelmezés szerint a DPS-gazdagépek egy nyilvánosan elérhető IP-címmel rendelkező nyilvános végponthoz rendelnek leképezést az interneten keresztül. Ez a nyilvános végpont minden ügyfél számára látható. A nyilvános végponthoz való hozzáférést a IoT-eszközök széles körű hálózatokon, illetve helyszíni hálózatokon is megpróbálkozhat.

Az ügyfelek több okból is korlátozni szeretnék az Azure-erőforrásokhoz, például a DPS-hoz való kapcsolódást. Ezek az okok a következők:

* A kapcsolat expozíciójának megakadályozása a nyilvános interneten. A kitettség csökkenthető azáltal, hogy a IoT hub és a DPS-erőforrások hálózati szintű elkülönítése révén további biztonsági rétegeket is bevezetnek

* A helyszíni hálózati eszközök privát kapcsolódási élményének lehetővé tétele, amely biztosítja, hogy az adatok és a forgalom közvetlenül az Azure gerinc-hálózatra legyen továbbítva.

* A bizalmas helyszíni hálózatok kiszűrése elleni támadásának megakadályozása. 

* Az Azure-szintű csatlakozási mintákat a [privát végpontok](../private-link/private-endpoint-overview.md)használatával követte.

A kapcsolatok korlátozásának gyakori módszerei közé tartozik a [DPS IP-szűrési szabályok](./iot-dps-ip-filtering.md) és a virtuális HÁLÓZATKEZELÉS (VNET) [privát végpontokkal](../private-link/private-endpoint-overview.md). Ennek a cikknek a célja, hogy leírja a DPS VNET megközelítését privát végpontok használatával. 

A helyszíni hálózatokban üzemelő eszközök [virtuális magánhálózati (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoute](https://azure.microsoft.com/services/expressroute/) privát kapcsolatok használatával csatlakozhatnak az Azure-beli VNET, és privát végpontokon keresztül férhetnek hozzá a DPS-erőforrásokhoz. 

A privát végpontok egy olyan magánhálózati IP-cím, amely az Azure-erőforrások elérhetőségét biztosító, az ügyfél tulajdonában lévő VNET van lefoglalva. Ha a DPS-erőforráshoz privát végpontot használ, lehetővé válik, hogy a VNET belül működő eszközöket a DPS-erőforrás általi kiépítés kérelmezése nélkül is engedélyezzék a nyilvános végpontra irányuló forgalom engedélyezése nélkül.


## <a name="prerequisites"></a>Előfeltételek

A folytatás előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

* A DPS-erőforrás már létre lett hozva, és az IoT-hubhoz van társítva. Az új DPS-erőforrások beállításával kapcsolatos útmutatásért lásd: [IoT hub Device Provisioning Service beállítása a Azure Portal](./quick-setup-auto-provision.md)

* Kiépített egy Azure-VNET egy olyan alhálózattal, amelyben a magánhálózati végpont létre lesz hozva. További információ: [virtuális hálózat létrehozása az Azure CLI használatával](../virtual-network/quick-create-cli.md).

* A helyszíni hálózatokon belül üzemelő eszközökön [virtuális magánhálózati (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) vagy [ExpressRoute](https://azure.microsoft.com/services/expressroute/) -alapú privát kapcsolatok állíthatók be az Azure VNET.

## <a name="private-endpoint-limitations"></a>Magánhálózati végpontokra vonatkozó korlátozások

Privát végpontok használata esetén vegye figyelembe a következő jelenlegi korlátozásokat a DPS esetében:

* A privát végpontok nem fognak működni a DPS-vel, ha a DPS-erőforrás és a társított központ különböző felhőkben található. Például [Azure Government és globális Azure](../azure-government/documentation-government-welcome.md).

* Jelenleg a DPS- [Azure functions rendelkező egyéni foglalási szabályzatok](how-to-use-custom-allocation-policies.md) nem fognak működni, ha az Azure-függvényt VNET és privát végpontokra zárolják. 

* Az aktuális DPS VNET-támogatás csak a DPS-ba irányuló adatforgalom esetén támogatott. Az adatforgalom, amely a DPS és a IoT Hub közötti forgalom, egy belső, szolgáltatások közötti mechanizmust használ, nem pedig dedikált VNET. A teljes VNET-alapú kimenő forgalom támogatása a DPS és a IoT Hub között jelenleg nem érhető el.

* A legalacsonyabb késési kiosztási szabályzattal rendelhet hozzá egy eszközt a IoT hubhoz a legalacsonyabb késéssel. Ez a kiosztási szabályzat nem megbízható a virtuális hálózati környezetben. 

## <a name="set-up-a-private-endpoint"></a>Privát végpont beállítása

Privát végpont beállításához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a DPS-erőforrást, és kattintson a **hálózatkezelés** fülre. Kattintson a **privát végponti kapcsolatok** és a **+ privát végpont** elemre.

    ![Új privát végpont hozzáadása a DPS-hez](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. A _privát végpont_ alapjainak létrehozása lapon adja meg az alábbi táblázatban szereplő adatokat.

    ![Privát végpontok létrehozása – alapismeretek](./media/virtual-network-support/create-private-endpoint-basics.png)

    | Mező | Érték |
    | :---- | :-----|
    | **Előfizetés** | Válassza ki a kívánt Azure-előfizetést, hogy tartalmazza a privát végpontot.  |
    | **Erőforráscsoport** | Válasszon ki vagy hozzon létre egy erőforráscsoportot, amely tartalmazza a privát végpontot |
    | **Név**       | Adja meg a privát végpont nevét |
    | **Régió**     | A választott régiónak meg kell egyeznie a VNET tartalmazó régióval, de nem kell megegyeznie a DPS-erőforrással. |

    Kattintson a **Tovább gombra:** az erőforrás konfigurálásához, amelyre a privát végpont mutat.

3. A _magánhálózati végpont-erőforrás létrehozása_ lapon adja meg az alábbi táblázatban szereplő adatokat.

    ![Privát végponti erőforrás létrehozása](./media/virtual-network-support/create-private-endpoint-resource.png)

    | Mező | Érték |
    | :---- | :-----|
    | **Előfizetés**        | Válassza ki azt az Azure-előfizetést, amely azt a DPS-erőforrást tartalmazza, amelyet a privát végpont fog mutatni.  |
    | **Erőforrás típusa**       | Válassza a **Microsoft. Devices/ProvisioningServices** lehetőséget. |
    | **Erőforrás**            | Válassza ki azt a DPS-erőforrást, amelyhez a privát végpont hozzá fog rendelni. |
    | **Célzott alerőforrás** | Válassza a **iotDps** lehetőséget. |

    > [!TIP]
    > Ebben a cikkben az **Azure-erőforráshoz való kapcsolódás erőforrás-azonosító vagy alias** -beállítás alapján [című szakasza](#request-a-private-endpoint) nyújt tájékoztatást.


    Kattintson a **Tovább gombra: konfiguráció** lehetőségre a privát végpont VNET konfigurálásához.

4. A magánhálózati _végpont konfigurálása_ lapon válassza ki a virtuális hálózatot és az alhálózatot a saját végpont létrehozásához a alkalmazásban.
 
    Kattintson a **Tovább gombra: címkék**, és opcionálisan adja meg az erőforráshoz tartozó címkéket.

    ![Privát végpont konfigurálása](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. Kattintson a **felülvizsgálat + létrehozás** , majd a **Létrehozás** elemre a saját végpont-erőforrás létrehozásához.


## <a name="request-a-private-endpoint"></a>Privát végpont kérése

Az erőforrás-azonosító alapján privát végpontot is igényelhet a DPS-erőforráshoz. A kérelem elvégzéséhez az erőforrás-tulajdonosnak meg kell adnia az erőforrás-azonosítót. 

1. Az erőforrás-azonosítót a DPS-erőforrás Tulajdonságok lapján, az alábbi ábrán látható módon kell megadnia.

    ![DPS-Tulajdonságok lap](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > Ügyeljen arra, hogy az erőforrás-azonosító tartalmazza az előfizetés AZONOSÍTÓját. 

2. Ha rendelkezik az erőforrás-AZONOSÍTÓval, kövesse a [privát végpont beállítása](#set-up-a-private-endpoint) a 3. lépésben a _privát végpont erőforrás létrehozása_ oldalon található lépéseket. Kattintson a **Kapcsolódás Azure-erőforráshoz erőforrás-azonosító vagy alias alapján** lehetőségre, és adja meg az alábbi táblázatban szereplő adatokat. 

    | Mező | Érték |
    | :---- | :-----|
    | **Erőforrás-azonosító vagy alias** | Adja meg a DPS-erőforrás erőforrás-AZONOSÍTÓját. |
    | **Célzott alerőforrás** | **IotDps** megadása |
    | **Kérelem üzenete** | Adja meg a DPS-erőforrás tulajdonosának kérési üzenetét.<br>Példa: <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    Kattintson a **Tovább gombra: konfiguráció** lehetőségre a privát végpont VNET konfigurálásához.

3. A magánhálózati _végpont konfigurálása_ lapon válassza ki azt a virtuális hálózatot és alhálózatot, amelyben létre szeretné hozni a privát végpontot a alkalmazásban.
 
    Kattintson a **Tovább gombra: címkék**, és opcionálisan adja meg az erőforráshoz tartozó címkéket.

4. Válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás** lehetőséget a saját végponti kérelem létrehozásához.

5. A DPS-tulajdonos a privát végponti kérést fogja látni a saját végponti **kapcsolatok** listájában a DPS hálózatkezelés lapján. Ezen az oldalon a tulajdonos **jóváhagyhatja** vagy **elutasíthatja** a privát végpont kérelmét az alább látható módon.

    ![DPS-jóváhagyás](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>Privát végpontok díjszabása

A díjszabással kapcsolatos információkért lásd: az [Azure Private link díjszabása](https://azure.microsoft.com/pricing/details/private-link).



## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozásokat követve további információkat tudhat meg a DPS biztonsági funkcióiról:

* [Biztonság](./concepts-service.md#attestation-mechanism)
* [TLS 1,2-támogatás](tls-support.md)