---
title: A Azure Firewall használata a privát végpontoknak szánt forgalom vizsgálatára
titleSuffix: Azure Private Link
description: Ebből a témakörből megtudhatja, hogyan vizsgálhatja meg Azure Firewall használatával a privát végpontoknak szánt forgalmat.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: 3ed349616ae6456913c19bb073f6e9ea28e7d549
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575129"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>A Azure Firewall használata a privát végpontoknak szánt forgalom vizsgálatára

Az Azure Private-végpont az Azure privát kapcsolatának alapvető építőeleme. A privát végpontok lehetővé teszik, hogy a virtuális hálózaton üzembe helyezett Azure-erőforrások magánjellegű módon kommunikáljanak a privát kapcsolati erőforrásokkal.

A magánhálózati végpontok lehetővé teszik, hogy az erőforrások hozzáférjenek a virtuális hálózatban üzembe helyezett privát kapcsolati szolgáltatáshoz. A magánhálózati végponthoz való hozzáférés a virtuális hálózatok és a helyszíni hálózati kapcsolatok révén kiterjeszti a kapcsolatot.

Előfordulhat, hogy meg kell vizsgálnia vagy le kell tiltania az ügyfelektől a privát végpontokon keresztül közzétett szolgáltatások felé irányuló forgalmat. Ezt a vizsgálatot [Azure Firewall](../firewall/overview.md) vagy külső hálózati virtuális berendezés használatával hajtsa végre.

Az alábbi korlátozások érvényesek:

* A hálózati biztonsági csoportok (NSG) nem vonatkoznak a privát végpontokra
* A felhasználó által megadott útvonalak (UDR-EK) nem vonatkoznak a privát végpontokra
* Egyetlen útválasztási tábla csatlakoztatható egy alhálózathoz
* Az útválasztási táblázat legfeljebb 400 útvonalat támogat

A Azure Firewall a következők egyikével szűri a forgalmat:

