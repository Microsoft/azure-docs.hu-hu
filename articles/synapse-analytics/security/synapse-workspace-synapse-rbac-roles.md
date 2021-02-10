---
title: Synapse RBAC-szerepkörök
description: Ez a cikk a beépített szinapszis RBAC-szerepköröket ismerteti
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 958a954d37ba1f808be4bf1806f1efd32f498a48
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007193"
---
# <a name="synapse-rbac-roles"></a>Szinapszis RBAC szerepkörei

A cikk ismerteti a beépített szinapszis RBAC-szerepköröket, az általuk biztosított engedélyeket, valamint azokat a hatóköröket, amelyeken használhatók.  

## <a name="whats-changed-since-the-preview"></a>Mi változott az előzetes verzió óta?
Az előzetes verzióban elérhető szinapszis RBAC-szerepkörökkel rendelkező felhasználók esetében az alábbi módosítások érvényesek:
- A munkaterület-adminisztrátor átnevezte a **szinapszis-rendszergazdát**
- Apache Spark a rendszergazda átnevezi a **szinapszis Apache Spark rendszergazdát** , és jogosult az összes közzétett programkód megtekintésére, beleértve az SQL-parancsfájlokat is.  Ez a szerepkör már nem ad engedélyt a munkaterület MSI használatára, amelyhez a szinapszis hitelesítő adatának felhasználói szerepköre szükséges.  Ez az engedély szükséges a folyamatok futtatásához. 
- Az SQL-rendszergazda a **SZINAPSZIS SQL Administrator** nevet kapta, és rendelkezik engedéllyel az összes közzétett kód megjelenítéséhez, beleértve a Spark-jegyzetfüzeteket és a feladatokat.  Ez a szerepkör már nem ad engedélyt a munkaterület MSI használatára, amelyhez a szinapszis hitelesítő adatának felhasználói szerepköre szükséges. Ez az engedély szükséges a folyamatok futtatásához.
- A rendszer **új, finomabb szinapszis-RBAC szerepköröket** vezet be, amelyek az egyes elemzési futtatókörnyezetek helyett a fejlesztési és üzemeltetési personák támogatására összpontosítanak.  
- A rendszer **új alacsonyabb szintű hatóköröket** vezet be több szerepkörhöz.  Ezek a hatókörök lehetővé teszik a szerepkörök meghatározott erőforrásokra vagy objektumokra való korlátozását.

>[!Note]
>Az **új SZINAPSZIS RBAC szerepkörei és alsó szintű hatóköre jelenleg előzetes verzióban érhető el**.  Javasoljuk, hogy használja ezeket az új szerepköröket és hatóköröket, amelyek teljes mértékben támogatottak, és visszajelzéseket biztosítanak a használatáról.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>Beépített szinapszis RBAC-szerepkörök és-hatókörök

A következő táblázat ismerteti azokat a beépített szerepköröket és hatóköröket, amelyeken használhatók.

>[!Note]
> A bármely hatókörben található szinapszis RBAC szerepkörrel rendelkező felhasználók automatikusan a szinapszis felhasználói szerepkörrel rendelkeznek a munkaterület hatókörében. 

