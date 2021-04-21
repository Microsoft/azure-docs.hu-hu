---
title: A Azure Migrate használata privát végpontokkal
description: A Azure Migrate kapcsolat támogatása privát kapcsolat használatával deríthető fel, értékelhető és mi áttelepítésre használható.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/07/2020
ms.openlocfilehash: 82811c731c158d970d7ec2c2350a0cba106f6a67
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835487"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>A Azure Migrate használata privát végpontokkal  

Ez a cikk azt ismerteti, hogyan használható Azure Migrate kiszolgálók felderítésére, értékelésére és áttelepítésére egy magánhálózaton az Azure privát [kapcsolat használatával.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 

A [Azure Migrate:](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-discovery-and-assessment-tool) Felderítés és felmérés és [Azure Migrate: Server Migration-eszközök](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) használatával privát és biztonságos módon csatlakozhat az Azure Migrate-szolgáltatáshoz ExpressRoute privát társviszony-létesítésen vagy hely–hely VPN-kapcsolaton keresztül, Azure privát kapcsolat használatával. 

A privát végpont csatlakozási módszere akkor ajánlott, ha a nyilvános hálózatokon való áthaladás Azure Migrate az Azure Migrate szolgáltatáshoz és más Azure-erőforrásokhoz való hozzáférésre van szükség. A privát kapcsolatok támogatásával a meglévő ExpressRoute privát társviszony-létesítés kapcsolati kapcsolatát is használhatja a nagyobb sávszélességre vagy késésre vonatkozó követelmények érdekében. 

## <a name="support-requirements"></a>Támogatási követelmények 

### <a name="required-permissions"></a>Szükséges engedélyek

**Közreműködői és felhasználói hozzáférés-rendszergazdai** vagy **tulajdonosi** engedélyek az előfizetésben. 

### <a name="supported-scenarios-and-tools"></a>Támogatott forgatókönyvek és eszközök

**Üzembe helyezés** | **Részletek** | **Eszközök** 
--- | --- | ---
**Felderítés és értékelés** | Ügynök nélküli, nagy léptékű felderítést és értékelést végezhet a kiszolgálókon, amelyek bármely platformon futnak – hipervizorplatformokon, például [az VMware vSphere-on](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware) vagy [az Microsoft Hyper-V-on,](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v)nyilvános felhőkön, például az [AWS-ben](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws) vagy [a GCP-ben,](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp)vagy akár operációs rendszer nélküli kiszolgálókon. [](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical) | Azure Migrate: Felderítés és értékelés  <br/> 
**Szoftverleltár** | Felderítheti a VMware virtuális gépeken futó alkalmazásokat, szerepköröket és szolgáltatásokat. | Azure Migrate: Felderítés és értékelés  
**Függőségek vizualizációja** | A függőségelemzési funkcióval azonosíthatja és megértheti a kiszolgálók közötti függőségeket. <br/> [Az ügynök nélküli függőségek](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies-agentless) vizualizációja natív módon támogatott a Azure Migrate kapcsolat támogatásával. <br/>[Az ügynökalapú függőségvizualizációhoz](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) internetkapcsolat szükséges. [Megtudhatja,](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) hogyan használhatja a privát végpontokat az ügynökalapú függőségek vizualizációjára. | Azure Migrate: Felderítés és értékelés |
**Migrálás** | Ügynök [nélküli Hyper-V áttelepítéseket](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) hajt végre, vagy az ügynökalapú megközelítést használja a [VMware](./tutorial-migrate-vmware-agent.md)virtuális gépek, [a Hyper-V](./tutorial-migrate-physical-virtual-machines.md)virtuális [gépek,](./tutorial-migrate-physical-virtual-machines.md)a fizikai kiszolgálók, az [AWS-ben](./tutorial-migrate-aws-virtual-machines.md)futó virtuális gépek, a [GCP-vel](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)futó virtuális gépek vagy egy másik virtualizálási szolgáltatón futó virtuális gépek áttelepítéséhez. | Azure Migrate: Kiszolgáló migrálása
 
>[!Note]
>
> [Az ügynök nélküli VMware-migráláshoz](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) internetkapcsolatra vagy kapcsolatra van szükség az ExperessRoute Microsoft társviszony-létesítésen keresztül. <br/> [Ismerje meg,](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute) hogyan végezhet replikálást privát végpontok használatával ExpressRoute privát társviszony-létesítésen vagy hely–hely (S2S) VPN-kapcsolaton keresztül.  <br/><br/> 
   
#### <a name="other-integrated-tools"></a>Egyéb integrált eszközök

Előfordulhat, hogy egyes migrálási eszközök nem tudják feltölteni a használati adatokat a Azure Migrate projektbe, ha a nyilvános hálózati hozzáférés le van tiltva. A Azure Migrate-projektet úgy kell konfigurálni, hogy az összes hálózatról származó forgalom más Microsoft- vagy külső független szoftverszállítói [(ISV-)](https://docs.microsoft.com/azure/migrate/migrate-services-overview#isv-integration) ajánlatokból fogadható legyen. 


Ha engedélyezni szeretné a nyilvános hálózati hozzáférést a Azure Migrate projekthez, a Azure Migrate **tulajdonságok** lapján válassza a **Nem** lehetőséget, Azure Portal a Mentés **lehetőséget.**

![A hálózati hozzáférési mód beállítását bemutató ábra.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>További szempontok   

**Megfontolások** | **Részletek**
--- | --- 
**Díjszabás** | Díjszabási információkért lásd: [Az Azure Blob díjszabása és](https://azure.microsoft.com/pricing/details/storage/page-blobs/) az Azure privát kapcsolat [díjszabása.](https://azure.microsoft.com/pricing/details/private-link/)  
**A virtuális hálózat követelményei** | Az ExpressRoute/VPN-átjáró végpontjának a kiválasztott virtuális hálózaton vagy egy csatlakoztatott virtuális hálózaton kell lennie. Előfordulhat, hogy a virtuális hálózatban ~15 IP-címre lesz szüksége.  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Projekt létrehozása privát végponti kapcsolattal

Ebből a [cikkből](https://docs.microsoft.com/azure/migrate/create-manage-projects#create-a-project-for-the-first-time) egy új Azure Migrate állíthat be. 

> [!Note]
> A kapcsolati módszer nem változtatható privát végpontkapcsolatra meglévő Azure Migrate projektek esetében.

A Speciális **konfiguráció** szakaszban adja meg az alábbi adatokat egy privát végpont létrehozásához a Azure Migrate projekthez.
- A **Kapcsolati módszerben** válassza a **Privát végpont lehetőséget.** 
- A **Nyilvános végpont hozzáférésének letiltása beállításnál** tartsa meg a Nem alapértelmezett **beállítást.** Előfordulhat, hogy néhány migrálási eszköz nem tud használati adatokat feltölteni a Azure Migrate projektbe, ha a nyilvános hálózati hozzáférés le van tiltva. [Részletek](#other-integrated-tools)
- A **Virtuális hálózati előfizetés mezőben** válassza ki a privát végpont virtuális hálózatának előfizetését. 
- A **Virtuális hálózat részen** válassza ki a privát végpont virtuális hálózatát. A Azure Migrate berendezésnek és az egyéb szoftverösszetevőknek, amelyeknek csatlakozniuk kell a Azure Migrate projekthez, ezen a hálózaton vagy egy csatlakoztatott virtuális hálózaton kell lennie.
- Az **Alhálózat mezőben** válassza ki a privát végpont alhálózatát. 

Válassza a **Létrehozás** lehetőséget. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Ne zárja be ezt az oldalt, amíg a projekt létrehozása folyamatban van.

![Projekt létrehozása](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

    
Ez létrehoz egy áttelepítési projektet, és csatol hozzá egy privát végpontot. 

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>Kiszolgálók felderítése és felmérése migráláshoz az Azure private link használatával 

### <a name="set-up-the-azure-migrate-appliance"></a>A Azure Migrate berendezés beállítása 

1. A **Gépek felderítése**  >  **Virtualizáltak a gépek?** mezőben válassza ki a kiszolgáló típusát.
2. A Generate Azure Migrate project key (Projektkulcs **létrehozása)** alatt adja meg a Azure Migrate nevét. 
3. Válassza **a Kulcs létrehozása lehetőséget** a szükséges Azure-erőforrások létrehozásához. 

    > [!Important]
    > Ne zárja be a Gépek felderítése lapot az erőforrások létrehozása során.  
    - Ebben a lépésben a Azure Migrate létrehoz egy kulcstartót, egy tárfiókot, egy Recovery Services-tárolót (csak ügynök nélküli VMware-migrálások esetén), valamint néhány belső erőforrást, és csatol egy privát végpontot az egyes erőforrásokhoz. A privát végpontok a projekt létrehozásakor kiválasztott virtuális hálózatban vannak létrehozva.  
    - A privát végpontok létrehozása után az Azure Migrate-erőforrások DNS CNAME erőforrásrekordjai egy "privatelink" előtagú altartomány aliasaiként frissülnek. Alapértelmezés szerint a Azure Migrate létrehoz egy privát DNS-zónát is, amely megfelel a "privatelink" altartománynak minden erőforrástípushoz, és beszúrja a DNS A rekordokat a társított privát végpontok számára. Ez lehetővé teszi a Azure Migrate berendezés és a forráshálózatban található egyéb szoftverösszetevők számára, hogy elérjék Azure Migrate magánhálózati IP-címeken található erőforrás-végpontokat.  
    - Azure Migrate egy felügyelt identitást [is](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) lehetővé tesz a miigrate projekt számára, és engedélyeket ad a felügyelt identitásnak a tárfiók biztonságos eléréséhez.  

4. A kulcs sikeres létrehozása után másolja ki a kulcs adatait a berendezés konfigurálása és regisztrálása érdekében.   

#### <a name="download-the-appliance-installer-file"></a>A berendezés telepítőfájlja letöltése  

Azure Migrate: A felderítéshez és az értékeléshez egy kis Azure Migrate kell használni. A berendezés elvégzi a kiszolgálófelderítést, és elküldi a kiszolgáló konfigurációját és a teljesítmény metaadatait a Azure Migrate.

A berendezés beállításhoz töltse le a telepítő szkriptjét tartalmazó tömörített fájlt a portálról. Másolja ki a tömörített fájlt a berendezést gazdakiszolgálón. A tömörített fájl letöltése után ellenőrizze a fájl biztonságát, és futtassa a telepítő szkriptjét a berendezés üzembe helyezéséhez. 

Az egyes forgatókönyvek letöltési hivatkozásai a kivonatértékekkel együtt a következőek:

Eset | Letöltési hivatkozás | Kivonat értéke
--- | --- | ---
Hyper-V | [AzureMigrateInstaller-HyperV-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160557) | 17EFA01E3A7683F1CE2A08E3A9197A27D8BD2CC03C3AB5C6E00E4261A822BDB3
Fizikai | [AzureMigrateInstaller-Physical-Public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160558) | 01028F92C2095452F2DCB89986CDC1F177AAC58E150A5B219A69CF1B7DA3BE0
VMware | [AzureMigrateInstaller-VMware-public-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160648) | 66D3217AEC1DE51D84EC608B22BDDA605EC9C4FCAF69FEC985886627C224
VMware-felskálás | [AzureMigrateInstaller-VMware-Public-Scaleout-PrivateLink.zip](https://go.microsoft.com/fwlink/?linkid=2160811) | 42C1E8D5CF428E35E5B98E4e7465DD08439F0F0FD5C319340CE3E3ADC3DC1717A6

#### <a name="verify-security"></a>Biztonság ellenőrzése

Az üzembe helyezés előtt ellenőrizze, hogy a tömörített fájl biztonságos-e.

1. Nyisson meg egy rendszergazdai parancsablakot a kiszolgálón, amelyre letöltötte a fájlt.
2. Futtassa a következő parancsot a tömörített fájl kivonatának létrehozásához

    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Példa a nyilvános felhő használatára: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink.zip SHA256 ```

3.  Ellenőrizze a berendezés legújabb verzióját a fenti táblázat kivonatértékének összehasonlításával.

Győződjön meg arról, [](https://docs.microsoft.com/azure/migrate/migrate-appliance) hogy a kiszolgáló megfelel a választott forgatókönyv (VMware/Hyper-V/Fizikai vagy egyéb) hardverkövetelményeinek, és képes csatlakozni a szükséges Azure URL-címekhez [–](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) nyilvános és kormányzati [](./migrate-appliance.md#government-cloud-urls-for-private-link-connectivity) felhőkhöz.


#### <a name="run-the-script"></a>A szkript futtatása

1. Bontsa ki a tömörített fájlt a berendezést tartalmazó kiszolgáló egyik mappájába. 
2. Indítsa el a PowerShellt a gépen rendszergazdai (emelt szintű) jogosultságokkal.
3. Módosítsa a PowerShell-könyvtárat arra a mappára, amely a letöltött tömörített fájlból kicsomagolt tartalmat tartalmazza.
4. Futtassa a **AzureMigrateInstaller.ps1** a következőképpen:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-VMware-public-PrivateLink> .\AzureMigrateInstaller.ps1```
   
5. Miután a szkript sikeresen lefutott, elindítja a berendezéskonfiguráció-kezelőt, hogy konfigurálni tudja a berendezést. Ha bármilyen probléma merül fel, tekintse át a C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log fájlban található szkriptnaplókat.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>A berendezés konfigurálása és a folyamatos felderítés kezdete

Nyisson meg egy böngészőt bármely gépen, amely csatlakozni tud a berendezéskiszolgálóhoz, és nyissa meg a berendezés konfigurációkezelőjének URL-címét: `https://appliance name or IP address: 44368` . Másik lehetőségként megnyithatja a konfigurációkezelőt a berendezés kiszolgálójának asztalán a konfigurációkezelő parancsikonjának kiválasztásával.

#### <a name="set-up-prerequisites"></a>Előfeltételek beállítása

1. Olvassa el a harmadik féltől származó információkat, és fogadja el a **licencszerződést.**    
 
2. A Configuration Managerben > **előfeltételek beállítását.** Tegye a következőket:
   - **Kapcsolat:** A berendezés ellenőrzi a szükséges URL-címekhez való hozzáférést. Ha a kiszolgáló proxyt használ:
     - Válassza **a Proxy beállítása lehetőséget** a proxycím vagy a figyelőport `http://ProxyIPAddress` `http://ProxyFQDN` megadásához.
     - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel. Csak a HTTP-proxyk használata támogatott.
     - Ha szeretné, hozzáadhatja azon URL-címek/IP-címek listáját, amelyek megkerülik a proxykiszolgálót. ExpressRoute privát társviszony-létesítés használata esetén ügyeljen arra, hogy megkerülje ezeket az [URL-címeket.](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute#configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations)
     - A konfiguráció **regisztráláshoz válassza** a Mentés lehetőséget, ha frissítette a proxykiszolgáló adatait, vagy URL-címeket/IP-címeket adott hozzá a proxy megkerülése miatt.
     
        > [!Note]
        > Ha a kapcsolat ellenőrzése során aka.ms/* hivatkozással kapcsolatos hibaüzenetet kap, és nem szeretné, hogy a berendezés hozzáférjen ehhez az URL-címhez az interneten keresztül, le kell tiltania az automatikus frissítési szolgáltatást a berendezésen az itt található lépéseket [**követve.**](https://docs.microsoft.com/azure/migrate/migrate-appliance#turn-off-auto-update) Az automatikus frissítés letiltása után a aka.ms/* URL-kapcsolat ellenőrzése ki lesz hagyva. 

   - **Időszinkronizálás:** A berendezésen található időnek szinkronban kell lennie az internettel, hogy a felderítés megfelelően működjön.
   - **Frissítések telepítése:** A berendezés gondoskodik arról, hogy a legújabb frissítések telepítve vannak. Az ellenőrzés befejezése után a  Készülékszolgáltatások megtekintése lehetőséget választva megtekintheti a berendezéskiszolgálón futó szolgáltatások állapotát és verzióit.
        > [!Note]
        > Ha úgy döntött, hogy letiltja az automatikus frissítési szolgáltatást a berendezésen, manuálisan frissítheti a berendezési szolgáltatásokat, hogy le tudja szerezni a szolgáltatások legújabb verzióit az itt található lépéseket [**követve.**](https://docs.microsoft.com/azure/migrate/migrate-appliance#manually-update-an-older-version)
   - **VDDK** telepítése: ( Csak _VMware-berendezéshez szükséges)_ A berendezés ellenőrzi, hogy VMware vSphere Virtual Disk Development Kit (VDDK) telepítve van-e. Ha nincs telepítve, töltse le a VDDK 6.7-et a VMware-ből, és bontsa ki a letöltött zip-fájlokat a berendezés megadott helyére a telepítési **utasításokban megadottak szerint.**

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Regisztrálja a berendezést, és indítsa el a folyamatos felderítést

Az előfeltételek ellenőrzése után az alábbi lépésekkel regisztrálhatja a berendezést, és elindíthatja a folyamatos felderítést a megfelelő forgatókönyvek esetében: [VMware](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#register-the-appliance-with-azure-migrate)virtuális gépek, [Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v#register-the-appliance-with-azure-migrate)virtuális [gépek,](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical#register-the-appliance-with-azure-migrate)fizikai kiszolgálók, [AWS](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws#register-the-appliance-with-azure-migrate)virtuális gépek, GCP virtuális [gépek.](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp#register-the-appliance-with-azure-migrate)


>[!Note]
> Ha DNS-feloldási problémákat kap a berendezés regisztrációja vagy a felderítés indítása során, győződjön meg arról, hogy a portálon a Kulcs létrehozása lépés során létrehozott Azure Migrate-erőforrások elérhetőek a berendezést üzemeltető helyszíni Azure Migrate-berendezésről.  [További információ a hálózati kapcsolat ellenőrzésével kapcsolatos tudnivalókról.](#troubleshoot-network-connectivity)

### <a name="assess-your-servers-for-migration-to-azure"></a>A kiszolgálók felmérése az Azure-ba való migráláshoz
A felderítés befejezése után mérje fel a kiszolgálókat[(VMware](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm)virtuális [](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm)gépeket, [Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v)virtuális gépeket, fizikai kiszolgálókat, [AWS](https://docs.microsoft.com/azure/migrate/tutorial-assess-aws)virtuális gépeket, [GCP](https://docs.microsoft.com/azure/migrate/tutorial-assess-gcp)virtuális gépeket) az Azure-beli virtuális gépekre vagy az Azure VMware Solution(AVS) szolgáltatásba való migráláshoz az Azure Migrate: Felderítés és felmérés eszköz használatával. 

A helyszíni [](https://docs.microsoft.com/azure/migrate/tutorial-discover-import#prepare-the-csv) gépeket az importált, vesszővel elválasztott (CSV) fájllal Azure Migrate: Felderítés és felmérés eszköz használatával is értékelheti.   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>Kiszolgálók áttelepítése az Azure-ba privát Azure-kapcsolat használatával

A következő szakaszok ismertetik a privát [](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) végpontokkal Azure Migrate expressRoute privát társviszony-létesítés vagy VPN-kapcsolatok használatával való migráláshoz szükséges lépéseket.  

Ez a cikk a [VMware](./tutorial-migrate-vmware-agent.md)virtuális gépek, [a Hyper-V](./tutorial-migrate-physical-virtual-machines.md)virtuális gépek, a fizikai kiszolgálók, az [](./tutorial-migrate-physical-virtual-machines.md) [AWS-ben](./tutorial-migrate-aws-virtual-machines.md)futó virtuális gépek, a [GCP-vel](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)futó virtuális gépek vagy a más virtualizálási szolgáltatón futó virtuális gépek áttelepítésére szolgáló ügynökalapú replikációk egy koncepció igazolási útvonalát mutatja be. Hasonló megközelítést használhat ügynök nélküli [Hyper-V áttelepítések](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) privát kapcsolat használatával való végrehajtásához.

>[!Note]
>[Az ügynök nélküli VMware-migráláshoz](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical) internetkapcsolatra vagy kapcsolatra van szükség az ExperessRoute Microsoft társviszony-létesítésen keresztül. 

### <a name="set-up-a-replication-appliance-for-migration"></a>Replikációs berendezés beállítása migráláshoz 

Az alábbi ábra az ügynökalapú replikációs munkafolyamatot mutatja be privát végpontokkal a Azure Migrate: Server Migration eszköz használatával.  

![Replikációs architektúra](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

Az eszköz egy replikációs berendezés használatával replikálja a kiszolgálókat az Azure-ba. Ebben a cikkben előkészítheti és beállíthatja a gépet [a replikációs berendezéshez. ](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#prepare-a-machine-for-the-replication-appliance)

A replikációs berendezés beállítása után az alábbi utasításokat követve hozza létre a migráláshoz szükséges erőforrásokat. 

1. A **Gépek felderítése**  >  **Virtualizáltak a gépek?** alatt válassza a Nem **virtualizált/Egyéb lehetőséget.**
2. A **Célterület területen** válassza ki és erősítse meg azt az Azure-régiót, amelybe a gépeket át szeretné miolni.
3. Válassza **az Erőforrások létrehozása lehetőséget** a szükséges Azure-erőforrások létrehozásához. Ne zárja be az oldalt az erőforrások létrehozása során.   
    - Ez létrehoz egy Recovery Services-tárolót a háttérben, és engedélyezi a tároló felügyelt identitását. A Recovery Services-tároló egy olyan entitás, amely a kiszolgálók replikációs adatait tartalmazza, és a replikációs műveletek aktiválására szolgál.  
    - Ha a Azure Migrate projekt privát végpontkapcsolattal rendelkezik, a rendszer létrehoz egy privát végpontot a Recovery Services-tárolóhoz. Ez öt teljes privát nevet (FQDN) ad hozzá a privát végponthoz, egyet-egyet a Recovery Services-tárolóhoz csatolt minden egyes mikroszolgáltatáshoz.   
    - Az öt tartománynév a következő mintában van formázva: <br/> _{Vault-ID}-asr-pod01-{type}-. {target-geo-code}_. privatelink.siterecovery.windowsazure.com  
    - Alapértelmezés szerint a Azure Migrate létrehoz egy privát DNS-zónát, és DNS A-rekordokat ad hozzá a Recovery Services-tároló mikroszolgáltatásaihoz. A privát DNS-zóna ezután a privát végpont virtuális hálózatához lesz csatolva. Ez lehetővé teszi, hogy a helyszíni replikációs berendezés feloldja a teljes tartományneveket a magánhálózati IP-címére.

4. A replikációs berendezés regisztrálása előtt győződjön meg arról, hogy a tároló privát kapcsolati FQDN-i elérhetőek a replikációs berendezést üzemeltető gépről. [További információ a hálózati kapcsolat ellenőrzésével kapcsolatos tudnivalókról.](#troubleshoot-network-connectivity) 

5. A kapcsolat ellenőrzése után töltse le a berendezés beállítását és a kulcsfájlt, futtassa a telepítési folyamatot, és regisztrálja a berendezést a Azure Migrate. Tekintse át [a részletes lépéseket itt.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#set-up-the-replication-appliance) Miután beállította a replikációs berendezést, kövesse az alábbi utasításokat a mobilitási szolgáltatás telepítéséhez az át telepíteni kívánt gépeken. [](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#install-the-mobility-service) 

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>Kiszolgálók replikálása az Azure-ba privát Azure-kapcsolat használatával 

Most kövesse az [alábbi lépéseket a](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) replikációhoz szükséges kiszolgálók kiválasztásához.  

A **Célbeállítások**  >  **replikálása**  >  **Gyorsítótár-/replikációs tárfiókja lapon** a legördülő menüben válassza ki a privát kapcsolaton keresztül replikálni kívánt tárfiókot.  

Ha a Azure Migrate projekt privát végpontkapcsolattal rendelkezik, engedélyeket kell megadnia a  [Recovery Services-tároló](#grant-access-permissions-to-the-recovery-services-vault) felügyelt identitása számára a Azure Migrate.   

Emellett a privát kapcsolaton keresztüli replikáció engedélyezéséhez hozzon létre egy privát végpontot [a tárfiókhoz.](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Hozzáférési engedélyek megadása a Recovery Services-tárolóhoz

A Recovery Services-tároló felügyelt identitásának engedélyekkel kell rendelkeznie a gyorsítótár-/replikációs tárfiókhoz való hitelesített hozzáféréshez. 

Az alábbi útmutató segítségével azonosíthatja a felhasználók által létrehozott Recovery Services Azure Migrate és meg tudja adni a szükséges engedélyeket. 

**_A Recovery Services-tároló és a felügyelt identitás objektumazonosítójának azonosítása_**

A Recovery Services-tároló részleteit a Azure Migrate: Kiszolgálóáttelepítés tulajdonságai **lapon találja.** 

1. A központi **Azure Migrate válassza** az **Áttekintés lehetőséget** a Azure Migrate: Server Migration csempén.

    ![Áttekintés lap a Azure Migrate központban](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. A bal oldali panelen válassza a Tulajdonságok **lehetőséget.** Jegyezze fel a Recovery Services-tároló nevét és a felügyelt identitás azonosítóját. A tároló _kapcsolattípusa Privát végpont,_ replikációs típusként pedig egyéb **lesz.**   Erre az információra szüksége lesz, amikor hozzáférést biztosít a tárolóhoz.
      
    ![Azure Migrate: Kiszolgálóáttelepítés tulajdonságai lap](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**_A tárfiók eléréséhez szükséges engedélyek megadása_**

 A tároló felügyelt identitásának a következő szerepkör-engedélyekkel kell rendelkeznie a replikációhoz szükséges tárfiókon.  Ebben az esetben előre létre kell hoznia a tárfiókot.

>[!Note]
> A Hyper-V virtuális gépek azure-ba privát kapcsolat használatával történő áttelepítése esetén hozzáférést kell ad a replikációs tárfiókhoz és a gyorsítótár-tárfiókhoz is. 

A szerepkör engedélyei a tárfiók típusától függően változnak.

- Resource Manager tárfiókok (Standard típus):
  - [Közreműködő](../role-based-access-control/built-in-roles.md#contributor) _és_
  - [Storage-blobadatok közreműködője](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager tárfiókok (prémium szintű típus):
  - [Közreműködő](../role-based-access-control/built-in-roles.md#contributor) _és_
  - [Storage-blobadatok tulajdonosa](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. Ugrás a replikációhoz kiválasztott replikációs/gyorsítótár-tárfiókra. A bal oldali panelen válassza a Hozzáférés-vezérlés **(IAM)** lehetőséget. 

1. A **Szerepkör-hozzárendelés hozzáadása szakaszban** válassza a Hozzáadás **lehetőséget:**

   ![Szerepkör-hozzárendelés hozzáadása](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)


1. A **Szerepkör-hozzárendelés hozzáadása** lap  Szerepkör mezőjében válassza ki a megfelelő szerepkört a fent említett engedélylistából. Adja meg a tároló korábban feljegyzett nevét, és válassza a **Mentés lehetőséget.**

    ![Szerepköralapú hozzáférés biztosítanak](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

4. Ezeken az engedélyeken kívül engedélyeznie kell a Microsoft megbízható szolgáltatásaihoz való hozzáférést is. Ha a hálózati hozzáférés a kiválasztott hálózatokra van korlátozva, válassza  a Hálózat lap Kivételek szakaszában található Allow trusted Microsoft-szolgáltatások to access **this storage account** (Megbízható Microsoft-szolgáltatások hozzáférés engedélyezése a **tárfiókhoz)** lehetőséget. 

![Megbízható Microsoft-szolgáltatások engedélyezése a tárfiókhoz](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)


### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>Privát végpont létrehozása a tárfiókhoz (nem kötelező)

Privát társviszony-létesítésű ExpressRoute használatával történő replikáláshoz hozzon létre egy privát végpontot [a](https://docs.microsoft.com/azure/private-link/tutorial-private-endpoint-storage-portal#create-storage-account-with-a-private-endpoint) gyorsítótár-/replikációs tárfiókok számára (cél-alforrás: **_blob)._** 

>[!Note]
>
> - Privát végpontokat csak egy 2-es általános célú (GPv2) tárfiókon hozhat létre. Díjszabási információkért lásd: [Az Azure Page Blobs díjszabása és](https://azure.microsoft.com/pricing/details/storage/page-blobs/) az Azure privát kapcsolat [díjszabása](https://azure.microsoft.com/pricing/details/private-link/)

A tárfiók privát végpontját ugyanabban a virtuális hálózatban kell létrehozni, mint a Azure Migrate projekt privát végpontját, vagy egy ehhez a hálózathoz csatlakozó másik virtuális hálózatot. 

Válassza az **Igen lehetőséget,** és integrálja egy privát DNS-zónával. A privát DNS-zóna segít a virtuális hálózat kapcsolatainak privát kapcsolaton keresztüli átirányításában a tárfiókba. Az **Igen lehetőség** kiválasztása automatikusan összeköti a DNS-zónát a virtuális hálózattal, és hozzáadja a DNS-rekordokat az új IP-cím és a létrehozott teljes tartománynevek feloldása érdekében. További információ a privát [DNS-zónákról.](https://docs.microsoft.com/azure/dns/private-dns-overview)

Ha a privát végpontot létrehozó felhasználó egyben a tárfiók tulajdonosa is, a rendszer automatikusan jóváhagyja a privát végpontot. Ellenkező esetben a tárfiók tulajdonosának jóvá kell hagynia a privát végpontot a használathoz. A kért privát végponti kapcsolat jóváhagyásához  vagy elutasításához a tárfiók oldalán, a Hálózat alatt válassza a Privát végponti kapcsolatok lapot. 

Mielőtt továbblépne, tekintse át a privát végpont kapcsolati állapotának állapotát. 

![Privát végpont jóváhagyási állapota](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

Miután létrehozta a privát végpontot, a Cél replikálása beállítások gyorsítótár-tárfiókja legördülő menüben válassza ki a privát kapcsolaton keresztüli replikáláshoz használt  >    >   tárfiókot.  

Győződjön meg arról, hogy a helyszíni replikációs berendezés rendelkezik hálózati kapcsolattal a privát végponton található tárfiókhoz. [További információ a hálózati kapcsolat ellenőrzésével kapcsolatos tudnivalókról.](#troubleshoot-network-connectivity)

>[!Note]
>
> - Hyper-V virtuális gépek Azure-ba történő migrálása esetén, ha a replikációs tárfiók _prémium_ szintű, akkor egy másik _Standard_ típusú tárfiókot kell választania a gyorsítótár tárfiókja számára. Ebben az esetben privát végpontokat kell létrehoznia a replikációs és gyorsítótár-tárfiókhoz.  

Ezután kövesse ezeket az utasításokat a replikáció áttekintéshez és [a replikáció elkezdése,](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) valamint [a migrálások végrehajtásához.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#run-a-test-migration)  

## <a name="troubleshoot-network-connectivity"></a>Hálózati kapcsolatok hibaelhárítása 

### <a name="validate-private-endpoints-configuration"></a>Privát végpontok konfigurációjának ellenőrzése 

Győződjön meg arról, hogy a privát végpont jóváhagyott állapotban van.  

1. Ugrás a Azure Migrate: Felderítési és felmérési és kiszolgálóáttelepítési tulajdonságok lapra.
2. A Tulajdonságok lap tartalmazza azon privát végpontok és privát kapcsolatIQDN-ek listáját, amelyek a Azure Migrate.  

3. Válassza ki a diagnosztizálni kívánt privát végpontot.  
    1. Ellenőrizze, hogy a kapcsolat állapota Jóváhagyva.
    2. Ha a kapcsolat Függőben állapotban van, jóvá kell hagynia.
    3. A privát végpont erőforrásához is navigálhat, és áttekintheti, hogy a virtuális hálózat megegyezik-e a projekt privát végpontjának virtuális hálózatával. 

    ![Privát végpont kapcsolatának megtekintése](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)

### <a name="verify-dns-resolution"></a>DNS-feloldás ellenőrzése 

A helyszíni berendezés (vagy replikációszolgáltató) a teljes privát kapcsolati tartománynevük (FQDN- Azure Migrate) használatával fér hozzá az erőforrásokhoz. További DNS-beállításokra lehet szükség a magánhálózati végpontok magánhálózati IP-címének a forráskörnyezetből való feloldásához. [Ebből a cikkből](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) megértheti a DNS-konfigurációs forgatókönyveket, amelyek segíthetnek a hálózati kapcsolati problémák elhárításában.  

A privát kapcsolat kapcsolatának ellenőrzéséhez végezze el a Azure Migrate-erőforrásvégpont (privát kapcsolati erőforrás teljes tartományneveinek) DNS-feloldását a Migrate-berendezést üzemeltető helyszíni kiszolgálóról, és győződjön meg arról, hogy az magánhálózati IP-címre oldódik fel. A privát végpont részletei és a privát kapcsolati erőforrások teljes tartományhálózati adatai a Felderítés és értékelés és kiszolgálóáttelepítés tulajdonságainak oldalán érhetők el. A **lista megtekintéséhez válassza a DNS-beállítások** letöltése lehetőséget.   

 ![Azure Migrate: Felderítési és értékelési tulajdonságok](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 ![Azure Migrate: Kiszolgálóáttelepítés tulajdonságai](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties.png)

Szemléltető példa a tárfiók privát kapcsolatának teljes tartománynevének DNS-feloldási példájára.  

- Adja _meg az nslookup<storage-account-name_ nevet>.blob.core.windows.net.  Cserélje <storage-account-name> a tárfiókhoz használt Azure Migrate.  

    Ehhez hasonló üzenet jelenik meg:  

   ![DNS-feloldási példa](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- A rendszer a 10.1.0.5 magánhálózati IP-címet ad vissza a tárfiókhoz. Ez a cím a privát végpont virtuális hálózatának alhálózatához tartozik.   

Más összetevők DNS-feloldási Azure Migrate hasonló módszer használatával ellenőrizheti.   

Ha a DNS-feloldás helytelen, kövesse az alábbi lépéseket:  

- Ha egyéni DNS-t használ, tekintse át az egyéni DNS-beállításokat, és ellenőrizze, hogy a DNS-konfiguráció helyes-e. Útmutatásért lásd: Privát [végpont áttekintése: DNS-konfiguráció.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)
- Ha Azure által biztosított DNS-kiszolgálókat használ, a további hibaelhárításhoz tekintse meg az alábbi szakaszt.  

> [!Tip]
> Manuálisan frissítheti a forráskörnyezet DNS-rekordjait, ha szerkeszti a DNS-gazdagépek fájlját a helyszíni berendezésen a privát kapcsolati erőforrás FQDN-ekkel és a társított magánhálózati IP-címekkel. Ez a lehetőség csak teszteléshez ajánlott. <br/>  


### <a name="validate-the-private-dns-zone"></a>A saját DNS ellenőrzése   
Ha a DNS-feloldás nem működik az előző szakaszban leírtak szerint, akkor a saját zónájában saját DNS probléma.  

#### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Ellenőrizze, hogy a szükséges saját DNS-erőforrás létezik-e  
Alapértelmezés szerint a Azure Migrate létrehoz egy privát DNS-zónát is, amely megfelel a "privatelink" altartománynak minden erőforrástípushoz. A privát DNS-zóna ugyanabban az Azure-erőforráscsoportban jön létre, mint a privát végpont erőforráscsoportja. Az Azure-erőforráscsoportnak a következő formátumú privát DNS-zónaerőforrásokat kell tartalmaznia:
- privatelink.vaultcore.azure.net kulcstartóhoz 
- privatelink.blob.core.windows.net tárfiókhoz
- privatelink.siterecovery.windowsazure.com recovery services-tárolóhoz (Hyper-V- és ügynökalapú replikációkhoz)
- privatelink.prod.migration.windowsazure.com – projekt, értékelési projekt és felderítési hely áttelepítése.   

A privát DNS-zónát a rendszer automatikusan Azure Migrate (kivéve a felhasználó által kiválasztott gyorsítótár-/replikációs tárfiókot). A csatolt privát DNS-zónát a privát végpont oldalára navigálva, majd a DNS-konfigurációk kiválasztásával keresheti meg. A privát DNS-zónának a privát DNS-integráció szakaszban kell lennie. 

![A DNS-konfiguráció képernyőképe](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration.png)  

Ha a DNS-zóna nincs jelen (ahogy az alább látható), hozzon létre [egy új saját DNS zóna erőforrást.](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal)  

![Új saját DNS létrehozása](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone.png) 

#### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Ellenőrizze, hogy a saját DNS zóna csatolva van-e a virtuális hálózathoz  
A privát DNS-zónát ahhoz a virtuális hálózathoz kell kapcsolni, amely a DNS-lekérdezés privát végpontját tartalmazza az erőforrásvégpont magánhálózati IP-címének feloldásához. Ha a privát DNS-zóna nincs a megfelelő tartományhoz Virtual Network virtuális hálózat DNS-feloldása figyelmen kívül hagyja a privát DNS-zónát.   

Lépjen a privát DNS-zóna erőforráshoz a Azure Portal, és válassza ki a virtuális hálózati hivatkozásokat a bal oldali menüből. A virtuális hálózatoknak össze van kapcsolva.

![Virtuális hálózati hivatkozások megtekintése](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links.png) 

Itt megjelenik a hivatkozások listája, amelyek mindegyikéhez egy virtuális hálózat neve szerepel az előfizetésében. A privát végpont erőforrást tartalmazó virtuális hálózatnak itt kell szerepelnie. Egyéb esetben [kövesse ezt a cikket](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) a privát DNS-zóna virtuális hálózathoz való csatolásához.    

Miután a privát DNS-zóna a virtuális hálózathoz kapcsolódik, a virtuális hálózatról származó DNS-kérések DNS-rekordokat keresnek a privát DNS-zónában. Ez a magánhálózati végpontot létrehozó virtuális hálózat megfelelő címfeloldásához szükséges.   

#### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Győződjön meg arról, hogy a privát DNS-zóna a megfelelő A rekordot tartalmazza 

Ugrás arra a privát DNS-zónára, amelyről a hibaelhárítást el szeretné hárítani. Az Áttekintés lapon az adott privát DNS-zóna összes DNS-rekordja látható. Ellenőrizze, hogy létezik-e DNS A rekord az erőforráshoz. Az A rekord (az IP-cím) értékének az erőforrások magánhálózati IP-címének kell lennie. Ha nem megfelelő IP-címmel találja az A rekordot, el kell távolítania a helytelen IP-címet, és hozzá kell adni egy újat. Javasoljuk, hogy távolítsa el a teljes A rekordot, adjon hozzá egy újat, és dns-sel ürítse ki a helyszíni forrásberendezést.   

Szemléltető példa a tárfiók DNS A rekordjára a privát DNS-zónában:

![DNS records](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Szemléltető példa a Recovery Services-tároló mikroszolgáltatására A DNS-rekordokat a privát DNS-zónában: 

![A Recovery Services-tároló DNS-rekordjai](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records.png)   

>[!Note]
> Egy A rekord eltávolításakor vagy módosításakor előfordulhat, hogy a gép továbbra is a régi IP-címre lesz feloldva, mert előfordulhat, hogy az TTL (time to Live) érték még nem járt le.  

#### <a name="other-things-that-may-affect-private-link-connectivity"></a>Egyéb olyan dolgok, amelyek befolyásolhatják a privát kapcsolat kapcsolatát  

Ez a speciális vagy összetett forgatókönyvekben megtalálható elemek nem teljes listája: 

- Tűzfalbeállítások– Azure Firewall virtuális hálózathoz csatlakoztatott virtuális hálózathoz vagy a berendezés gépében üzembe helyezett egyéni tűzfalmegoldáshoz.  
- Hálózati társviszony létesítése, amely hatással lehet a használt DNS-kiszolgálókra és a forgalom útválasztásának mikéntre.  
- Az egyéni átjáró- (NAT-) megoldások hatással lehetnek a forgalom útválasztásra, beleértve a DNS-lekérdezések forgalmát is.

További információért tekintse át a privát végpontok csatlakozási [problémáival kapcsolatos hibaelhárítási útmutatót.](https://docs.microsoft.com/azure/private-link/troubleshoot-private-endpoint-connectivity)  

## <a name="next-steps"></a>Következő lépések 
- [A migrálási folyamat befejezése és](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#complete-the-migration) a migrálás utáni [ajánlott eljárások áttekintése.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#post-migration-best-practices)