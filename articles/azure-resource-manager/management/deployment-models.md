---
title: A Resource Manager-alapú és a klasszikus üzemi modell
description: A cikk a Resource Manager-alapú és a klasszikus (vagy szolgáltatásfelügyeleti) üzemi modellek közötti különbségeket ismerteti.
ms.topic: conceptual
ms.date: 02/06/2020
ms.openlocfilehash: da260db84de0cc8bd092eb95491b7cc38df93a12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185844"
---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Az Azure Resource Manager-alapú és a klasszikus üzemelő példányok: Az üzemi modellek és az erőforrások állapotának ismertetése

> [!NOTE]
> A cikkben ismertetett információ arra vonatkozik, ha a klasszikusból az Azure Resource Manager-alapú üzemi modellbe migrál.

Ebben a cikkben az Azure Resource Manager-alapú és a klasszikus üzemi modellel ismerkedhet meg. A Resource Manager-alapú és a klasszikus üzemi modell két eltérő módost kínál az Azure-megoldások üzembe helyezésére és felügyeletére. Két eltérő API-készleten keresztül használhatja őket, és az üzembe helyezett erőforrásokban is lehetnek lényeges különbségek. A két modell nem kompatibilis egymással. Ez a cikk röviden ismerteti ezeket az eltéréseket.

Az erőforrások egyszerűbb üzembe helyezése és felügyelete érdekében a Microsoft a Resource Manager használatát javasolja az összes új erőforráshoz. Amennyiben lehetséges, a Microsoft javasolja, hogy a meglévő erőforrásokat is helyezze újra üzembe a Resource Manager használatával.

Ha most ismerkedik a Resource Managerrel, érdemes áttekinteni a [Azure Resource Manager áttekintésében](overview.md)meghatározott terminológiát.

## <a name="history-of-the-deployment-models"></a>Az üzemi modellek története

Az Azure-ban eredetileg csak a klasszikus üzemi modell volt elérhető. Ebben a modellben az erőforrások egymástól függetlenül léteztek, a kapcsolódó erőforrásokat nem lehetett csoportosítani. Manuálisan kellett nyomon követni, hogy az egyes megoldások és alkalmazások melyik erőforrásokból épültek fel, és gondot kellett fordítani azok összehangolt kezelésére. A megoldások üzembe helyezéséhez vagy létre kellett hozni egyenként az egyes erőforrásokat a portálon, vagy írni kellett egy olyan szkriptet, amely a megfelelő sorrendben helyezte üzembe az erőforrásokat. A megoldások törléséhez külön-külön kellett törölni az egyes erőforrásokat. Nem sikerült egyszerűen alkalmazni és frissíteni a kapcsolódó erőforrásokhoz tartozó hozzáférés-vezérlési házirendeket. Végül nem alkalmazhat címkéket olyan erőforrásokra, amelyekkel felcímkézheti azokat olyan feltételekkel, amelyek segítségével figyelheti az erőforrásokat, és kezelheti a számlázást.

Az Azure 2014-ben mutatta be a Resource Managert, amely bevezette az erőforráscsoportok fogalmát. Az erőforráscsoport közös életciklussal rendelkező erőforrások tárolója. A Resource Manager-alapú üzemi modell számos előnyt kínál:

* A megoldás összes szolgáltatását egy csoportként helyezheti üzembe, felügyelheti és figyelheti meg, és nem különálló erőforrásokként kell kezelnie azokat.
* A megoldás a teljes életciklusa során ismételten üzembe helyezhető, és az erőforrások üzembe helyezése biztosan konzisztens lesz.
* Hozzáférés-vezérlést alkalmazhat az összes erőforrásra az erőforráscsoportban, és a rendszer automatikusan alkalmazza ezeket a szabályzatokat, amikor új erőforrásokat ad a csoporthoz.
* Címkékkel láthatja el az erőforrásokat, így logikusan rendszerezhető az előfizetés összes erőforrása.
* A megoldás infrastruktúráját JavaScript Object Notation (JSON) formátumban definiálhatja. Ez a JSON-fájl az úgynevezett Resource Manager-sablon.
* Meghatározhatja az erőforrások közötti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