|Szerepkör |Engedélyek|Hatókörök|
|---|---|-----|
|Szinapszis-rendszergazda  |Teljes szinapszis-hozzáférés a kiszolgáló nélküli SQL-készletekhez, Apache Spark készletekhez és az integrációs modulokhoz.  Magában foglalja a létrehozási, olvasási, frissítési és törlési hozzáférést az összes közzétett kód összetevőhöz.  Magában foglalja a számítási operátort, a csatolt Data Manager és a hitelesítő adatok felhasználói engedélyeit a munkaterület rendszer-identitásának hitelesítő adatain.  Magában foglalja a szinapszis RBAC szerepköreinek hozzárendelését. A szinapszis-rendszergazda mellett az Azure-tulajdonosok is hozzárendelhet szinapszis RBAC-szerepköröket. A számítási erőforrások létrehozásához, törléséhez és kezeléséhez Azure-engedélyek szükségesek. </br></br>_Az összetevők olvasása és írása </br> a Spark-tevékenységekre vonatkozó összes műveletet elvégezheti. </br> Megtekintheti a Spark Pool-naplók megtekintésével megtekintheti </br> a mentett jegyzetfüzeteket és a folyamat kimeneteit használhatja </br> a társított szolgáltatások vagy a hitelesítő adatok által tárolt titkos kulcsok SQL </br> Server nélküli végpontokhoz való kapcsolódáshoz, valamint az `db_datareader` `db_datawriter` `connect` `grant` engedélyek </br> hozzárendelését és visszavonását a jelenlegi hatókörben található RBAC szerepkörökhöz._|Munkaterület </br> Spark-készlet<br/>Integrációs modul </br>Társított szolgáltatások</br>Hitelesítő adat |
|Szinapszis Apache Spark rendszergazdája</br>|Teljes szinapszis-hozzáférés Apache Spark készletekhez.  A közzétett Spark-feladatokhoz tartozó definíciók, jegyzetfüzetek és kimenetek, valamint a könyvtárak, a társított szolgáltatások és a hitelesítő adatok elérésének létrehozása, olvasása, frissítése és törlése.  Olvasási hozzáférést tartalmaz az összes többi közzétett kód összetevőhöz. Nem tartalmazza a hitelesítő adatok használatára és a folyamatok futtatására vonatkozó engedélyt. Nem tartalmazza a hozzáférés megadását. </br></br>_A Spark-összetevőkre vonatkozó összes művelet elvégezhető </br> a Spark-tevékenységekre vonatkozó összes művelet végrehajtásakor_|Munkaterület</br>Spark-készlet|
|Szinapszis SQL-rendszergazda|Teljes szinapszis-hozzáférés a kiszolgáló nélküli SQL-készletekhez.  A közzétett SQL-parancsfájlok, hitelesítő adatok és társított szolgáltatások elérésének létrehozása, olvasása, frissítése és törlése.  Olvasási hozzáférést tartalmaz az összes többi közzétett kód összetevőhöz.  Nem tartalmazza a hitelesítő adatok használatára és a folyamatok futtatására vonatkozó engedélyt. Nem tartalmazza a hozzáférés megadását. </br></br>*Az SQL-parancsfájlok összes művelete az SQL <br/> `db_datareader` -,, `db_datawriter` `connect` -és `grant` engedélyekkel rendelkező SQL Server nélküli végpontokhoz is csatlakozhat.*|Munkaterület|
|Szinapszis közreműködője|Teljes szinapszis-hozzáférés Apache Spark készletekhez és integrációs futtatókörnyezetekhez. Magában foglalja a létrehozási, olvasási, frissítési és törlési hozzáférést az összes közzétett kód összetevőhöz és azok kimenetéhez, beleértve a hitelesítő adatokat és a társított szolgáltatásokat.  Magában foglalja a számítási operátor engedélyeit. Nem tartalmazza a hitelesítő adatok használatára és a folyamatok futtatására vonatkozó engedélyt. Nem tartalmazza a hozzáférés megadását. </br></br>_Az összetevők olvasása és írása megtekintheti </br> a mentett jegyzetfüzeteket, és a folyamat kimenete megteheti az </br> összes művelet </br> megtekintheti a Spark-készleteket_|Munkaterület </br> Spark-készlet<br/> Integrációs modul|
|Szinapszis-összetevő közzétevője|A közzétett kódrészletekhez és azok kimenetéhez való hozzáférés létrehozása, olvasása, frissítése és törlése. Nem tartalmazza a kód vagy a folyamatok futtatására, illetve a hozzáférés engedélyezésére vonatkozó engedélyt. </br></br>_A képes a közzétett összetevők olvasására és az összetevők közzétételére </br> , megtekintheti a mentett jegyzetfüzeteket, a Spark-feladatokat és a folyamat kimenetét_|Munkaterület
|Szinapszis-összetevő felhasználója|Olvasási hozzáférés a közzétett kódrészletekhez és kimenetekhez. Új összetevők hozhatók létre, de nem tehetnek közzé módosításokat, és nem futtathatnak kódokat további engedélyek nélkül.|Munkaterület
|Szinapszis számítási operátor |Spark-feladatok és jegyzetfüzetek elküldése és a naplók megtekintése.  Magában foglalja a bármely felhasználó által benyújtott Spark-feladatok megszakítását. További használati hitelesítő adatokat igényel a munkaterület rendszeridentitásához a folyamatok futtatásához, a folyamat futásának és kimenetének megtekintéséhez. </br></br>_Elküldheti és megszakíthatja a feladatokat, beleértve a mások által beküldött feladatokat </br> is, megtekinthetik a Spark-készlet_|Munkaterület</br>Spark-készlet</br>Integrációs modul|
|Szinapszis hitelesítő felhasználója|A titkok futásidejű és konfigurációs idejű használata a hitelesítő adatokban és a társított szolgáltatásokban olyan tevékenységekben, mint a folyamat futtatása. A folyamatok futtatásához ez a szerepkör szükséges, hatóköre a munkaterület rendszeridentitása. </br></br>_A hitelesítő adatok hatóköre lehetővé teszi az adatokhoz való hozzáférést egy olyan társított szolgáltatáson keresztül, amelyet a hitelesítő adatok véd (Emellett számítási használati engedélyt is igényel) </br> lehetővé teszi a munkaterület rendszeridentitási hitelesítő adatai által védett folyamatok végrehajtását (további számítási használati engedélyekkel)._|Munkaterület </br>Társított szolgáltatás</br>Hitelesítő adat
|Szinapszis csatolt Data Manager|Felügyelt privát végpontok, társított szolgáltatások és hitelesítő adatok létrehozása és kezelése. Létrehozhat olyan felügyelt magánhálózati végpontokat, amelyek hitelesítő adatokkal védett társított szolgáltatásokat használnak|Munkaterület|
|Szinapszis-felhasználó|SQL-készletek, Apache Spark készletek, integrációs modulok és közzétett társított szolgáltatások és hitelesítő adatok listázása és megtekintése. Nem tartalmaz más közzétett programkódot.  Létrehozhat új összetevőket, de nem futtathat és nem tehet közzé további engedélyek nélkül.</br></br>_Kilistázhatja és beolvashatja a Spark-készleteket, az integrációs modulokat._|Munkaterület, Spark-készlet</br>Társított szolgáltatások </br>Hitelesítő adat|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>A szinapszis RBAC szerepkörei és az általuk engedélyezett műveletek

