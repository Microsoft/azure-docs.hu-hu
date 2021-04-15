---
title: Erőforrás-elnevezési korlátozások
description: Megjeleníti az Azure-erőforrások elnevezésére vonatkozó szabályokat és korlátozásokat.
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: e260c9055b26d82f2fd2f8458d287a35a838f40f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477791"
---
# <a name="naming-rules-and-restrictions-for-azure-resources"></a>Az Azure-erőforrásokra vonatkozó elnevezési szabályok és korlátozások

Ez a cikk az Azure-erőforrások elnevezési szabályait és korlátozásait foglalja össze. Az erőforrások elnevezésére vonatkozó javaslatokat lásd: [Ajánlott elnevezési és címkézési konvenciók.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)

Ez a cikk erőforrás-szolgáltatói névtér szerint sorolja fel az erőforrásokat. Az erőforrás-szolgáltatók Azure-szolgáltatásokkal való egyeztetésének listáját lásd: [Erőforrás-szolgáltatók Azure-szolgáltatásokhoz.](azure-services-resource-providers.md)

Az erőforrásnevek nem érzékenyek, kivéve, ha az érvényes karakterek oszlopban fel van jelölve.

A következő táblázatokban az alfanumerikus kifejezés a következőre utal:

* **a–z** (kisbetűk) 
* **A–Z** (nagybetűk) 
* **0 és** **9** között (számok)

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Szerverek | erőforráscsoport | 3-63 | Kisbetűk és számok.<br><br>Kezdje kisbetűvel. |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | szolgáltatás | Globális | 1-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel, és végződik alfanumerikus karakterekkel. |
> | szolgáltatás/API-k | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / api-k / problémák | api | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / apis / problémák / mellékletek | issue | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / apis / problémák / megjegyzések | issue | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / api-k / műveletek | api | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / apis / műveletek / címkék | művelet | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / api-k / kiadások | api | 1–80 | Alfanumerikus karakterek, aláhúzásjelek és kötőjelek.<br><br>Kezdjen és végződve alfanumerikus vagy aláhúzásjelet használva. |
> | szolgáltatás / api-k / sémák | api | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | service / apis / tagDescriptions | api | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / api-k / címkék | api | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás /api-version-sets | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás /engedélyezésKiszolgálók | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/háttérkiszolgálók | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/tanúsítványok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás /diagnosztika | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/csoportok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / csoportok / felhasználók | csoport | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/identitásProviders | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/naplózók | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás /értesítések | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / értesítések / recipientEmails | értesítés | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / openidConnectProviders | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/szabályzatok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/termékek | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / termékek / api-k | product | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / termékek /csoportok | product | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás / termékek / címkék | product | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás /tulajdonságok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/előfizetések | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás /címkék | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/sablonok | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |
> | szolgáltatás/felhasználók | szolgáltatás | 1-256 | Nem használható:<br> `*#&+:<>?` |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | configurationStores | erőforráscsoport | 5-50 | Alfanumerikus karakterek, aláhúzásjelek és kötőjelek. |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Zárak | hozzárendelés hatóköre | 1–90 | Alfanumerikus karakterek, pont, aláhúzásjel, kötőjel és zárójel.<br><br>Nem végződhet pontra. |
> | policyAssignments | hozzárendelés hatóköre | 1–128 megjelenített név<br><br>1–64 erőforrásnév<br><br>1–24 erőforrásnév a felügyeleti csoport hatókörében | A megjelenített név bármilyen karaktert tartalmazhat.<br><br>Az erőforrás neve nem tartalmazhat és nem `%` végződhet pontra vagy szóközre. |
> | policyDefinitions (szabályzatdefiníciók) | a definíció hatóköre | 1–128 megjelenített név<br><br>1–64 erőforrásnév | A megjelenített név bármilyen karaktert tartalmazhat.<br><br>Az erőforrás neve nem tartalmazhat és nem `%` végződhet pontra vagy szóközre. |
> | policySetDefinitions | a definíció hatóköre | 1–128 megjelenített név<br><br>1–64 erőforrásnév<br><br>1–24 erőforrásnév a felügyeleti csoport hatókörében | A megjelenítendő név bármilyen karaktert tartalmazhat.<br><br>Az erőforrás neve nem tartalmazhat és nem `%` végződhet pontra vagy szóközre.  |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | automationAccounts | erőforráscsoport & régió <br>(Lásd az alábbi megjegyzést) | 6-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel, és végződve alfanumerikussal. |
> | automationAccounts / tanúsítványok | Automation-fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem végződhet szóközre.  |
> | automationAccounts /connections | Automation-fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem végződhet szóközre. |
> | automationAccounts /credentials | Automation-fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem végződhet szóközre. |
> | automationAccounts/runbookok | Automation-fiók | 1–63 | Alfanumerikus karakterek, aláhúzásjelek és kötőjelek.<br><br>Kezdje betűvel.  |
> | automationAccounts /schedules | Automation-fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem végződhet szóközre. |
> | automationAccounts / változók | Automation-fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem végződhet szóközre. |
> | automationAccounts /watchers | Automation-fiók | 1–63 |  Alfanumerikus karakterek, aláhúzásjelek és kötőjelek.<br><br>Kezdje betűvel. |
> | automationAccounts /webhooks | Automation-fiók | 1-128 | Nem használható:<br> `<>*%&:\?.+/` <br><br>Nem végződhet szóközre. |