A Resource Manager bevezetésekor az összes erőforrás visszamenőleg hozzá lett rendelve alapértelmezett erőforráscsoportokhoz. Ha most létrehoz egy erőforrást a klasszikus üzembe helyezéssel, az erőforrás automatikusan létrejön az adott szolgáltatáshoz tartozó alapértelmezett erőforráscsoporthoz, még akkor is, ha az adott erőforráscsoportot nem adta meg az üzembe helyezéshez. Egy erőforráscsoport esetében azonban nem jelenti azt, hogy az erőforrás a Resource Manager-modellre lett konvertálva.

## <a name="understand-support-for-the-models"></a>A modellek támogatásának bemutatása

Három forgatókönyvről érdemes szót ejteni:

1. A Cloud Services nem támogatja a Resource Manager-alapú üzemi modellt.
2. A virtuális gépek, a tárfiókok és a virtuális hálózatok a Resource Manager-alapú és a klasszikus üzemi modellt egyaránt támogatják.
3. Az összes többi Azure-szolgáltatás a Resource Manager használatát támogatja.

A virtuális gépek, a tárfiókok és a virtuális hálózatok esetében, ha az erőforrás a klasszikus modellben lett üzembe helyezve, továbbra is klasszikus műveletekkel kell üzemeltetni. Ha a virtuális gép, a tárfiók vagy a virtuális hálózat Resource Manager-alapú modellben lett üzembe helyezve, továbbra is Resource Manager-műveleteket kell alkalmaznia. Ez elég kusza helyzeteket eredményezhet, ha az előfizetés Resource Manager-alapú és klasszikus üzemi modellben létrehozott erőforrásokat vegyesen tartalmaz. Az erőforrások ezen kombinációja váratlan eredményeket hozhat létre, mert az erőforrások nem támogatják ugyanazt a műveletet.

Bizonyos esetekben egyes Resource Manager-alapú parancsokkal lekérhetők a klasszikus üzemi modellben létrehozott erőforrásokra vonatkozó adatok, vagy végrehajthatók adminisztratív feladatok, például egy klasszikus erőforrás áthelyezése egy másik erőforráscsoportba. Ezek az esetek azonban nem engedik azt a benyomást, hogy a típus támogatja a Resource Manager-műveleteket. Tegyük fel például, hogy egy erőforráscsoportban egy olyan virtuális géppel rendelkezünk, amely a klasszikus üzemi modellben lett létrehozva. Ha futtatjuk az alábbi Resource Manager PowerShell-parancsot:

```powershell
Get-AzResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines
```

A következő virtuális gépet adja vissza:

```powershell
Name              : ExampleClassicVM
ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
ResourceName      : ExampleClassicVM
ResourceType      : Microsoft.ClassicCompute/virtualMachines
ResourceGroupName : ExampleGroup
Location          : westus
SubscriptionId    : {guid}
```

A Resource Manager **-parancsmag Get-AzVM** azonban csak a Resource Manageren keresztül üzembe helyezett virtuális gépeket adja vissza. A következő parancs nem a klasszikus telepítéssel létrehozott virtuális gépet tér vissza.

```powershell
Get-AzVM -ResourceGroupName ExampleGroup
```

Csak a Resource Manager-alapú modellben létrehozott erőforrások támogatják a címkéket. Nem alkalmazhat címkéket a klasszikus erőforrásokra.

## <a name="changes-for-compute-network-and-storage"></a>A számítási, hálózati és tárolási erőforrások változásai

Az alábbi diagramon Resource Manager-alapú modellben üzembe helyezett számítási, hálózati és tárolási erőforrások láthatók.