>[!Note]
>- Az alábbi táblázatokban felsorolt összes művelet előre rögzített, "Microsoft. szinapszis/..."</br>
>- Az összes összetevő olvasási, írási és törlési művelete az élő szolgáltatásban közzétett összetevőkre vonatkozik.  Ezek az engedélyek nem érintik a csatlakoztatott git-tárházban található összetevők elérését.  

A következő táblázat felsorolja a beépített szerepköröket és az egyes támogatott műveleteket/engedélyeket.

Szerepkör|Műveletek
--|--
Szinapszis-rendszergazda|munkaterületek/olvasás</br>munkaterületek/roleAssignments/írás, törlés</br>munkaterületek/managedPrivateEndpoint/írás, törlés</br>munkaterületek/bigDataPools/useCompute/művelet</br>munkaterületek/bigDataPools/viewLogs/művelet</br>munkaterületek/integrationRuntimes/useCompute/művelet</br>munkaterületek/összetevők/olvasás</br>munkaterületek/jegyzetfüzetek/írás, törlés</br>munkaterületek/sparkJobDefinitions/írás, törlés</br>munkaterületek/sqlScripts/írás, törlés</br>munkaterületek/adatfolyamok/írás, törlés</br>munkaterületek/folyamatok/írás, törlés</br>munkaterületek/triggerek/írás, törlés</br>munkaterületek/adatkészletek/írás, törlés</br>munkaterületek/könyvtárak/írás, törlés</br>munkaterületek/linkedServices/írás, törlés</br>munkaterületek/hitelesítő adatok/írás, törlés</br>munkaterületek/jegyzetfüzetek/viewOutputs/művelet</br>munkaterületek/folyamatok/viewOutputs/művelet</br>munkaterületek/linkedServices/useSecret/művelet</br>munkaterületek/hitelesítő adatok/useSecret/művelet|
|Szinapszis Apache Spark rendszergazdája|munkaterületek/olvasás</br>munkaterületek/bigDataPools/useCompute/művelet</br>munkaterületek/bigDataPools/viewLogs/művelet</br>munkaterületek/jegyzetfüzetek/viewOutputs/művelet</br>munkaterületek/összetevők/olvasás</br>munkaterületek/jegyzetfüzetek/írás, törlés</br>munkaterületek/sparkJobDefinitions/írás, törlés</br>munkaterületek/könyvtárak/írás, törlés</br>munkaterületek/linkedServices/írás, törlés</br>munkaterületek/hitelesítő adatok/írás, törlés|
|Szinapszis SQL-rendszergazda|munkaterületek/olvasás</br>munkaterületek/összetevők/olvasás</br>munkaterületek/sqlScripts/írás, törlés</br>munkaterületek/linkedServices/írás, törlés</br>munkaterületek/hitelesítő adatok/írás, törlés|
|Szinapszis közreműködője|munkaterületek/olvasás</br>munkaterületek/bigDataPools/useCompute/művelet</br>munkaterületek/bigDataPools/viewLogs/művelet</br>munkaterületek/integrationRuntimes/useCompute/művelet</br>munkaterületek/integrationRuntimes/viewLogs/művelet</br>munkaterületek/összetevők/olvasás</br>munkaterületek/jegyzetfüzetek/írás, törlés</br>munkaterületek/sparkJobDefinitions/írás, törlés</br>munkaterületek/sqlScripts/írás, törlés</br>munkaterületek/adatfolyamok/írás, törlés</br>munkaterületek/folyamatok/írás, törlés</br>munkaterületek/triggerek/írás, törlés</br>munkaterületek/adatkészletek/írás, törlés</br>munkaterületek/könyvtárak/írás, törlés</br>munkaterületek/linkedServices/írás, törlés</br>munkaterületek/hitelesítő adatok/írás, törlés</br>munkaterületek/jegyzetfüzetek/viewOutputs/művelet</br>munkaterületek/folyamatok/viewOutputs/művelet|
|Szinapszis-összetevő közzétevője|munkaterületek/olvasás</br>munkaterületek/összetevők/olvasás</br>munkaterületek/jegyzetfüzetek/írás, törlés</br>munkaterületek/sparkJobDefinitions/írás, törlés</br>munkaterületek/sqlScripts/írás, törlés</br>munkaterületek/adatfolyamok/írás, törlés</br>munkaterületek/folyamatok/írás, törlés</br>munkaterületek/triggerek/írás, törlés</br>munkaterületek/adatkészletek/írás, törlés</br>munkaterületek/könyvtárak/írás, törlés</br>munkaterületek/linkedServices/írás, törlés</br>munkaterületek/hitelesítő adatok/írás, törlés</br>munkaterületek/jegyzetfüzetek/viewOutputs/művelet</br>munkaterületek/folyamatok/viewOutputs/művelet|
|Szinapszis-összetevő felhasználója|munkaterületek/olvasás</br>munkaterületek/összetevők/olvasás</br>munkaterületek/jegyzetfüzetek/viewOutputs/művelet</br>munkaterületek/folyamatok/viewOutputs/művelet|
|Szinapszis számítási operátor |munkaterületek/olvasás</br>munkaterületek/bigDataPools/useCompute/művelet</br>munkaterületek/bigDataPools/viewLogs/művelet</br>munkaterületek/integrationRuntimes/useCompute/művelet</br>munkaterületek/integrationRuntimes/viewLogs/művelet|
|Szinapszis hitelesítő felhasználója|munkaterületek/olvasás</br>munkaterületek/linkedServices/useSecret/művelet</br>munkaterületek/hitelesítő adatok/useSecret/művelet|
|Szinapszis csatolt Data Manager|munkaterületek/olvasás</br>munkaterületek/managedPrivateEndpoint/írás, törlés</br>munkaterületek/linkedServices/írás, törlés</br>munkaterületek/hitelesítő adatok/írás, törlés|
|Szinapszis-felhasználó|munkaterületek/olvasás|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Szinapszis RBAC-műveletek és az azokat engedélyező szerepkörök

