---
title: Erőforrás-elnevezési korlátozások
description: Az Azure-erőforrások elnevezési szabályait és korlátozásait mutatja be.
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: dbe19dae4509e1dd6aa95763e4d034c339bb0531
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220853"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Az Azure-erőforrásokra vonatkozó elnevezési szabályok és korlátozások

Ez a cikk az Azure-erőforrások elnevezési szabályait és korlátozásait foglalja össze. Az erőforrások elnevezésével kapcsolatos javaslatokért lásd: [ajánlott elnevezési és címkézési konvenciók](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

Ez a cikk erőforrás-szolgáltatói névtér alapján sorolja fel az erőforrásokat. Az erőforrás-szolgáltatók Azure-szolgáltatásokkal való egyezésének listáját az [Azure-szolgáltatások erőforrás-szolgáltatói](azure-services-resource-providers.md)című témakörében tekintheti meg.

Az erőforrásnevek nem megkülönböztetik a kis-és nagybetűket, kivéve, ha kifejezetten az érvényes karaktereket tartalmazó oszlopban vannak feltüntetve.

Az alábbi táblázatokban az alfanumerikus kifejezés a következőre hivatkozik:

* **a** – **z** (kisbetűs)
* **A** – **Z** (nagybetűs)
* **0** – **9** (szám)

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | kiszolgálók | erőforráscsoport | 3-63 | Kisbetűk és számok.<br><br>Kezdje kisbetűvel. |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | szolgáltatás | globális | 1-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje a betűvel, és az alfanumerikus karakterrel végződik. |
> | szolgáltatás/API-k | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/API-k/problémák | api | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/API-k/problémák/mellékletek | issue | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/API-k/problémák/megjegyzések | issue | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/API-k/műveletek | api | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/API-k/műveletek/Címkék | művelet | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/API-k/kiadások | api | 1–80 | Alfanumerikus karakterek, aláhúzások és kötőjelek.<br><br>Kezdő és záró alfanumerikus vagy aláhúzással. |
> | szolgáltatás/API-k/sémák | api | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/API-k/tagDescriptions | api | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/API-k/Címkék | api | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/API-Version-sets | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/authorizationServers | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/háttérrendszer | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/tanúsítványok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/diagnosztika | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/csoportok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/csoportok/felhasználók | csoport | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/identityProviders | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/naplózók | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/értesítések | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/értesítések/recipientEmails | értesítés | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/openidConnectProviders | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/házirendek | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/termékek | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/termékek/API-k | product | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/termékek/csoportok | product | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/termékek/Címkék | product | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/tulajdonságok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/előfizetések | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/Címkék | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/sablonok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/felhasználók | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | configurationStores | erőforráscsoport | 5-50 | Alfanumerikus karakterek, aláhúzások és kötőjelek. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | zárak | hozzárendelés hatóköre | 1–90 | Alfanumerikus karakterek, időszakok, aláhúzások, kötőjelek és zárójelek.<br><br>Nem végződhet ponttal. |
> | policyAssignments | hozzárendelés hatóköre | 1-128 megjelenítendő név<br><br>1-64 erőforrás neve<br><br>1-24 erőforrás neve a felügyeleti csoport hatókörében | A megjelenítendő név bármilyen karaktert tartalmazhat.<br><br>Az erőforrás neve nem tartalmazhat `%` , és nem végződhet ponttal vagy szóközzel. |
> | policyDefinitions | definíció hatóköre | 1-128 megjelenítendő név<br><br>1-64 erőforrás neve | A megjelenítendő név bármilyen karaktert tartalmazhat.<br><br>Az erőforrás neve nem tartalmazhat `%` , és nem végződhet ponttal vagy szóközzel. |
> | policySetDefinitions | definíció hatóköre | 1-128 megjelenítendő név<br><br>1-64 erőforrás neve<br><br>1-24 erőforrás neve a felügyeleti csoport hatókörében | A megjelenítendő név bármilyen karaktert tartalmazhat.<br><br>Az erőforrás neve nem tartalmazhat `%` , és nem végződhet ponttal vagy szóközzel.  |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | automationAccounts | erőforráscsoport & régiója <br>(Lásd az alábbi megjegyzést) | 6-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje a betűvel, és az alfanumerikus karakterrel végződik. |
> | automationAccounts/tanúsítványok | Automation-fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem végződhet szóközzel.  |
> | automationAccounts/kapcsolatok | Automation-fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem végződhet szóközzel. |
> | automationAccounts/hitelesítő adatok | Automation-fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem végződhet szóközzel. |
> | automationAccounts/runbookok | Automation-fiók | 1–63 | Alfanumerikus karakterek, aláhúzások és kötőjelek.<br><br>Kezdje a betűvel.  |
> | automationAccounts/ütemtervek | Automation-fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem végződhet szóközzel. |
> | automationAccounts/változók | Automation-fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem végződhet szóközzel. |
> | automationAccounts/figyelők | Automation-fiók | 1–63 |  Alfanumerikus karakterek, aláhúzások és kötőjelek.<br><br>Kezdje a betűvel. |
> | automationAccounts/webhookok | Automation-fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem végződhet szóközzel. |

> [!NOTE]
> Az Automation-fiókok nevei régiónként és erőforráscsoporthoz egyediek. Előfordulhat, hogy a törölt Automation-fiókok neve nem érhető el azonnal.

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | batchAccounts | Régió | 3–24 | Kisbetűk és számok. |
> | batchAccounts/alkalmazások | batch-fiók | 1-64 | Alfanumerikus karakterek, aláhúzások és kötőjelek. |
> | batchAccounts/tanúsítványok | batch-fiók | 5-45 | Alfanumerikus karakterek, aláhúzások és kötőjelek. |
> | batchAccounts/készletek | batch-fiók | 1-64 | Alfanumerikus karakterek, aláhúzások és kötőjelek. |

## <a name="microsoftblockchain"></a>Microsoft. Blockchain

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | blockchainMembers | globális | 2-20 | Kisbetűk és számok.<br><br>Kezdje kisbetűvel. |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | botServices | globális | 2–64 |  Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. |
> | botServices/csatornák | bot Service | 2–64 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. |
> | botServices/kapcsolatok | bot Service | 2–64 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. |
> | enterpriseChannels | erőforráscsoport | 2–64 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Redis | globális | 1–63 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdő és záró alfanumerikus karakterek. Egymást követő kötőjelek nem engedélyezettek. |
> | Redis/firewallRules | Redis | 1-256 | Alfanumerikus karaktereket |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | profilok | erőforráscsoport | 1-260 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | profilok/végpontok | globális | 1-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Tanúsítványrendelések | erőforráscsoport | 3-30 | Alfanumerikus karaktereket. |

## <a name="microsoftcognitiveservices"></a>Microsoft. CognitiveServices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fiókok | erőforráscsoport | 2–64 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | availabilitySets | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Az alfanumerikus vagy aláhúzás karakterrel végződik. |
> | diskEncryptionSets | erőforráscsoport | 1–80 | Alfanumerikus karakterek és aláhúzások. |
> | lemezek | erőforráscsoport | 1–80 | Alfanumerikus karakterek és aláhúzások. |
> | katalógusok | erőforráscsoport | 1–80 | Alfanumerikus karakterek és időszakok.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | galériák/alkalmazások | katalógus | 1–80 | Alfanumerikus karakterek, kötőjelek és időszakok.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | galériák/alkalmazások/verziók | alkalmazás | 32 bites egész szám | Számok és időszakok. |
> | galériák/lemezképek | katalógus | 1–80 | Alfanumerikus karakterek, aláhúzások, kötőjelek és időszakok.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | galériák/lemezképek/verziók | image | 32 bites egész szám | Számok és időszakok. |
> | images | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Az alfanumerikus vagy aláhúzás karakterrel végződik. |
> | pillanatképek | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Az alfanumerikus vagy aláhúzás karakterrel végződik. |
> | virtualMachines | erőforráscsoport | 1-15 (Windows)<br>1-64 (Linux)<br><br>Lásd az alábbi megjegyzést. | A szóköz vagy a következő karakterek nem használhatók:<br> `\/"'[]:|<>+=;,?*@&_`<br><br>A Windows rendszerű virtuális gépek nem tartalmazhatnak pontot, és nem állhatnak kötőjeltel.<br><br>A Linux rendszerű virtuális gépek nem végződhet ponttal vagy kötőjeltel. |
> | virtualMachineScaleSets | erőforráscsoport | 1-15 (Windows)<br>1-64 (Linux)<br><br>Lásd az alábbi megjegyzést. | A szóköz vagy a következő karakterek nem használhatók:<br> `\/"'[]:|<>+=;,?*@&`<br><br>Nem kezdődhet aláhúzással. Nem végződhet ponttal vagy kötőjeltel. |

> [!NOTE]
> Az Azure-beli virtuális gépek két különböző névvel rendelkeznek: az erőforrás neve és az állomásnév. Amikor létrehoz egy virtuális gépet a portálon, ugyanazt az értéket használja mindkét névhez. Az előző táblázatban szereplő korlátozások az állomásnévre vonatkoznak. A tényleges erőforrásnév legfeljebb 64 karakterből állhat.

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | containerGroups | erőforráscsoport | 1–63 | Kisbetűk, számok és kötőjelek.<br><br>A kötőjel nem kezdődhet vagy végződhet. Szomszédos kötőjelek használata nem megengedett. |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | kibocsátásiegység | globális | 5-50 | Alfanumerikus karaktereket. |
> | kibocsátásiegység-forgalmi jegyzékek/buildTasks | registry | 5-50 | Alfanumerikus karaktereket. |
> | kibocsátásiegység-forgalmi jegyzékek/buildTasks/lépések | felépítési feladat | 5-50 | Alfanumerikus karaktereket. |
> | kibocsátásiegység-forgalmi jegyzékek/replikálások | registry | 5-50 | Alfanumerikus karaktereket. |
> | kibocsátásiegység-forgalmi jegyzékek/scopeMaps | registry | 5-50 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | kibocsátásiegység-forgalmi jegyzékek/feladatok | registry | 5-50 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | kibocsátásiegység-forgalmi jegyzékek/jogkivonatok | registry | 5-50 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | kibocsátásiegység-forgalmi jegyzékek/webhookok | registry | 5-50 | Alfanumerikus karaktereket. |

## <a name="microsoftcontainerservice"></a>Microsoft. Tárolószolgáltatás

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | managedClusters | erőforráscsoport | 1–63 | Alfanumerikus karakterek, aláhúzások és kötőjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | openShiftManagedClusters | erőforráscsoport | 1-30 | Alfanumerikus karaktereket. |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Hubs | erőforráscsoport | 1-64 | Alfanumerikus karaktereket.<br><br>Kezdje a betűvel.  |
> | hubok/Applyallocationpolicy | hub | 1-50 | Alfanumerikus karakterek, aláhúzások és időszakok.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | hubok és összekötők | hub | 1-128 | Alfanumerikus karakterek és aláhúzások.<br><br>Kezdje a betűvel. |
> | hubok/összekötők/leképezések | összekötő | 1-128 | Alfanumerikus karakterek és aláhúzások.<br><br>Kezdje a betűvel. |
> | hubok/interakciók | hub | 1-128 | Alfanumerikus karakterek és aláhúzások.<br><br>Kezdje a betűvel. |
> | hubok/KPI | hub | 1-512 | Alfanumerikus karakterek és aláhúzások.<br><br>Kezdje a betűvel. |
> | hubok/hivatkozások | hub | 1-512 | Alfanumerikus karakterek és aláhúzások.<br><br>Kezdje a betűvel. |
> | hubok/előrejelzések | hub | 1-512 | Alfanumerikus karakterek és aláhúzások.<br><br>Kezdje a betűvel. |
> | hubok/profilok | hub | 1-128 | Alfanumerikus karakterek és aláhúzások.<br><br>Kezdje a betűvel. |
> | hubok/relationshipLinks | hub | 1-512 | Alfanumerikus karakterek és aláhúzások.<br><br>Kezdje a betűvel. |
> | hubok/kapcsolatok | hub | 1-512 | Alfanumerikus karakterek és aláhúzások.<br><br>Kezdje a betűvel. |
> | hubok/roleAssignments | hub | 1-128 | Alfanumerikus karakterek és aláhúzások.<br><br>Kezdje a betűvel. |
> | hubok/nézetek | hub | 1-512 | Alfanumerikus karakterek és aláhúzások.<br><br>Kezdje a betűvel. |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | szövetségek | erőforráscsoport | 1-180 | Nem használható:<br>`%&\\?/`<br><br>Nem végződhet ponttal vagy szóközzel. |
> | resourceProviders | erőforráscsoport | 3-64 | Nem használható:<br>`%&\\?/`<br><br>Nem végződhet ponttal vagy szóközzel. |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | feladatok | erőforráscsoport | 3–24 | Alfanumerikus karakterek, kötőjelek, aláhúzások és időszakok. |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | munkaterületek | erőforráscsoport | 3-30 | Alfanumerikus karakterek, aláhúzások és kötőjelek |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | előállítók | globális | 3-63 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | gyárak/adatfolyamok | gyári | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdje alfanumerikusan. |
> | gyárak/adatkészletek | gyári | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdje alfanumerikusan. |
> | gyárak/integrationRuntimes | gyári | 3-63 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | gyárak/linkedservices | gyári | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdje alfanumerikusan. |
> | gyárak/folyamatok | gyári | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdje alfanumerikusan. |
> | gyárak/eseményindítók | gyári | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdje alfanumerikusan. |
> | gyárak/triggerek/rerunTriggers | eseményindító | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdje alfanumerikusan. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fiókok | globális | 3–24 | Kisbetűk és számok. |
> | fiókok/computePolicies | account | 3-60 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | fiókok/dataLakeStoreAccounts | account | 3–24 | Kisbetűk és számok. |
> | fiókok/firewallRules | account | 3-50 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | fiókok/storageAccounts | account | 3-60 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |

## <a name="microsoftdatalakestore"></a>Microsoft. Data Lake Store

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fiókok | globális | 3–24 | Kisbetűk és számok. |
> | fiókok/firewallRules | account | 3-50 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | fiókok/virtualNetworkRules | account | 3-50 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |

## <a name="microsoftdatamigration"></a>Microsoft. DataMigration

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | services | erőforráscsoport | 2-62 | Alfanumerikus karakterek, kötőjelek, időszakok és aláhúzások.<br><br>Kezdje alfanumerikusan. |
> | szolgáltatások/projektek | szolgáltatás | 2-57 | Alfanumerikus karakterek, kötőjelek, időszakok és aláhúzások.<br><br>Kezdje alfanumerikusan. |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | kiszolgálók | globális | 3-63 | Kisbetűs karakterek, kötőjelek és számok.<br><br>A kötőjel nem kezdődhet vagy végződhet. |
> | kiszolgálók/adatbázisok | kiszolgálók | 1–63 | Alfanumerikus karakterek és kötőjelek. |
> | kiszolgálók/firewallRules | kiszolgálók | 1-128 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | kiszolgálók/virtualNetworkRules | kiszolgálók | 1-128 | Alfanumerikus karakterek és kötőjelek. |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | kiszolgálók | globális | 3-63 | Kisbetűs karakterek, kötőjelek és számok.<br><br>A kötőjel nem kezdődhet vagy végződhet. |
> | kiszolgálók/adatbázisok | kiszolgálók | 1–63 | Alfanumerikus karakterek és kötőjelek. |
> | kiszolgálók/firewallRules | kiszolgálók | 1-128 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | kiszolgálók/virtualNetworkRules | kiszolgálók | 1-128 | Alfanumerikus karakterek és kötőjelek. |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | kiszolgálók | globális | 3-63 | Kisbetűs karakterek, kötőjelek és számok.<br><br>A kötőjel nem kezdődhet vagy végződhet. |
> | kiszolgálók/adatbázisok | kiszolgálók | 1–63 | Alfanumerikus karakterek és kötőjelek. |
> | kiszolgálók/firewallRules | kiszolgálók | 1-128 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | kiszolgálók/virtualNetworkRules | kiszolgálók | 1-128 | Alfanumerikus karakterek és kötőjelek. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | IotHubs | globális | 3-50 | Alfanumerikus karakterek és kötőjelek.<br><br>A kötőjel nem végződhet. |
> | IotHubs/tanúsítványok | IoT hub | 1-64 | Alfanumerikus karakterek, kötőjelek, időszakok és aláhúzások. |
> | IotHubs/eventHubEndpoints/ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumerikus karakterek, kötőjelek, időszakok és aláhúzások. |
> | provisioningServices | erőforráscsoport | 3-64 | Alfanumerikus karakterek és kötőjelek.<br><br>Alfanumerikus karakterrel végződik. |
> | provisioningServices/tanúsítványok | provisioningServices | 1-64 | Alfanumerikus karakterek, kötőjelek, időszakok és aláhúzások. |

## <a name="microsoftdevtestlab"></a>Microsoft. segédösszetevője

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Labs | erőforráscsoport | 1-50 | Alfanumerikus karakterek, aláhúzások és kötőjelek. |
> | Labor/customimages | Lab | 1–80 | Alfanumerikus karakterek, aláhúzások, kötőjelek és zárójelek. |
> | laborok/képletek | Lab | 1–80 | Alfanumerikus karakterek, aláhúzások, kötőjelek és zárójelek. |
> | Labor/virtualmachines | Lab | 1-15 (Windows)<br>1-64 (Linux) | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdő és záró alfanumerikus karakterek. Nem lehet az összes szám. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | databaseAccounts | globális | 3-44 | Kisbetűk, számok és kötőjelek.<br><br>Kezdés kisbetűs betűvel vagy számmal. |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | tartományok | erőforráscsoport | 3-50 | Alfanumerikus karakterek és kötőjelek. |
> | tartományok/témakörök | domain | 3-50 | Alfanumerikus karakterek és kötőjelek. |
> | eventSubscriptions | erőforráscsoport | 3-64 | Alfanumerikus karakterek és kötőjelek. |
> | témakörök | erőforráscsoport | 3-50 | Alfanumerikus karakterek és kötőjelek. |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fürtök | erőforráscsoport | 6-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje a betűvel. Betűvel vagy számmal végződik. |
> | névterek | globális | 6-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje a betűvel. Betűvel vagy számmal végződik. |
> | névterek/engedélyezési szabályok | névtér | 1-50 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdő és záró betűvel vagy számmal. |
> | névterek/disasterRecoveryConfigs | névtér | 1-50 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdő és záró betűvel vagy számmal. |
> | névterek/eventhubs | névtér | 1-50 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdő és záró betűvel vagy számmal. |
> | névterek/eventhubs/engedélyezési szabályok | Event hub | 1-50 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdő és záró betűvel vagy számmal. |
> | névterek/eventhubs/consumergroups | Event hub | 1-50 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdő és záró betűvel vagy számmal. |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fürtök | globális | 3-59 | Alfanumerikus karakterek és kötőjelek<br><br>Kezdő és záró betűvel vagy számmal. |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | feladatok | erőforráscsoport | 2–64 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje a betűvel. |

## <a name="microsoftinsights"></a>Microsoft. bepillantások

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | actionGroups | erőforráscsoport | 1-260 | Nem használható:<br>`/&%\?` <br><br>Nem végződhet szóközzel vagy ponttal.  |
> | összetevők | erőforráscsoport | 1-260 | Nem használható:<br>`%&\?/` <br><br>Nem végződhet szóközzel vagy ponttal.  |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | IoTApps | globális | 2-63 | Kisbetűk, számok és kötőjelek.<br><br>Kezdés kisbetűs betűvel vagy számmal. |

## <a name="microsoftkeyvault"></a>Microsoft. kulcstartó

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | boltívek | globális | 3–24 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje a betűvel. Betűvel vagy számmal végződik. Egymást követő kötőjelek nem szerepelhetnek. |
> | tárolók/titkok | Tároló | 1-127 | Alfanumerikus karakterek és kötőjelek. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fürtök | globális | 4-22 | Kisbetűk és számok.<br><br>Kezdje a betűvel. |
> | /Clusters/adatbázisok | cluster | 1-260 | Alfanumerikus karakterek, kötőjelek, szóközök és időszakok. |
> | /Clusters/adatbázisok/dataConnections | adatbázis | 1-40 | Alfanumerikus karakterek, kötőjelek, szóközök és időszakok. |
> | /Clusters/adatbázisok/eventhubconnections | adatbázis | 1-40 | Alfanumerikus karakterek, kötőjelek, szóközök és időszakok. |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | integrationAccounts | erőforráscsoport | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzások, időszakok és zárójelek. |
> | integrationAccounts/szerelvények | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzások, időszakok és zárójelek. |
> | integrationAccounts / batchConfigurations | integrációs fiók | 1-20 | Alfanumerikus karaktereket. |
> | integrationAccounts/tanúsítványok | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzások, időszakok és zárójelek. |
> | integrationAccounts/Maps | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzások, időszakok és zárójelek. |
> | integrationAccounts/partnerek | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzások, időszakok és zárójelek. |
> | integrationAccounts / rosettanetprocessconfigurations | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzások, időszakok és zárójelek. |
> | integrationAccounts/sémák | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzások, időszakok és zárójelek. |
> | integrationAccounts/munkamenetek | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzások, időszakok és zárójelek. |
> | integrationServiceEnvironments | erőforráscsoport | 1–80 | Alfanumerikus karakterek, kötőjelek, időszakok és aláhúzások. |
> | integrationServiceEnvironments/król | integrációs szolgáltatási környezet | 1–80 | Alfanumerikus karakterek, kötőjelek, időszakok és aláhúzások. |
> | munkafolyamatok | erőforráscsoport | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzások, időszakok és zárójelek. |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | commitmentPlans | erőforráscsoport | 1-260 | Nem használható:<br>`<>*%&:?+/\\`<br><br>Nem végződhet szóközzel. |
> | webServices | erőforráscsoport | 1-260 | Nem használható:<br>`<>*%&:?+/\\`<br><br>Nem végződhet szóközzel. |
> | munkaterületek | erőforráscsoport | 1-260 | Nem használható:<br>`<>*%&:?+/\\`<br><br>Nem végződhet szóközzel. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | munkaterületek | erőforráscsoport | 3-33 | Alfanumerikus karakterek és kötőjelek. |
> | munkaterületek/számítások | munkaterület | 2–16 | Alfanumerikus karakterek és kötőjelek. |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | userAssignedIdentities | erőforráscsoport | 3-128 | Alfanumerikus karakterek, kötőjelek és aláhúzások<br><br>Kezdje betűvel vagy számmal. |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fiókok | erőforráscsoport | 1-98 (az erőforráscsoport neve és a fiók neve) | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Mediaservices | erőforráscsoport | 3–24 | Kisbetűk és számok. |
> | Mediaservices/liveEvents | Media Service | 1-32 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje alfanumerikusan. |
> | Mediaservices/liveEvents/liveOutputs | Élő esemény | 1-256 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje alfanumerikusan. |
> | Mediaservices/streamingEndpoints | Media Service | 1-24 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje alfanumerikusan. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | applicationGateways | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | applicationSecurityGroups | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | azureFirewalls | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Az alfanumerikus vagy aláhúzás karakterrel végződik. |
> | bastionHosts | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | kapcsolatok | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | dnsZones | erőforráscsoport | 1-63 karakter<br><br>2 – 34 felirat<br><br>Minden címke egy ponttal elválasztott karakterből áll. Például a **contoso.com** 2 címkével rendelkezik. | Az egyes címkék alfanumerikus karaktereket, aláhúzásokat és kötőjeleket tartalmazhatnak.<br><br>Mindegyik címkét egy pont választja el egymástól. |
> | expressRouteCircuits | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | firewallPolicies | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | firewallPolicies / ruleGroups | tűzfal házirendje | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | frontDoors | globális | 5-64 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | frontdoorWebApplicationFirewallPolicies | erőforráscsoport | 1-128 | Alfanumerikus karaktereket.<br><br>Kezdje a betűvel. |
> | loadBalancers | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | loadBalancers/inboundNatRules | terheléselosztó | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | localNetworkGateways | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | networkInterfaces | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | networkSecurityGroups | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | networkSecurityGroups/securityRules | hálózati biztonsági csoport | 1–80 |  Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | networkWatchers | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | privateDnsZones | erőforráscsoport | 1-63 karakter<br><br>2 – 34 felirat<br><br>Minden címke egy ponttal elválasztott karakterből áll. Például a **contoso.com** 2 címkével rendelkezik. | Az egyes címkék alfanumerikus karaktereket, aláhúzásokat és kötőjeleket tartalmazhatnak.<br><br>Mindegyik címkét egy pont választja el egymástól. |
> | privateDnsZones / virtualNetworkLinks | privát DNS-zóna | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | Nyilvános IP | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | publicIPPrefixes | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | routeFilters | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | routeFilters / routeFilterRules | útvonal szűrője | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | routeTables | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | routeTables/útvonalak | útvonaltábla | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | serviceEndpointPolicies | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | trafficmanagerprofiles | globális | 1–63 | Alfanumerikus karakterek, kötőjelek és időszakok.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | virtualNetworkGateways | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | virtualNetworks | erőforráscsoport | 2–64 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | virtualnetworks/alhálózatok | virtuális hálózat | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | virtualNetworks/virtualNetworkPeerings | virtuális hálózat | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | virtualWans | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | vpnGateways | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | vpnGateways/VPN | VPN-átjáró | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |
> | vpnSites | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzások, időszakok és kötőjelek.<br><br>Kezdje alfanumerikusan. Záró alfanumerikus vagy aláhúzás. |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | névterek | globális | 6-50 | Alfanumerikus karakterek és kötőjelek<br><br>Kezdje a betűvel. Alfanumerikus karakterrel végződik. |
> | névterek/engedélyezési szabályok | névtér | 1-256 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Alfanumerikus karakterek indítása |
> | névterek/notificationHubs | névtér | 1-260 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Alfanumerikus karakterek indítása |
> | névterek/notificationHubs/engedélyezési szabályok | értesítési központ | 1-256 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Alfanumerikus karakterek indítása |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fürtök | erőforráscsoport | 4-63 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | munkaterületek | globális | 4-63 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | megoldások | munkaterület | N/A | A Microsoft által létrehozott megoldások esetében a névnek a következő mintában kell szerepelnie:<br>`SolutionType(WorkspaceName)`<br><br>Harmadik felek által létrehozott megoldások esetében a névnek a következő mintában kell lennie:<br>`SolutionType[WorkspaceName]`<br><br>Egy érvényes név például a következő:<br>`AntiMalware(contoso-IT)`<br><br>A megoldás típusa megkülönbözteti a kis-és nagybetűket. |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | irányítópultok | erőforráscsoport | 3-160 | Alfanumerikus karakterek és kötőjelek.<br><br>Ha korlátozott karaktereket szeretne használni, vegyen fel egy **Hidden-title** nevű címkét a használni kívánt irányítópult-névvel. A portál ezt a nevet jeleníti meg az irányítópult megjelenítésekor. |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | workspaceCollections | régió | 3-63 | Alfanumerikus karakterek és kötőjelek.<br><br>Nem kezdődhet kötőjeltel. Egymást követő kötőjelek nem használhatók. |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | kapacitások | régió | 3-63 | Kisbetűk vagy számok<br><br>Kezdje kisbetűvel. |

## <a name="microsoftrecoveryservices"></a>Microsoft. Recoveryservices szolgáltatónál

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | boltívek | erőforráscsoport | 2-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje a betűvel. |
> | tárolók/backupPolicies | tár | 3-150 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje a betűvel. A kötőjel nem végződhet. |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | névterek | globális | 6-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. Betűvel vagy számmal végződik. |
> | névterek/engedélyezési szabályok | névtér | 1-50 |  Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | névterek/HybridConnections | névtér | 1-260 | Alfanumerikus karakterek, időszakok, kötőjelek, aláhúzások és perjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | névterek/HybridConnections/engedélyezési szabályok | hibrid kapcsolatok | 1-50 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | névterek/WcfRelays | névtér | 1-260 | Alfanumerikus karakterek, időszakok, kötőjelek, aláhúzások és perjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | névterek/WcfRelays/engedélyezési szabályok | WCF-továbbító | 1-50 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdő és záró alfanumerikus karakterek. |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | központi telepítések | erőforráscsoport | 1-64 | Alfanumerikus karakterek, aláhúzások, zárójelek, kötőjelek és időszakok. |
> | resourcegroups | előfizetést | 1–90 | Alfanumerikus karakterek, aláhúzások, zárójelek, kötőjelek, időszakok és Unicode-karakterek, amelyek megfelelnek a [regex dokumentációjának](/rest/api/resources/resourcegroups/createorupdate).<br><br>Nem végződhet ponttal. |
> | tagNames | erőforrás | 1-512 | Nem használható:<br>`<>%&\?/` |
> | tagNames / tagValues | címke neve | 1-256 | Minden karakter. |
> | templateSpecs | erőforráscsoport | 1–90 | Alfanumerikus karakterek, aláhúzások, zárójelek, kötőjelek és időszakok. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | névterek | globális | 6-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. Betűvel vagy számmal végződik.<br><br>További információt a [névtér létrehozása](/rest/api/servicebus/create-namespace)című témakörben talál. |
> | névterek/engedélyezési szabályok | névtér | 1-50 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdés és Befejezés a alphnumeric. |
> | névterek/disasterRecoveryConfigs | globális | 6-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje a betűvel. Alfanumerikus karakterrel végződik. |
> | névterek/migrationConfigurations | névtér |  | Mindig **$default** kell lennie. |
> | névterek/várólisták | névtér | 1-260 | Alfanumerikus karakterek, időszakok, kötőjelek, aláhúzások és perjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | névterek/várólisták/engedélyezési szabályok | üzenetsor | 1-50 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdés és Befejezés a alphnumeric. |
> | névterek/témakörök | névtér | 1-260 | Alfanumerikus karakterek, időszakok, kötőjelek, aláhúzások és perjelek.<br><br>Kezdő és záró alfanumerikus karakterek. |
> | névterek/témakörök/engedélyezési szabályok | témakör | 1-50 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdés és Befejezés a alphnumeric. |
> | névterek/témakörök/előfizetések | témakör | 1-50 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdés és Befejezés a alphnumeric. |
> | névterek/témakörök/előfizetések/szabályok | előfizetést | 1-50 | Alfanumerikus karakterek, időszakok, kötőjelek és aláhúzások.<br><br>Kezdés és Befejezés a alphnumeric. |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fürtök | régió | 4-23 | Kisbetűk, számok és kötőjelek.<br><br>Kezdje kisbetűvel. Kisbetűs betűvel vagy számmal végződik. |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | signalR | globális | 3-63 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje a betűvel. Betűvel vagy számmal végződik.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | managedInstances | globális | 1–63 | Kisbetűk, számok és kötőjelek.<br><br>A kötőjel nem kezdődhet vagy végződhet. <br><br> Nem lehet speciális karakter, például: `@` . |
> | kiszolgálók | globális | 1–63 | Kisbetűk, számok és kötőjelek.<br><br>A kötőjel nem kezdődhet vagy végződhet. |
> | kiszolgálók/rendszergazdák | kiszolgáló |  | Kell lennie `ActiveDirectory` . |
> | kiszolgálók/adatbázisok | kiszolgáló | 1-128 | Nem használható:<br>`<>*%&:\/?`<br><br>Nem végződhet ponttal vagy szóközzel. |
> | kiszolgálók/adatbázisok/syncGroups | adatbázis | 1-150 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | kiszolgálók/elasticPools | kiszolgáló | 1-128 | Nem használható:<br>`<>*%&:\/?`<br><br>Nem végződhet ponttal vagy szóközzel. |
> | kiszolgálók/failoverGroups | globális | 1–63 | Kisbetűk, számok és kötőjelek.<br><br>A kötőjel nem kezdődhet vagy végződhet. |
> | kiszolgálók/firewallRules | kiszolgáló | 1-128 | Nem használható:<br>`<>*%&:;\/?`<br><br>Nem végződhet ponttal. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | storageAccounts | globális | 3–24 | Kisbetűk és számok. |
> | storageAccounts/blobServices | tárfiók |  | Kell lennie `default` . |
> | storageAccounts/blobServices/tárolók | tárfiók | 3-63 | Kisbetűk, számok és kötőjelek.<br><br>Kezdés kisbetűs betűvel vagy számmal. Egymást követő kötőjelek nem használhatók. |
> | storageAccounts/fileServices | tárfiók |  | Kell lennie `default` . |
> | storageAccounts/fileServices/megosztások | tárfiók | 3-63 | Kisbetűk, számok és kötőjelek.<br><br>A kötőjel nem kezdődhet vagy végződhet. Egymást követő kötőjelek nem használhatók. |
> | storageAccounts/managementPolicies | tárfiók |  | Kell lennie `default` . |
> | blob | tároló | 1–1024 | Bármely URL-karakter, kis-és nagybetűk megkülönböztetése |
> | üzenetsor | tárfiók | 3-63 | Kisbetűk, számok és kötőjelek.<br><br>A kötőjel nem kezdődhet vagy végződhet. Egymást követő kötőjelek nem használhatók. |
> | tábla | tárfiók | 3-63 | Alfanumerikus karaktereket.<br><br>Kezdje a betűvel. |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | storageSyncServices | erőforráscsoport | 1-260 | Alfanumerikus karakterek, szóközök, időszakok, kötőjelek és aláhúzások.<br><br>Nem végződhet ponttal vagy szóközzel. |
> | storageSyncServices / syncGroups | Storage Sync szolgáltatás | 1-260 | Alfanumerikus karakterek, szóközök, időszakok, kötőjelek és aláhúzások.<br><br>Nem végződhet ponttal vagy szóközzel. |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | kezelők | erőforráscsoport | 2-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje a betűvel. Alfanumerikus karakterrel végződik. |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | streamingjobs | erőforráscsoport | 3-63 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | streamingjobs/függvények | folyamatos átviteli feladatok | 3-63 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | streamingjobs/bemenetek | folyamatos átviteli feladatok | 3-63 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | streamingjobs/kimenetek | folyamatos átviteli feladatok | 3-63 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |
> | streamingjobs/átalakítások | folyamatos átviteli feladatok | 3-63 | Alfanumerikus karakterek, kötőjelek és aláhúzások. |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | környezetben | erőforráscsoport | 1–90 | Nem használható:<br>`'<>%&:\?/#` |
> | környezetek/accessPolicies | környezet | 1–90 | Nem használható:<br> `'<>%&:\?/#` |
> | környezetek/eventSources | környezet | 1–90 | Nem használható:<br>`'<>%&:\?/#` |
> | környezetek/referenceDataSets | környezet | 3-63 | Alfanumerikus karaktereket |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | tanúsítványok | erőforráscsoport | 1-260 | Nem használható:<br>`/` <br><br>Nem végződhet szóközzel vagy ponttal.  | 
> | kiszolgálófarmok | erőforráscsoport | 1-40 | Alfanumerikus karakterek és kötőjelek. |
> | helyek | globális | 2-60 | Alfanumerikus karaktereket és kötőjeleket tartalmaz.<br><br>A kötőjel nem kezdődhet vagy végződhet. |
> | helyek/bővítőhelyek | hely | 2-59 | Alfanumerikus karakterek és kötőjelek. |

> [!NOTE]
> A Azure Functions azonos elnevezési szabályokkal és korlátozásokkal rendelkezik, mint a Microsoft. Web/Sites.

## <a name="next-steps"></a>Következő lépések

Az erőforrások elnevezésével kapcsolatos ajánlásokat a [Ready: ajánlott elnevezési és címkézési konvenciók](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)című témakörben talál.