![Resource Manager-architektúra](./media/deployment-models/arm_arch3.png)

Biztonsági csomag: Storage erőforrás-szolgáltató, CRP: számítási erőforrás-szolgáltató, NRP: hálózati erőforrás-szolgáltató

Vegye figyelembe az erőforrások közötti következő összefüggéseket:

* Mindegyik erőforrás egy erőforráscsoportban található.
* A virtuális gép egy, a Storage erőforrás-szolgáltatóban definiált adott tárfióktól függ, hogy a lemezeit blobtárolóban tárolhassa (kötelező).
* A virtuális gép a hálózati erőforrás-szolgáltatóban (kötelező) meghatározott hálózati adapterre és a számítási erőforrás-szolgáltatóban definiált rendelkezésre állási csoportra hivatkozik (nem kötelező).
* A hálózati kártya a virtuális gép hozzárendelt IP-címére (kötelező), a virtuális gép virtuális hálózatának alhálózatára (kötelező) és egy hálózati biztonsági csoportra (nem kötelező) hivatkozik.
* A virtuális hálózaton belüli alhálózat egy hálózati biztonsági csoportra hivatkozik (nem kötelező).
* A terheléselosztó-példány olyan IP-címek háttér-készletére hivatkozik, amelyek tartalmazzák a virtuális gép hálózati adapterét (nem kötelező), és a terheléselosztó nyilvános vagy magánhálózati IP-címére hivatkozik (nem kötelező).

Íme az összetevők és a kapcsolataik a klasszikus üzemi modellben:

![klasszikus architektúra](./media/deployment-models/arm_arch1.png)

A klasszikus megoldás a virtuális gépek futtatására a következő:

* Egy szükséges felhőszolgáltatás, amely tárolóként szolgál a virtuális gépek futtatásához (Compute). A virtuális gépeket a rendszer automatikusan a hálózati kártya és az Azure által hozzárendelt IP-cím használatával kapja meg. Ezenkívül a felhőalapú szolgáltatás tartalmaz egy külső terheléselosztó példányt, egy nyilvános IP-címet és alapértelmezett végpontokat a távoli asztali és távoli PowerShell-forgalom lehetővé tételére a Windows-alapú virtuális gépek, valamint Secure Shell- (SSH-) forgalom lehetővé tételére a Linux-alapú virtuális gépek esetében.
* Egy szükséges Storage-fiók, amely egy virtuális gép virtuális merevlemezeit tárolja, beleértve az operációs rendszert, az ideiglenes és a további adatlemezeket (Storage).
* Olyan választható virtuális hálózat, amely további tárolóként működik, amelyben létrehozhat egy alhálózattal rendelkező struktúrát, és kiválaszthatja azt az alhálózatot, amelyen a virtuális gép található (hálózat).

A következő táblázat a Compute, a Network és a Storage erőforrás-szolgáltatók együttműködésének változásait ismerteti:

