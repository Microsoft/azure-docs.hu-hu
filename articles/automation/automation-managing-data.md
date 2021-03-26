---
title: Adatbiztonság Azure Automation
description: Ebből a cikkből megtudhatja, hogyan védi a Azure Automation az adatvédelmet, és gondoskodik az adatok védelméről.
services: automation
ms.subservice: shared-capabilities
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: c3d1dfc5d6ea16a128f5f3bc1129f5f50bc9cb61
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954673"
---
# <a name="management-of-azure-automation-data"></a>Azure Automation-adatok kezelése

Ez a cikk számos olyan témakört tartalmaz, amelyekből megtudhatja, hogyan védi és biztosítja az adatvédelmet egy Azure Automation környezetben.

## <a name="tls-12-enforcement-for-azure-automation"></a>TLS 1,2 kényszerítés a Azure Automation

A Azure Automation felé irányuló adatforgalom biztosításához határozottan javasoljuk, hogy konfigurálja a Transport Layer Security (TLS) 1,2 használatát. Az alábbi lista azokat a módszereket vagy ügyfeleket sorolja fel, amelyek HTTPS protokollon keresztül kommunikálnak az Automation szolgáltatással:

* Webhook-hívások

* Hibrid Runbook-feldolgozók, beleértve a Update Management és a Change Tracking és a leltár által felügyelt gépeket.

* DSC-csomópontok

A TLS/SSL (SSL) régebbi verziói sebezhetőnek találták, és miközben jelenleg is működnek a visszamenőleges kompatibilitás érdekében, **nem ajánlottak**. Nem ajánlott explicit módon beállítani az ügynököt úgy, hogy csak a TLS 1,2-et használja, kivéve, ha a szükséges, mivel ez a platform szintű biztonsági funkciókat is lehetővé teszi, amelyekkel automatikusan észlelhetők és kihasználhatják az elérhetővé váló újabb biztonságosabb protokollokat, például a TLS 1,3.

