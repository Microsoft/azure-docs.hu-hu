---
title: Azure Automation adatbiztonság
description: Ebből a cikkből megtudhatja, hogyan Azure Automation az adatvédelem és az adatok védelme érdekében.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cb18cca782b85e608c3c7ddb001ecb03b86055f6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833525"
---
# <a name="management-of-azure-automation-data"></a>Azure Automation-adatok kezelése

Ez a cikk több témakört is tartalmaz, amelyek azt ismertetik, hogyan védik és védik az adatokat egy Azure Automation környezetben.

## <a name="tls-12-enforcement-for-azure-automation"></a>TLS 1.2 kényszerítési Azure Automation

Az átvitel közbeni adatok biztonságának Azure Automation javasoljuk, hogy konfigurálja a Transport Layer Security (TLS) 1.2 használatát. Az alábbi lista azokat a metódusokat vagy ügyfeleket sorolja fel, amelyek HTTPS-protokollon keresztül kommunikálnak az Automation szolgáltatással:

* Webhookhívások

* Hibrid runbook-dolgozók, amelyek közé tartoznak az Update Management és változáskövetés és leltározás.

* DSC-csomópontok

A TLS/SSL (SSL) régebbi verziói sebezhetőnek vannak, és bár jelenleg is dolgoznak a visszamenőleges kompatibilitás érdekében, nem **ajánlottak.** Nem javasoljuk, hogy az ügynököt csak akkor használja a TLS 1.2 használatára, ha szükséges, mivel ez megszakíthatja a platformszintű biztonsági funkciókat, amelyek lehetővé teszik az újabb biztonságosabb protokollok, például a TLS 1.3 automatikus észlelését és használatát.

A Windows és Linux rendszerekhez használható Log Analytics-ügynök TLS 1.2-támogatásával kapcsolatos további információkért lásd: [A Log Analytics-ügynök áttekintése – TLS 1.2](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol).

### <a name="platform-specific-guidance"></a>Platformspecifikus útmutató