Az alábbi táblázat felsorolja a szinapszis-műveleteket és a beépített szerepköröket, amelyek lehetővé teszik a következő műveleteket:

Művelet|Szerepkör
--|--
munkaterületek/olvasás|Szinapszis-rendszergazda</br>Szinapszis Apache Spark rendszergazdája</br>Szinapszis SQL-rendszergazda</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője</br>Szinapszis-összetevő felhasználója</br>Szinapszis számítási operátor </br>Szinapszis hitelesítő felhasználója</br>Szinapszis csatolt Data Manager</br>Szinapszis-felhasználó 
munkaterületek/roleAssignments/írás, törlés|Szinapszis-rendszergazda
munkaterületek/managedPrivateEndpoint/írás, törlés|Szinapszis-rendszergazda</br>Szinapszis csatolt Data Manager
munkaterületek/bigDataPools/useCompute/művelet|Szinapszis-rendszergazda</br>Szinapszis Apache Spark rendszergazdája</br>Szinapszis közreműködője</br>Szinapszis számítási operátor 
munkaterületek/bigDataPools/viewLogs/művelet|Szinapszis-rendszergazda</br>Szinapszis Apache Spark rendszergazdája</br>Szinapszis közreműködője</br>Szinapszis számítási operátor 
munkaterületek/integrationRuntimes/useCompute/művelet|Szinapszis-rendszergazda</br>Szinapszis közreműködője</br>Szinapszis számítási operátor 
munkaterületek/összetevők/olvasás|Szinapszis-rendszergazda</br>Szinapszis Apache Spark rendszergazdája</br>Szinapszis SQL-rendszergazda</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője</br>Szinapszis-összetevő felhasználója
munkaterületek/jegyzetfüzetek/írás, törlés|Szinapszis-rendszergazda</br>Szinapszis Apache Spark rendszergazdája</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője
munkaterületek/sparkJobDefinitions/írás, törlés|Szinapszis-rendszergazda</br>Szinapszis Apache Spark rendszergazdája</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője
munkaterületek/sqlScripts/írás, törlés|Szinapszis-rendszergazda</br>Szinapszis SQL-rendszergazda</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője
munkaterületek/adatfolyamok/írás, törlés|Szinapszis-rendszergazda</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője
munkaterületek/folyamatok/írás, törlés|Szinapszis-rendszergazda</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője
munkaterületek/triggerek/írás, törlés|Szinapszis-rendszergazda</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője
munkaterületek/adatkészletek/írás, törlés|Szinapszis-rendszergazda</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője
munkaterületek/könyvtárak/írás, törlés|Szinapszis-rendszergazda</br>Szinapszis Apache Spark rendszergazdája</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője
munkaterületek/linkedServices/írás, törlés|Szinapszis-rendszergazda</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője</br>Szinapszis csatolt Data Manager
munkaterületek/hitelesítő adatok/írás, törlés|Szinapszis-rendszergazda</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője</br>Szinapszis csatolt Data Manager
munkaterületek/jegyzetfüzetek/viewOutputs/művelet|Szinapszis-rendszergazda</br>Szinapszis Apache Spark rendszergazdája</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője</br>Szinapszis-összetevő felhasználója
munkaterületek/folyamatok/viewOutputs/művelet|Szinapszis-rendszergazda</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője</br>Szinapszis-összetevő felhasználója
munkaterületek/linkedServices/useSecret/művelet|Szinapszis-rendszergazda</br>Szinapszis hitelesítő felhasználója
munkaterületek/hitelesítő adatok/useSecret/művelet|Szinapszis-rendszergazda</br>Szinapszis hitelesítő felhasználója

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Szinapszis RBAC-hatókörök és azok támogatott szerepkörei