További információ a Windows és Linux rendszerhez készült Log Analytics ügynökkel való TLS 1,2-támogatásról, amely a hibrid Runbook-feldolgozói szerepkörtől függ: [log Analytics ügynök áttekintése – TLS 1,2](..//azure-monitor/agents/log-analytics-agent.md#tls-12-protocol).

### <a name="platform-specific-guidance"></a>Platform-specifikus útmutató

|Platform/nyelv | Támogatás | További információ |
| --- | --- | --- |
|Linux | A Linux-disztribúciók általában az [OpenSSL](https://www.openssl.org) -t használják a TLS 1,2 támogatásához.  | Ellenőrizze az OpenSSL- [changelog](https://www.openssl.org/news/changelog.html) , hogy az OpenSSL verziója támogatott-e.|
| Windows 8,0 – 10 | Támogatott, és alapértelmezés szerint engedélyezve van. | Annak megerősítéséhez, hogy továbbra is az [alapértelmezett beállításokat](/windows-server/security/tls/tls-registry-settings)használja.  |
| Windows Server 2012 – 2016 | Támogatott, és alapértelmezés szerint engedélyezve van. | Annak ellenőrzése, hogy továbbra is az [alapértelmezett beállításokat](/windows-server/security/tls/tls-registry-settings) használja-e |
| Windows 7 SP1 és Windows Server 2008 R2 SP1 | Támogatott, de alapértelmezés szerint nincs engedélyezve. | Az engedélyezésével kapcsolatos részletekért tekintse meg a [Transport Layer Security (TLS) beállításjegyzék-beállítások](/windows-server/security/tls/tls-registry-settings) lapját.  |

## <a name="data-retention"></a>Adatmegőrzés

Ha Azure Automationban töröl egy erőforrást, a rendszer a végleges eltávolítás előtt több napig megőrzi a naplózási célokra. Ebben az időszakban nem tekintheti meg és nem használhatja az erőforrást. Ez a szabályzat a törölt Automation-fiókhoz tartozó erőforrásokra is vonatkozik. Az adatmegőrzési szabályzat minden felhasználóra érvényes, és jelenleg nem szabható testre. Ha azonban hosszabb ideig kell megtartania az adatokra vonatkozó adatmegőrzést, [Azure Automation feladataikat Azure monitor naplókhoz továbbíthatja](automation-manage-send-joblogs-log-analytics.md).

A következő táblázat összefoglalja a különböző erőforrások adatmegőrzési szabályzatát.

| Adatok | Szabályzat |
|:--- |:--- |
| Fiókok |Egy fiók a felhasználó törlése után 30 nappal véglegesen el lesz távolítva. |
| Objektumok |Egy eszköz a felhasználó törlése után 30 nappal véglegesen el lesz távolítva, vagy 30 nappal azután, hogy egy felhasználó töröl egy olyan fiókot, amely tartalmazza az adategységet. Az eszközök közé tartoznak a változók, az ütemtervek, a hitelesítő adatok, a tanúsítványok, a Python 2 csomagok és a kapcsolatok. |
| DSC-csomópontok |A DSC-csomópontok véglegesen el lettek távolítva egy Automation-fiókból a Windows PowerShellben Azure Portal vagy a [Regisztráció törlése-AzAutomationDscNode](/powershell/module/az.automation/unregister-azautomationdscnode) parancsmag használatával. Egy csomópontot is véglegesen eltávolít 30 nappal azután, hogy a felhasználó törli a csomópontot tároló fiókot. |
| Feladatok |A rendszer töröl egy feladatot, és véglegesen eltávolítja azt a módosítás után 30 nappal, például a feladatok befejeződése után, leáll vagy felfüggesztve. |
| Modulok |Egy modul a felhasználó törlése után 30 nappal véglegesen el lesz távolítva, vagy 30 nappal azután, hogy a felhasználó törli a modult tartalmazó fiókot. |
| Csomópont-konfigurációk/MOF-fájlok |Egy régi csomópont-konfiguráció véglegesen el lesz távolítva egy új csomópont-konfiguráció generálása után 30 nappal. |
| Csomópont-jelentések |Az adott csomópontra vonatkozó új jelentés létrehozása után a Node-jelentés 90 nappal véglegesen törlődik. |
| Runbookok |Egy runbook véglegesen el lett távolítva 30 nappal azután, hogy egy felhasználó törli az erőforrást, vagy 30 nappal azután, hogy egy felhasználó törli az<sup>1</sup>. erőforrást tartalmazó fiókot. |

<sup>1</sup> A runbook a 30 napos időszakon belül helyreállítható egy Microsoft Azure támogatással rendelkező Azure támogatási incidens bejelentésével. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatási kérelem küldése** lehetőséget.

## <a name="data-backup"></a>Az adatbiztonsági mentés

Ha töröl egy Automation-fiókot az Azure-ban, a fiókban lévő összes objektum törlődik. Az objektumok közé tartoznak a runbookok, a modulok, a konfigurációk, a beállítások, a feladatok és az eszközök. A fiók törlése után nem állíthatók helyre. A következő információk segítségével biztonsági másolatot készíthet az Automation-fiók tartalmáról a törlés előtt.

### <a name="runbooks"></a>Runbookok

A runbookok a Windows PowerShell Azure Portal vagy a [Get-AzureAutomationRunbookDefinition](/powershell/module/servicemanagement/azure.service/get-azureautomationrunbookdefinition) parancsmagjának használatával exportálhatja parancsfájl-fájlokba. Ezeket a parancsfájlokat egy másik Automation-fiókba importálhatja, ahogy azt a [Azure Automation Runbookok kezelése](manage-runbooks.md)című szakaszban tárgyaljuk.

### <a name="integration-modules"></a>Integrációs modulok

Az integrációs modulokat nem exportálhatja Azure Automationból, hanem elérhetővé kell tenni az Automation-fiókon kívül.

### <a name="assets"></a>Objektumok

Azure Automation-eszközök nem exportálhatók: tanúsítványok, kapcsolatok, hitelesítő adatok, ütemtervek és változók. Ehelyett a Azure Portal és az Azure-parancsmagokkal is megjegyezheti az eszközök részleteit. Ezután ezekkel az adatokkal létrehozhat olyan eszközöket, amelyeket egy másik Automation-fiókba importált runbookok használ.

A titkosított változók vagy a hitelesítő adatok jelszava mezők a parancsmagok használatával nem olvashatók be. Ha nem ismeri ezeket az értékeket, lekérheti őket egy runbook. A változók értékeinek lekéréséhez lásd: [változó eszközök Azure Automationban](shared-resources/variables.md). A hitelesítő adatok értékének beolvasásával kapcsolatos további tudnivalókért tekintse meg a [hitelesítő adatok a Azure Automationban](shared-resources/credentials.md)című témakört.

### <a name="dsc-configurations"></a>DSC-konfigurációk

A DSC-konfigurációkat a Windows PowerShell Azure Portal vagy [export-AzAutomationDscConfiguration](/powershell/module/az.automation/export-azautomationdscconfiguration) parancsmagjának használatával is exportálhatja a parancsfájlok fájljaiba. Ezeket a konfigurációkat egy másik Automation-fiókban is importálhatja és használhatja.

## <a name="geo-replication-in-azure-automation"></a>Geo-replikálás Azure Automation

A Geo-replikáció a Azure Automation fiókokban szabványos. A fiók beállításakor ki kell választania egy elsődleges régiót. A belső Automation geo-Replication szolgáltatás automatikusan hozzárendel egy másodlagos régiót a fiókhoz. A szolgáltatás ezután folyamatosan biztonsági mentést készít az elsődleges régióból a másodlagos régióba. Az elsődleges és másodlagos régiók teljes listája megtalálható az [üzletmenet folytonossága és a vész-helyreállítási (BCDR): Azure párosított régiók](../best-practices-availability-paired-regions.md)között.

Az Automation geo-Replication szolgáltatás által létrehozott biztonsági másolat az Automation-eszközök, konfigurációk és hasonlók teljes másolata. Ez a biztonsági másolat akkor használható, ha az elsődleges régió leáll, és elveszíti az adatvesztést. Abban az esetben, ha az elsődleges régió adatainak elvesztése nem valószínű, a Microsoft megkísérli a helyreállítást.

> [!NOTE]
> A Azure Automation az ügyfél által kiválasztott régióban tárolja az ügyféladatokat. A BCDR esetében minden régió esetében, kivéve Dél-és Délkelet-Ázsiában, Azure Automation az adat egy másik régióban (Azure párosított régió) tárolódik. A Ázsia és a Csendes-óceáni térség földrajzban található Brazília déli régiója (Sao Paulo állam) régiója és Délkelet-ázsiai régiója (Szingapúr) esetében az adatAzure Automation tárolásra vonatkozó követelményeket az adott régióban tároljuk.

Az Automation geo-Replication szolgáltatás nem érhető el közvetlenül a külső ügyfelek számára, ha van regionális hiba. Ha az Automation-konfigurációt és a runbookok a regionális hibák során szeretné fenntartani:

1. Válasszon ki egy másodlagos régiót, amely párosítva van az elsődleges Automation-fiók földrajzi régiójával.

2. Hozzon létre egy Automation-fiókot a másodlagos régióban.

3. Az elsődleges fiókban a runbookok fájlba exportálja.

4. Importálja a runbookok az Automation-fiókjába a másodlagos régióban.

## <a name="next-steps"></a>Következő lépések

* Ha többet szeretne megtudni a Azure Automation található biztonságos eszközökről, tekintse meg a biztonságos adategységek [titkosítása Azure Automation-ben](automation-secure-asset-encryption.md)című témakört.

* További információ a Geo-replikációról: [Active geo-replikáció létrehozása és használata](../azure-sql/database/active-geo-replication-overview.md).