> [!NOTE]
> Az Automation-fióknevek régiónként és erőforráscsoportonként egyediek. Előfordulhat, hogy a törölt Automation-fiókok nevei nem érhetők el azonnal.

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | batchAccounts (kötegek száma) | Region | 3–24 | Kisbetűk és számok. |
> | batchAccounts / applications | batch-fiók | 1-64 | Alfanumerikus karakterek, aláhúzásjelek és kötőjelek. |
> | batchAccounts / tanúsítványok | batch-fiók | 5-45 | Alfanumerikus karakterek, aláhúzásjelek és kötőjelek. |
> | batchAccounts /pools | batch-fiók | 1-64 | Alfanumerikus karakterek, aláhúzásjelek és kötőjelek. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | blockchainMembers | Globális | 2-20 | Kisbetűk és számok.<br><br>Kezdje kisbetűvel. |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | botServices | Globális | 2–64 |  Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. |
> | botServices /channels | bot service | 2–64 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. |
> | botServices / Kapcsolatok | bot service | 2–64 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. |
> | enterpriseChannels | erőforráscsoport | 2–64 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Redis | Globális | 1–63 | Alfanumerikus karakterek és kötőjelek.<br><br>Alfanumerikus karakterekből indul ki és végződik. Egymást követő kötőjelek nem engedélyezettek. |
> | Redis/firewallRules | Redis | 1-256 | Alfanumerikus karakterek |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Profilok | erőforráscsoport | 1-260 | Alfanumerikus karakterek és kötőjelek.<br><br>Alfanumerikus karakterekből indul ki és végződik. |
> | profilok/végpontok | Globális | 1-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Alfanumerikus karakterekkel kezdődnek és végződnek. |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | certificateOrders (tanúsítványrendelések) | erőforráscsoport | 3-30 | Alfanumerikus karakterek. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fiókok | erőforráscsoport | 2–64 | Alfanumerikus karakterek és kötőjelek.<br><br>Alfanumerikus karakterekkel kezdődnek és végződnek. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | availabilitySets (rendelkezésre állási készlet) | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjeles végződő. |
> | diskEncryptionSets | erőforráscsoport | 1–80 | Alfanumerikus karakterek és aláhúzásjelek. |
> | Lemezek | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek és kötőjelek. |
> | Galériák | erőforráscsoport | 1–80 | Alfanumerikus karakterek és időszakok.<br><br>Alfanumerikus karakterekből indul ki és végződik. |
> | katalógusok /alkalmazások | katalógus | 1–80 | Alfanumerikus karakterek, kötőjelek és időszakok.<br><br>Alfanumerikus karakterekből indul ki és végződik. |
> | katalógusok / alkalmazások/verziók | Alkalmazás | 32 bites egész szám | Számok és időszakok. |
> | katalógusok /képek | katalógus | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, kötőjelek és időszakok.<br><br>Alfanumerikus karakterekből indul ki és végződik. |
> | katalógusok / képek / verziók | image | 32 bites egész szám | Számok és időszakok. |
> | images | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | pillanatképek | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjeles végződő. |
> | virtualMachines | erőforráscsoport | 1–15 (Windows)<br>1-64 (Linux)<br><br>Lásd az alábbi megjegyzést. | Nem használhat szóközt vagy a következő karaktereket:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>A Windows rendszerű virtuális gépek nem tartalmazhatnak kötőjellel végződő vagy időszakot.<br><br>A Linux rendszerű virtuális gépek nem végződnek pontokkal vagy kötőjellel. |
> | virtualMachineScaleSets | erőforráscsoport | 1–15 (Windows)<br>1-64 (Linux)<br><br>Lásd az alábbi megjegyzést. | Nem használhat szóközt vagy a következő karaktereket:<br> `~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \ | ; : . ' " , < > / ?`<br><br>Nem kezdődhet aláhúzásjelekkel. Nem végződhet pontra vagy kötőjelre. |

> [!NOTE]
> Az Azure-beli virtuális gépeknek két különböző neve van: erőforrásnév és gazdagépnév. Amikor létrehoz egy virtuális gépet a portálon, a rendszer ugyanazt az értéket használja mindkét névhez. Az előző táblázatban található korlátozások az állomásnévre vonatkoznak. A tényleges erőforrásnév legfeljebb 64 karakterből állhat.

