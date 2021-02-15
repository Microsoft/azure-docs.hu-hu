---
title: A szinapszis általános feladatai végrehajtásához szükséges szerepkörök megismerése
description: Ez a cikk azt ismerteti, hogy az adott feladatok elvégzéséhez mely beépített szinapszis-RBAC szerepkör (ek) szükségesek
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 950a786b29a8144c4bb192fa6078e8c88d67481d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384452"
---
# <a name="understand-the-roles-required-to-perform-common-tasks-in-synapse"></a>A szinapszis általános feladatai végrehajtásához szükséges szerepkörök megismerése

Ez a cikk segít megismerni, hogy mely szinapszisok RBAC (szerepköralapú hozzáférés-vezérlés) vagy az Azure RBAC szerepköreiből kell dolgoznia a szinapszis Studióban.  

## <a name="synapse-studio-access-control-and-workflow-summary"></a>A szinapszis Studio hozzáférés-vezérlési és munkafolyamat-összegzése 

### <a name="accessing-synapse-studio-and-viewing-its-content"></a>A szinapszis studióhoz való hozzáférés és a tartalom megtekintése

- Megnyithatja a szinapszis Studio alkalmazást, megtekintheti a munkaterület részleteit, és listázhatja az Azure-erőforrások (SQL-készletek, Spark-készletek vagy integrációs modulok) listáját, ha bármely szinapszis RBAC szerepkört hozzárendelt, vagy az Azure tulajdonos, közreműködő vagy olvasó szerepkörrel rendelkezik a munkaterületen.

### <a name="resource-management"></a>Erőforrás-kezelés

- SQL-készleteket, Apache Spark készleteket és integrációs modulokat hozhat létre, ha Ön Azure-tulajdonos vagy közreműködő a munkaterületen.
- Szüneteltetheti vagy méretezheti a dedikált SQL-készletet, konfigurálhat egy Spark-készletet vagy egy integrációs modult, ha Ön Azure-tulajdonos vagy közreműködő a munkaterületen vagy az erőforráson.

### <a name="viewing-and-editing-code-artifacts"></a>A kód összetevőinek megtekintése és szerkesztése

- A szinapszis studióhoz való hozzáférés révén új kódrészleteket hozhat létre, például SQL-parancsfájlokat, jegyzetfüzeteket, Spark-feladatokat, társított szolgáltatásokat, folyamatokat, adatfolyamok, eseményindítókat és hitelesítő adatokat.  (Ezek az összetevők közzétehető vagy menthetők további engedélyekkel.)  
- Ha Ön egy szinapszis-összetevő felhasználója, a szinapszis-összetevő közzétevője, a szinapszis közreműködője vagy a szinapszis rendszergazdája, akkor a már közzétett programkódokat is listázhatja, megnyithatja és szerkesztheti.

### <a name="executing-your-code"></a>A kód végrehajtása

- SQL-parancsfájlokat az SQL-készleteken hajthat végre, ha rendelkezik az SQL-készletekben meghatározott szükséges SQL-engedélyekkel.  
- Ha a munkaterületen vagy az adott Apache Spark-készleten van, akkor futtathat jegyzetfüzeteket és Spark-feladatokat.  
- A munkaterületre vagy adott integrációs modulokra vonatkozó számítási operátori engedélyekkel, valamint a folyamatok végrehajtásához szükséges hitelesítő adatokkal.

### <a name="monitoring-and-managing-execution"></a>A végrehajtás figyelése és kezelése
- Az Apache Spark-készletekben futó jegyzetfüzetek és feladatok állapotát a szinapszis felhasználóinak tekintheti át.
- Áttekintheti a naplókat, és lemondhatja a futó feladatokat és folyamatokat, ha Ön a munkaterületen vagy egy adott Spark-készleten vagy-adatcsatornán található szinapszis számítási operátor.  

### <a name="publishing-and-saving-your-code"></a>A kód közzététele és mentése

- A szolgáltatáshoz új vagy frissített kódrészleteket tehet közzé, ha Ön egy szinapszis-összetevő közzétevője, a szinapszis közreműködője vagy a szinapszis rendszergazdája. 
- Ha a munkaterület git-kompatibilis, és git-engedélyekkel rendelkezik, akkor véglegesítheti a kód összetevőit a git-tárház egy működő ágában. Ha a git engedélyezve van, a közzététel csak az együttműködési ágban engedélyezett.
- Ha úgy zárta be a szinapszis Studiot, hogy nem tesz közzé vagy nem végez módosításokat a kódban, akkor ezek a módosítások elvesznek.


## <a name="tasks-and-required-roles"></a>Feladatok és szükséges szerepkörök

Az alábbi táblázat felsorolja az általános feladatokat, valamint az egyes feladatokhoz, a szinapszis RBAC vagy az Azure RBAC-szerepkörökhöz szükséges lépéseket.  

