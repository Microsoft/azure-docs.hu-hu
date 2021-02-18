---
title: Ügyfél által kezelt tárfiókok használata az Azure Monitor Log Analyticsben
description: Saját Storage-fiók használata Log Analytics forgatókönyvekhez
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: 3c5a528ada9e7239f5c53da1cae6df7ceffac918
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617475"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Ügyfél által kezelt tárfiókok használata az Azure Monitor Log Analyticsben

A Log Analytics különböző helyzetekben az Azure Storage-ra támaszkodik. Ezt a használatot általában automatikusan kezeli a rendszer. Bizonyos esetekben azonban a saját Storage-fiók (más néven ügyfél által felügyelt Storage-fiók) megadására és felügyeletére van szükség. Ez a dokumentum az ügyfél által felügyelt tárolók használatát ismerteti a WAD/LAD-naplók, a privát hivatkozások és az ügyfél által felügyelt kulcs (CMK) titkosításához. 

> [!NOTE]
> Azt javasoljuk, hogy ne használja függőséget az ügyfél által felügyelt tárhelyre Log Analytics feltöltéseket, mivel a formázás és a tartalom változhat.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Azure Diagnostics-bővítményi naplók betöltése (WAD/LAD)
A Azure Diagnostics Extension Agents (más néven WAD és LAD Windows-és Linux-ügynökök esetében) különböző operációsrendszer-naplókat gyűjt, és egy ügyfél által felügyelt Storage-fiókba tárolja őket. Ezután betöltheti ezeket a naplókat Log Analyticsba, hogy áttekintse és elemezze őket.
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>Azure Diagnostics-bővítményi naplók összegyűjtése a Storage-fiókból
A Storage-fiókot a [Azure Portal](../essentials/diagnostics-extension-logs.md#collect-logs-from-azure-storage) vagy a Storage-ELEMZÉSi [API](/rest/api/loganalytics/storage%20insights/createorupdate)meghívásával a Storage-adatforráshoz a log Analytics munkaterülethez kell kötni.

Támogatott adattípusok:
* Rendszernapló
* Windows-események
* Service Fabric
* ETW események
* IIS-naplók

## <a name="using-private-links"></a>Privát hivatkozások használata
Az ügyfél által felügyelt Storage-fiókok egyéni naplók vagy IIS-naplók betöltésére használhatók, ha a Azure Monitor erőforrásokhoz való kapcsolódáshoz magánhálózati hivatkozások használatosak. Ezeknek az adattípusoknak a betöltési folyamata először feltölti a naplókat egy közvetítő Azure Storage-fiókba, és csak ezután tölti be őket egy munkaterületre. 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>Ügyfél által felügyelt Storage-fiók használata privát kapcsolaton keresztül
#### <a name="workspace-requirements"></a>Munkaterület-követelmények
Amikor privát kapcsolaton keresztül csatlakozik Azure Monitorhoz, Log Analytics-ügynökök csak privát hivatkozáson keresztül elérhető munkaterületek számára tudják elküldeni a naplókat. Ez a követelmény azt jelenti, hogy:
* Azure Monitor Private link scope (AMPLS) objektum konfigurálása
* Kapcsolódás a munkaterületekhez
* A AMPLS csatlakoztatása a hálózathoz privát kapcsolaton keresztül. 

A AMPLS konfigurációs eljárással kapcsolatos további információkért lásd: az [Azure privát hivatkozás használata a hálózatok Azure monitorhoz való biztonságos csatlakoztatásához](./private-link-security.md). 

#### <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények
Ahhoz, hogy a Storage-fiók sikeresen csatlakozhasson a privát kapcsolathoz, a következőket kell tennie:
* Legyen a VNet vagy egy társ hálózatán, és privát kapcsolaton keresztül csatlakozik a VNet.
* Ugyanazon a régión kell lennie, mint a munkaterületnek, amelyhez kapcsolódik.
* A Storage-fiók elérésének engedélyezése Azure Monitor számára. Ha úgy döntött, hogy csak a hálózatokat választja a Storage-fiókjához, válassza a "megbízható Microsoft-szolgáltatások elérésének engedélyezése a Storage-fiókhoz" kivételt.
![Storage-fiók megbízhatósága MS Services-rendszerkép](./media/private-storage/storage-trust.png)
* Ha a munkaterület más hálózatokból is kezeli a forgalmat, akkor konfigurálnia kell a Storage-fiókot, hogy engedélyezze a kapcsolódó hálózatokból/internetről érkező bejövő forgalmat.

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>Ügyfél által felügyelt Storage-fiók használata a CMK-alapú adattitkosításhoz
Az Azure Storage-ban a Storage-fiókokban tárolt összes adatok titkosítva vannak. Alapértelmezés szerint a Microsoft által felügyelt kulcsokat (MMK) használja az adattitkosításhoz; Az Azure Storage azonban lehetővé teszi, hogy a CMK az Azure Key vaultból is használhassa a tárolási adatok titkosításához. Importálhatja a saját kulcsait Azure Key Vaultba, vagy használhatja a Azure Key Vault API-kat kulcsok létrehozásához.
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>CMK-forgatókönyvek, amelyekhez ügyfél által felügyelt Storage-fiók szükséges
* Napló-riasztási lekérdezések titkosítása a CMK
* Mentett lekérdezések titkosítása a CMK

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>A CMK alkalmazása az ügyfél által felügyelt Storage-fiókokra
##### <a name="storage-account-requirements"></a>Storage-fiókra vonatkozó követelmények
A Storage-fióknak és a Key vaultnak ugyanabban a régióban kell lennie, de különböző előfizetésekben is lehetnek. Az Azure Storage titkosításával és a kulcskezelő szolgáltatással kapcsolatos további információkért lásd: [Az Azure Storage titkosítása inaktív adatokhoz](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>CMK alkalmazása a Storage-fiókokra
Ha úgy szeretné konfigurálni az Azure Storage-fiókot, hogy az CMK-t használja a Azure Key Vaulthoz, használja a [Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), a [PowerShellt](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)vagy a [parancssori](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)felületet. 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>Storage-fiókok csatolása a Log Analytics munkaterülethez
### <a name="using-the-azure-portal"></a>Az Azure Portal használata
A Azure Portal nyissa meg a munkaterület menüt, és válassza a *társított Storage-fiókok* lehetőséget. Megnyílik egy panel, amely megjeleníti a társított Storage-fiókokat a fentiekben említett használati esetek (privát kapcsolaton keresztüli betöltés, CMK alkalmazása mentett lekérdezésekre vagy riasztásokra).
![A csatolt Storage-fiókok paneljének képe ](./media/private-storage/all-linked-storage-accounts.png) a tábla egyik elemének kiválasztásával megnyithatja a Storage-fiókjának adatait, ahol beállíthatja vagy frissítheti a társított Storage-fiókot ehhez a típushoz. 
![Egy Storage-fiókhoz tartozó panel-rendszerkép csatolása használhatja ](./media/private-storage/link-a-storage-account-blade.png) ugyanazt a fiókot a különböző használati esetekben, ha szeretné.

### <a name="using-the-azure-cli-or-rest-api"></a>Az Azure CLI vagy a REST API használata
A Storage-fiókot az [Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) -n keresztül vagy [REST API](/rest/api/loganalytics/linkedstorageaccounts)is összekapcsolhatja a munkaterülettel.

A megfelelő dataSourceType-értékek a következők:
* CustomLogs – a Storage-fiók használata az egyéni naplókhoz és az IIS-naplók betöltéséhez
* Lekérdezés – a Storage-fiók használata a mentett lekérdezések tárolásához (CMK titkosításhoz szükséges)
* Riasztások – a Storage-fiók használata a log-alapú riasztások tárolásához (CMK titkosításhoz szükséges)


## <a name="managing-linked-storage-accounts"></a>Társított Storage-fiókok kezelése

### <a name="create-or-modify-a-link"></a>Hivatkozás létrehozása vagy módosítása
Ha egy munkaterülethez csatolja a Storage-fiókot, Log Analytics a szolgáltatás által birtokolt Storage-fiók helyett elkezdi használni. képes vagy 
* Több Storage-fiók regisztrálása a naplók közötti terhelés elosztásához
* Ugyanazt a Storage-fiókot használja több munkaterülethez

### <a name="unlink-a-storage-account"></a>Storage-fiók csatolásának megszüntetése
A Storage-fiók használatának megszüntetéséhez kapcsolja ki a tárolót a munkaterületről. Az összes Storage-fiók egy munkaterületről való leválasztása azt jelenti, Log Analytics megkísérli a szolgáltatás által felügyelt Storage-fiókokra támaszkodni. Ha a hálózat korlátozottan fér hozzá az internethez, előfordulhat, hogy ezek a tárolók nem érhetők el, és a tárterületre támaszkodó forgatókönyvek sikertelenek lesznek.

### <a name="replace-a-storage-account"></a>Storage-fiók cseréje
A betöltéshez használt Storage-fiók cseréje
1.  **Hozzon létre egy új Storage-fiókra mutató hivatkozást.** A naplózási ügynökök megkapják a frissített konfigurációt, és megkezdik az adatok küldését az új tárhelyre is. A folyamat eltarthat néhány percig.
2.  **Ezután leválaszthatja a régi Storage-fiókot, hogy az ügynökök ne kelljen írni az eltávolított fiókba.** A betöltési folyamat megtartja az adatoknak a fiókból való beolvasását mindaddig, amíg az összes adatot be nem tölti. Addig ne törölje a Storage-fiókot, amíg meg nem jelenik az összes napló betöltése.

### <a name="maintaining-storage-accounts"></a>Storage-fiókok karbantartása
#### <a name="manage-log-retention"></a>Napló megőrzésének kezelése
Saját Storage-fiók használata esetén a megőrzési idő az Ön számára. Log Analytics nem törli a privát tárolóban tárolt naplókat. Ehelyett úgy kell beállítania a házirendet, hogy a beállításoknak megfelelően kezelje a terhelést.

#### <a name="consider-load"></a>Vegye figyelembe a betöltést
A Storage-fiókok bizonyos terhelésű olvasási és írási kérelmeket kezelhetnek a kérelmek szabályozásának megkezdése előtt (További információ: a [blob Storage skálázhatósági és teljesítménybeli céljai](../../storage/common/scalability-targets-standard-account.md)). A szabályozás a naplók betöltéséhez szükséges időt befolyásolja. Ha a Storage-fiók túl van terhelve, regisztráljon egy további Storage-fiókot a terhelés elosztásához. A Storage-fiók kapacitásának és teljesítményének figyeléséhez tekintse át [a Azure Portal]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Kapcsolódó díjak
A Storage-fiókokat a tárolt adatmennyiség, a tároló típusa és a redundancia típusa alapján számítjuk fel. Részletekért lásd: a [Blobok díjszabásának](https://azure.microsoft.com/pricing/details/storage/blobs) és a [Table Storage díjszabásának](https://azure.microsoft.com/pricing/details/storage/tables)blokkolása.


## <a name="next-steps"></a>Következő lépések

- Tudnivalók az [Azure Private-hivatkozás használatáról a hálózatok Azure monitorhoz való biztonságos csatlakoztatásához](private-link-security.md)
- Ismerkedjen meg [Azure monitor ügyfél által felügyelt kulcsokkal](../logs/customer-managed-keys.md)