## <a name="microsoftcommunication"></a>Microsoft.Communication

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | communicationServices | Globális | 1–63 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | containerGroups | erőforráscsoport | 1–63 | Kisbetűk, számok és kötőjelek.<br><br>Nem kezdődhet vagy végződhet kötőjellel. Szomszédos kötőjelek használata nem megengedett. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Nyilvántartások | Globális | 5-50 | Alfanumerikus karakterek. |
> | beállításregisztrálók / buildTasks | registry | 5-50 | Alfanumerikus karakterek. |
> | beállításregisztrálók / buildTasks/steps | buildfeladat | 5-50 | Alfanumerikus karakterek. |
> | beállításregisztrációs jegyzékek /replikációk | registry | 5-50 | Alfanumerikus karakterek. |
> | beállítás-nyilvántartások / scopeMaps | registry | 5-50 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | beállításregisztrálók /feladatok | registry | 5-50 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | beállításregisztrálók /jogkivonatok | registry | 5-50 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | beállításregisztrálók /webhookok | registry | 5-50 | Alfanumerikus karakterek. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | managedClusters (felügyelt fürtök) | erőforráscsoport | 1–63 | Alfanumerikus karakterek, aláhúzásjelek és kötőjelek.<br><br>Alfanumerikus karakterekkel kezdődnek és végződnek. |
> | openShiftManagedClusters | erőforráscsoport | 1-30 | Alfanumerikus karakterek. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Csomópontok | erőforráscsoport | 1-64 | Alfanumerikus karakterek.<br><br>Kezdje betűvel.  |
> | hubok / authorizationPolicies | Hub | 1-50 | Alfanumerikus karakterek, aláhúzásjelek és időszakok.<br><br>Alfanumerikus karakterekből indul ki és végződik. |
> | hubok/összekötők | Hub | 1-128 | Alfanumerikus karakterek és aláhúzásjelek.<br><br>Kezdje betűvel. |
> | hubok / összekötők/leképezések | összekötő | 1-128 | Alfanumerikus karakterek és aláhúzásjelek.<br><br>Kezdje betűvel. |
> | hubok /interakciók | Hub | 1-128 | Alfanumerikus karakterek és aláhúzásjelek.<br><br>Kezdje betűvel. |
> | hubok / kpi | Hub | 1-512 | Alfanumerikus karakterek és aláhúzásjelek.<br><br>Kezdje betűvel. |
> | hubok /hivatkozások | Hub | 1-512 | Alfanumerikus karakterek és aláhúzásjelek.<br><br>Kezdje betűvel. |
> | hubok / előrejelzések | Hub | 1-512 | Alfanumerikus karakterek és aláhúzásjelek.<br><br>Kezdje betűvel. |
> | hubok/profilok | Hub | 1-128 | Alfanumerikus karakterek és aláhúzásjelek.<br><br>Kezdje betűvel. |
> | hubok /relationshipLinks | Hub | 1-512 | Alfanumerikus karakterek és aláhúzásjelek.<br><br>Kezdje betűvel. |
> | hubok /kapcsolatok | Hub | 1-512 | Alfanumerikus karakterek és aláhúzásjelek.<br><br>Kezdje betűvel. |
> | hubok /roleAssignments | Hub | 1-128 | Alfanumerikus karakterek és aláhúzásjelek.<br><br>Kezdje betűvel. |
> | hubok /nézetek | Hub | 1-512 | Alfanumerikus karakterek és aláhúzásjelek.<br><br>Kezdje betűvel. |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Egyesületek | erőforráscsoport | 1-180 | Nem használható:<br>`%&\\?/`<br><br>Nem végződhet pontra vagy szóközre. |
> | resourceProviders (erőforrás-providers) | erőforráscsoport | 3-64 | Nem használható:<br>`%&\\?/`<br><br>Nem végződhet pontra vagy szóközre. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Munkahelyek | erőforráscsoport | 3–24 | Alfanumerikus karakterek, kötőjelek, aláhúzásjelek és pontértékek. |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | munkaterületek | erőforráscsoport | 3-30 | Alfanumerikus karakterek, aláhúzásjelek és kötőjelek |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Gyárak | Globális | 3-63 | Alfanumerikus karakterek és kötőjelek.<br><br>Alfanumerikus karakterekből indul ki és végződik. |
> | gyárak/adatfolyamok | Gyár | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdje alfanumerikussal. |
> | gyárak/adatkészletek | Gyár | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdje alfanumerikussal. |
> | gyárak /integrationRuntimes | Gyár | 3-63 | Alfanumerikus karakterek és kötőjelek.<br><br>Alfanumerikus karakterekből indul ki és végződik. |
> | gyárak /linkedservices | Gyár | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdje alfanumerikus karakterekkel. |
> | gyárak/folyamatok | Gyár | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdje alfanumerikus karakterekkel. |
> | gyárak/eseményindítók | Gyár | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdje alfanumerikus karakterekkel. |
> | gyárak / eseményindítók / újrafuttatók | Ravaszt | 1-260 | Nem használható:<br>`<>*#.%&:\\+?/`<br><br>Kezdje alfanumerikus karakterekkel. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fiókok | Globális | 3–24 | Kisbetűk és számok. |
> | fiókok / computePolicies | account | 3-60 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | accounts / dataLakeStoreAccounts | account | 3–24 | Kisbetűk és számok. |
> | fiókok /firewallRules | account | 3-50 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | fiókok / storageAccounts | account | 3-60 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fiókok | Globális | 3–24 | Kisbetűk és számok. |
> | fiókok /firewallRules | account | 3-50 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | fiókok / virtualNetworkRules | account | 3-50 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | services | erőforráscsoport | 2-62 | Alfanumerikus karakterek, kötőjelek, pont és aláhúzásjel.<br><br>Kezdje alfanumerikus karakterekkel. |
> | szolgáltatások/projektek | szolgáltatás | 2-57 | Alfanumerikus karakterek, kötőjelek, pont és aláhúzásjel.<br><br>Kezdje alfanumerikus karakterekkel. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Szerverek | Globális | 3-63 | Kisbetűk, kötőjelek és számok.<br><br>Nem kezdődhet vagy végződhet kötőjellel. |
> | kiszolgálók /adatbázisok | Szerverek | 1–63 | Alfanumerikus karakterek és kötőjelek. |
> | servers /firewallRules | Szerverek | 1-128 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | servers /virtualNetworkRules | Szerverek | 1-128 | Alfanumerikus karakterek és kötőjelek. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Szerverek | Globális | 3-63 | Kisbetűk, kötőjelek és számok.<br><br>Nem kezdődhet vagy végződhet kötőjellel. |
> | kiszolgálók /adatbázisok | Szerverek | 1–63 | Alfanumerikus karakterek és kötőjelek. |
> | servers /firewallRules | Szerverek | 1-128 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | servers /virtualNetworkRules | Szerverek | 1-128 | Alfanumerikus karakterek és kötőjelek. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Szerverek | Globális | 3-63 | Kisbetűk, kötőjelek és számok.<br><br>Nem kezdődhet vagy végződhet kötőjellel. |
> | kiszolgálók /adatbázisok | Szerverek | 1–63 | Alfanumerikus karakterek és kötőjelek. |
> | servers /firewallRules | Szerverek | 1-128 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | servers /virtualNetworkRules | Szerverek | 1-128 | Alfanumerikus karakterek és kötőjelek. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | IotHubs | Globális | 3-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Nem végződhet kötőjellel. |
> | IotHubs / tanúsítványok | IoT Hub | 1-64 | Alfanumerikus karakterek, kötőjelek, pont és aláhúzásjel. |
> | IotHubs / eventHubEndpoints / ConsumerGroups | eventHubEndpoints | 1-50 | Alfanumerikus karakterek, kötőjelek, pont és aláhúzásjel. |
> | provisioningServices | erőforráscsoport | 3-64 | Alfanumerikus karakterek és kötőjelek.<br><br>Alfanumerikus karakterekkel végződik. |
> | provisioningServices / tanúsítványok | provisioningServices | 1-64 | Alfanumerikus karakterek, kötőjelek, pont és aláhúzásjel. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Labs | erőforráscsoport | 1-50 | Alfanumerikus karakterek, aláhúzásjelek és kötőjelek. |
> | labs / customimages | Labor | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, kötőjelek és zárójelek. |
> | tesztkörnyezetek /képletek | Labor | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, kötőjelek és zárójelek. |
> | tesztkörnyezetek /virtualmachines | Labor | 1–15 (Windows)<br>1-64 (Linux) | Alfanumerikus karakterek és kötőjelek.<br><br>Alfanumerikus karakterekkel kezdődnek és végződnek. Nem lehet minden szám. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | databaseAccounts (adatbázis-fiókok) | Globális | 3-44 | Kisbetűk, számok és kötőjelek.<br><br>Kezdje kisbetűvel vagy számmal. |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Tartományok | erőforráscsoport | 3-50 | Alfanumerikus karakterek és kötőjelek. |
> | tartományok/témakörök | domain | 3-50 | Alfanumerikus karakterek és kötőjelek. |
> | eventSubscriptions (eventSubscriptions) | erőforráscsoport | 3-64 | Alfanumerikus karakterek és kötőjelek. |
> | Témák | erőforráscsoport | 3-50 | Alfanumerikus karakterek és kötőjelek. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Klaszterek | erőforráscsoport | 6-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. Betűvel vagy számmal végződik. |
> | Névterek | Globális | 6-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. Betűvel vagy számmal végződik. |
> | névterek / Engedélyezési szabályok | névtér | 1-50 | Alfanumerikus karakterek, pontokat, kötőjeleket és aláhúzásjeleket.<br><br>Betűvel vagy számmal kezdődnek és végződnek. |
> | névterek / disasterRecoveryConfigs | névtér | 1-50 | Alfanumerikus karakterek, pontokat, kötőjeleket és aláhúzásjeleket.<br><br>Betűvel vagy számmal kezdődnek és végződnek. |
> | névterek / eventhubs | névtér | 1-50 | Alfanumerikus karakterek, pontokat, kötőjeleket és aláhúzásjeleket.<br><br>Betűvel vagy számmal kezdje és végződve. |
> | névterek / eventhubs / authorizationRules | eseményközpont | 1-50 | Alfanumerikus karakterek, pont, kötőjel és aláhúzásjel.<br><br>Betűvel vagy számmal kezdődnek és végződnek. |
> | névterek / eventhubs /consumergroups | eseményközpont | 1-50 | Alfanumerikus karakterek, pont, kötőjel és aláhúzásjel.<br><br>Betűvel vagy számmal kezdődnek és végződnek. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Klaszterek | Globális | 3-59 | Alfanumerikus karakterek és kötőjelek<br><br>Betűvel vagy számmal kezdődnek és végződnek. |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Munkahelyek | erőforráscsoport | 2–64 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | actionGroups (műveletcsoportok) | erőforráscsoport | 1-260 | Nem használható:<br>`/&%\?` <br><br>Nem végződhet szóközre vagy pontra.  |
> | Összetevők | erőforráscsoport | 1-260 | Nem használható:<br>`%&\?/` <br><br>Nem végződhet szóközre vagy pontra.  |
> | scheduledQueryRules | erőforráscsoport | 1-260 | Nem használható:<br>`*<>%{}&:\\?/#` <br><br>Nem végződhet szóközre vagy pontra.  |
> | metricAlerts (mérőszámok) | erőforráscsoport | 1-260 | Nem használható:<br>`*#&+:<>?@%{}\/` <br><br>Nem végződhet szóközre vagy pontra.  |
> | activityLogAlerts | erőforráscsoport | 1-260 | Nem használható:<br>`<>*%{}&:\\?+/#` <br><br>Nem végződhet szóközre vagy pontra.  |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | IoTApps | Globális | 2-63 | Kisbetűk, számok és kötőjelek.<br><br>Kezdje kisbetűvel vagy számmal. |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Boltívek | Globális | 3–24 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. Betűvel vagy számmal végződik. Nem tartalmazhatnak egymást követő kötőjeleket. |
> | tárolók /titkos kulcsok | Tároló | 1-127 | Alfanumerikus karakterek és kötőjelek. |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Klaszterek | Globális | 4-22 | Kisbetűk és számok.<br><br>Kezdje betűvel. |
> | /clusters / adatbázisok | cluster | 1-260 | Alfanumerikus karakterek, kötőjelek, szóközök és időszakok. |
> | /clusters / databases / dataConnections | adatbázis | 1-40 | Alfanumerikus karakterek, kötőjelek, szóközök és időszakok. |
> | /clusters / databases / eventhubconnections | adatbázis | 1-40 | Alfanumerikus karakterek, kötőjelek, szóközök és időszakok. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | integrationAccounts (integrationAccounts) | erőforráscsoport | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzásjel, pont és zárójel. |
> | integrationAccounts/assemblies | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzásjel, pont és zárójel. |
> | integrationAccounts / batchConfigurations | integrációs fiók | 1-20 | Alfanumerikus karakterek. |
> | integrationAccounts/certificates | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzásjel, pont és zárójel. |
> | integrationAccounts / maps | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzásjel, pont és zárójel. |
> | integrationAccounts /partners | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzásjel, pont és zárójel. |
> | integrationAccounts /tottanetprocessconfigurations | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzásjel, pont és zárójel. |
> | integrationAccounts /schemas | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzásjel, pont és zárójel. |
> | integrationAccounts/sessions | integrációs fiók | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzásjel, pont és zárójel. |
> | integrationServiceEnvironments | erőforráscsoport | 1–80 | Alfanumerikus karakterek, kötőjelek, pont és aláhúzásjel. |
> | integrationServiceEnvironments / managedApis | integrációs szolgáltatási környezet | 1–80 | Alfanumerikus karakterek, kötőjelek, pont és aláhúzásjel. |
> | Munkafolyamatok | erőforráscsoport | 1–80 | Alfanumerikus karakterek, kötőjelek, aláhúzásjel, pont és zárójel. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | commitmentPlans (kötelezettségvállalásisíkok) | erőforráscsoport | 1-260 | Nem használható:<br>`<>*%&:?+/\\`<br><br>Nem végződhet szóközre. |
> | webServices | erőforráscsoport | 1-260 | Nem használható:<br>`<>*%&:?+/\\`<br><br>Nem végződhet szóközre. |
> | munkaterületek | erőforráscsoport | 1-260 | Nem használható:<br>`<>*%&:?+/\\`<br><br>Nem végződhet szóközre. |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | munkaterületek | erőforráscsoport | 3-33 | Alfanumerikus karakterek és kötőjelek. |
> | munkaterületek /számítási erőforrások | munkaterület | 2–16 | Alfanumerikus karakterek és kötőjelek. |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | userAssignedIdentities (userAssignedIdentities) | erőforráscsoport | 3-128 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek<br><br>Kezdje betűvel vagy számmal. |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | fiókok | erőforráscsoport | 1–98 (erőforráscsoport és fióknév) | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikussal. |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | mediaservices | erőforráscsoport | 3–24 | Kisbetűk és számok. |
> | mediaservices /liveEvents | Media service | 1-32 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje alfanumerikussal. |
> | mediaservices / liveEvents / liveOutputs | Élő esemény | 1-256 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje alfanumerikussal. |
> | mediaservices / streamingEndpoints | Media service | 1-24 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje alfanumerikussal. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | applicationGateways | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | applicationSecurityGroups | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | azureFirewalls | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjeles végződő. |
> | bastionHosts | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | Kapcsolatok | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | dnsZones (dnsZones) | erőforráscsoport | 1–63 karakter<br><br>2–34 címke<br><br>Minden címke egy pontokkal elválasztott karakterkészlet. Például a **contoso.com** 2 címkével rendelkezik. | Minden címke tartalmazhat alfanumerikus karaktereket, aláhúzásjeleket és kötőjeleket.<br><br>Minden címke pontokkal van elválasztva. |
> | expressRouteCircuits | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | firewallPolicies | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | firewallPolicies / ruleGroups | tűzfal-szabályzat | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | frontDoors (frontDoors) | Globális | 5-64 | Alfanumerikus karakterek és kötőjelek.<br><br>Alfanumerikus karakterekkel kezdődnek és végződnek. |
> | frontdoorWebApplicationFirewallPolicies | erőforráscsoport | 1-128 | Alfanumerikus karakterek.<br><br>Kezdje betűvel. |
> | loadBalancers | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | loadBalancers / inboundNatRules | terheléselosztás | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | localNetworkGateways | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | networkInterfaces | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | networkSecurityGroups | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | networkSecurityGroups / securityRules | hálózati biztonsági csoport | 1–80 |  Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | networkWatchers | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | privateDnsZones | erőforráscsoport | 1–63 karakter<br><br>2–34 címke<br><br>Minden címke egy pontokkal elválasztott karakterkészlet. Például a **contoso.com** 2 címkével rendelkezik. | Minden címke tartalmazhat alfanumerikus karaktereket, aláhúzásjeleket és kötőjeleket.<br><br>Minden címkét pont választ el egymástól. |
> | privateDnsZones / virtualNetworkLinks | privát DNS-zóna | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | publicIPAddresses | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | publicIPPrefixes | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | routeFilters (útvonalszűrők) | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | routeFilters / routeFilterRules | útvonalszűrő | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | routeTables | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | routeTables /routes | útvonaltábla | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | serviceEndpointPolicies | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | trafficmanagerprofiles | Globális | 1–63 | Alfanumerikus karakterek, kötőjelek és időszakok.<br><br>Alfanumerikus karakterekkel kezdődnek és végződnek. |
> | virtualNetworkGateways | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | virtualNetworks | erőforráscsoport | 2–64 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | virtualnetworks /alhálózatok | virtuális hálózat | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | virtualNetworks / virtualNetworkPeerings | virtuális hálózat | 1–80 | Alfanumerikus karakterek, aláhúzásjel, pont és kötőjel.<br><br>Kezdje alfanumerikus karakterekkel. Alfanumerikus vagy aláhúzásjel végződő. |
> | virtualWans | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | vpnGateways | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | vpnGateways / vpnConnections | VPN-átjáró | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |
> | vpnSites (vpnSites) | erőforráscsoport | 1–80 | Alfanumerikus karakterek, aláhúzásjelek, időszakok és kötőjelek.<br><br>Kezdje alfanumerikussal. Alfanumerikus vagy aláhúzásjel végződő. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Névterek | Globális | 6-50 | Alfanumerikus karakterek és kötőjelek<br><br>Kezdje betűvel. Alfanumerikus karakterekkel végződik. |
> | névterek / Engedélyezési szabályok | névtér | 1-256 | Alfanumerikus karakterek, pont, kötőjel és aláhúzásjel.<br><br>Alfanumerikus karaktereket indít el. |
> | névterek / notificationHubs | névtér | 1-260 | Alfanumerikus karakterek, pont, kötőjel és aláhúzásjel.<br><br>Alfanumerikus karaktereket indít el. |
> | névterek / notificationHubs / AuthorizationRules | értesítési központ | 1-256 | Alfanumerikus karakterek, pont, kötőjel és aláhúzásjel.<br><br>Alfanumerikus karaktereket kezdjen. |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Klaszterek | erőforráscsoport | 4-63 | Alfanumerikus karakterek és kötőjelek.<br><br>Alfanumerikus karakterekből indul ki és végződik. |
> | munkaterületek | Globális | 4-63 | Alfanumerikus karakterek és kötőjelek.<br><br>Alfanumerikus karakterekből indul ki és végződik. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | megoldások | munkaterület | N/A | A Microsoft által írt megoldásoknál a névnek a következő mintában kell lennie:<br>`SolutionType(WorkspaceName)`<br><br>A külső felek által írt megoldásokhoz a névnek a következő mintában kell lennie:<br>`SolutionType[WorkspaceName]`<br><br>Érvényes név például a következő:<br>`AntiMalware(contoso-IT)`<br><br>A megoldás típusa megkülönbözteti a kis- és nagybetűket. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | irányítópultok | erőforráscsoport | 3-160 | Alfanumerikus karakterek és kötőjelek.<br><br>Ha korlátozott karaktereket szeretne használni, adjon hozzá egy **hidden-title nevű** címkét a használni kívánt irányítópult nevével. A portál megjeleníti ezt a nevet az irányítópult megjelenítésekor. |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | workspaceCollections (munkaterület-collections) | régió | 3-63 | Alfanumerikus karakterek és kötőjelek.<br><br>Nem kezdődhet kötőjellel. Nem használhat egymást követő kötőjeleket. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Kapacitások | régió | 3-63 | Kisbetűk vagy számok<br><br>Kezdje kisbetűvel. |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Boltívek | erőforráscsoport | 2-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. |
> | tárolók / backupPolicies | tár | 3-150 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. Nem végződhet kötőjellel. |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Névterek | Globális | 6-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. Betűvel vagy számmal végződik. |
> | névterek / Engedélyezési szabályok | névtér | 1-50 |  Alfanumerikus karakterek, pont, kötőjel és aláhúzásjel.<br><br>Alfanumerikus karakterekből indul ki és végződik. |
> | névterek / HybridConnections | névtér | 1-260 | Alfanumerikus karakterek, pontokat, kötőjeleket, aláhúzásjeleket és perjeleket.<br><br>Alfanumerikus karakterekből indul ki és végződik. |
> | névterek / HybridConnections/authorizationRules | hibrid kapcsolat | 1-50 | Alfanumerikus karakterek, pont, kötőjel és aláhúzásjel.<br><br>Alfanumerikus karakterekből indul ki és végződik. |
> | névterek / WcfRelays | névtér | 1-260 | Alfanumerikus karakterek, pontokat, kötőjeleket, aláhúzásjeleket és perjeleket.<br><br>Alfanumerikus karakterekből indul ki és végződik. |
> | névterek / WcfRelays / authorizationRules | Wcf Relay | 1-50 | Alfanumerikus karakterek, pontokat, kötőjeleket és aláhúzásjeleket.<br><br>Alfanumerikus karakterekkel kezdődnek és végződnek. |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Telepítések | erőforráscsoport | 1-64 | Alfanumerikus karakterek, aláhúzásjelek, zárójelek, kötőjelek és pontokat. |
> | erőforráscsoportok | előfizetést | 1–90 | Alfanumerikus karakterek, aláhúzásjelek, zárójelek, kötőjelek, pontok és Unicode-karakterek, amelyek megfelelnek a [regex dokumentációjának.](/rest/api/resources/resourcegroups/createorupdate)<br><br>Nem végződhet pontra. |
> | tagNames (címkenevek) | Erőforrás | 1-512 | Nem használható:<br>`<>%&\?/` |
> | tagNames / tagValues | címke neve | 1-256 | Minden karakter. |
> | templateSpecs | erőforráscsoport | 1–90 | Alfanumerikus karakterek, aláhúzásjelek, zárójelek, kötőjelek és pontokat. |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Névterek | Globális | 6-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. Betűvel vagy számmal végződik.<br><br>További információ: [Névtér létrehozása.](/rest/api/servicebus/create-namespace) |
> | névterek / Engedélyezési szabályok | névtér | 1-50 | Alfanumerikus karakterek, pont, kötőjel és aláhúzásjel.<br><br>Alphnumerikus karakterekkel kezdődnek és végződnek. |
> | névterek / disasterRecoveryConfigs | Globális | 6-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. Alfanumerikus karakterekkel végződik. |
> | névterek / migrationConfigurations | névtér |  | Mindig a **$default.** |
> | névterek /üzenetsorok | névtér | 1-260 | Alfanumerikus karakterek, pontokat, kötőjeleket, aláhúzásjeleket és perjeleket.<br><br>Alfanumerikus karakterekkel kezdődnek és végződnek. |
> | névterek / üzenetsorok / engedélyezési szabályok | üzenetsor | 1-50 | Alfanumerikus karakterek, pont, kötőjel és aláhúzásjel.<br><br>A kezdéshez és a véghez alphnumerikus karaktereket kell bejelenni. |
> | névterek /témakörök | névtér | 1-260 | Alfanumerikus karakterek, pontokat, kötőjeleket, aláhúzásjeleket és perjeleket.<br><br>Alfanumerikus karakterekkel kezdődnek és végződnek. |
> | névterek / témakörök / authorizationRules | témakör | 1-50 | Alfanumerikus karakterek, pont, kötőjel és aláhúzásjel.<br><br>A kezdéshez és a véghez alphnumerikus karaktereket kell bevetni. |
> | névterek / témakörök / előfizetések | témakör | 1-50 | Alfanumerikus karakterek, pont, kötőjel és aláhúzásjel.<br><br>A kezdéshez és a véghez alphnumerikus karaktereket kell bejelenni. |
> | névterek / témakörök / előfizetések / szabályok | előfizetést | 1-50 | Alfanumerikus karakterek, pont, kötőjel és aláhúzásjel.<br><br>Alphnumerikus karakterekkel kezdődnek és végződnek. |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Klaszterek | régió | 4-23 | Kisbetűk, számok és kötőjelek.<br><br>Kezdje kisbetűvel. Kisbetűvel vagy számmal végződik. |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | signalR | Globális | 3-63 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. Betűvel vagy számmal végződik.  |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | managedInstances | Globális | 1–63 | Kisbetűk, számok és kötőjelek.<br><br>Nem kezdődhet vagy végződhet kötőjellel. <br><br> Nem tartalmazhat speciális karaktereket, például `@` : . |
> | Szerverek | Globális | 1–63 | Kisbetűk, számok és kötőjelek.<br><br>Nem kezdődhet vagy végződhet kötőjellel. |
> | kiszolgálók/rendszergazdák | kiszolgáló |  | A(nak) kell `ActiveDirectory` lennie. |
> | kiszolgálók/adatbázisok | kiszolgáló | 1-128 | Nem használható:<br>`<>*%&:\/?`<br><br>Nem végződhet pontra vagy szóközre. |
> | servers / databases / syncGroups | adatbázis | 1-150 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | servers /elasticPools | kiszolgáló | 1-128 | Nem használható:<br>`<>*%&:\/?`<br><br>Nem végződhet pontra vagy szóközre. |
> | servers /failoverGroups | Globális | 1–63 | Kisbetűk, számok és kötőjelek.<br><br>Nem kezdődhet vagy végződhet kötőjellel. |
> | servers /firewallRules | kiszolgáló | 1-128 | Nem használható:<br>`<>*%&:;\/?`<br><br>Nem végződhet pontra. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | storageAccounts (tárfiókok) | Globális | 3–24 | Kisbetűk és számok. |
> | storageAccounts / blobServices | tárfiók |  | A(nak) kell `default` lennie. |
> | storageAccounts / blobServices / tárolók | tárfiók | 3-63 | Kisbetűk, számok és kötőjelek.<br><br>Kezdje kisbetűvel vagy számmal. Nem használhat egymást követő kötőjeleket. |
> | storageAccounts / fileServices | tárfiók |  | A(nak) kell `default` lennie. |
> | storageAccounts / fileServices / shares | tárfiók | 3-63 | Kisbetűk, számok és kötőjelek.<br><br>Nem kezdődhet vagy végződhet kötőjellel. Nem használhat egymást követő kötőjeleket. |
> | storageAccounts / managementPolicies | tárfiók |  | A(nak) kell `default` lennie. |
> | blob | tároló | 1–1024 | Bármilyen URL-karakter, megkülönbözteti a kis- és nagybetűket |
> | üzenetsor | tárfiók | 3-63 | Kisbetűk, számok és kötőjelek.<br><br>Nem kezdődhet vagy végződhet kötőjellel. Nem használhat egymást követő kötőjeleket. |
> | tábla | tárfiók | 3-63 | Alfanumerikus karakterek.<br><br>Kezdje betűvel. |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | storageSyncServices | erőforráscsoport | 1-260 | Alfanumerikus karakterek, szóközök, pont, kötőjel és aláhúzásjel.<br><br>Nem végződhet pontra vagy szóközre. |
> | storageSyncServices / syncGroups | tárolószinkronizálási szolgáltatás | 1-260 | Alfanumerikus karakterek, szóközök, pont, kötőjel és aláhúzásjel.<br><br>Nem végződhet pontra vagy szóközre. |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Vezetők | erőforráscsoport | 2-50 | Alfanumerikus karakterek és kötőjelek.<br><br>Kezdje betűvel. Alfanumerikus karakterekkel végződik. |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | streamingjobs | erőforráscsoport | 3-63 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | streamingjobs / függvények | streamelési feladat | 3-63 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | streamingjobs/bemenetek | streamelési feladat | 3-63 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | streamingjobs /outputs | streamelési feladat | 3-63 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |
> | streamingjobs /átalakítások | streamelési feladat | 3-63 | Alfanumerikus karakterek, kötőjelek és aláhúzásjelek. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | Környezetben | erőforráscsoport | 1–90 | Nem használható:<br>`'<>%&:\?/#` |
> | környezetek / accessPolicies | környezet | 1–90 | Nem használható:<br> `'<>%&:\?/#` |
> | környezetek /eventSources | környezet | 1–90 | Nem használható:<br>`'<>%&:\?/#` |
> | környezetek / referenceDataSets | környezet | 3-63 | Alfanumerikus karakterek |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Entitás | Hatókör | Hossz | Érvényes karakterek |
> | --- | --- | --- | --- |
> | tanúsítványok | erőforráscsoport | 1-260 | Nem használható:<br>`/` <br><br>Nem végződhet szóközre vagy pontra.  | 
> | kiszolgálófarmok | erőforráscsoport | 1-40 | Alfanumerikus karakterek és kötőjelek. |
> | Helyek | globális vagy tartományonkénti. Lásd az alábbi megjegyzést. | 2-60 | Alfanumerikus karaktereket és kötőjeleket tartalmaz.<br><br>Nem kezdődhet vagy végződhet kötőjellel. |
> | helyek/tárolóhelyek | Oldalon | 2-59 | Alfanumerikus karakterek és kötőjelek. |

> [!NOTE]
> A webhelynek globálisan egyedi URL-lel kell lennie. Ha üzemeltetési tervet használó webhelyet hoz létre, az URL-cím `http://<app-name>.azurewebsites.net` a következő: . Az alkalmazás nevének globálisan egyedinek kell lennie. Ha olyan webhelyet hoz létre, amely App Service Environment, az alkalmazás nevének egyedinek kell lennie a [App Service Environment.](../../app-service/environment/using-an-ase.md#app-access) A webhely URL-címe mindkét esetben globálisan egyedi.
>
> Azure Functions microsoft.web/sites elnevezési szabályokkal és korlátozásokkal rendelkezik.

## <a name="next-steps"></a>Következő lépések

Az erőforrások elnevezésére vonatkozó javaslatokért [lásd: Ready: Recommended naming and tagging conventions (Kész: Ajánlott elnevezési és címkézési konvenciók).](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)