|Platform/Nyelv | Támogatás | További információ |
| --- | --- | --- |
|Linux | A Linux-disztribúciók általában [OpenSSL-re](https://www.openssl.org) támaszkodnak a TLS 1.2 támogatásához.  | Ellenőrizze az [OpenSSL-változásnaplóban,](https://www.openssl.org/news/changelog.html) hogy az Ön OpenSSL-verziója támogatott-e.|
| Windows 8.0 – 10 | Támogatott és alapértelmezés szerint engedélyezve van. | Annak megerősítéséhez, hogy továbbra is az alapértelmezett [beállításokat használja.](/windows-server/security/tls/tls-registry-settings)  |
| Windows Server 2012 – 2016 | Támogatott és alapértelmezés szerint engedélyezve van. | Annak ellenőrzése, hogy továbbra is az alapértelmezett [beállításokat használja-e](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 és Windows Server 2008 R2 SP1 | Támogatott, de alapértelmezés szerint nincs engedélyezve. | Az engedélyezés Transport Layer Security az Transport Layer Security [(TLS)](/windows-server/security/tls/tls-registry-settings) beállításjegyzék-beállítások oldalán talál további információt.  |

## <a name="data-retention"></a>Adatmegőrzés

Amikor töröl egy erőforrást a Azure Automation, a rendszer számos napig megőrzi az erőforrásokat naplózási célokból a végleges eltávolítás előtt. Ez idő alatt az erőforrás nem látható és nem használható. Ez a szabályzat a törölt Automation-fiókhoz tartozó erőforrásokra is vonatkozik. Az adatmegőrzési szabályzat minden felhasználóra vonatkozik, és jelenleg nem szabható testre. Ha azonban hosszabb ideig kell adatokat tartania, a feladatadatokat továbbíthatja Azure Automation [naplókba Azure Monitor naplókba.](automation-manage-send-joblogs-log-analytics.md)

Az alábbi táblázat összefoglalja a különböző erőforrások megőrzési szabályzatát.

| Adatok | Szabályzat |
|:--- |:--- |
| Fiókok |A fiók véglegesen el lesz távolítva 30 nappal azután, hogy a felhasználó törölte. |
| Objektumok |Az eszköz véglegesen el lesz távolítva 30 nappal azután, hogy a felhasználó törölte azt, vagy 30 nappal azután, hogy a felhasználó törölte az eszközt használó fiókot. Az eszközök közé tartoznak a változók, az ütemezések, a hitelesítő adatok, a tanúsítványok, a Python 2-csomagok és a kapcsolatok. |
| DSC-csomópontok |Egy DSC-csomópont véglegesen el lesz távolítva 30 nappal azután, hogy az Azure Portal vagy a [Unregister-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) parancsmag használatával törölték az Automation-fiókból a Windows PowerShell. A csomópont 30 nappal azután is véglegesen el lesz távolítva, hogy a felhasználó törli a csomópontot használó fiókot. |
| Feladatok |A rendszer töröl és véglegesen eltávolít egy feladatot a módosítás után 30 nappal, például miután a feladat befejeződött, le lett állítva vagy fel lett függesztve. |
| Modulok |A modul véglegesen el lesz távolítva 30 nappal azután, hogy a felhasználó törölte azt, vagy 30 nappal azután, hogy a felhasználó törölte a modult használó fiókot. |
| Csomópont-konfigurációk/MOF-fájlok |Egy régi csomópont-konfiguráció véglegesen el lesz távolítva 30 nappal az új csomópont-konfiguráció létrehozása után. |
| Node-jelentések |A csomópontjelentés véglegesen el lesz távolítva 90 nappal azután, hogy új jelentést generált a csomóponthoz. |
| Runbookok |A runbook véglegesen el lesz távolítva 30 nappal azután, hogy egy felhasználó törölte az erőforrást, vagy 30 nappal azután, hogy egy felhasználó törölte az<sup>1.</sup>erőforrást használó fiókot. |

<sup>1</sup> A runbook a 30 napos időkereten belül helyreállítható, ha egy incidenst Azure-támogatás a Microsoft Azure ügyfélszolgálattal. A Azure-támogatás [webhelyre, és](https://azure.microsoft.com/support/options/) válassza **a Támogatási kérelem elküldése lehetőséget.**

## <a name="data-backup"></a>Adatok biztonsági mentése

Amikor töröl egy Automation-fiókot az Azure-ban, a fiókban lévő összes objektum törlődik. Az objektumok közé tartoznak a runbookok, modulok, konfigurációk, beállítások, feladatok és objektumok. A fiók törlése után nem állíthatók helyre. Az alábbi információk segítségével biztonságimentheti az Automation-fiók tartalmát a törlés előtt.

### <a name="runbooks"></a>Runbookok

A runbookokat szkriptfájlokba exportálhatja a Azure Portal vagy a [get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) parancsmag használatával a Windows PowerShell. Ezeket a szkriptfájlokat importálhatja egy másik Automation-fiókba a [Runbookok](manage-runbooks.md)kezelése a következőben Azure Automation.

### <a name="integration-modules"></a>Integrációs modulok

Az integrációs modulokat nem exportálhatja Azure Automation, ezeket az Automation-fiókon kívül kell elérhetővé tenni.

### <a name="assets"></a>Objektumok

A következő adateszközök nem exportálhatók Azure Automation: tanúsítványok, kapcsolatok, hitelesítő adatok, ütemezések és változók. Ehelyett használhatja a Azure Portal azure-parancsmagokat az eszközök részleteinek megjegyzésére. Ezután ezekkel az adatokkal létrehozhatja a más Automation-fiókba importált runbookok által használt összes adateszközt.

A titkosított változók értékei vagy a hitelesítő adatok jelszómezői nem kérhetők le parancsmagok használatával. Ha nem ismeri ezeket az értékeket, lekérheti őket egy runbookban. A változóértékek leolvasását lásd: Változóeszközök a [Azure Automation.](shared-resources/variables.md) A hitelesítő adatok értékeinek lekért értékeivel kapcsolatos további információkért lásd a következő [Azure Automation.](shared-resources/credentials.md)

### <a name="dsc-configurations"></a>DSC-konfigurációk

A DSC-konfigurációkat szkriptfájlokba exportálhatja a Azure Portal vagy az [Export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) parancsmag használatával a Windows PowerShell. Ezeket a konfigurációkat importálhatja és használhatja egy másik Automation-fiókban.

## <a name="geo-replication-in-azure-automation"></a>Georeplikáció a Azure Automation

A georeplikáció szabványos a Azure Automation fiókokban. A fiók beállításakor elsődleges régiót kell választania. A belső Automation georeplikációs szolgáltatás automatikusan hozzárendel egy másodlagos régiót a fiókhoz. A szolgáltatás ezután folyamatosan biztonsági adatbázist hoz létre a fiókadatokról az elsődleges régióból a másodlagos régióba. Az elsődleges és másodlagos régiók teljes listája az Üzletmenet-folytonosság és vészhelyreállítás [(BCDR): Párosított Azure-régiók.](../best-practices-availability-paired-regions.md)

Az Automation georeplikációs szolgáltatás által létrehozott biztonsági másolat az Automation-eszközök, -konfigurációk és hasonlók teljes másolata. Ez a biztonsági mentés akkor használható, ha az elsődleges régió leáll, és adatokat veszít. Abban a valószínűtlen esetben, ha egy elsődleges régió adatai elvesznek, a Microsoft megpróbálja helyreállítani az adatokat.

> [!NOTE]
> Azure Automation az ügyfél által kiválasztott régióban tárolja az ügyféladatokat. A BCDR esetében a dél- és délkelet-ázsiai régió kivételével minden régióban a Azure Automation adatok egy másik régióban (Azure párosított régióban) tárolódnak. Csak a dél-brazíliai (Sao Paulo State) régióban, ahol Brazília földrajzi helye és a Ázsia és a Csendes-óceáni térség földrajzi hely Délkelet-Ázsia (Szingapúr) régiója található, az Azure Automation-adatokat ugyanabban a régióban tároljuk, hogy igazodni tudjuk az ezekre a régiókra vonatkozó adathellyel kapcsolatos követelményekhez.

Regionális hiba esetén az Automation georeplikációs szolgáltatás nem érhető el közvetlenül a külső ügyfelek számára. Ha regionális hibák esetén is fenn szeretné tartani az Automation konfigurációját és runbookját:

1. Válasszon ki egy másodlagos régiót az elsődleges Automation-fiók földrajzi régiójával párosítani.

2. Hozzon létre egy Automation-fiókot a másodlagos régióban.

3. Az elsődleges fiókban exportálja a forgatókönyveket szkriptfájlokként.

4. Importálja a forgatókönyveket a másodlagos régióban található Automation-fiókjába.

## <a name="next-steps"></a>Következő lépések

* További információ a biztonságos eszközökről a [Azure Automation: Biztonságos](automation-secure-asset-encryption.md)eszközök titkosítása a Azure Automation.

* További információ a georeplikációról: [Aktív georeplikáció létrehozása és használata.](../azure-sql/database/active-geo-replication-overview.md)