| Elem | Klasszikus | Resource Manager |
| --- | --- | --- |
| Felhőszolgáltatás a virtuális gépekhez |A Cloud Service egy tároló volt a virtuális gépekhez, amely platform és a terheléselosztás Rendelkezésre állását is igényelte. |Az új modell használatával a Cloud Service már nem szükséges objektum egy virtuális gép létrehozásához. |
| Virtuális hálózatok |A virtuális géphez nem szükséges virtuális hálózat. Ha szerepel, a virtuális hálózat nem telepíthető a Resource Managerrel. |A virtuális géphez egy, a Resource Managerrel üzembe helyezett virtuális hálózat szükséges. |
| Storage-fiókok |A virtuális gépnek olyan Storage-fiókra van szüksége, amely a virtuális merevlemezeket az operációs rendszer, az ideiglenes és a további adatlemezek számára tárolja. |A virtuális gép számára szükséges egy tárfiók, hogy a lemezeit blobtárolóban tárolhassa. |
| Rendelkezésre állási csoportok |A platform felé való rendelkezésre állást azonos „AvailabilitySetName” konfigurálásával lehetett jelezni a virtuális gépeken. A tartalék tartományok maximális száma 2 volt. |A Rendelkezésre állási csoport egy Microsoft.Compute szolgáltató által közzétett erőforrás. A nagy rendelkezésre állást igénylő virtuális gépeket szerepeltetni kell a Rendelkezésre állási csoportban. A tartalék tartományok maximális száma mostantól 3. |
| Affinitáscsoportok |Virtuális hálózatok létrehozásához szükség volt Affinitáscsoportokra. A regionális virtuális hálózatok bevezetése azonban már nem volt szükséges. |Egyszerűbben fogalmazva az Azure Resource Manageren keresztül közzétett API-kban nem létezik az Affinitáscsoportok koncepciója. |
| Terheléselosztás |Egy felhőszolgáltatás létrehozása egy implicit terheléselosztót biztosít a telepített virtuális gépekhez. |A Load Balancer egy Microsoft.Network szolgáltató által közzétett erőforrás. A terheléselosztást igénylő virtuális gépek elsődleges hálózati adapterének hivatkoznia kell a terheléselosztóra. Egy terheléselosztó lehet külső vagy belső. Egy terheléselosztó példány az IP-címek háttérkészletére hivatkozik, amelyek között egy virtuális gép hálózati adaptere is található (nem kötelező), továbbá hivatkozik egy terheléselosztó nyilvános vagy privát IP-címére is (nem kötelező). |
| Virtuális IP-cím |A Cloud Services egy alapértelmezett VIP-t (virtuális IP-cím) kap, amikor egy virtuális gépet hozzáadnak egy felhőszolgáltatáshoz. A Virtuális IP-cím az implicit terheléselosztóhoz társított cím. |A nyilvános IP-cím egy Microsoft.Network szolgáltató által közzétett erőforrás. Egy nyilvános IP-cím lehet statikus (fenntartott) vagy dinamikus. A dinamikus nyilvános IP-címek hozzárendelhetők egy terheléselosztóhoz. A nyilvános IP-címek védelme biztonsági csoportok segítségével biztosítható. |
| Fenntartott IP-címek |Az Azure-ban fenntarthat egy IP-címet, és társíthatja egy felhőszolgáltatáshoz, hogy biztosítsa az IP-cím állandóságát. |A nyilvános IP-címek létrehozhatók statikus módban, amely ugyanazokat a képességeket biztosítja, mint a fenntartott IP-címek. |
| Virtuális gépenként megadott nyilvános IP-cím (PIP) |A nyilvános IP-címek közvetlenül is hozzárendelhetők egy virtuális géphez. |A nyilvános IP-cím egy Microsoft.Network szolgáltató által közzétett erőforrás. Egy nyilvános IP-cím lehet statikus (fenntartott) vagy dinamikus. |
| Végpontok |A virtuális gépen konfigurálni kell a bemeneti végpontokat, hogy bizonyos portok csatlakoztathatóvá váljanak. A virtuális gépekhez való csatlakozás egyik legelterjedtebb módja a bemeneti végpontok beállítása. |A bejövő NAT-szabályok konfigurálhatók a terheléselosztókon, így azonos képességek érhetők el a végpontok engedélyezésére adott portokon a virtuális gépekhez való csatlakozás céljából. |
| DNS-név |Egy felhőszolgáltatás egy implicit globálisan egyedi DNS-nevet kap. Példa: `mycoffeeshop.cloudapp.net`. |A DNS-nevek opcionális paraméterek, amelyek egy nyilvános IP-cím erőforráson adhatók meg. Az FQDN formátuma a következő lesz: `<domainlabel>.<region>.cloudapp.azure.com`. |
| Hálózati illesztők |Az elsődleges és másodlagos hálózati adapter és tulajdonságai egy virtuális gép hálózati konfigurációjaként voltak megadva. |A hálózati adapter egy Microsoft.Network szolgáltató által közzétett erőforrás. A hálózati adapter életciklusa nem kötődik virtuális géphez. A virtuális gép hozzárendelt IP-címére (kötelező), a virtuális gép virtuális hálózatának alhálózatára (kötelező), valamint egy hálózati biztonsági csoportra (nem kötelező) hivatkozik. |

