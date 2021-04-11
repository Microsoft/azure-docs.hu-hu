---
title: Ethereum proof-of-Authority Consortium megoldási sablon üzembe helyezése az Azure-ban
description: Az Azure-beli többtagú konzorcium Ethereum üzembe helyezéséhez és konfigurálásához használja a Ethereum-szolgáltatói konzorciumi megoldást.
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: f4b12a9b3b830fcc4f45cb5e957232fee5a756a3
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078645"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Ethereum proof-of-Authority Consortium megoldási sablon üzembe helyezése az Azure-ban

Az Azure-beli Ethereum-alapú hitelesítésszolgáltató [előzetes](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) verziójának használatával üzembe helyezheti, konfigurálhatja és szabályozhatja a többtagú konzorciumok Ethereum-hálózatát, minimális Azure-és Ethereum-ismerettel.

A megoldási sablon az egyes konzorciumok tagjai által használható blockchain hálózati lábnyomok kiépítésére Microsoft Azure számítási, hálózatkezelési és tárolási szolgáltatások használatával. Mindegyik konzorciumi tag hálózati lábnyoma olyan elosztott terhelésű érvényesítő csomópontokból áll, amelyeket az alkalmazás vagy a felhasználó használhat a Ethereum-tranzakciók elküldéséhez.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Azure Blockchain-megoldás kiválasztása

Mielőtt kiválasztja a Ethereum proof-of-Authority Consortium megoldás sablonját, hasonlítsa össze a forgatókönyvet az elérhető Azure Blockchain-beállítások gyakori felhasználási eseteivel.

