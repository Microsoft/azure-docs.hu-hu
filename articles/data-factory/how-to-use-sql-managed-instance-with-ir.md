---
title: Az Azure SQL felügyelt példányának használata az Azure-SQL Server Integration Services (SSIS) használatával Azure Data Factory
description: Ismerje meg, hogyan használható az Azure SQL felügyelt példánya SQL Server Integration Services (SSIS) használatával Azure Data Factoryban.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: c268dba51ecf2c2b246c54574ecf3838533b017e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710209"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Az Azure SQL felügyelt példányának használata SQL Server Integration Services (SSIS) használatával Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Most már áthelyezheti SQL Server Integration Services (SSIS) projektjeit, csomagjait és munkaterheléseit az Azure-felhőbe. SSIS-projektek és-csomagok üzembe helyezése, futtatása és kezelése Azure SQL Database vagy SQL felügyelt példányon olyan ismerős eszközökkel, mint például a SQL Server Management Studio (SSMS). Ez a cikk az Azure SQL felügyelt példányának Azure-SSIS Integration Runtime (IR) használatával történő használatakor a következő konkrét területeket emeli ki:

- [Azure-SSIS IR kiépítése az Azure SQL felügyelt példányai által üzemeltetett SSIS-katalógussal (SSISDB)](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [SSIS-csomagok végrehajtása az Azure SQL felügyelt példány-ügynökének feladata](how-to-invoke-ssis-package-managed-instance-agent.md)
- [SSISDB-naplók törlése az Azure SQL felügyelt példány-ügynök feladata alapján](#clean-up-ssisdb-logs)
- [Azure-SSIS IR feladatátvétel az Azure SQL felügyelt példányával](configure-bcdr-azure-ssis-integration-runtime.md)
- [A helyszíni SSIS számítási feladatainak áttelepíthetők az ADF-ben lévő SSIS az Azure SQL felügyelt példányával az adatbázis-munkaterhelés céljaként](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Azure-SSIS IR kiépítése az Azure SQL felügyelt példányai által üzemeltetett SSISDB

### <a name="prerequisites"></a>Előfeltételek

1. A Azure Active Directory hitelesítés kiválasztásakor [engedélyezze Azure Active Directory (Azure ad) használatát az Azure SQL felügyelt példányain](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Válassza ki, hogyan csatlakozhat az SQL felügyelt példányhoz magán-végponton vagy nyilvános végponton keresztül:

    - Privát végponton keresztül (előnyben részesített)

        1. Válassza ki a virtuális hálózatot, Azure-SSIS IR csatlakozni szeretne:
            - Ugyanazon a virtuális hálózaton belül, ahol a felügyelt példány **eltérő alhálózattal** rendelkezik.
            - Egy másik virtuális hálózaton belül, mint a felügyelt példány, virtuális hálózati közvetítéssel (amely az adott régióra korlátozódik a globális VNet-megkötések miatt) vagy virtuális hálózatról virtuális hálózatra való kapcsolódás esetén.

            További információ az SQL felügyelt példányok kapcsolatáról: [az alkalmazás csatlakoztatása az Azure SQL felügyelt példányához](https://review.docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connect-app).

        1. [Konfigurálja a virtuális hálózatot](#configure-virtual-network).

    - Nyilvános végponton keresztül

        Az Azure SQL felügyelt példányai [nyilvános végpontokon](../azure-sql/managed-instance/public-endpoint-configure.md)keresztül is biztosíthatnak kapcsolatot. A bejövő és kimenő követelményeknek meg kell felelniük a felügyelt SQL-példány és a Azure-SSIS IR közötti adatforgalom engedélyezéséhez:

        - Ha Azure-SSIS IR nem a virtuális hálózaton belül (előnyben részesített)

            **Az SQL felügyelt példányának bejövő követelménye**, hogy engedélyezze a Azure-SSIS IR érkező bejövő forgalmat.

            | Átviteli protokoll | Forrás | Forrásporttartomány | Cél | Célporttartomány |
            |---|---|---|---|---|
            |TCP|Azure Cloud Service-címke|*|VirtualNetwork|3342|

            További információ: [a nyilvános végponti forgalom engedélyezése a hálózati biztonsági csoportban](../azure-sql/managed-instance/public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).

        - virtuális hálózaton belüli Azure-SSIS IR

            Ha az SQL felügyelt példány olyan régióban található, amely Azure-SSIS IR nem támogatott, az Azure-SSIS IR egy virtuális hálózaton belül van, anélkül, hogy VNet a globális VNet-társítási korlátozás miatt. Ebben az esetben a **virtuális hálózatokon belüli Azure-SSIS IR** a **nyilvános végponton keresztül** kapcsolódik az SQL felügyelt példányához. Használja az alábbi hálózati biztonsági csoport (NSG) szabályait az SQL felügyelt példány és a Azure-SSIS IR közötti adatforgalom engedélyezéséhez:

            1. **Az SQL felügyelt példányának bejövő követelménye**, hogy engedélyezze a Azure-SSIS IR érkező bejövő forgalmat.

                | Átviteli protokoll | Forrás | Forrásporttartomány | Cél |Célporttartomány |
                |---|---|---|---|---|
                |TCP|Azure-SSIS IR statikus IP-címe <br> Részletekért lásd: [saját nyilvános IP-cím használata Azure-SSIS IRhoz](join-azure-ssis-integration-runtime-virtual-network.md#publicIP).|*|VirtualNetwork|3342|

             1. **A Azure-SSIS IR kimenő** adatforgalmának engedélyezése a kimenő forgalom számára az SQL felügyelt példányain.

                | Átviteli protokoll | Forrás | Forrásporttartomány | Cél |Célporttartomány |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[SQL felügyelt példány nyilvános végpontjának IP-címe](../azure-sql/managed-instance/management-endpoint-find-ip-address.md)|3342|

### <a name="configure-virtual-network"></a>Virtuális hálózat konfigurálása

1. **Felhasználói engedély**. A Azure-SSIS IR létrehozó felhasználónak legalább az alábbi lehetőségek egyikével kell rendelkeznie a [szerepkör-hozzárendeléssel](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) Azure Data Factory erőforráson:

    - Használja a beépített hálózati közreműködő szerepkört. Ehhez a szerepkörhöz a _Microsoft. Network/ \*_ engedély tartozik, amely a szükségesnél sokkal nagyobb hatókörű.
    - Hozzon létre egy egyéni szerepkört, amely csak a szükséges _Microsoft. Network/virtualNetworks/ \* /JOIN/Action_ engedélyt tartalmazza. Ha a Azure-SSIS IR saját nyilvános IP-címeit is szeretné csatlakoztatni a Azure Resource Manager virtuális hálózathoz való csatlakozáshoz, akkor a szerepkörben a _Microsoft. Network/nyilvános IP/*/JOIN/Action_ engedély is szerepel.

1. **Virtuális hálózat**.

    1. Győződjön meg arról, hogy a virtuális hálózat erőforráscsoport létrehozhat és törölhet bizonyos Azure-hálózati erőforrásokat.

        A Azure-SSIS IR létre kell hoznia bizonyos hálózati erőforrásokat a virtuális hálózattal azonos erőforráscsoporthoz. Ezek az erőforrások a következők:
        - Egy Azure Load Balancer, amelynek neve: *\<Guid> azurebatch-cloudserviceloadbalancer*
        - Egy hálózati biztonsági csoport, amelynek neve * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Egy Azure-beli nyilvános IP-cím, amelynek neve: azurebatch-cloudservicepublicip

        Ezek az erőforrások a Azure-SSIS IR indításakor lesznek létrehozva. Ha a Azure-SSIS IR leáll, a rendszer törli őket. Ha nem szeretné megakadályozni a Azure-SSIS IR leállítását, ne használja újra ezeket a hálózati erőforrásokat a többi erőforrásban.

    1. Győződjön meg arról, hogy nincs [erőforrás-zárolás](../azure-resource-manager/management/lock-resources.md) azon az erőforráscsoport/előfizetés esetében, amelyhez a virtuális hálózat tartozik. Ha írásvédett/törlési zárolást konfigurál, a Azure-SSIS IR elindítása és leállítása sikertelen lesz, vagy a rendszer nem válaszol.

    1. Győződjön meg arról, hogy nem rendelkezik olyan Azure-házirenddel, amely megakadályozza, hogy a következő erőforrások jöjjenek létre a virtuális hálózathoz tartozó erőforráscsoport/előfizetés alatt:
        - Microsoft. Network/LoadBalancers
        - Microsoft. Network/NetworkSecurityGroups

    1. A hálózati biztonsági csoport (NSG) szabály forgalmának engedélyezése a felügyelt SQL-példány és a Azure-SSIS IR közötti forgalom, valamint a Azure-SSIS IR által igényelt forgalom engedélyezéséhez.
        1. **Az SQL felügyelt példányának bejövő követelménye**, hogy engedélyezze a Azure-SSIS IR érkező bejövő forgalmat.

            | Átviteli protokoll | Forrás | Forrásporttartomány | Cél | Célporttartomány | Megjegyzések |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000-11999|Ha az SQL Database kiszolgáló-kapcsolódási házirendje az **átirányítás** helyett **proxyként** van beállítva, akkor csak az 1433-es port szükséges.|

        1. **A Azure-SSIS IR kimenő** adatforgalmának engedélyezése az SQL felügyelt példányának, valamint a Azure-SSIS IR által igényelt egyéb forgalom engedélyezéséhez.

        | Átviteli protokoll | Forrás | Forrásporttartomány | Cél | Célporttartomány | Megjegyzések |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 |A kimenő adatforgalom engedélyezése a felügyelt SQL-példányok számára. Ha az **átirányítás** helyett a kapcsolódási szabályzat **proxyra** van beállítva, akkor csak az 1433-es port szükséges. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | A virtuális hálózat Azure-SSIS IR csomópontjai ezt a portot használják az Azure-szolgáltatások, például az Azure Storage és az Azure Event Hubs eléréséhez. |
        | TCP | VirtualNetwork | * | Internet | 80 | Választható A virtuális hálózat Azure-SSIS IR csomópontjai ezt a portot használják a visszavont tanúsítványok listájának internetről való letöltéséhez. Ha letiltja ezt a forgalmat, akkor a teljesítmény-visszalépést tapasztalhatja, ha elindítja az IR-t, és elveszti a tanúsítvány-visszavonási listát a tanúsítvány használatának ellenőrzése érdekében. Ha a célhelyet bizonyos FQDN-re szeretné szűkíteni, tekintse meg az [Azure ExpressRoute vagy a felhasználó által megadott útvonal (UDR) használatát](./join-azure-ssis-integration-runtime-virtual-network.md#route)ismertető témakört.|
        | TCP | VirtualNetwork | * | Tárolás | 445 | Választható Ez a szabály csak akkor szükséges, ha a Azure Filesban tárolt SSIS-csomagot szeretné végrehajtani. |
        |||||||

        1. A **Azure-SSIS IR bejövő követelménye**, amely lehetővé teszi a Azure-SSIS IR számára szükséges forgalom engedélyezését.

        | Átviteli protokoll | Forrás | Forrásporttartomány | Cél | Célporttartomány | Megjegyzések |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (ha az IR-t egy Resource Manager-beli virtuális hálózathoz csatlakoztatja) <br/><br/>10100, 20100, 30100 (ha az IR-t egy klasszikus virtuális hálózathoz csatlakoztatja)| A Data Factory szolgáltatás ezeket a portokat használja a virtuális hálózatban lévő Azure-SSIS IR csomópontjaival való kommunikációhoz. <br/><br/> Azt határozza meg, hogy alhálózati NSG hoz-e létre, Data Factory mindig a Azure-SSIS IR futtató virtuális gépekhez csatlakoztatott hálózati adapterek (NIC-EK) szintjén konfigurálja a NSG. A hálózati adapter szintű NSG csak a megadott portok Data Factory IP-címeiről érkező bejövő forgalmat engedélyezi. Még ha a portok az alhálózat szintjén is megnyitják az internetes forgalmat, akkor a nem Data Factory IP-címekről érkező adatforgalmat a hálózati adapter szintjén blokkolja a rendszer. |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | Választható Ez a szabály csak akkor szükséges, ha a Microsoft támogatási szolgálata megkéri az ügyfelet, hogy nyissa meg a speciális hibaelhárítást, és a hibaelhárítás után közvetlenül be lehessen zárni. A **CorpNetSaw** szolgáltatás címkéje csak a biztonságos hozzáférési munkaállomásokat engedélyezi a Microsoft vállalati hálózaton a távoli asztal használatához. Ezt a szolgáltatási címkét nem lehet kiválasztani a portálról, és csak Azure PowerShell vagy az Azure CLI-n keresztül érhető el. <br/><br/> A hálózati adapterek szintjének NSG a 3389-es port alapértelmezés szerint meg van nyitva, és lehetővé tesszük, hogy a 3389-es portot az alhálózat szintjén NSG, míg a Azure-SSIS IR az egyes IR-csomópontokon a Windows tűzfal szabályában nem engedélyezte a kimenő port 3389. |
        |||||||

    1. További információért lásd a [virtuális hálózat konfigurációját](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration) :
        - Ha saját nyilvános IP-címeit hozza a Azure-SSIS IR
        - Ha saját tartománynévrendszer-(DNS-) kiszolgálót használ
        - Ha Azure ExpressRoute vagy felhasználó által megadott útvonalat használ (UDR)
        - Ha testreszabott Azure-SSIS IR használ

### <a name="provision-azure-ssis-integration-runtime"></a>Kiépítés Azure-SSIS Integration Runtime

1. Válassza az SQL felügyelt példány magánhálózati végpont vagy nyilvános végpont lehetőséget.

    Az Azure Portal/ADF alkalmazásban [Azure-SSIS IR kiépítés](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) esetén az SQL-beállítások lapon az SQL felügyelt példány **privát végpontját** vagy **nyilvános végpontját** használhatja a SSIS-katalógus (SSISDB) létrehozásakor.

    A nyilvános végpont állomásneve <mi_name>. public. <dns_zone>. database.windows.net és a csatlakozáshoz használt port 3342.  

    ![A képernyőképen az Integration Runtime telepítőjét láthatja, amelynek a létrehozása S S S-s katalógus kiválasztott](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. Ha érvényes, válassza az Azure AD-hitelesítést.

    ![Katalógus – nyilvános végpont](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Az Azure AD-hitelesítés engedélyezésével kapcsolatos további információkért lásd: az [Azure ad engedélyezése az Azure SQL felügyelt példányain](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Ha érvényes, csatlakoztassa Azure-SSIS IRt a virtuális hálózathoz.

    A speciális beállítás lapon válassza ki a Virtual Network és az alhálózatot, amelyhez csatlakozni szeretne.
    
    Ha ugyanabban a virtuális hálózatban van, mint az SQL felügyelt példánya, válasszon **másik alhálózatot** , mint az SQL felügyelt példánya. 

    További információ a Azure-SSIS IR virtuális hálózathoz való csatlakoztatásáról: [Azure-SSIS integrációs modul csatlakoztatása egy virtuális hálózathoz](join-azure-ssis-integration-runtime-virtual-network.md).

    ![A képernyőfelvételen az Integration Runtime speciális beállításai láthatók, ahol kiválaszthat egy virtuális hálózatot a futtatókörnyezethez a csatlakozáshoz.](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

A Azure-SSIS IR létrehozásával kapcsolatos további információkért lásd: [Azure-SSIS integrációs modul létrehozása Azure Data Factory-ben](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime).

## <a name="clean-up-ssisdb-logs"></a>SSISDB-naplók törlése

A SSISDB-naplók adatmegőrzési házirendjét az alábbi tulajdonságok határozzák meg [Catalog.catalog_propertiesban](/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database):

- OPERATION_CLEANUP_ENABLED

    Ha az érték TRUE (igaz), a rendszer törli a katalógusból a művelet részleteit és a RETENTION_WINDOW (nap) régebbi műveleti üzeneteket. Ha az érték FALSE (hamis), az összes művelet részletei és a művelet üzenetei a katalógusban tárolódnak. Megjegyzés: a SQL Server feladat végrehajtja a művelet tisztítását.

- RETENTION_WINDOW

    Azon napok száma, amelyekben a művelet részletei és a művelet üzenetei a katalógusban tárolódnak. Ha az érték-1, az adatmegőrzési időszak végtelen. Megjegyzés: Ha nincs szükség karbantartásra, állítsa a OPERATION_CLEANUP_ENABLED hamis értékre.

A rendszergazda által beállított adatmegőrzési ablakon kívüli SSISDB-naplók eltávolításához aktiválhatja a tárolt eljárást `[internal].[cleanup_server_retention_window_exclusive]` . Igény szerint az SQL felügyelt példány-ügynök feladat-végrehajtását ütemezheti a tárolt eljárás elindításához.

## <a name="next-steps"></a>Következő lépések

- [SSIS-csomagok végrehajtása az Azure SQL felügyelt példány-ügynökének feladata](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Üzletmenet-folytonosság és vész-helyreállítás beállítása (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [Helyszíni SSIS számítási feladatok migrálása az ADF-SSIS](scenario-ssis-migration-overview.md)