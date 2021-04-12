---
title: A VM v2 (előzetes verzió) indítása/leállítása – áttekintés
description: Ez a cikk a virtuális gépek indítása/leállítása (előzetes verzió) funkciójának két verzióját ismerteti, amely a Azure Resource Manager és a klasszikus virtuális gépek ütemterven való elindítását vagy leállítását mutatja be.
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.openlocfilehash: 44bfbaa8b18ebeab3b74bc696a16fc4cfb6c08ec
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220934"
---
# <a name="startstop-vms-v2-preview-overview"></a>A VM v2 (előzetes verzió) indítása/leállítása – áttekintés

A VM v2 (előzetes verzió) indítási/leállítási funkciója elindítja vagy leállítja az Azure Virtual Machines (VM) szolgáltatást több előfizetésen belül. A felhasználó által meghatározott ütemterveken elindítja vagy leállítja az Azure-beli virtuális gépeket, elemzéseket nyújt az [azure Application Insightson](../../azure-monitor/app/app-insights-overview.md)keresztül, és nem kötelező értesítéseket küldhet a [műveleti csoportok](../../azure-monitor/alerts/action-groups.md)használatával. A szolgáltatás a legtöbb forgatókönyv esetén a Azure Resource Manager virtuális gépeket és a klasszikus virtuális gépeket is képes kezelni.

A Start/Stop VM v2 (előzetes verzió) új verziója egy decentralizált, alacsony költségű automatizálási lehetőséget biztosít azon ügyfelek számára, akik optimalizálni szeretnék a virtuális gépek költségeit. A szolgáltatás az összes olyan funkciót kínálja, mint az [eredeti verzió](../../automation/automation-solution-vm-management.md) , Azure Automation, de úgy van kialakítva, hogy kihasználja az új technológiákat az Azure-ban.

## <a name="overview"></a>Áttekintés

A virtuális gépek v2 indítása/leállítása (előzetes verzió) újratervezése megtörténik, és nem függ Azure Automation vagy Azure Monitor naplóktól, ahogy azt az [előző verzió](../../automation/automation-solution-vm-management.md)megköveteli. Ez a verzió a [Azure functions](../../azure-functions/functions-overview.md) a virtuális gép indításának és leállításának elvégzésére támaszkodik.

A rendszer felügyelt identitást hoz létre Azure Active Directory (Azure AD) ehhez a Azure Functions alkalmazáshoz, és lehetővé teszi a virtuális gépek v2 (előzetes verzió) indítását és leállítását, így egyszerűen hozzáférhet más Azure AD-védelemmel ellátott erőforrásokhoz, például a Logic apps és az Azure virtuális gépek További információ az Azure AD-beli felügyelt identitásokról: [felügyelt identitások az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md).

A rendszer létrehoz egy HTTP-trigger végponti függvényt, amely támogatja a funkcióban található ütemezéseket és a sorozatot, ahogy az alábbi táblázatban is látható.

|Name |Eseményindító |Leírás |
|-----|--------|------------|
|AlertAvailabilityTest |Időzítő |Ez a függvény végrehajtja a rendelkezésre állási tesztet annak biztosításához, hogy az elsődleges függvény **AutoStopVM** mindig elérhető legyen.|
|Alapleállítás |HTTP |Ez a függvény támogatja az **autostop** forgatókönyvet, amely a logikai alkalmazásból meghívott belépési pont függvény.|
|AutoStopAvailabilityTest |Időzítő |Ez a függvény végrehajtja a rendelkezésre állási tesztet annak ellenőrzéséhez, hogy az elsődleges funkció az **autostop** mindig elérhető-e.|
|AutoStopVM |HTTP |A virtuális gép riasztása automatikusan aktiválja ezt a funkciót, ha a riasztási feltétel igaz.|
|CreateAutoStopAlertExecutor |Üzenetsor |Ez a függvény beolvassa a hasznos adatokat az **autostop** függvényből, hogy létrehozza a riasztást a virtuális gépen.|
|Ütemezett |HTTP |Ez a függvény mind az ütemezett, mind a szekvenciális (a hasznos adatok sémája által megkülönböztetett) forgatókönyv esetén használható. Ez a logikai alkalmazásból meghívott belépési pont függvény, amely a virtuális gép indítási vagy leállítási műveletének feldolgozásához szükséges hasznos adatokat veszi fel. |
|ScheduledAvailabilityTest |Időzítő |Ez a függvény végrehajtja a rendelkezésre állási tesztet annak biztosításához, hogy az **ütemezett** elsődleges függvény mindig elérhető legyen.|
|VirtualMachineRequestExecutor |Üzenetsor |Ez a függvény végrehajtja a tényleges indítási és leállítási műveletet a virtuális gépen.|
|VirtualMachineRequestOrchestrator |Üzenetsor |Ez a függvény beolvassa a hasznos adatokat az **ütemezett** függvényből, és összehangolja a virtuális gépek indítási és leállítási kéréseit.|

Az **ütemezett** http-trigger függvény például az ütemezési és a sorozatot érintő forgatókönyvek kezelésére szolgál. Hasonlóképpen, **az automatikus leállítás http** -trigger funkciója kezeli az automatikus leállítási forgatókönyvet.