> [!IMPORTANT]
> Érdemes lehet az [Azure Blockchain szolgáltatást](../service/overview.md) használni az Azure-megoldás Ethereum helyett. Az Azure Blockchain Service egy támogatott felügyelt Azure-szolgáltatás. A paritásos Ethereum a Közösség által vezérelt fejlesztésre és karbantartásra váltott. További információ: [paritásos Ethereum átváltása a OPENETHEREUM DAO-](https://www.parity.io/parity-ethereum-openethereum-dao/)ra.

Beállítás | Szolgáltatási modell | Gyakori használati eset
-------|---------------|-----------------
Megoldássablonok | IaaS | A megoldási sablonok Azure Resource Manager sablonok, amelyekkel teljes körűen konfigurált blockchain-topológiát lehet kiépíteni. A Sablonok Microsoft Azure számítási, hálózatkezelési és tárolási szolgáltatásokat telepítenek és konfigurálnak egy adott blockchain hálózati típushoz. A megoldási sablonokat szolgáltatói szerződés nélkül biztosítjuk. Támogatásért használja a [Microsoft Q&a kérdéses lapot](/answers/topics/azure-blockchain-workbench.html) .
[Azure Blockchain Service](../service/overview.md) | PaaS | Az Azure Blockchain szolgáltatás előzetes verziója leegyszerűsíti a konzorciumi Blockchain hálózatok képződését, kezelését és irányítását. Használja az Azure Blockchain szolgáltatást a Pásti, a konzorciumok felügyeletére, vagy a szerződés és a tranzakció adatvédelmet igénylő megoldásokhoz.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS és Péter | Az Azure Blockchain Workbench előzetes verziója olyan Azure-szolgáltatások és-funkciók gyűjteménye, amelyek segítségével Blockchain-alkalmazásokat hozhat létre és helyezhet üzembe más szervezetekkel való üzleti folyamatok és adatmennyiségek megosztásához. Az Azure Blockchain Workbench használatával Blockchain-megoldást vagy Blockchain-alkalmazást készíthet. Az Azure Blockchain Workbenchre nem vonatkozik szolgáltatói szerződés. Támogatásért használja a [Microsoft Q&a kérdéses lapot](/answers/topics/azure-blockchain-workbench.html) .

## <a name="solution-architecture"></a>Megoldásarchitektúra

A Ethereum-megoldás sablonnal egyetlen vagy több régión alapuló, többtagú Ethereum-alapú konzorciumi hálózatot helyezhet üzembe.

![üzembe helyezési architektúra](./media/ethereum-poa-deployment/deployment-architecture.png)

Mindegyik konzorciumi tag üzembe helyezése a következőket foglalja magában:

* Virtual Machines a PoA validatorok futtatásához
* Azure Load Balancer az RPC, a társítási és a irányítási DApp kérelmek terjesztéséhez
* Azure Key Vault az érvényesítő identitások biztonságossá tételéhez
* Az Azure Storage állandó hálózati információk üzemeltetéséhez és a lízing koordinálásához
* Azure Monitor a naplók és a teljesítménnyel kapcsolatos statisztikák összesítéséhez
* VNet-átjáró (nem kötelező) a VPN-kapcsolatok privát virtuális hálózatok való engedélyezéséhez

Alapértelmezés szerint az RPC-és a társítási végpontok elérhetők a nyilvános IP-címeken keresztül az előfizetések és a felhők közötti egyszerűsített kapcsolat engedélyezéséhez. Az alkalmazás szintű hozzáférés-vezérléshez használhatja a [paritás jogosultsági szerződéseit](https://openethereum.github.io/Permissioning.html). A VPN mögött üzembe helyezett hálózatok, amelyek a VNet-átjárókat használják az előfizetések közötti kapcsolathoz, támogatottak. Mivel a VPN-és VNet-telepítések összetettebbek, érdemes lehet nyilvános IP-modellel kezdeni a megoldás prototípusakor.

A Docker-tárolók a megbízhatóság és a modularitás érdekében használatosak. A Azure Container Registry az egyes központi telepítések részeként a verzióval ellátott rendszerképek tárolására és kiszolgálására szolgál. A tárolók képei a következőkből állnak:

* Orchestrator – identitásokat és irányítási szerződéseket hoz létre. Identitások tárolása egy identitás-tárolóban.
* Paritásos ügyfél – bérletek identitása az Identity Store-ból. Felfedi és csatlakozik a társaihoz.
* EthStats-ügynök – helyi naplókat és statisztikákat gyűjt az RPC-n keresztül, és leküldi az adatokat a Azure Monitor.
* Irányítási DApp – webes felület az irányítási szerződésekkel való interakcióhoz.

### <a name="validator-nodes"></a>Érvényesítő csomópontok

A hitelesítő hatóság protokolljában az ellenőrző csomópontok a hagyományos bányász-csomópontok helyét helyezik el. Minden érvényesítő egyedi Ethereum-identitással rendelkezik, amely lehetővé teszi, hogy részt vegyen a blokk létrehozási folyamatában. Mindegyik konzorciumi tag két vagy több, öt régióban lévő validator-csomópontot tud kiépíteni a Geo-redundancia érdekében. Az érvényesítő csomópontjai más érvényesítő csomópontokkal kommunikálnak, hogy konszenzust kapjanak az alapul szolgáló elosztott Főkönyv állapotán. A hálózatban való méltányos részvétel biztosítása érdekében minden egyes konzorciumi tag számára tilos több validator használata, mint a hálózat első tagja. Ha például az első tag három érvényesítő telepít, az egyes tagok legfeljebb három validatort tartalmazhatnak.

### <a name="identity-store"></a>Identity Store

A rendszer az egyes tagok előfizetésében helyez üzembe egy Identity Store-t, amely biztonságosan tárolja a generált Ethereum-identitásokat. Az egyes érvényesítő tárolók esetében a Ethereum egy titkos kulcsot hoz létre, és a Azure Key Vault tárolja.

## <a name="deploy-ethereum-consortium-network"></a>A Ethereum Consortium Network üzembe helyezése

Ebben az útmutatóban feltételezzük, hogy többrésztvevős Ethereum Consortium-hálózatot hoz létre. A következő folyamat több résztvevős telepítésre mutat példát:

1. Három tag mindegyike Ethereum-fiókot hoz MetaMask használatával
1. *Az a tag* üzembe helyezi a Ethereum cselekvési tervét, és megadja a Ethereum nyilvános címeit
1. Az *a tag* megadja a konzorcium URL-címét a *B* és *C* tag számára
1. *B. tag* és *C. tag* üzembe helyezése, Ethereum PoA, a Ethereum nyilvános címe és *a tag* konzorciumi URL-címének megadása
1. *Tag a* *B taghoz* tartozó szavazatot rendszergazdaként
1. Az *A* és *B* tag, a *C* tagot pedig rendszergazdaként

A következő részekben bemutatjuk, hogyan konfigurálhatja az első tag hálózati lábnyomát.

### <a name="create-resource"></a>Erőforrás létrehozása

A [Azure Portal](https://portal.azure.com)válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban.

Válassza a **Blockchain**  >  **Ethereum-szolgáltatói konzorcium (előzetes verzió)** lehetőséget.

### <a name="basics"></a>Alapvető beállítások

Az **alapértékek területen minden** központi telepítéshez meg kell adnia a szabványos paraméterek értékét.

![Alapvető beállítások](./media/ethereum-poa-deployment/basic-blade.png)

Paraméter | Leírás | Példaérték
----------|-------------|--------------
Új hálózat létrehozása vagy meglévő hálózat csatlakoztatása | Létrehozhat egy új konzorciumi hálózatot, vagy csatlakozhat egy már meglévő konzorciumi hálózathoz. Egy meglévő hálózathoz való csatlakozáshoz további paraméterek szükségesek. | Új létrehozása
E-mail-cím | E-mailben értesítést kap, ha az üzemelő példány az üzembe helyezéssel kapcsolatos információkat tartalmaz. | Érvényes e-mail-cím
Virtuális gép felhasználóneve | Az egyes telepített virtuális gépek rendszergazdai felhasználóneve | 1-64 alfanumerikus karakter
Hitelesítéstípus | A virtuális géphez való hitelesítés módszere. | Jelszó
Jelszó | Az egyes telepített virtuális gépek rendszergazdai fiókjának jelszava. Az összes virtuális gép kezdetben ugyanazzal a jelszóval rendelkezik. A jelszót a kiépítés után módosíthatja. | 12-72 karakter 
Előfizetés | Az előfizetés, amelyre a konzorcium-hálózatot telepíteni kell |
Erőforráscsoport| Az az erőforráscsoport, amelyre a konzorcium-hálózatot telepíteni kell. | myResourceGroup
Hely | Az erőforráscsoport Azure-régiója. | USA 2. nyugati régiója

Válassza az **OK** lehetőséget.

### <a name="deployment-regions"></a>Központi telepítési régiók

A *központi telepítési régiók* területen határozza meg az egyes régiók és helyszínek számát. Legfeljebb öt régióban helyezhető üzembe. Az első régiónak meg kell egyeznie az erőforráscsoport helyével az *alapok* szakaszban. A fejlesztési és tesztelési hálózatok esetében egyetlen régiót használhat tagként. Éles környezetben a magas rendelkezésre állás érdekében helyezzen üzembe két vagy több régióban.

![központi telepítési régiók](./media/ethereum-poa-deployment/deployment-regions.png)

Paraméter | Leírás | Példaérték
----------|-------------|--------------
Régió (k) száma|A konzorcium-hálózat üzembe helyezéséhez szükséges régiók száma| 2
Első régió | A konzorcium-hálózat üzembe helyezésének első régiója | USA 2. nyugati régiója
Második régió | A konzorcium-hálózat üzembe helyezésének második régiója. A további régiók akkor is láthatók, ha a régiók száma kettő vagy nagyobb. | USA 2. keleti régiója

Válassza az **OK** lehetőséget.

### <a name="network-size-and-performance"></a>Hálózati méret és teljesítmény

A *hálózati méret és teljesítmény* területen válassza a konzorciumi hálózat méretének bemeneteit. Az érvényesítő csomópont tárolási mérete a blockchain lehetséges méretét határozza meg. A méret módosítható az üzembe helyezés után.

![Hálózati méret és teljesítmény](./media/ethereum-poa-deployment/network-size-and-performance.png)

Paraméter | Leírás | Példaérték
----------|-------------|--------------
Elosztott terhelésű érvényesítő csomópontok száma | A hálózat részeként kiépített érvényesítő csomópontok száma. | 2
Érvényesítő csomópont tárolási teljesítménye | Az egyes telepített érvényesítő csomópontok felügyelt lemezének típusa. A díjszabással kapcsolatos további információkért lásd: [Storage díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/) | Standard SSD
Érvényesítő csomópont virtuális gép mérete | Az érvényesítő csomópontjaihoz használt virtuális gép mérete. A díjszabással kapcsolatos további információkért lásd: [virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) | Standard D2 v3

A virtuális gép és a tárolási rétegek hatással vannak a hálózati teljesítményre.  A következő táblázat segítségével kiválaszthatja a költséghatékonyságot:

Virtuális gép SKU|Tárolási rétegek|Ár|Átviteli sebesség|Késés
---|---|---|---|---
F1|Standard SSD|alacsony|alacsony|magas
D2_v3|Standard SSD|közepes|közepes|közepes
F16s|Prémium SSD|magas|magas|alacsony

Válassza az **OK** lehetőséget.

### <a name="ethereum-settings"></a>Ethereum-beállítások

A *Ethereum beállításai* területen válassza a Ethereum kapcsolatos konfigurációs beállításokat.

![Ethereum-beállítások](./media/ethereum-poa-deployment/ethereum-settings.png)

Paraméter | Leírás | Példaérték
----------|-------------|--------------
Konzorciumi tag azonosítója | A konzorciumi hálózaton résztvevő egyes tagokhoz tartozó azonosító. Az ütközések elkerülése érdekében az IP-címtartomány konfigurálására szolgál. Magánhálózat esetén a tagok AZONOSÍTÓjának egyedinek kell lennie az ugyanazon a hálózaton lévő különböző szervezetek között.  Egyedi tag AZONOSÍTÓra van szükség, még akkor is, ha ugyanaz a szervezet több régióban is üzembe helyezi. Jegyezze fel ennek a paraméternek az értékét, mert meg kell osztania a többi csatlakozó taggal, hogy ne legyen ütközés. Az érvényes tartomány 0 és 255 között van. | 0
Hálózati azonosító | Az üzembe helyezett konzorcium Ethereum hálózati azonosítója. Mindegyik Ethereum-hálózat saját hálózati AZONOSÍTÓval rendelkezik, és 1 a nyilvános hálózat azonosítója. Az érvényes tartomány 5 – 999 999 999 | 10101010
Rendszergazdai Ethereum címe | A PoA-irányításban való részvételhez használt Ethereum-fiók címe. A MetaMask használatával létrehozhat egy Ethereum-címeket. |
Speciális beállítások | Speciális beállítások a Ethereum beállításaihoz | Engedélyezés
Üzembe helyezés nyilvános IP-cím használatával | Ha a privát VNet van kiválasztva, a hálózat egy VNet-átjáró mögött van telepítve, és eltávolítja a társ-hozzáférési hozzáférést. A privát VNet esetében az összes tagnak VNet-átjárót kell használnia ahhoz, hogy a kapcsolódás kompatibilis legyen. | Nyilvános IP-cím
Gáz korlátjának letiltása | A hálózat indítási blokkjának gáz-korlátja. | 50000000
Visszazárási időszak blokkolása (mp) | Az üres blokkok létrehozásának gyakorisága, ha nincsenek tranzakciók a hálózaton. A magasabb szintű gyakoriság gyorsabb lesz, de nagyobb a tárolási költségek. | 15
Tranzakciós engedély szerződése | A tranzakció bytecode vonatkozó szerződés. Az intelligens szerződések üzembe helyezésének és végrehajtásának korlátozása a Ethereum-fiókok engedélyezett listájára. |

Válassza az **OK** lehetőséget.

### <a name="monitoring"></a>Figyelés

A figyelés lehetővé teszi a hálózati naplózási erőforrás konfigurálását. A monitorozási ügynök a hálózat hasznos mérőszámait és naplóit gyűjti, és lehetővé teszi a hálózati állapot vagy hibakeresési problémák gyors ellenőrzését.

![Azure-figyelő](./media/ethereum-poa-deployment/azure-monitor.png)

Paraméter | Leírás | Példaérték
----------|-------------|--------------
Figyelés | A figyelés engedélyezésének lehetősége | Engedélyezés
Kapcsolódás meglévő Azure Monitor naplókhoz | Lehetőség új Azure Monitor naplók példányának létrehozására vagy meglévő példányhoz való csatlakozásra | Új létrehozása
Hely | Az új példányt telepítő régió | USA keleti régiója
Meglévő log Analytics-munkaterület azonosítója (Csatlakozás meglévő Azure Monitor naplókhoz = csatlakozás meglévőhöz)|A meglévő Azure Monitor naplók példányának munkaterület-azonosítója|NA
Meglévő log Analytics elsődleges kulcs (Csatlakozás meglévő Azure Monitor naplókhoz = csatlakozás meglévőhöz)|A meglévő Azure Monitor naplók példányához való kapcsolódáshoz használt elsődleges kulcs|NA

Válassza az **OK** lehetőséget.

### <a name="summary"></a>Összefoglalás

Az összefoglalás segítségével tekintse át a megadott bemeneteket, és futtassa az alapszintű telepítés előtti ellenőrzést. A telepítése előtt letöltheti a sablont és a paramétereket.

Válassza a **Létrehozás** elemet a telepítéshez.

Ha az üzembe helyezés VNet-átjárókat is tartalmaz, a telepítés 45 – 50 percet is igénybe vehet.

## <a name="deployment-output"></a>Központi telepítés kimenete

Miután az üzembe helyezés befejeződött, a Azure Portal használatával érheti el a szükséges paramétereket.

### <a name="confirmation-email"></a>Visszaigazoló e-mail

Ha e-mail-címet ([alapismeretek szakaszt](#basics)) ad meg, a rendszer elküld egy e-mailt, amely tartalmazza a központi telepítési információkat és a dokumentációra mutató hivatkozásokat.

![üzembe helyezési e-mail](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portál

Miután az üzembe helyezés sikeresen befejeződött, és az összes erőforrás kiépítése megtörtént, megtekintheti az erőforráscsoport kimeneti paramétereit.

1. Nyissa meg az erőforráscsoportot a portálon.
1. Válassza az **áttekintés > üzemelő példányok** lehetőséget.

    ![Erőforráscsoport – áttekintés](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Válassza ki a **Microsoft-Azure-blockchain. Azure-blockchain-ether-...** üzemelő példányt.
1. Válassza a **kimenetek** szakaszt.

    ![Üzembe helyezési kimenetek](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>A konzorcium egyre nagyobb

A konzorcium kibontásához először össze kell kapcsolni a fizikai hálózatot. Ha VPN-t helyez üzembe, tekintse meg az [VNet-átjáró csatlakoztatása](#connecting-vnet-gateways) című szakaszt, amely az új tag központi telepítésének részeként konfigurálja a hálózati kapcsolatot. Az üzembe helyezés befejezése után az [irányítási DApp](#governance-dapp) váljon hálózati rendszergazda.

### <a name="new-member-deployment"></a>Új tag üzembe helyezése

Ossza meg a következő információkat a csatlakozó taggal. Az információk az üzembe helyezés utáni e-mailben vagy a portál telepítési kimenetében találhatók.

* Konzorcium-adaturl-cím
* A telepített csomópontok száma
* VNet-átjáró erőforrás-azonosítója (VPN használata esetén)

A központi telepítéssel rendelkező tagnak a következő útmutatóval kell rendelkeznie a hálózati jelenlétének üzembe helyezése során ugyanazzal a Ethereum-szolgáltatói megoldási sablonnal:

* Válassza a **Csatlakozás meglévőt** lehetőséget
* Válassza ki ugyanazt a számú érvényesítő csomópontot, mint a hálózat többi tagját a tisztességes ábrázolás biztosításához.
* Ugyanazt a rendszergazdai Ethereum-címeket használja
* A megadott *konzorcium-Adaturl-cím* használata a *Ethereum-beállításokban*
* Ha a hálózat többi része VPN-kapcsolattal rendelkezik, a speciális szakaszban válassza a **privát VNet** elemet.

### <a name="connecting-vnet-gateways"></a>VNet-átjárók csatlakoztatása

Ez a szakasz csak akkor szükséges, ha privát VNet használ. Ha nyilvános IP-címeket használ, kihagyhatja ezt a szakaszt.

Magánhálózat esetén a különböző tagok a VNet-átjáró kapcsolatain keresztül kapcsolódnak egymáshoz. Ahhoz, hogy egy tag csatlakozzon a hálózathoz, és látni tudja a tranzakciós forgalmat, a meglévő tagnak a VPN-átjárón a kapcsolat elfogadásához végső konfigurációval kell rendelkeznie. A csatlakozási tag Ethereum csomópontjai nem futnak, amíg meg nem történik a kapcsolatok létrehozása. Egy adott meghibásodás esélyeinak csökkentése érdekében hozzon létre redundáns hálózati kapcsolatokat a konzorciumban.

Az új tag üzembe helyezése után a meglévő tagnak be kell fejeznie a kétirányú kapcsolódást egy VNet-átjáró-kapcsolódás az új taggal való beállításával. A meglévő tag igényei:

* A csatlakozó tag VNet Gateway-ResourceID. Lásd: [központi telepítés kimenete](#deployment-output).
* A megosztott csatlakoztatási kulcs.

A meglévő tagnak a következő PowerShell-parancsfájlt kell futtatnia a kapcsolódás befejezéséhez. A portálon a jobb felső navigációs sávon található Azure Cloud Shell is használhatja.

![Cloud Shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="governance-dapp"></a>Irányítási DApp

Az igazoló hatóság középpontjában a decentralizált irányítás van. Mivel a hitelesítő hatóság a hálózati hatóságok engedélyezett listájára támaszkodik, hogy a hálózat kifogástalan maradjon, fontos, hogy az engedélyek listájának módosítására igazságos mechanizmust biztosítson. Mindegyik üzembe helyezéshez intelligens szerződés és portál tartozik, amely az engedélyezett lista láncon belüli irányítására használható. Ha egy javasolt változás a konzorcium tagjainak többségi szavazatát éri el, a módosítást a rendszer hozza meg. A szavazás lehetővé teszi, hogy az új konszenzussal rendelkező résztvevők hozzá legyenek adva, vagy feltörték a résztvevőket, hogy transzparens módon eltávolítsanak egy becsületes hálózatot.

Az irányítási DApp az előre telepített [intelligens szerződések](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) és a hálózatban lévő hatóságok irányítására szolgáló webalkalmazások összessége. A hatóságok rendszergazdai identitásokra és érvényesítő csomópontokra vannak bontva.
A rendszergazdák jogosultak a konszenzusos részvétel delegálására az érvényesítő csomópontjain. A rendszergazdák más rendszergazdákat is szavazhatnak a hálózatról vagy onnan.

![Irányítási DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **Decentralizált irányítás:** A hálózati kiszolgálókon végrehajtott módosításokat a rendszergazdák elemre kattintva felügyelheti a láncon belüli szavazással.
* **Érvényesítő delegálása:** A hatóságok kezelhetik az egyes PoA-telepítésekben beállított érvényesítő csomópontjaikat.
* **Naplózható változások előzményei:** A rendszer minden módosítást rögzít a blockchain, amely átláthatóságot és auditálást biztosít.

### <a name="getting-started-with-governance"></a>Az irányítás első lépései

Ha bármilyen tranzakciót szeretne végrehajtani az irányítási DApp keresztül, egy Ethereum-pénztárcát kell használnia. A legegyszerűbb megközelítés egy böngészőbeli mobiltárca használata, például a [MetaMask](https://metamask.io); mivel azonban ezek az intelligens szerződések a hálózaton vannak telepítve, automatizálhatja az irányítási Szerződéssel való interakciót is.

A MetaMask telepítése után navigáljon az irányítási DApp a böngészőben.  Az URL-címet az üzembe helyezési kimenetben található Azure Portalon keresztül keresheti meg.  Ha nincs telepítve a böngészőbeli mobiltárca, nem fog tudni műveleteket végrehajtani; megtekintheti azonban a rendszergazdai állapotot is.  

### <a name="becoming-an-admin"></a>Rendszergazdavé válás

Ha Ön a hálózaton üzembe helyezett első tag, akkor automatikusan rendszergazda lesz, a paritásos csomópontok pedig validatorként jelennek meg. Ha csatlakozik a hálózathoz, meg kell szavaznia a (50%-nál nagyobb) többségi rendszergazdaként a meglévő rendszergazda készletből. Ha úgy dönt, hogy nem lesz rendszergazda, a csomópontok továbbra is szinkronizálják és érvényesítik a blockchain; azonban nem vesznek részt a blokk létrehozási folyamatában. A szavazási folyamat elindításához **válassza a kijelölés lehetőséget** , és adja meg a Ethereum címét és aliasát.

![Nevezés](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Jelöltek

A **jelöltek** lapon a jelölt rendszergazdák aktuális készletét láthatja.  Ha a jelölt a jelenlegi adminisztrátorok többsége eléri a szavazatot, a pályázót egy rendszergazdának kell előléptetni.  A jelöltre való szavazáshoz válassza ki a sort, és válassza a **szavazás a következőben:**. Ha meggondolja magát szavazásra, válassza ki a jelöltet, és válassza a **szavazás** törlése lehetőséget.

![Jelöltek](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Rendszergazdák

A **rendszergazdák** lap a rendszergazdák aktuális készletét jeleníti meg, és lehetővé teszi, hogy szavazzon.  Ha egy rendszergazda több mint 50%-os támogatást veszít, akkor a rendszer a hálózatban rendszergazdaként eltávolítja őket. A rendszergazda által birtokolt validator-csomópontok elveszítik az érvényesítő állapotát, és a hálózat tranzakciós csomópontjai lesznek. A rendszergazdák tetszőleges számú okból eltávolíthatók. azonban a konzorciumnak meg kell egyeznie a szabályzattal.

![Rendszergazdák](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Érvényesítőket

Az **érvényesítések** lapon a példány aktuálisan telepített paritási csomópontjai és a jelenlegi állapotuk (csomópont típusa) láthatók. Az egyes konzorciumi tagok más validatorokkal rendelkeznek ebben a listában, mivel ez a nézet az aktuálisan telepített konzorciumi tagot jelképezi. Ha a példány újonnan lett üzembe helyezve, és még nem adta hozzá a validatorokat, akkor lehetősége lesz a **validatorok hozzáadására**. Az ellenőrzők hozzáadásával automatikusan kiválaszthatja a paritásos csomópontok regionálisan elosztott készletét, és hozzárendelheti őket az érvényesítő készlethez. Ha a rendelkezésre álló kapacitásnál több csomópontot telepített, a többi csomópont a hálózat tranzakciós csomópontjaira vált.

Az egyes validatorok címe automatikusan az Azure-beli [Identity Store](#identity-store) -on keresztül lesz hozzárendelve.  Ha egy csomópont leáll, a rendszer lekérdezi az identitását, és lehetővé teszi, hogy a központi telepítés egy másik csomópontja tegye a helyét. Ez a folyamat biztosítja, hogy a konszenzus részvétele nagy rendelkezésre állású legyen.

![Érvényesítőket](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Konzorcium neve

Bármelyik rendszergazda frissítheti a konzorcium nevét.  A konzorcium nevének frissítéséhez kattintson a bal felső sarokban található fogaskerék ikonra.

### <a name="account-menu"></a>Fiók menü

A jobb felső sarokban a Ethereum-fiók aliasa és a Identicon.  Ha Ön rendszergazda, lehetősége van az alias frissítésére.

![Fiók](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="support-and-feedback"></a>Támogatás és visszajelzés<a id="tutorials"></a>

Az Azure Blockchain híreket az Azure [Blockchain blogon](https://azure.microsoft.com/blog/topics/blockchain/) tekintheti meg, ahol naprakészen tarthatja a Blockchain szolgáltatás ajánlatait és az Azure Blockchain Engineering csapata információit.

Ha visszajelzést szeretne küldeni, vagy új szolgáltatásokat szeretne kérni, tegye közzé vagy szavazzon a [blockchain készült Azure feedback fórumán](https://aka.ms/blockchainuservoice)keresztül.

### <a name="community-support"></a>Közösségi támogatás

Folytassa a Microsoft-mérnökökkel és az Azure Blockchain közösségi szakértőivel.

* [A Microsoft Q&egy kérdés oldalát](/answers/topics/azure-blockchain-workbench.html). A blockchain-sablonok mérnöki támogatása az üzembe helyezési problémákra korlátozódik.
* [A Microsoft technikai közössége](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Következő lépések

További Azure Blockchain-megoldásokért tekintse meg az [Azure Blockchain dokumentációját](../index.yml).