A különböző üzemi modellekből származó virtuális hálózatok összekapcsolásával kapcsolatban lásd a [különböző üzemi modellekből származó virtuális hálózatok a portálon történő összekapcsolását](../../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) ismertető szakaszt.

## <a name="migrate-from-classic-to-resource-manager"></a>Migrálás klasszikusról Resource Manager-alapú környezetbe

Ha készen áll az erőforrások áttelepítésére a klasszikus üzembe helyezésről a Resource Manager-alapú üzembe helyezésre, tekintse meg a következőket:

1. [Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról](../../virtual-machines/migration-classic-resource-manager-deep-dive.md)
2. [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való, platform által támogatott migrálása](../../virtual-machines/migration-classic-resource-manager-overview.md)
3. [IaaS-erőforrások migrálása a klasszikusból Resource Manager-alapú környezetbe az Azure PowerShell használatával](../../virtual-machines/migration-classic-resource-manager-ps.md)
4. [IaaS-erőforrások migrálása a klasszikusból Resource Manager-alapú környezetbe az Azure CLI használatával](../../virtual-machines/migration-classic-resource-manager-cli.md)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**Létrehozhatok virtuális gépet a Resource Managerrel úgy, hogy azután egy klasszikus üzembe helyezéssel létrehozott virtuális hálózatban legyen elérhető?**

Ez a konfiguráció nem támogatott. A Resource Manager használatával nem telepíthet virtuális gépeket a klasszikus üzembe helyezéssel létrehozott virtuális hálózatra.

**Létrehozhatok virtuális gépeket a Resource Managerrel a klasszikus üzemi modellben létrehozott felhasználói rendszerképből?**

Ez a konfiguráció nem támogatott. A virtuális merevlemezek fájljait azonban átmásolhatja egy olyan Storage-fiókból, amely a klasszikus üzemi modellel lett létrehozva, és hozzáadhatja őket egy, a Resource Manager használatával létrehozott új fiókhoz.

**Milyen változások vonatkoznak az előfizetésemhez tartozó kvótára?**

Az Azure Resource Managerrel létrehozott virtuális gépek, virtuális hálózatok és a tárfiókok kvótái nem azonosak a többi kvótával. Minden előfizetés saját kvótákat kap az erőforrások az új API-kkal való létrehozására. A további kvótákról [itt](../../azure-resource-manager/management/azure-subscription-service-limits.md) talál részletes információkat.

**Használhatom továbbra is az automatizált szkriptjeimet virtuális gépek, virtuális hálózatok és tárfiókok a Resource Manager API-kban történő kiépítésére?**

Az összes már létrehozott automatizálás és szkript továbbra is működik az Azure szolgáltatásfelügyeleti módban létrehozott, már meglévő virtuális gépeivel és virtuális hálózataival. Ha viszont az új sémával szeretné használni a szkripteket ugyanazon erőforrásoknak a Resource Manager módban való létrehozásához, frissítenie kell őket.

**Hol találhatok példákat az Azure Resource Manager-sablonokra?**

Az alapszintű sablonok átfogó készlete [Azure Resource Manager Gyorsindítás sablonokban](https://azure.microsoft.com/documentation/templates/)található.

## <a name="next-steps"></a>Következő lépések

* A sablonok üzembe helyezésével kapcsolatos parancsokért lásd [az alkalmazások Azure Resource Manager-sablonnal történő üzembe helyezését](../templates/deploy-powershell.md) ismertető szakaszt.