A szolgáltatás támogatásához üzenetsor-alapú trigger-függvények szükségesek. Az időzítő-alapú eseményindítók a rendelkezésre állási teszt elvégzésére és a rendszer állapotának figyelésére szolgálnak.

 [Azure Logic apps](../../logic-apps/logic-apps-overview.md) használatával konfigurálhatja és kezelheti a virtuális gép indítási és leállítási ütemterveit, ha a függvényt JSON-adattartalommal hívja meg. Alapértelmezés szerint a kezdeti üzembe helyezés során a rendszer összesen öt Logic Apps hoz létre a következő esetekben:

- Az ütemezett indítási és leállítási műveletek a Azure Resource Manager és a klasszikus virtuális gépeken megadott ütemezés alapján vannak meghatározva. **ststv2_vms_Scheduled_start** és **ststv2_vms_Scheduled_stop** konfigurálja az ütemezett kezdést és leállítást.

- A sorrendbe állított – indítási és leállítási műveletek az előre definiált szekvencia-címkékkel rendelkező virtuális gépekre irányuló ütemezésen alapulnak. Csak két megnevezett címke támogatott – **sequencestart** és **sequencestop**. **ststv2_vms_Sequenced_start** és **ststv2_vms_Sequenced_stop** konfigurálja az előkészített indítást és a leállítást.

    > [!NOTE]
    > Ez a forgatókönyv csak Azure Resource Manager virtuális gépeket támogatja.

- Autostop – ez a funkció csak a CPU-kihasználtsága alapján a Azure Resource Manager és a klasszikus virtuális gépekre vonatkozó leállítási műveletek végrehajtásához használatos. Ez egy ütemezett *művelet* is lehet, amely riasztásokat hoz létre a virtuális gépeken, és a feltétel alapján a riasztás a Leállítás művelet végrehajtásához aktiválódik. **ststv2_vms_AutoStop** konfigurálja az automatikus leállítási funkciót.

Minden indítási/leállítási művelet támogatja egy vagy több előfizetés, erőforráscsoport vagy virtuális gép listájának hozzárendelését.

A függvények által igényelt Azure Storage-fiókot a virtuális gépek v2 (előzetes verzió) indítási/leállítási funkciója is használja két célra:

   - Az Azure Table Storage használatával tárolja a végrehajtási művelet metaadatait (azaz a virtuális gép indítása/leállítása műveletet).

   - Az Azure Queue Storage használatával támogatja az Azure Functions üzenetsor-alapú eseményindítókat.

Az összes telemetria-adat, amely nyomkövetési naplók a Function app-végrehajtásból, a rendszer elküldi a csatlakoztatott Application Insights-példánynak. A Application Insights tárolt telemetria-adatok a megosztott [Azure-irányítópulton](../../azure-portal/azure-portal-dashboards.md)megjelenített előre definiált vizualizációk készletében tekinthetők meg.

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="Virtuális gépek megosztott állapotának indítása/leállítása irányítópult":::

A virtuális gépeken végrehajtott műveletek eredményeképpen a rendszer e-mailes értesítéseket is küld.

## <a name="new-releases"></a>Új kiadások

Ha a Start/Stop VM v2 (előzetes verzió) új verziója jelenik meg, a példány automatikusan frissül, anélkül, hogy manuálisan újra kellene telepíteni.

## <a name="supported-scoping-options"></a>Támogatott hatóköri beállítások

### <a name="subscription"></a>Előfizetés

Az előfizetés hatóköre akkor használható, ha a teljes előfizetésben lévő összes virtuális gép indítási és leállítási műveletét el szeretné végezni, és szükség esetén több előfizetést is kijelölhet.  

Megadhatja a kizárni kívánt virtuális gépek listáját is, és figyelmen kívül hagyja azokat a műveletből. Helyettesítő karaktereket is használhat az egyidejűleg figyelmen kívül hagyható nevek megadásához.

### <a name="resource-group"></a>Erőforráscsoport

Az erőforráscsoport hatóköre akkor használható, ha az összes virtuális gépen el kell végezni az indítási és leállítási műveletet egy vagy több erőforráscsoport-név megadásával, valamint egy vagy több előfizetésben.

Megadhatja a kizárni kívánt virtuális gépek listáját is, és figyelmen kívül hagyja azokat a műveletből. Helyettesítő karaktereket is használhat az egyidejűleg figyelmen kívül hagyható nevek megadásához.

### <a name="vmlist"></a>VMList

A virtuális gépek listájának megadása akkor használható, ha a kezdési és a leállítási műveletet egy adott virtuálisgép-készleten, illetve több előfizetésen keresztül kell végrehajtania. Ez a beállítás nem támogatja a kizárni kívánt virtuális gépek listájának megadását.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiókkal kell rendelkeznie. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

- A fiókja megkapta a [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) engedélyt az előfizetésben.

- A VM v2 (előzetes verzió) indítása/leállítása az összes Azure globális és USA-beli kormányzati Felhőbeli régióban elérhető, amelyek a Azure Functions [régió lapján elérhető termékek területen](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions) jelennek meg.

## <a name="next-steps"></a>Következő lépések

A szolgáltatás központi telepítéséhez lásd: a [virtuális gépek indítása/leállítása](deploy.md) (előzetes verzió).