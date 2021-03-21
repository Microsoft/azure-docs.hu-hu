---
title: 'Egyetlen objektum szinkronizálása Azure AD Connect '
description: Megtudhatja, hogyan szinkronizálhat egy objektumot Active Directoryról az Azure AD-be a hibaelhárításhoz.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 373cebee4e7f95062791d8bc68bfee7d845e1465
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726063"
---
# <a name="azure-ad-connect-single-object-sync"></a>Egyetlen objektum szinkronizálása Azure AD Connect 

Az Azure AD Connect egy objektum-szinkronizáló eszköz egy PowerShell-parancsmag, amely az egyes objektumok Active Directoryról Azure Active Directoryre való szinkronizálására használható. A létrehozott jelentés az objektumok szinkronizálásával kapcsolatos problémák kivizsgálására és hibaelhárítására használható. 

> [!NOTE]
> Az eszköz támogatja a Active Directoryról Azure Active Directoryra történő szinkronizálást. Nem támogatja a Azure Active Directoryról Active Directoryra történő szinkronizálást. 
>
> Az eszköz támogatja az objektumok módosításának hozzáadását és frissítését. Nem támogatja az objektum módosítási törlésének szinkronizálását. 

## <a name="how-it-works"></a>Működés
Az egyobjektumos szinkronizáló eszköznek egy Active Directory megkülönböztető nevet kell adni bemenetként, hogy megkeresse a forrás-összekötőt és a partíciót az importáláshoz. A módosításokat a Azure Active Directoryba exportálja. Az eszköz a **provisioningObjectSummary** -ERŐFORRÁSTÍPUS hasonló JSON-kimenetet hoz létre. 

Az egyetlen objektum szinkronizáló eszköze a következő lépéseket hajtja végre: 

 1. Annak megállapítása, hogy az objektum (forrás) tartománya (Active Directory összekötő és partíció) szinkron hatókörben van-e. 
 2. Annak megállapítása, hogy az objektum (cél) tartománya (Azure Active Directory összekötő és partíció) szinkron hatókörben van-e. 
 3. Annak meghatározása, hogy a szinkronizálási hatókörben van-e az objektum szervezeti egysége. 
 4. Annak megállapítása, hogy az objektum elérhető-e az összekötő-fiók hitelesítő adataival. 
 5. Annak megállapítása, hogy az objektum típusa a szinkronizálási hatókörben van-e. 
 6. Annak megállapítása, hogy az objektum szinkron hatókörben van-e, ha engedélyezve van a csoport szűrése. 
 7. Objektum importálása Active Directoryról Active Directory-összekötő területére. 
 8. Objektum importálása Azure Active Directoryról Azure Active Directory-összekötő területére. 
 9. Objektum szinkronizálása Active Directory-összekötő területéről. 
 10. Objektum exportálása Azure Active Directory-összekötő területéről a Azure Active Directoryba. 

A JSON-kimeneten kívül az eszköz egy HTML-jelentést hoz létre, amely a szinkronizálási művelet összes adatával rendelkezik. A HTML-jelentés a **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ ADSyncSingleObjectSyncResult- <date> . htm fájlban** található. Ez a HTML-jelentés megosztható a támogatási csapattal, ha szükséges, további hibaelhárítást végezhet. 

A HTML-jelentés a következő: 

|Tab|Description|
|-----|-----|
|Lépések|Az objektumok szinkronizálásához szükséges lépéseket ismerteti. Az egyes lépések a hibaelhárítás részleteit tartalmazzák. Az importálási, szinkronizálási és exportálási lépések további attribútum-információkat tartalmaznak, például a nevet, a többértékű, a típus, az érték, az érték hozzáadása, az érték törlése, a művelet, a szinkronizálási szabály, a leképezés típusa és az adatforrás.| 
|Hibaelhárítás & javaslat|Megadja a hibakódot és az okot. A hiba adatai csak akkor érhetők el, ha hiba történik.| 
|Módosított tulajdonságok|Megjeleníti a régi értéket és az új értéket. Ha nincs régi érték, vagy ha az új érték törlődik, akkor ez a cella üres. A többértékű attribútumok esetében a darabszámot jeleníti meg. Az attribútum neve egy hivatkozás a Steps (lépések) lapra: objektum exportálása Azure Active Directory-összekötő területéről a Azure Active Directory: attribútum-információ, amely az attribútum további részleteit tartalmazza, például a név, a többértékű, a típus, az érték, a Hozzáadás, az érték törlése, a művelet, a szinkronizálási szabály, a leképezés típusa és az adatforrás.| 
|Összefoglalás|Áttekintést nyújt az objektumról a forrás-és a célszámítógépeken.| 

## <a name="prerequisites"></a>Előfeltételek 

Az egyetlen objektum-szinkronizáló eszköz használatához a Azure AD Connect március 2021-es vagy újabb kiadását kell használnia. 

### <a name="run-the-single-object-sync-tool"></a>Az egyetlen objektum-szinkronizáló eszköz futtatása 

Az egyetlen objektum-szinkronizáló eszköz futtatásához hajtsa végre a következő lépéseket: 

 1. Nyisson meg egy új Windows PowerShell-munkamenetet a Azure AD Connect-kiszolgálón a Futtatás rendszergazdaként beállítással. 

 2. Állítsa a [végrehajtási házirendet](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy) RemoteSigned vagy nem korlátozott értékre. 

 3. Tiltsa le a szinkronizálási ütemező szolgáltatást annak ellenőrzése után, hogy nem futnak-e szinkronizálási műveletek. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. A AdSync diagnosztikai modul importálása 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. Az egyetlen objektum szinkronizálási parancsmagjának meghívása. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. Engedélyezze újra a szinkronizálási ütemező alkalmazást. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|Egyetlen objektum szinkronizálásának bemeneti paraméterei|Description| 
|-----|----|
|DistinguishedName|Ez egy kötelező karakterlánc-paraméter. </br></br>Ez a Active Directory objektum megkülönböztető neve, amely szinkronizálást és hibaelhárítást igényel.| 
|StagingMode|Ez egy opcionális kapcsoló paraméter.</br></br>Ezzel a paraméterrel megakadályozható, hogy a módosítások exportálása Azure Active Directory.</br></br>**Megjegyzés**: a parancsmag véglegesíti a szinkronizálási műveletet. </br></br>**Megjegyzés**: Azure ad Connect az előkészítési kiszolgáló nem exportálja Azure Active Directory a módosításokat.|
|NoHtmlReport|Ez egy opcionális kapcsoló paraméter.</br></br>Ezzel a paraméterrel megakadályozható a HTML-jelentés generálása. 

## <a name="single-object-sync-throttling"></a>Egyetlen objektum szinkronizálásának szabályozása 

Az egyobjektumú szinkronizáló **eszköz az** objektumok szinkronizálásával kapcsolatos problémák kivizsgálására és hibaelhárítására szolgál. **Nem** helyettesíti az ütemező által futtatott szinkronizálási ciklust. A Azure Active Directoryból való importálás és a Azure Active Directoryba való exportálás a szabályozási korlátok hatálya alá tartozik. Ha eléri a sávszélesség-szabályozási korlátot, próbálkozzon újra 5 perc múlva. 

## <a name="next-steps"></a>Következő lépések
- [Objektumok szinkronizálásának hibaelhárítása](tshoot-connect-objectsync.md)
- [Nem szinkronizált objektum – problémamegoldás](tshoot-connect-object-not-syncing.md)
- [Azure AD Connect objektumok és attribútumok végpontok közötti hibaelhárítása](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)
