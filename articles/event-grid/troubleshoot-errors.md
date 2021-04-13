---
title: Azure Event Grid – hibaelhárítási útmutató
description: Ez a cikk a hibakódok, a hibaüzenetek, a leírások és a javasolt műveletek listáját tartalmazza.
ms.topic: conceptual
ms.date: 04/09/2021
ms.openlocfilehash: 4647039f67cf84a5471dc7b3f38ca754ca9eecb0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310869"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Azure Event Grid hibák elhárítása
Ez a hibaelhárítási útmutató a következő információkat tartalmazza: 

- Azure Event Grid hibakódok
- Hibaüzenetek
- A hibák leírása
- A hibák fogadásakor végrehajtandó ajánlott műveletek. 

## <a name="error-code-400"></a>Hibakód: 400
| Hibakód | Hibaüzenet | Description | Ajánlás |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. BadRequest<br/>400 | A témakör nevének 3 és 50 karakter közöttinek kell lennie. | Az egyéni témakör nevének hossza csak 3 és 50 karakter közötti lehet. A témakör neve csak alfanumerikus betűket, számokat és a "-" karaktert tartalmazhat. A név nem kezdődhet a következő fenntartott szavakkal: <ul><li>Microsoft</li><li>EventGrid</li><li>Rendszer</li></ul> | Válasszon egy másik témakör nevét, amely megfelel a témakör neve követelményeinek. |
| HttpStatusCode. BadRequest<br/>400 | A tartománynév hossza csak 3 és 50 karakter közötti lehet. | A tartománynév hossza csak 3 és 50 karakter közötti hosszúságú lehet. A tartománynév csak alfanumerikus betűket, számokat és a "-" karaktert tartalmazhat. A név nem kezdődhet a következő fenntartott szavakkal:<ul><li>Microsoft</li><li>EventGrid</li><li>Rendszer</li> | Válasszon másik tartománynevet, amely megfelel a tartománynév követelményeinek. |
| HttpStatusCode. BadRequest<br/>400 | Érvénytelen lejárati idő. | Az esemény-előfizetés lejárati ideje határozza meg, hogy mikor kell kivonni az esemény-előfizetést. Az értéknek a jövőben érvényes DateTime értéknek kell lennie.| Győződjön meg arról, hogy az esemény-előfizetés lejárati ideje érvényes DateTime formátumban van megadva, és a jövőre van beállítva. |

## <a name="error-code-409"></a>Hibakód: 409
| Hibakód | Hibaüzenet | Description | Javasolt művelet |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. Conflict <br/>409 | A megadott nevű témakör már létezik. Válasszon másik témakör-nevet.   | Az egyéni témakör nevének egyedinek kell lennie egyetlen Azure-régióban, így biztosítva a megfelelő közzétételi műveletet. Ugyanezt a nevet különböző Azure-régiókban is használhatja. | Válasszon másik nevet a témakörnek. |
| HttpStatusCode. Conflict <br/> 409 | A megadott tartomány már létezik. Válasszon másik tartománynevet. | A tartománynévnek egyedinek kell lennie egyetlen Azure-régióban a megfelelő közzétételi művelet biztosítása érdekében. Ugyanezt a nevet különböző Azure-régiókban is használhatja. | Válasszon másik nevet a tartománynak. |
| HttpStatusCode. Conflict<br/>409 | Elérte a kvóta korlátját. További információ ezekről a korlátozásokról: [Azure Event Grid korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Az egyes Azure-előfizetések az általa használható Azure Event Grid erőforrások számát korlátozzák. A kvóta egy részét vagy egészét túllépte, és nem hozható létre több erőforrás. | Győződjön meg az erőforrások aktuális használatáról, és törölje a szükséges adatokat. Ha nem tud erőforrásokat törölni, hozzon létre egy másik Azure-előfizetést, és hozzon létre Event Grid erőforrásokat az adott előfizetésben. |

## <a name="error-code-403"></a>Hibakód: 403

| Hibakód | Hibaüzenet | Description | Javasolt művelet |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode. tiltott <br/>403 | Az IP-cím-szűrési szabályok miatt a rendszer elutasította a ({témakör/tartomány}) {Ip_cím} ügyfél általi közzétételét. | A témakörhöz vagy tartományhoz IP-tűzfalszabályok vannak konfigurálva, és a hozzáférés csak a konfigurált IP-címekre van korlátozva. | Adja hozzá az IP-címet az IP-tűzfalszabályok számára: [IP-tűzfal konfigurálása](configure-firewall.md) |
| HttpStatusCode. tiltott <br/> 403 | A (z) {topic/domain} ügyfél általi közzétételét a rendszer elutasította, mivel a kérés a privát végponttól érkezett, és nem található az erőforráshoz tartozó saját végpont-kapcsolódás. | A témakör vagy a tartomány saját végpontokkal rendelkezik, és a közzétételi kérelem egy nem konfigurált vagy jóváhagyott privát végpontból érkezett. | Magánhálózati végpont konfigurálása a témakörhöz/tartományhoz. [Privát végpontok konfigurálása](configure-private-endpoints.md) |

Azt is ellenőrizze, hogy a webhook egy Azure Application Gateway vagy webalkalmazási tűzfal mögött van-e. Ha igen, tiltsa le a következő tűzfalszabályok, és tegye meg újra a HTTP-BEJEGYZÉST:

- 920300 (a kérelemből hiányzik egy Accept fejléc)
- 942430 (korlátozott SQL-karakteres anomáliák észlelése (argumentumok): a speciális karakterek száma túllépve (12)
- 920230 (több URL-kódolás észlelhető)
- 942130 (SQL injection támadás: az SQL-tautológia észlelve.)
- 931130 (lehetséges távoli fájlok felvételének (RFI) támadása = off-domain Reference/link)



## <a name="next-steps"></a>Következő lépések
Ha további segítségre van szüksége, tegye fel a problémát a [stack overflow fórumba](https://stackoverflow.com/questions/tagged/azure-eventgrid) , vagy nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/). 