Az alábbi táblázat felsorolja a szinapszis RBAC-hatóköröket és az egyes hatókörökhöz hozzárendelhető szerepköröket. 

>[!note]
>Egy objektum létrehozásához vagy törléséhez magasabb szintű hatókörű engedélyekkel kell rendelkeznie.

Hatókör|Szerepkörök
--|--
Munkaterület |Szinapszis-rendszergazda</br>Szinapszis Apache Spark rendszergazdája</br>Szinapszis SQL-rendszergazda</br>Szinapszis közreműködője</br>Szinapszis-összetevő közzétevője</br>Szinapszis-összetevő felhasználója</br>Szinapszis számítási operátor </br>Szinapszis hitelesítő felhasználója</br>Szinapszis csatolt Data Manager</br>Szinapszis-felhasználó
Apache Spark készlet | Szinapszis-rendszergazda </br>Szinapszis közreműködője </br> Szinapszis számítási operátor
Integrációs modul | Szinapszis-rendszergazda </br>Szinapszis közreműködője </br> Szinapszis számítási operátor
Társított szolgáltatások |Szinapszis-rendszergazda </br>Szinapszis hitelesítő felhasználója
Hitelesítő adat |Szinapszis-rendszergazda </br>Szinapszis hitelesítő felhasználója
 
>[!note]
>Az összes összetevő-szerepkör és művelet hatóköre a munkaterület szintjén történik. 

## <a name="next-steps"></a>Következő lépések

Ismerje meg [, hogyan tekintheti át a munkaterületek szinapszis-RBAC szerepkör-hozzárendeléseit](./how-to-review-synapse-rbac-role-assignments.md) .

Tudnivalók [a SZINAPSZIS RBAC szerepköreinek hozzárendeléséről](./how-to-manage-synapse-rbac-role-assignments.md)