* A TCP-és UDP [-protokollok hálózati szabályainak teljes tartományneve](../firewall/fqdn-filtering-network-rules.md)
* FQDN a HTTP-, HTTPS-és MSSQL [-alkalmazási szabályokban](../firewall/features.md#application-fqdn-filtering-rules) . 

A privát végpontokon keresztül elérhető szolgáltatások többsége HTTPS protokollt használ. Az Azure SQL használata esetén ajánlott az alkalmazási szabályok használata a hálózati szabályokon keresztül.

> [!NOTE]
> Az SQL FQDN-szűrés csak [proxy módban](../azure-sql/database/connectivity-architecture.md#connection-policy) támogatott (1433-es port). A **proxy** mód több késést eredményezhet az *átirányításhoz* képest. Ha továbbra is az átirányítási módot szeretné használni, amely az Azure-on keresztül csatlakozó ügyfelek esetében az alapértelmezett, akkor a hozzáférést a tűzfal hálózati szabályok teljes tartománynevével szűrheti.

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>1. forgatókönyv: hub és küllős architektúra – dedikált virtuális hálózat privát végpontokhoz

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="Dedikált Virtual Network privát végpontokhoz" border="true":::

Ez a forgatókönyv a legátfogóbb architektúra, amely privát végpontok használatával több Azure-szolgáltatáshoz is csatlakozhat. Létrejön egy útvonal, amely arra a hálózati címtartományre mutat, ahol a magánhálózati végpontok telepítve vannak. Ez a konfiguráció csökkenti az adminisztratív terhelést, és megakadályozza a futtatást a 400 útvonalon.

Ha a virtuális hálózatok egymáshoz kapcsolódnak, az ügyfél virtuális hálózata és a központi virtuális hálózat Azure Firewall közötti kapcsolat díjköteles lesz.

A társ virtuális hálózatokkal létesített kapcsolatokkal kapcsolatos díjakról további információt a [díjszabási](https://azure.microsoft.com/pricing/details/private-link/) oldal gyakori kérdések szakasza tartalmaz.

>[!NOTE]
> Ez a forgatókönyv bármely harmadik féltől származó NVA vagy Azure Firewall hálózati szabály használatával valósítható meg az alkalmazási szabályok helyett.

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>2. forgatókönyv: hub és küllős architektúra – megosztott virtuális hálózat magánhálózati végpontok és virtuális gépek számára

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="Magánhálózati végpontok és Virtual Machinesek azonos Virtual Network" border="true":::

Ez a forgatókönyv a következő esetekben valósítható meg:

* A privát végpontokhoz nem lehet dedikált virtuális hálózat.

* Ha csak néhány szolgáltatást tesznek elérhetővé a virtuális hálózaton privát végpontok használatával

A virtuális gépek/32 rendszerútvonalak mindegyik privát végpontra mutatnak. A magánhálózati végpontok egy útvonala úgy van konfigurálva, hogy Azure Firewallon keresztül irányítsa a forgalmat. 

Az útválasztási táblázat fenntartásának adminisztratív terhelése növekszik, mivel a szolgáltatások a virtuális hálózaton elérhetők. Az útvonal-korlát megnyomásának lehetősége is nő.

A teljes architektúrától függően lehetséges, hogy az 400-es útvonalakra vonatkozó korlátot futtatja. Ajánlott az 1. esetet használni, amikor csak lehetséges.

Ha a virtuális hálózatok egymáshoz kapcsolódnak, az ügyfél virtuális hálózata és a központi virtuális hálózat Azure Firewall közötti kapcsolat díjköteles lesz.

A társ virtuális hálózatokkal létesített kapcsolatokkal kapcsolatos díjakról további információt a [díjszabási](https://azure.microsoft.com/pricing/details/private-link/) oldal gyakori kérdések szakasza tartalmaz.

>[!NOTE]
> Ez a forgatókönyv bármely harmadik féltől származó NVA vagy Azure Firewall hálózati szabály használatával valósítható meg az alkalmazási szabályok helyett.

## <a name="scenario-3-single-virtual-network"></a>3. forgatókönyv: egyetlen virtuális hálózat

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="Egyetlen virtuális hálózat" border="true":::

Bizonyos korlátozások vonatkoznak a megvalósításra: a központba való Migrálás és a küllős architektúra nem lehetséges. Ugyanazok a szempontok, mint a 2. forgatókönyv esetében. Ebben a forgatókönyvben a virtuális hálózati társítási díjak nem érvényesek.

>[!NOTE]
> Ha ezt a forgatókönyvet harmadik féltől származó NVA vagy Azure Firewall használatával szeretné megvalósítani, akkor az alkalmazási szabályok helyett hálózati szabályokra van szükség ahhoz, hogy SNAT a titkos végpontokra irányuló forgalmat. Ellenkező esetben a virtuális gépek és a magánhálózati végpontok közötti kommunikáció sikertelen lesz.

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>4. forgatókönyv: a helyszíni forgalom magánhálózati végpontokra

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="Helyszíni forgalom magánhálózati végpontokra" border="true":::

Ez az architektúra akkor valósítható meg, ha a következővel konfigurálta a helyszíni hálózathoz való kapcsolódást: 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [Helyek közötti VPN](../vpn-gateway/tutorial-site-to-site-portal.md) 

Ha a biztonsági követelmények megkövetelik, hogy az ügyfél a privát végpontokon keresztül elérhető szolgáltatásokra legyen továbbítva egy biztonsági berendezésen keresztül, akkor ezt a forgatókönyvet kell telepíteni.

Ugyanazokat a szempontokat kell figyelembe venni, mint a fenti 2. forgatókönyvben. Ebben a forgatókönyvben nincsenek virtuális hálózati társítási díjak. További információ arról, hogyan konfigurálhatja a DNS-kiszolgálókat, hogy a helyszíni számítási feladatok hozzáférjenek a privát végpontokhoz, lásd: helyszíni [munkaterhelések DNS-továbbító használatával](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

>[!NOTE]
> Ha ezt a forgatókönyvet harmadik féltől származó NVA vagy Azure Firewall használatával szeretné megvalósítani, akkor az alkalmazási szabályok helyett hálózati szabályokra van szükség ahhoz, hogy SNAT a titkos végpontokra irányuló forgalmat. Ellenkező esetben a virtuális gépek és a magánhálózati végpontok közötti kommunikáció sikertelen lesz.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés.
* Egy Log Analytics-munkaterület.  

Ha még nem rendelkezik az előfizetésében, a [Azure Portal hozzon létre egy log Analytics munkaterületet a](../azure-monitor/logs/quick-create-workspace.md) munkaterületen.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és alhálózatot a privát kapcsolati erőforrás elérésére használt virtuális gép üzemeltetéséhez. Az Azure SQL Database-t használja példaként szolgáló szolgáltatásként.

## <a name="virtual-networks-and-parameters"></a>Virtuális hálózatok és paraméterek

Hozzon létre három virtuális hálózatot és a hozzájuk tartozó alhálózatokat a következőhöz:

* A virtuális gép és a privát végpont közötti kommunikáció korlátozására szolgáló Azure Firewall tartalmazza.
* A magánhálózati kapcsolati erőforrás eléréséhez használt virtuális gép üzemeltetése.
* A magánhálózati végpont üzemeltetése.

Cserélje le a következő paramétereket a lépésekben az alábbi információkkal:

### <a name="azure-firewall-network"></a>Azure Firewall hálózat
| Paraméter                   | Érték                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | USA déli középső régiója      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>Virtuálisgép-hálózat
| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | USA déli középső régiója      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | Alhálózat      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>Magánhálózati végpont hálózata
| Paraméter                   | Érték                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | USA déli középső régiója      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet    |        |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. Ismételje meg az 1 – 9. lépést a virtuális gép és a magánhálózati végpont erőforrásainak üzemeltetéséhez szükséges virtuális hálózatok létrehozásához.

### <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** lehetőséget.

2. A **virtuális gép létrehozása – alapismeretek** területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az erőforráscsoportot az előző szakaszban hozta létre.  |
    | **Példány adatai** |  |
    | Virtuális gép neve | Adja meg a **myVM**. |
    | Region | Válassza ki az USA **déli középső** régióját. |
    | Rendelkezésre állási beállítások | Az alapértelmezett **infrastruktúra-redundancia megadása nem kötelező**. |
    | Kép | Válassza az **Ubuntu Server 18,04 LTS-Gen1** lehetőséget. |
    | Méret | Válassza a **Standard_B2s** lehetőséget. |
    | **Rendszergazdai fiók** |  |
    | Hitelesítéstípus | Válassza a **jelszó** lehetőséget. |
    | Felhasználónév | Adja meg a választott felhasználónevet. |
    | Jelszó | Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Jelszó megerősítése | Adja meg újra a jelszót. |
    | **Bejövő portok szabályai** |  |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |
    |||

3. Válassza a **Tovább: lemezek** lehetőséget.

4. A **Virtuális gép létrehozása – Lemezek** lehetőségnél hagyja meg az alapértelmezett értékeket, és válassza a **Tovább: Hálózatkezelés** lehetőséget.

5. A **virtuálisgép-hálózat létrehozása** területen válassza ki ezt az információt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Válassza a **myVMVNet** lehetőséget.  |
    | Alhálózat | Válassza a **alhálózat (10.1.0.0/24)** lehetőséget.|
    | Nyilvános IP-cím | Hagyja meg az alapértelmezett **(új) myVm-IP-címet**. |
    | Nyilvános bejövő portok | Válassza a **kiválasztott portok engedélyezése** lehetőséget. |
    | Válassza ki a bejövő portokat | Válassza az **SSH** lehetőséget.|
    ||

6. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját.

7. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget.

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

2. Írja be a **tűzfal** kifejezést a keresőmezőbe, majd nyomja le az **ENTER** billentyűt.

3. Válassza a **tűzfal** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

4. A **Tűzfal létrehozása** oldalon konfigurálja a tűzfalat a következő táblázatban található értékekkel:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget.  |
    | **Példány adatai** |  |
    | Name | Adja meg a **myAzureFirewall**. |
    | Region | Válassza az **USA déli középső** régióját. |
    | A rendelkezésre állási zóna | Hagyja meg az alapértelmezett **nincs** értéket. |
    | Válasszon egy virtuális hálózatot    |    Válassza a **meglévő használata** lehetőséget.    |
    | Virtuális hálózat    |    Válassza a **myAzFwVNet** lehetőséget.    |
    | Nyilvános IP-cím    |    Válassza az **új hozzáadása** lehetőséget, és a név mezőben adja meg a **myFirewall-IP** értéket.    |
    | Alagúthasználat kényszerítése    | Hagyja meg az alapértelmezett **Letiltva** értéket.    |
    |||
5. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját.

6. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget.

## <a name="enable-firewall-logs"></a>Tűzfal naplófájljainak engedélyezése

Ebben a szakaszban engedélyezheti a naplókat a tűzfalon.

1. A Azure Portal a bal oldali menüben válassza a **minden erőforrás** elemet.

2. Válassza ki a tűzfal **myAzureFirewall** az erőforrások listájában.

3. A **tűzfal beállításai területen válassza** a **diagnosztikai beállítások** elemet.

4. Válassza a **+ diagnosztika hozzáadása** lehetőséget a diagnosztikai beállításokban.

5. A **diagnosztika beállítása** mezőben adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Diagnosztikai beállítás neve | Adja meg a **myDiagSetting**. |
    | Kategória részletei | |
    | napló | Válassza a **AzureFirewallApplicationRule** és a **AzureFirewallNetworkRule** lehetőséget. |
    | Célhely részletei | Válassza **a küldés log Analytics** lehetőséget. |
    | Előfizetés | Válassza ki előfizetését. |
    | Log Analytics-munkaterület | Válassza ki Log Analytics munkaterületét. |

6. Kattintson a **Mentés** gombra.

## <a name="create-azure-sql-database"></a>Azure SQL Database létrehozása

Ebben a szakaszban egy privát SQL Database hoz létre.

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **adatbázisok**  >  **SQL Database** lehetőséget.

2. A **SQL Database létrehozása – alapismeretek** területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. Ezt az erőforráscsoportot az előző szakaszban hozta létre.|
    | **Adatbázis adatai** |  |
    | Adatbázis neve  | Adja meg a **mydatabase**.  |
    | Kiszolgáló | Válassza az **új létrehozása** lehetőséget, és adja meg az alábbi információkat.    |
    | Kiszolgálónév | Adja meg a **mydbserver**. Ha ezt a nevet hozza, adjon meg egy egyedi nevet.   |
    | Kiszolgáló-rendszergazdai bejelentkezés | Adja meg a választott nevet. |
    | Jelszó    |    Adjon meg egy tetszőleges jelszót.    |
    | Jelszó megerősítése | Jelszó újbóli megadása    |
    | Hely    | Válassza ki az USA **déli középső** régióját.    |
    | Rugalmas SQL-készletet szeretne használni    | Hagyja meg az alapértelmezett **nem** értéket. |
    | Számítás + tárolás | Hagyja meg az alapértelmezett **általános célú Gen5, 2 virtuális mag, 32 GB tárterületet**. |
    |||

3. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját.

4. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget.

## <a name="create-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban egy privát végpontot hoz létre az Azure SQL Database-hez az előző szakaszban.

1. A Azure Portal a bal oldali menüben válassza a **minden erőforrás** elemet.

2. Válassza ki az Azure SQL Server **mydbserver** a szolgáltatások listájában.  Ha más kiszolgálónevet használt, válassza ki ezt a nevet.

3. A kiszolgáló beállításai között válassza a **privát végponti kapcsolatok** lehetőséget a **Biztonság** területen.

4. Válassza a **+ privát végpont** lehetőséget.

5. A **privát végpont létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget. |
    | **Példány adatai** | |
    | Name | Adja meg a **SQLPrivateEndpoint**. |
    | Region | Válassza ki az USA **déli középső** régióját. |

6. Válassza ki az **erőforrás** lapot, vagy válassza a **Next (tovább** ) gombot az oldal alján.

7. Az **erőforrás** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kapcsolati módszer | Válassza a **Kapcsolódás egy Azure-erőforráshoz a címtárban** lehetőséget. |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft. SQL/kiszolgálók** lehetőséget. |
    | Erőforrás | Válassza ki a **mydbserver** vagy az előző lépésben létrehozott kiszolgáló nevét.
    | Célzott alerőforrás | Válassza a **sqlServer** lehetőséget. |

8. Válassza a **konfiguráció** lapot, vagy válassza a **következő: konfigurálás** elemet az oldal alján.

9. A **konfiguráció** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Hálózat** | |
    | Virtuális hálózat | Válassza a **myPEVnet** lehetőséget. |
    | Alhálózat | Válassza a **PrivateEndpointSubnet** lehetőséget. |
    | **saját DNS integráció** | |
    | Integrálás saját DNS-zónával | Válassza az **Igen** lehetőséget. |
    | Előfizetés | Válassza ki előfizetését. |
    | Privát DNS-zónák | Hagyja meg az alapértelmezett **privatelink.database.Windows.net**. |

10. Válassza a **felülvizsgálat + létrehozás** lapot, vagy válassza a lap alján található **felülvizsgálat + létrehozás** elemet.

11. Válassza a **Létrehozás** lehetőséget.

12. A végpont létrehozása után válassza a **tűzfalak és virtuális hálózatok** lehetőséget a **Biztonság** területen.

13. A **tűzfalak és virtuális hálózatok** területen válassza az **Igen** lehetőséget, hogy az **Azure-szolgáltatások és-erőforrások hozzáférjenek ehhez a kiszolgálóhoz**.

14. Kattintson a **Mentés** gombra.

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>Virtuális hálózatok összekötése virtuális hálózati kapcsolattal

Ebben a szakaszban a Virtual Networks **myVMVNet** és a **myPEVNet** összekapcsolását a **myAzFwVNet** szolgáltatással. Nem lesz közvetlen kapcsolat a **myVMVNet** és a **myPEVNet** között.

1. A portál keresési sávján adja meg a **myAzFwVNet**.

2. Válassza a társítások lehetőséget a **Beállítások** menüben, majd **válassza a** **+ Hozzáadás** lehetőséget.

3. A társítás **hozzáadása** területen adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | A myAzFwVNet-ből távoli virtuális hálózatra irányuló társ neve | Adja meg **a myAzFwVNet-to-myVMVNet** értéket. |
    | **Társ részletei** |  |
    | Virtuális hálózat telepítési modellje  | Hagyja meg az alapértelmezett **erőforrás-kezelőt**.  |
    | Ismerem az erőforrás-azonosítót | Hagyja üresen.    |
    | Előfizetés | Válassza ki előfizetését.    |
    | Virtuális hálózat | Válassza a **myVMVNet** lehetőséget. |
    | A távoli virtuális hálózatról a myAzFwVNet-re irányuló peer neve    |    Adja meg **a myVMVNet-to-myAzFwVNet** értéket.    |
    | **Konfigurálás** | |
    | **Virtuális hálózati hozzáférési beállítások konfigurálása** | |
    | Virtuális hálózati hozzáférés engedélyezése a myAzFwVNet a távoli virtuális hálózathoz | Hagyja meg az alapértelmezett **beállítást**.    |
    | Virtuális hálózati hozzáférés engedélyezése a távoli virtuális hálózatról a myAzFwVNet    | Hagyja meg az alapértelmezett **beállítást**.    |
    | **Továbbított forgalmi beállítások konfigurálása** | |
    | Továbbított forgalom távoli virtuális hálózatról myAzFwVNet-re való továbbításának engedélyezése    | Válassza az **Engedélyezve** lehetőséget. |
    | Továbbított forgalom engedélyezése a myAzFwVNet-ből távoli virtuális hálózatra | Válassza az **Engedélyezve** lehetőséget. |
    | **Átjáró átviteli beállításainak konfigurálása** | |
    | Átjáró átvitelének engedélyezése | Ne jelölje be |
    |||

4. Válassza az **OK** lehetőséget.

5. Válassza a **+ Hozzáadás** lehetőséget.

6. A társítás **hozzáadása** területen adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | A myAzFwVNet-ből távoli virtuális hálózatra irányuló társ neve | Adja meg **a myAzFwVNet-to-myPEVNet** értéket. |
    | **Társ részletei** |  |
    | Virtuális hálózat telepítési modellje  | Hagyja meg az alapértelmezett **erőforrás-kezelőt**.  |
    | Ismerem az erőforrás-azonosítót | Hagyja üresen.    |
    | Előfizetés | Válassza ki előfizetését.    |
    | Virtuális hálózat | Válassza a **myPEVNet** lehetőséget. |
    | A távoli virtuális hálózatról a myAzFwVNet-re irányuló peer neve    |    Adja meg **a myPEVNet-to-myAzFwVNet** értéket.    |
    | **Konfigurálás** | |
    | **Virtuális hálózati hozzáférési beállítások konfigurálása** | |
    | Virtuális hálózati hozzáférés engedélyezése a myAzFwVNet a távoli virtuális hálózathoz | Hagyja meg az alapértelmezett **beállítást**.    |
    | Virtuális hálózati hozzáférés engedélyezése a távoli virtuális hálózatról a myAzFwVNet    | Hagyja meg az alapértelmezett **beállítást**.    |
    | **Továbbított forgalmi beállítások konfigurálása** | |
    | Továbbított forgalom távoli virtuális hálózatról myAzFwVNet-re való továbbításának engedélyezése    | Válassza az **Engedélyezve** lehetőséget. |
    | Továbbított forgalom engedélyezése a myAzFwVNet-ből távoli virtuális hálózatra | Válassza az **Engedélyezve** lehetőséget. |
    | **Átjáró átviteli beállításainak konfigurálása** | |
    | Átjáró átvitelének engedélyezése | Ne jelölje be |

7. Válassza az **OK** lehetőséget.

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>A virtuális hálózatok összekapcsolása a magánhálózati DNS-zónával

Ebben a szakaszban a Virtual Networks **myVMVNet** és a **myAzFwVNet** összekapcsoljuk a **privatelink.database.Windows.net** magánhálózati DNS-zónával. Ez a zóna a privát végpont létrehozásakor jött létre. 

A hivatkozás szükséges ahhoz, hogy a virtuális gép és a tűzfal feloldja az adatbázis teljes tartománynevét a saját végpontjának címeként. A virtuális hálózati **myPEVNet** automatikusan csatolták a magánhálózati végpont létrehozásakor.

>[!NOTE]
>Ha nem kapcsolja össze a virtuális GÉPET és a tűzfal virtuális hálózatait a magánhálózati DNS-zónával, a virtuális gép és a tűzfal továbbra is képes lesz feloldani a SQL Server teljes tartománynevet. A rendszer feloldja a nyilvános IP-címét.

1. A portál keresési sávjában adja meg a **privatelink. Database** kifejezést.

2. A keresési eredmények között válassza a **privatelink.database.Windows.net** lehetőséget.

3. Válassza a **virtuális hálózati kapcsolatok** lehetőséget a **Beállítások** területen.

4. Válassza a **+ Hozzáadás** lehetőséget

5. A **virtuális hálózat hozzáadása hivatkozásnál** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Hivatkozás neve | Adja meg **a hivatkozás – myVMVNet** értéket. |
    | **Virtuális hálózat részletei** |  |
    | Tudom, hogy a virtuális hálózat erőforrás-azonosítója  | Hagyja üresen.  |
    | Előfizetés | Válassza ki előfizetését.    |
    | Virtuális hálózat | Válassza a **myVMVNet** lehetőséget. |
    | **CONFIGURATION** | |
    | Automatikus regisztráció engedélyezése | Hagyja üresen.    |


6. Válassza az **OK** lehetőséget.

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>Alkalmazás-szabály konfigurálása SQL FQDN használatával Azure Firewall

Ebben a szakaszban egy olyan alkalmazás-szabályt konfigurál, amely lehetővé teszi a **myVM** és a privát végpont közötti kommunikációt SQL Server **mydbserver.database.Windows.net**. 

Ez a szabály lehetővé teszi az előző lépésekben létrehozott tűzfalon keresztüli kommunikációt.

1. A portál keresési sávján adja meg a **myAzureFirewall**.

2. A keresési eredmények között válassza a **myAzureFirewall** lehetőséget.

3. A **myAzureFirewall** áttekintésében válassza a **Beállítások** alatt található **szabályok** elemet.

4. Válassza ki az **alkalmazás-szabály gyűjtemény** fület.

5. Válassza az **+ alkalmazás-szabály gyűjtemény hozzáadása** elemet.

6. Az **alkalmazási szabály hozzáadása gyűjteménybe** írja be vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **SQLPrivateEndpoint**. |
    | Prioritás | Adja meg a **100** értéket. |
    | Művelet | Adja meg az **Engedélyezés** értéket. |
    | **Szabályok** |  |
    | **FQDN-címkék** | |
    | Name  | Hagyja üresen.  |
    | Forrás típusa | Hagyja meg az alapértelmezett **IP-címet**.    |
    | Forrás | Hagyja üresen. |
    | FQDN-címkék | Hagyja meg az alapértelmezett **0 beállítást**. |
    | **Cél teljes tartománynevek** | |
    | Name | Adja meg a **SQLPrivateEndpoint**.    |
    | Forrás típusa | Hagyja meg az alapértelmezett **IP-címet**. |
    | Forrás | Adja meg a **10.1.0.0/16** értéket. |
    | Protokoll: Port | Adja meg az **MSSQL: 1433** értéket. |
    | Cél teljes tartománynevek | Adja meg a **mydbserver.database.Windows.net**. |
    |||

7. Válassza a **Hozzáadás** lehetőséget.

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>Átirányítja a forgalmat a virtuális gép és a magánhálózati végpont között Azure Firewall

Nem hoztunk létre virtuális hálózati kapcsolatot közvetlenül a Virtual Networks **myVMVNet** és a **myPEVNet** között. A virtuális gép **myVM** nem rendelkezik a létrehozott privát végpont útvonalával. 

Ebben a szakaszban egy útválasztási táblázatot hozunk létre egy egyéni útvonallal. 

Az útvonal továbbítja a forgalmat a **myVM** alhálózatról a virtuális hálózati **myPEVNet** a Azure Firewall keresztül.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

2. Írja be a keresőmezőbe az **útválasztási táblázat** kifejezést, majd nyomja le az **ENTER** billentyűt.

3. Válassza az **útválasztási táblázat** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

4. Az útválasztási **táblázat létrehozása** lapon a következő táblázat segítségével konfigurálja az útválasztási táblázatot:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **myResourceGroup** lehetőséget.  |
    | **Példány adatai** |  |
    | Region | Válassza az **USA déli középső** régióját. |
    | Name | Adja meg **a alhálózat-to-AzureFirewall** értéket. |
    | Átjáró-útvonalak propagálása | Válassza a **Nem** lehetőséget. |

5. Válassza az **Áttekintés + létrehozás** lehetőséget. Az **Áttekintés és létrehozása** lapra kerül, ahol az Azure érvényesíti az Ön konfigurációját.

6. Amikor megjelenik a **Megfelelt az ellenőrzésen** üzenet, válassza a **Létrehozás** lehetőséget.

7. Miután az üzembe helyezés befejeződött, válassza az **erőforrás keresése** lehetőséget.

8. Válassza az **útvonalak** elemet a **Beállítások** területen.

9. Válassza a **+ Hozzáadás** lehetőséget.

10. Az **útvonal hozzáadása** lapon adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Útvonal neve | Adja meg **a myVMsubnet-to-privateendpoint** értéket. |
    | Címelőtag | Adja meg a **10.2.0.0/16** értéket.  |
    | A következő ugrás típusa | Válassza a **Virtuális berendezés** lehetőséget. |
    | A következő ugrás címe | Adja meg a **10.0.0.4**. |

11. Válassza az **OK** lehetőséget.

12. Válassza ki az **alhálózatok** elemet a **Beállítások** területen.

13. Válassza a **+ hozzárendelés** lehetőséget.

14. Az **alhálózat hozzárendelése** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Virtuális hálózat | Válassza a **myVMVNet** lehetőséget. |
    | Alhálózat | Válassza a **alhálózat** lehetőséget.  |

15. Válassza az **OK** lehetőséget.

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>Kapcsolódás a virtuális géphez az ügyfélszámítógépről

Kapcsolódjon a virtuális gép **myVm** az internetről a következőképpen:

1. A portál keresési sávjában adja meg a **myVm-IP** értéket.

2. Válassza a **myVM-IP** elemet a keresési eredmények között.

3. Másolja vagy írja le az értéket az **IP-cím** területen.

4. Ha a Windows 10 rendszert használja, futtassa a következő parancsot a PowerShell használatával. Más Windows-ügyfél-verziók esetében használjon egy SSH-ügyfelet, például a [Putty](https://www.putty.org/)-t:

* Cserélje le a **felhasználónevet** a virtuális gép létrehozásakor megadott rendszergazdai felhasználónévre.

* Cserélje **le az IP-** címet az előző lépésben megadott IP-címekre.

    ```bash
    ssh username@IPaddress
    ```

5. Adja meg a **myVm** létrehozásakor megadott jelszót

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>Hozzáférési SQL Server a virtuális gépről

Ebben a szakaszban a privát végponton keresztül fog csatlakozni a SQL Databasehoz.

1. `nslookup mydbserver.database.windows.net` megadása
    
    Az alábbihoz hasonló üzenet jelenik meg:

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. Telepítse [SQL Server parancssori eszközöket](/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-ver15#tools).

3. Futtassa a következő parancsot a SQL Serverhoz való kapcsolódáshoz. Használja az előző lépésekben a SQL Server létrehozásakor megadott kiszolgáló-rendszergazdát és jelszót.

* A helyére írja **\<ServerAdmin>** be az SQL Server létrehozásakor megadott rendszergazdai felhasználónevet.

* A helyére írja **\<YourPassword>** be az SQL Server létrehozásakor megadott rendszergazdai jelszót.

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. A sikeres bejelentkezés során egy SQL-parancssor jelenik meg. A **Sqlcmd** eszközből való kilépéshez írja be a **Kilépés parancsot** .

5. Zárja be a **myVM** való kapcsolódást a **Kilépés** beírásával.

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>Azure Firewall naplók forgalmának ellenőrzése

1. A Azure Portal válassza a **minden erőforrás** lehetőséget, majd válassza ki a log Analytics munkaterületet.

2. A Log Analytics munkaterület lapon válassza az **általános** **lehetőséget.**

3. Válassza a kék **első lépések** gombot.

4. A **példa lekérdezések** ablakban válassza a **tűzfalak** lehetőséget az **összes lekérdezés** területen.

5. Kattintson a **Futtatás** gombra az **alkalmazási szabály naplójának adata** alatt.

6. A naplózási lekérdezés kimenetében ellenőrizze, hogy a **mydbserver.database.Windows.net** szerepel-e a **teljes tartománynév** területen, és a **SQLPrivateEndpoint** elem szerepel-e a **RuleCollection** területen.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült az erőforrásokkal, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Adja meg a **myResourceGroup** a portál tetején található **keresőmezőbe** , és válassza a **myResourceGroup** lehetőséget a keresési eredmények közül.

1. Válassza az **Erőforráscsoport törlése** elemet.

1. Írja be a **myResourceGroup** **nevet az erőforráscsoport neveként** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben különböző forgatókönyveket ismertetett, amelyekkel korlátozhatja a virtuális gépek és a privát végpontok közötti forgalmat Azure Firewall használatával. 

Csatlakoztatta a virtuális gépet, és biztonságosan kommunikál az adatbázissal Azure Firewall privát hivatkozás használatával.

További információ a privát végpontról: [Mi az az Azure Private Endpoint?](private-endpoint-overview.md).