>[!Note]
>- A szinapszis rendszergazdája nem szerepel az egyes feladatokban, kivéve, ha ez az egyetlen olyan szerepkör, amely biztosítja a szükséges engedélyeket.  A szinapszis-rendszergazdák minden olyan feladatot elvégezhetnek, amelyet más szinapszis RBAC szerepkörei is engedélyeznek.</br>
>- Meg kell jeleníteni a minimálisan szükséges szinapszis-RBAC szerepkört.
>- Az összes hatókörben található RBAC-szerepkör a teljes munkaterületen biztosíthatja a szinapszis felhasználói engedélyeit.
>- A táblázatban látható összes szinapszis RBAC-engedély/művelet előre rögzített Microsoft/szinapszis/munkaterület/... </br>


Feladat (szeretnék...) |Szerepkör (...)|Szinapszis RBAC engedély/művelet
--|--|--
|A szinapszis Studio megnyitása munkaterületen|Szinapszis-felhasználó vagy|olvasás
| |Azure-tulajdonos, közreműködő vagy olvasó a munkaterületen|Nincs
|SQL-készletek, Apache Spark készletek, integrációs modulok listázása és a konfigurációs adatok elérése|Szinapszis-felhasználó vagy|olvasás|
||Azure-tulajdonos, közreműködő vagy olvasó a munkaterületen|Nincs
|Társított szolgáltatások, hitelesítő adatok, felügyelt magánhálózati végpontok listázása|Szinapszis-felhasználó|olvasás
SQL-KÉSZLETEK|
Dedikált SQL-készlet vagy kiszolgáló nélküli SQL-készlet létrehozása|Az Azure-tulajdonos vagy-közreműködő a munkaterületen|Nincs
Dedikált SQL-készlet kezelése (szüneteltetés, méretezés vagy törlés)|Azure-tulajdonos vagy-közreműködő az SQL-készletben vagy-munkaterületen|Nincs
SQL-parancsfájl létrehozása</br>|Szinapszis-felhasználó vagy </br>Az Azure tulajdonos vagy közreműködő a munkaterületen, </br>*SQL-parancsfájlok futtatásához, közzétételéhez vagy a módosítások elvégzéséhez további SQL-engedélyek szükségesek*.|
Bármely közzétett SQL-parancsfájl listázása és megnyitása| Szinapszis összetevő-felhasználó, összetevő-közzétevő, szinapszis közreműködő|összetevők/olvasás
SQL-parancsfájl futtatása kiszolgáló nélküli SQL-készleten|SQL-engedélyek a készleten (a szinapszis-rendszergazda számára automatikusan biztosított)|Nincs
SQL-szkript futtatása dedikált SQL-készleten|SQL-engedélyek a készleten|Nincs
Új, frissített vagy törölt SQL-szkript közzététele|Szinapszis-összetevő kiadója, szinapszis közreműködője|sqlScripts/írás, törlés
SQL-parancsfájl módosításainak végrehajtása a git-tárházban|Git-engedélyeket igényel a tárházban|
Active Directory-rendszergazda hozzárendelésének engedélyezése a munkaterületen (az Azure Portal munkaterület-tulajdonságainak használatával)|Az Azure-tulajdonos vagy-közreműködő a munkaterületen |
APACHE SPARK-KÉSZLETEK|
Apache Spark-készlet létrehozása|Az Azure-tulajdonos vagy-közreműködő a munkaterületen|
Apache Spark alkalmazások figyelése| Szinapszis-felhasználó|olvasás
A jegyzetfüzetek és a feladatok végrehajtásához tartozó naplók megtekintése |Szinapszis számítási operátor|
Apache Spark-készleten futó jegyzetfüzetek vagy Spark-feladatok megszakítása|Szinapszis számítási operátor a Apache Spark készleten.|bigDataPools/useCompute
Jegyzetfüzet vagy feladatdefiníció létrehozása|Szinapszis-felhasználó vagy </br>Azure-tulajdonos, közreműködő vagy olvasó a munkaterületen</br> *További engedélyek szükségesek a módosítások futtatásához, közzétételéhez vagy végrehajtásához.*|olvasás</br></br></br></br></br> 
Közzétett jegyzetfüzet vagy feladatdefiníció listázása és megnyitása, beleértve a mentett kimenetek áttekintését|Szinapszis-összetevő felhasználója, szinapszis összetevő-közzétevő, szinapszis közreműködő a munkaterületen|összetevők/olvasás
Jegyzetfüzet futtatása és kimenetének megtekintése, illetve Spark-feladatok elküldése|Szinapszis Apache Spark Administrator, szinapszis számítási operátor a kiválasztott Apache Spark-készleten|bigDataPools/useCompute 
Jegyzetfüzet vagy feladatdefiníció (beleértve a kimenetet is) közzététele vagy törlése a szolgáltatáshoz|Összetevő-közzétevő a munkaterületen, szinapszis Apache Spark Administrator|jegyzetfüzetek/írás, törlés
Egy jegyzetfüzet vagy feladatdefiníció módosításainak végrehajtása a git-tárházban|Git-engedélyek|Nincs
FOLYAMATOK, INTEGRÁCIÓS MODULOK, ADATFOLYAMOK, ADATKÉSZLETEK & ESEMÉNYINDÍTÓK|
Integrációs modul létrehozása, frissítése vagy törlése|Az Azure-tulajdonos vagy-közreműködő a munkaterületen|
Integrációs modul állapotának figyelése|Szinapszis-felhasználó|olvasás, folyamatok/viewOutputs
Folyamat-futtatások áttekintése|Szinapszis összetevő kiadója/szinapszis közreműködője|olvasás, folyamatok/viewOutputs 
Folyamat létrehozása |Szinapszis-felhasználó</br>*További szinapszis-engedélyek szükségesek a hibakereséshez, eseményindítók hozzáadásához, közzétételéhez vagy a módosítások elvégzéséhez*|olvasás
Adatfolyam vagy adatkészlet létrehozása |Szinapszis-felhasználó</br>*További szinapszis-engedélyek szükségesek a közzétételhez vagy a módosítások elvégzéséhez*|olvasás
Közzétett folyamat listázása és megnyitása |Szinapszis-összetevő felhasználója | összetevők/olvasás
Adatkészlet-adat előnézete|Szinapszis felhasználó + szinapszis hitelesítő felhasználó a WorkspaceSystemIdentity| 
Folyamat hibakeresése az alapértelmezett Integration Runtime használatával|Szinapszis felhasználó + szinapszis hitelesítő adatok felhasználója a WorkspaceSystemIdentity hitelesítő adatain|olvasni </br>hitelesítő adatok/useSecret
Trigger létrehozása, beleértve az triggert (a folyamat végrehajtásához szükséges engedély)|Szinapszis felhasználó + szinapszis hitelesítő felhasználó a WorkspaceSystemIdentity|olvasás, hitelesítő adatok/useSecret/művelet
Folyamat végrehajtása/futtatása|Szinapszis felhasználó + szinapszis hitelesítő felhasználó a WorkspaceSystemIdentity|olvasás, hitelesítő adatok/useSecret/művelet
Adatmásolás az Adatok másolása eszköz használatával|Szinapszis felhasználó + szinapszis hitelesítő adatok felhasználója a munkaterület rendszerének identitásán|olvasás, hitelesítő adatok/useSecret/művelet
Adatbevitel (ütemterv használatával)|Szinapszis szerző + szinapszis hitelesítő adatokkal rendelkező felhasználó a munkaterület rendszer-identitásán|olvasás, hitelesítő adatok/useSecret/művelet
Új, frissített vagy törölt folyamat, adatfolyam vagy trigger közzététele a szolgáltatásban|Szinapszis-összetevő közzétevője a munkaterületen|folyamatok/írás, törlés</br>adatfolyamok/írás, törlés</br>eseményindítók/írás, törlés
Folyamatok, adatfolyamok, adatkészletek vagy eseményindítók módosításainak elvégzése a git-tárházban |Git-engedélyek|Nincs 
TÁRSÍTOTT SZOLGÁLTATÁSOK|
Társított szolgáltatás létrehozása (beleértve a hitelesítő adatok hozzárendelését)|Szinapszis-felhasználó</br>*A társított szolgáltatás hitelesítő adatokkal való használatához, illetve a módosítások közzétételéhez vagy elkövetéséhez további engedélyek szükségesek.*|olvasás
Közzétett társított szolgáltatás listázása és megnyitása|Szinapszis-összetevő felhasználója|linkedServices/írás, törlés  
A kapcsolat tesztelése egy hitelesítő adat által védett társított szolgáltatáson|Szinapszis felhasználó + szinapszis hitelesítő felhasználó|hitelesítő adatok/useSecret/művelet|
Társított szolgáltatás közzététele|Szinapszis-összetevő közzétevője, szinapszis csatolt Data Manager|linkedServices/írás, törlés
Társított szolgáltatási definíciók elvégzése a git-tárházban|Git-engedélyek|Nincs
HOZZÁFÉRÉS-KEZELÉS|
A szinapszis RBAC szerepkör-hozzárendeléseinek áttekintése bármely hatókörben|Szinapszis-felhasználó|olvasás
A szinapszis RBAC szerepkör-hozzárendelések hozzárendelése és eltávolítása a felhasználók, csoportok és egyszerű szolgáltatások számára| A szinapszis rendszergazdája a munkaterületen vagy egy adott munkaterület-elem hatókörében|roleAssignments/írás, törlés 

>[!Note]
>A másik bérlő vendégei nem tudják áttekinteni, hozzáadni vagy módosítani a szerepkör-hozzárendeléseket, függetlenül attól, hogy milyen szerepkört rendeltek hozzájuk. 

## <a name="next-steps"></a>Következő lépések

Ismerje meg [, hogyan tekintheti át a SZINAPSZIS RBAC szerepkör-hozzárendeléseit](./how-to-review-synapse-rbac-role-assignments.md)

Ismerje meg [, hogyan kezelheti a SZINAPSZIS RBAC szerepkör-hozzárendeléseit](./how-to-manage-synapse-rbac-role-assignments.md). 
