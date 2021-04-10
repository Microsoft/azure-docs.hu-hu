---
title: Platform által támogatott áttelepítési eszköz.
description: Technikai részletes információk a platform által támogatott áttelepítésről a klasszikus üzemi modellből a Azure Resource Managerra.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 12/17/2020
ms.author: tagore
ms.openlocfilehash: 43473b734968b25ca90bd8809c8291625011d46f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667014"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Részletes műszaki útmutató a klasszikusból az Azure Resource Manager-alapú üzemi modellbe történő, platform által támogatott migrálásról

> [!IMPORTANT]
> Napjainkban a IaaS virtuális gépek 90%-a [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)használ. 2020. február 28-án a klasszikus virtuális gépek elavultak, és 2023. március 1-jén teljesen megszűnnek. [További]( https://aka.ms/classicvmretirement) információ erről az elavult szolgáltatásról, valamint arról, [hogy Ön hogyan befolyásolja Önt](./classic-vm-deprecation.md#how-does-this-affect-me).

Vessünk egy alapos bevezetést a klasszikus Azure üzemi modellből a Azure Resource Manager üzemi modellbe való Migrálás során. Egy erőforrás és szolgáltatás szintjén tekintjük meg az erőforrásokat, amelyekkel megismerheti, hogy az Azure platform hogyan telepítse át az erőforrásokat a két üzembe helyezési modell között. További információkért olvassa el a szolgáltatásról szóló közleményt: a [platform által támogatott IaaS-erőforrások áttelepítése klasszikusról Azure Resource Managerra](migration-classic-resource-manager-overview.md).


## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>IaaS-erőforrások áttelepítése a klasszikus üzemi modellből a Azure Resource Manager
Először is fontos megérteni az adatsík és a felügyeleti sík-műveletek közötti különbséget az infrastruktúra-szolgáltatás (IaaS) erőforrásaiban.

* A felügyelet */vezérlési sík* leírja azokat a hívásokat, amelyek a felügyeleti/vezérlési síkon vagy az erőforrások módosítására szolgáló API-ban jönnek. Például az olyan műveletek, mint a virtuális gépek létrehozása vagy újraindítása, vagy a virtuális hálózatok új alhálózattal való frissítése a futó erőforrások felügyeletét szolgálják. Nem befolyásolják közvetlenül a virtuális gépekhez való csatlakozást.
* Az *adatsík* (alkalmazás) leírja az alkalmazás futtatókörnyezetét, és olyan példányokkal való interakciót is magában foglal, amelyek nem haladják meg az Azure API-t. Például a webhelyhez való hozzáférés, illetve az adatok egy futó SQL Server-példányból vagy egy MongoDB-kiszolgálóról való lekérése adatsík vagy alkalmazás-interakció. Egyéb példák közé tartozik például a blob másolása egy Storage-fiókból, és egy nyilvános IP-cím elérése RDP protokoll (RDP) vagy Secure Shell (SSH) használatával a virtuális géphez. Ezek a műveletek működtetik az alkalmazást a számítási, hálózati és tárolási erőforrásokon.

Az adatsík a klasszikus üzembe helyezési modell és a Resource Manager-verem között azonos. A különbség az, hogy az áttelepítési folyamat során a Microsoft lefordítja az erőforrások ábrázolását a klasszikus üzemi modellből a Resource Manager-verembe. Ennek eredményeképpen új eszközöket, API-kat és SDK-kat kell használnia az erőforrások kezeléséhez a Resource Manager-veremben.

![A felügyelet/vezérlési sík és az adatsík közötti különbséget bemutató diagram](./media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> Egyes migrálási forgatókönyvekben az Azure platform leállítja, felszabadítja és újraindítja a virtuális gépeket. Ez egy rövid adatsík-állásidőt okoz.
>

## <a name="the-migration-experience"></a>A migrálási folyamat
Az áttelepítés megkezdése előtt:

* Győződjön meg róla, hogy a migrálni kívánt erőforrások nem használnak nem támogatott szolgáltatásokat vagy konfigurációkat. A platform általában észleli ezeket a problémákat, és hibát jelez.
* Ha olyan virtuális gépekkel rendelkezik, amelyek nem virtuális hálózatban találhatók, akkor az előkészítési művelet részeként leállítják és lefoglalják őket. Ha nem szeretné elveszíteni a nyilvános IP-címet, érdemes megfontolnia az IP-cím megőrzését az előkészítési művelet elindítása előtt. Ha a virtuális gépek virtuális hálózaton vannak, nem állnak le és nem lesznek lefoglalva.
* A migrálást ütemezze munkaidőn kívülre, hogy a migrálás során esetlegesen felmerülő nem várt hibák kezelhetőek legyenek.
* Töltse le a virtuális gépek aktuális konfigurációját a PowerShell vagy a parancssori felület (CLI) parancsai, illetve REST API-k segítségével, hogy könnyebb legyen az érvényesítés az előkészítési lépés után.
* Frissítse az automatizálási és operacionalizálási parancsfájlokat a Resource Manager-alapú üzemi modell kezeléséhez az áttelepítés megkezdése előtt. GET műveleteket is használhat, ha az erőforrások már előkészített állapotban vannak.
* Értékelje ki a klasszikus üzemi modell IaaS-erőforrásain konfigurált Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) házirendeket, és tervezze meg az áttelepítés befejezését követően.

Az áttelepítési munkafolyamat a következő:

![Az áttelepítési munkafolyamatot bemutató diagram](./media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> A következő szakaszokban ismertetett műveletek mindegyike idempotens. Ha a nem támogatott funkció vagy a konfigurációs hiba nem a megfelelő hibával rendelkezik, próbálkozzon újra az előkészítési, a megszakítási vagy a végrehajtási művelettel. Az Azure újra próbálkozik a művelettel.
>
>

### <a name="validate"></a>Érvényesítés
Az érvényesítés művelet a migrálási folyamat első lépése. Ennek a lépésnek a célja, hogy elemezze az áttelepíteni kívánt erőforrások állapotát a klasszikus üzemi modellben. A művelet kiértékeli, hogy az erőforrások képesek-e áttelepítésre (sikeres vagy sikertelen).

Válassza ki a virtuális hálózatot vagy a felhőalapú szolgáltatást (ha az nem virtuális hálózatban van), amelyet szeretne érvényesíteni az áttelepítéshez. Ha az erőforrás nem képes áttelepítésre, az Azure felsorolja az okokat.

#### <a name="checks-not-done-in-the-validate-operation"></a>Az ellenőrzés nem történt meg az érvényesítési művelet során.

Az érvényesítési művelet csak a klasszikus üzemi modellben lévő erőforrások állapotát elemzi. A klasszikus üzemi modell különböző konfigurációi miatt a hibák és a nem támogatott forgatókönyvek ellenőrzését is lehetővé teszi. Nem lehet megkeresni az összes olyan problémát, amelyet a Azure Resource Manager verem az erőforrásokra az áttelepítés során felmerülhet. Ezeket a problémákat csak akkor ellenőrzi a rendszer, ha az erőforrások átalakulnak az áttelepítés következő lépésében (az előkészítési művelet). A következő táblázat felsorolja az érvényesítési műveletben nem ellenőrzött összes problémát:


|A hálózati ellenőrzések nem szerepelnek az érvényesítési műveletben.|
|-|
|Olyan virtuális hálózat, amely mind az ER, mind a VPN-átjáróval rendelkezik.|
|Egy virtuális hálózati átjáró kapcsolata leválasztott állapotban van.|
|A rendszer az összes ER áramkört előre áttelepíti Azure Resource Manager verembe.|
|Azure Resource Manager kvóta ellenőrzi a hálózati erőforrásokat. Például: statikus nyilvános IP-cím, dinamikus nyilvános IP-címek, terheléselosztó, hálózati biztonsági csoportok, útválasztási táblák és hálózati adapterek. |
| A terheléselosztó összes szabálya érvényes a telepítés és a virtuális hálózat között. |
| Ütköző magánhálózati IP-címek az azonos virtuális hálózatban lévő leállított virtuális gépek között. |

### <a name="prepare"></a>Előkészítés
Az előkészítés művelet a migrálási folyamat második lépése. Ennek a lépésnek a célja a IaaS-erőforrások átalakításának szimulálása a klasszikus üzemi modellből a Resource Manager-erőforrásokra. Emellett az előkészítési művelet ezt a párhuzamosan jeleníti meg.

> [!NOTE] 
> A klasszikus üzemi modell erőforrásai nem módosulnak ebben a lépésben. Ez egy biztonságos lépés, ha az áttelepítést próbálja meg futtatni. 

Válassza ki a virtuális hálózatot vagy a felhőalapú szolgáltatást (ha az nem virtuális hálózat), amelyet elő szeretne készíteni az áttelepítéshez.

* Ha az erőforrás nem képes áttelepítésre, az Azure leállítja az áttelepítési folyamatot, és felsorolja az előkészítési művelet okát.
* Ha az erőforrás áttelepítésre képes, az Azure lezárja a felügyeleti sík műveleteit az áttelepítés alatt lévő erőforrásokhoz. Például nem csatolhat adatlemezt egy migrálás alatt álló virtuális géphez.

Az Azure ezután elindítja a metaadatok áttelepítését a klasszikus üzemi modellből a Resource Managerbe a Migrálás erőforrásaihoz.

Az előkészítési művelet befejezése után lehetősége van arra, hogy a klasszikus üzemi modellben és a Resource Managerben is megjelenítse az erőforrásokat. Az Azure platform a klasszikus üzemi modellben minden egyes felhőszolgáltatáshoz létrehoz egy erőforráscsoport-nevet a következő mintának megfelelően: `cloud-service-name>-Migrated`.

> [!NOTE]
> Nem lehet kijelölni az áttelepített erőforrásokhoz létrehozott erőforráscsoport nevét (azaz "-Migrálva"). Az áttelepítés befejezése után azonban a Azure Resource Manager áthelyezés funkciójával áthelyezheti az erőforrásokat bármely olyan erőforráscsoporthoz, amelyet használni szeretne. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Az alábbi két képernyőkép a sikeres előkészítési művelet utáni eredményt mutatja. Az első egy olyan erőforráscsoportot mutat be, amely az eredeti Cloud Service-t tartalmazza. A második az új "áttelepített" erőforráscsoportot mutatja, amely az egyenértékű Azure Resource Manager erőforrásokat tartalmazza.

![Az eredeti Cloud Service-t bemutató képernyőkép](./media/migration-classic-resource-manager/portal-classic.png)

![Az előkészítési művelet Azure Resource Manager erőforrásait bemutató képernyőkép](./media/migration-classic-resource-manager/portal-arm.png)

A mögöttes jeleneteket az előkészítési fázis befejezése után tekintheti meg az erőforrások között. Vegye figyelembe, hogy az adatsíkon lévő erőforrás ugyanaz. Ez a felügyeleti síkon (a klasszikus üzemi modellben) és a vezérlési síkon (Resource Manager) egyaránt képviselteti magát.

![Az előkészítési fázis ábrája](./media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> A klasszikus üzemi modellben nem virtuális hálózatban lévő virtuális gépek leállnak és fel lesznek foglalva az áttelepítés ezen fázisában.
>

### <a name="check-manual-or-scripted"></a>Ellenőrzés (manuális vagy szkriptalapú)
Az ellenőrzés lépésnél lehetősége van arra, hogy a korábban letöltött konfigurációt használja annak ellenőrzéséhez, hogy az áttelepítés helyesnek tűnik-e. Azt is megteheti, hogy bejelentkezik a portálra, és a tulajdonságok és erőforrások helyszíni ellenőrzésével ellenőrzi, hogy a metaadatok áttelepítése jól néz-e.

Ha virtuális hálózatot migrál, a virtuális gépek legtöbb konfigurációja nem indul újra. A virtuális gépeken futó alkalmazások esetében ellenőrizheti, hogy az alkalmazás továbbra is fut-e.

A figyelési és működési parancsfájlok tesztelésével ellenőrizheti, hogy a virtuális gépek a várt módon működnek-e, és hogy a frissített parancsfájlok megfelelően működnek-e. Csak a GET műveletek támogatottak, amikor az erőforrások az előkészített állapotban vannak.

Nincs olyan beállított időszak, amely előtt véglegesíteni kell az áttelepítést. Bármennyi időt eltölthet ebben az állapotban. A felügyeleti sík azonban zárolva van ezekhez az erőforrásokhoz, amíg meg nem szakítja vagy véglegesíti a műveletet.

Ha bármilyen problémát észlel, mindig megszakíthatja a migrálást, és visszatérhet a klasszikus üzemi modellhez. A visszatérést követően az Azure megnyitja a felügyeleti sík műveleteit az erőforrásokon, így a hagyományos üzemi modellben folytathatja a normál működést a virtuális gépeken.

### <a name="abort"></a>Megszakítás
Ez egy nem kötelező lépés, ha a módosításokat a klasszikus üzemi modellre kívánja visszaállítani, és le szeretné állítani az áttelepítést. Ez a művelet törli az erőforrásokhoz tartozó Resource Manager-metaadatokat (az előkészítési lépésben létrehozva). 

![Megszakítási lépés diagramja](media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Ez a művelet nem hajtható végre, miután elindította a véglegesítő műveletet.     
>

### <a name="commit"></a>Véglegesítés
Az ellenőrzés befejezése után véglegesítheti a migrálást. Az erőforrások már nem jelennek meg a klasszikus üzembe helyezési modellben, és csak a Resource Manager-alapú üzemi modellben érhetők el. A migrált erőforrások csak az új portálon kezelhetők.

> [!NOTE]
> Ez egy idempotens művelet. Ha nem sikerül, próbálja megismételni a műveletet. Ha továbbra is sikertelen, hozzon létre egy támogatási jegyet, vagy hozzon létre egy fórumot a [Microsoft Q&a](/answers/index.html)
>
>

![A véglegesítő lépés ábrája](media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Áttelepítési folyamatábra

Az alábbi folyamatábra bemutatja, hogyan folytathatja az áttelepítést:

![Képernyőkép a migrálási lépésekről](media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>A klasszikus üzembe helyezési modell fordítása Resource Manager-erőforrásokra
A következő táblázatban található erőforrások klasszikus üzemi modelljét és erőforrás-kezelői képviseleteit találhatja meg. Az egyéb szolgáltatások és erőforrások jelenleg nem támogatottak.

| Klasszikus ábrázolás | Resource Manager-ábrázolás | Jegyzetek |
| --- | --- | --- |
| Felhőalapú szolgáltatás neve (üzemeltetett szolgáltatás neve) |DNS-név |A migrálás során minden felhőszolgáltatáshoz egy új erőforráscsoport jön létre a következő elnevezési mintának megfelelően: `<cloudservicename>-migrated`. Ez az erőforráscsoport tartalmazza az összes erőforrást. A felhőszolgáltatás egy DNS-névvé alakul, amely a nyilvános IP-címhez van társítva. |
| Virtuális gép |Virtuális gép |A virtuális gépre jellemző tulajdonságok a migrálás során nem változnak. Bizonyos osProfile-információk, például a számítógépnév nem a klasszikus üzemi modellben tárolódnak, és az áttelepítés után üresek maradnak. |
| A virtuális géphez csatolt lemezerőforrások |A virtuális géphez csatolt implicit lemezek |A lemezek nem legfelső szintű erőforrásként vannak modellezve a Resource Manager-alapú üzemi modellben. A virtuális gép implicit lemezeiként lesznek migrálva. Jelenleg csak a virtuális géphez csatolt lemezek támogatottak. A Resource Manager-alapú virtuális gépek mostantól a klasszikus üzemi modellben használhatják a Storage-fiókokat, ami lehetővé teszi, hogy a lemezek a frissítés nélkül is könnyen áttelepíthetők legyenek. |
| Virtuálisgép-bővítmények |Virtuálisgép-bővítmények |Az összes erőforrás-bővítmény, az XML-bővítmények kivételével, migrálva lett a klasszikus üzemi modellből. |
| A virtuális gép tanúsítványai |Tanúsítványok az Azure Key Vaultban |Ha egy felhőalapú szolgáltatás szolgáltatási tanúsítványokat tartalmaz, az áttelepítés létrehoz egy új Azure Key vaultot a Cloud Service-ben, és áthelyezi a tanúsítványokat a kulcstartóba. A virtuális gépek frissülnek, hogy a kulcstartóban található tanúsítványokra hivatkozzanak. <br><br> Ne törölje a Key vaultot. Ennek hatására a virtuális gép meghibásodott állapotba léphet. |
| WinRM-konfiguráció |WinRM-konfiguráció osProfile alatt |A Rendszerfelügyeleti webszolgáltatások konfiguráció az áthelyezésekor változatlan marad a migrálás során. |
| Rendelkezésre állási csoport tulajdonsága |Rendelkezésre állási csoport erőforrás | A rendelkezésre állási készlet specifikációja a virtuális gép egy tulajdonsága a klasszikus üzemi modellben. A rendelkezésre állási csoportok legfelső szintű erőforrásokká alakulnak a migrálás részeként. A következő konfigurációk nem támogatottak: több rendelkezésre állási csoport felhőszolgáltatásonként, illetve egy vagy több rendelkezésre állási csoport olyan virtuális gépekkel, amelyek nem tartoznak egy rendelkezésre állási csoporthoz sem egy felhőszolgáltatásban. |
| Hálózati konfiguráció egy virtuális gépen |Elsődleges hálózati adapter |A hálózati konfiguráció egy virtuális gépen az elsődleges hálózati adapter erőforrásként jelenik meg a migrálás után. Azoknál a virtuális gépeknél, amelyek nem tagjai virtuális hálózatnak, a belső IP-cím módosul a migrálás során. |
| Több hálózati adapter egy virtuális gépen |Hálózati adapterek |Ha egy virtuális gépnek több hálózati adaptere van társítva, minden egyes hálózati adapter legfelső szintű erőforrás lesz az áttelepítés részeként, az összes tulajdonsággal együtt. |
| Elosztott terhelésű végpont csoport |Terheléselosztóval |A klasszikus üzemi modellben a platform egy implicit terheléselosztót rendel minden felhőszolgáltatáshoz. A migrálás során egy új terheléselosztó erőforrás jön létre, és a terheléselosztó végpont csoport terheléselosztó szabályokká alakul. |
| Bejövő NAT-szabályok |Bejövő NAT-szabályok |A virtuális gépen meghatározott bemeneti végpontok a terheléselosztó hálózati címfordításra vonatkozó bejövő szabályaivá alakulnak a migrálás során. |
| Virtuális IP-cím |Nyilvános IP-cím DNS-névvel |A virtuális IP-cím egy nyilvános IP-cím lesz, és a terheléselosztó társítva van. A virtuális IP-cím csak akkor migrálható, ha társítva van hozzá egy bemeneti végpont. |
| Virtuális hálózat |Virtuális hálózat |A virtuális hálózat az összes tulajdonságával a Resource Manager-alapú üzemi modellbe lesz migrálva. Létrejön egy új erőforráscsoport a következő névvel: `-migrated`. |
| Fenntartott IP-címek |Nyilvános IP-cím statikus kiosztási módszerrel |A terheléselosztóhoz társított fenntartott IP-címek a felhőszolgáltatással vagy a virtuális géppel együtt migrálódnak. A nem társított fenntartott IP-címek áttelepíthetők a [Move-AzureReservedIP](/powershell/module/servicemanagement/azure.service/move-azurereservedip)használatával.  |
| Nyilvános IP-cím virtuális gépenként |Nyilvános IP-cím dinamikus kiosztási módszerrel |A virtuális géphez társított nyilvános IP-cím nyilvános IP-cím erőforrássá alakul, statikus kiosztási módszerrel beállítva. |
| NSG-k |NSG-k |Az alhálózatokhoz társított hálózati biztonsági csoportokat a Resource Manager-alapú üzemi modellbe való migrálás részeként a rendszer klónozza. A hálózati biztonsági csoportok a klasszikus üzemi modellben nem törlődnek a migráláskor. A hálózati biztonsági csoportok felügyeletisík-műveletei azonban zárolva vannak, amíg a migrálás folyamatban van. A nem társított NSG áttelepíthetők a [Move-AzureNetworkSecurityGroup](/powershell/module/servicemanagement/azure.service/move-azurenetworksecuritygroup)használatával.|
| DNS-kiszolgálók |DNS-kiszolgálók |A virtuális hálózathoz vagy virtuális géphez társított DNS-kiszolgálók a megfelelő erőforrás-migrálás részeként migrálódnak az összes tulajdonsággal együtt. |
| UDR-ek |UDR-ek |Az alhálózatokhoz társított felhasználó által megadott útvonalakat a Resource Manager-alapú üzemi modellbe való migrálás részeként a rendszer klónozza. A felhasználó által megadott útvonalak a klasszikus üzemi modellben nem törlődnek a migráláskor. A felhasználó által megadott útvonalak felügyeletisík-műveletei zárolva vannak, amíg a migrálás folyamatban van. A nem társított UDR áttelepíthetők a [Move-AzureRouteTable](/powershell/module/servicemanagement/azure.service/Move-AzureRouteTable)használatával. |
| IP-továbbítási tulajdonság a virtuális gép hálózati konfigurációjában |IP-továbbítási tulajdonság a hálózati adapteren |A virtuális gép IP-továbbítási tulajdonsága a hálózati adapter tulajdonságává alakul a migrálás közben. |
| Terheléselosztó több IP-címmel |Terheléselosztó több nyilvános IP-erőforrással |A terheléselosztó összes nyilvános IP-címe át lett alakítva egy nyilvános IP-erőforrásra, és az áttelepítés után a terheléselosztó társítva van. |
| Belső DNS-nevek a virtuális gépen |Belső DNS-nevek a hálózati adapteren |A migrálás során a virtuális gép belső DNS-utótagjai egy „InternalDomainNameSuffix” nevű csak olvasható tulajdonságba migrálódnak a hálózati adapteren. Az utótag változatlan marad az áttelepítés után, és a virtuális gép feloldásának a korábbinál továbbra is működnie kell. |
| Virtuális hálózati átjáró |Virtuális hálózati átjáró |A virtuális hálózati átjáró tulajdonságai változatlanok lesznek áttelepítve. Az átjáróhoz társított virtuális IP-cím sem változik. |
| Helyi hálózati hely |Helyi hálózati átjáró |A helyi hálózati hely tulajdonságait a rendszer változatlanul egy helyi hálózati átjáró nevű új erőforrásra telepíti át. Ez a helyszíni cím előtagjait és a távoli átjáró IP-címét jelöli. |
| Kapcsolati hivatkozások |Kapcsolat |Az átjáró és a hálózati konfiguráció helyi hálózati helye közötti kapcsolati referenciákat egy kapcsolat nevű új erőforrás jelképezi. A hálózati konfigurációs fájlokban a kapcsolati hivatkozás összes tulajdonságát a rendszer változatlanul másolja a kapcsolati erőforrásra. A virtuális hálózatok közötti kapcsolat a klasszikus üzemi modellben úgy érhető el, hogy két IPsec-alagutat hoz létre a virtuális hálózatokat jelképező helyi hálózati helyekhez. Ezt a rendszer a virtuális hálózat – virtuális hálózat típusú kapcsolatok típusára alakítja át a Resource Manager-modellben anélkül, hogy helyi hálózati átjáróra lenne szükség. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Az automatizálás és az eszközök módosítása a migrálás után
Az erőforrások klasszikus üzemi modellből a Resource Manager-alapú üzemi modellbe való áttelepítésének részeként frissítenie kell a meglévő automatizálást vagy eszközöket, hogy az a Migrálás után is működjön.


## <a name="next-steps"></a>Következő lépések

* [A IaaS-erőforrások platform által támogatott áttelepítésének áttekintése klasszikusról Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Az IaaS-erőforrások klasszikusból Azure Resource Manager-alapú környezetbe való áttelepítésének megtervezése](migration-classic-resource-manager-plan.md)
* [A IaaS-erőforrások migrálása a Klasszikusból a Azure Resource Managerba a PowerShell használatával](migration-classic-resource-manager-ps.md)
* [A CLI használatával IaaS-erőforrásokat telepíthet át a klasszikusról Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Klasszikusról VPN Gateway a Resource Manager-áttelepítésre](../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [ExpressRoute-áramkörök és társított virtuális hálózatok áttelepítése a Klasszikusból a Resource Manager-alapú üzemi modellbe](../expressroute/expressroute-migration-classic-resource-manager.md)
* [Közösségi eszközök a IaaS-erőforrások Klasszikusból Azure Resource Managerba való áttelepítésének támogatásához](migration-classic-resource-manager-community-tools.md)
* [A leggyakoribb áttelepítési hibák áttekintése](migration-classic-resource-manager-errors.md)
* [Tekintse át a IaaS-erőforrások klasszikusról Azure Resource Managerra való áttelepítésével kapcsolatos leggyakoribb kérdéseket](migration-classic-resource-manager-faq.md)
