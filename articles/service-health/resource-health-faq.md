---
title: Azure Resource Health GYIK
description: A Azure Resource Health áttekintése
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: dc24688d8393b7a13e8c93d3f4d0171cce4c213c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570056"
---
# <a name="azure-resource-health-faq"></a>Azure Resource Health GYIK
Ismerkedjen meg a Azure Resource Healthekkel kapcsolatos gyakori kérdésekre adott válaszokkal.

## <a name="what-is-azure-resource-health"></a>Mi az Azure Resource Health?
A Resource Health segítséget nyújt a diagnosztizálásban és a támogatás igénylésében, ha egy Azure-ral kapcsolatos probléma hatással van az erőforrásaira. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.  

## <a name="what-is-the-resource-health-intended-for"></a>Mi a célja a Resource Health?
Ha észlelt egy erőforrással kapcsolatos problémát, Resource Health segíthet a kiváltó ok diagnosztizálásában. Segítséget nyújt a probléma megoldásához és a technikai támogatáshoz, ha további segítségre van szüksége az Azure-szolgáltatásokkal kapcsolatos problémák megoldásához.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Milyen állapot-ellenőrzéseket végez a Resource Health?
A Resource Health különböző ellenőrzéseket végez az [erőforrástípus](resource-health-checks-resource-types.md)alapján. Ezek az ellenőrzések háromféle típusú probléma megvalósítására lettek kialakítva: 
- Nem tervezett események, például váratlan gazdagép újraindítása
- Tervezett események, például a gazdagép operációs rendszerének ütemezett frissítései
- Felhasználói műveletek által aktivált események, például egy virtuális gépet újraindító felhasználó

## <a name="what-does-each-of-the-health-status-mean"></a>Mit jelent az egyes állapotadatok?
Három különböző állapot van:
- Elérhető: nincs olyan ismert probléma az Azure platformon, amely hatással lehet erre az erőforrásra
- Nem érhető el: az erőforrás állapota olyan problémákat észlelt, amelyek hatással vannak az erőforrásra
- Ismeretlen: az erőforrás állapota nem tudja meghatározni az erőforrás állapotát, mert leállt az adatok fogadása. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>Mit jelent az ismeretlen állapot? Helytelen az erőforrásom?
Az állapot értéke ismeretlen, ha Resource Health leállítja egy adott erőforrásra vonatkozó információk fogadását. Habár ez az állapot nem az erőforrás állapotának végleges jele, ha problémát tapasztal, előfordulhat, hogy van egy Azure-probléma.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Hogyan Kérhetek segítséget egy olyan erőforráshoz, amely nem érhető el?
Egy támogatási kérést a Resource Health panelről küldhet be. Nem kell támogatási szerződést kötnie a Microsofttal egy kérelem megnyitásához, ha az erőforrás nem érhető el, mert a platform eseményei vannak.

## <a name="does-resource-health-differentiate-between-unavailability-caused-by-platform-problems-versus-something-i-did"></a>Resource Health különbséget tenni a platformmal kapcsolatos problémák által okozott hiányok között, és amit tettem?
Igen, ha egy erőforrás nem érhető el, Resource Health a következő kategóriák egyikén belül azonosítja a kiváltó okot: 
-   Felhasználó által kezdeményezett művelet
-   Tervezett esemény 
-   Nem tervezett esemény

A portálon a felhasználó által kezdeményezett műveletek kék értesítési ikonnal jelennek meg, míg a tervezett és nem tervezett események vörös figyelmeztető ikon használatával jelennek meg. További részletek a [Resource Health áttekintése című témakörben](Resource-health-overview.md)találhatók.  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Integrálható a Resource Health a monitorozási eszközökkel?
A Resource Health [támogatja](resource-health-alert-arm-template-guide.md) a tevékenység napló alapú riasztásait. A műveletnapló riasztások használatával értesítheti a felhasználókat arról, hogy riasztást [váltott ki.](../azure-monitor/alerts/action-groups.md) A műveleti csoportok különböző értesítési csatornákat támogatnak, például e-maileket, SMS-t, webhookot és ITSM műveleteket.

## <a name="where-do-i-find-resource-health"></a>Hol találom a Resource Health?
Miután bejelentkezett a Azure Portalba, több módon is elérheti Resource Health:
- Navigáljon az erőforráshoz. A bal oldali navigációs sávon válassza az **erőforrás állapota** lehetőséget.
- Lépjen a Azure Service Health panelre.  A bal oldali navigációs sávon válassza az **erőforrás állapota** lehetőséget.

A Resource Health API-val is felhasználhatja az erőforrások állapotával kapcsolatos információkat.

## <a name="is-resource-health-available-for-all-resource-types"></a>Minden erőforrástípus elérhető Resource Health?
Az Resource Health által támogatott állapot-ellenőrzéseket és erőforrástípusok listáját [itt](resource-health-checks-resource-types.md)találja.

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>Mi a teendő, ha az erőforrásom elérhető, de úgy vélem, hogy nem?
Egy erőforrás állapotának ellenőrzésekor közvetlenül az állapot állapota területen kattintson a **helytelen állapot állapotának jelentése** lehetőségre. A jelentés elküldése előtt lehetősége van további részletek megadására arról, hogy miért nem megfelelő az aktuális állapot.

## <a name="is-resource-health-available-for-all-azure-regions"></a>Resource Health érhető el az összes Azure-régióhoz? 
Az erőforrás állapota az összes Azure-térségek elérhető.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Miben különbözik az Resource Health az Azure status vagy az Service Health irányítópulttól?
A Resource Health által megadott információk az Azure status vagy az Service Health irányítópult által biztosítottnál konkrétabbak.

Míg az [Azure status](https://status.azure.com) és a Service Health irányítópult tájékoztat arról, hogy az ügyfelek széles körét befolyásoló szolgáltatási problémák (például egy Azure-régió) Resource Health az adott erőforráshoz kapcsolódó részletesebb eseményeket tesznek elérhetővé. Ha például egy gazdagép váratlanul újraindul, Resource Health csak azokat az ügyfeleket értesíti, akiknek virtuális gépei futnak a gazdagépen.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Aktiválni kell Resource Health minden erőforráshoz?
Nem, a Resource Healthon keresztül elérhető összes erőforrástípus számára elérhető az állapot információja. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Engedélyezni kell a Resource Health a saját szervezetem számára?
Nem.  A Azure Resource Health a Azure Portaln belül elérhető a telepítési követelmények nélkül.

## <a name="is-resource-health-available-free-of-charge"></a>Ingyenesen elérhető Resource Health?
Igen.  Azure Resource Health díjmentes.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Mik a Resource Health által biztosított javaslatok?
Az állapotadatok alapján a Resource Health javaslatokat tesz a hibaelhárításhoz használt idő csökkentésének céljával. A rendelkezésre álló erőforrások esetében a javaslatok az ügyfelek által tapasztalt leggyakoribb problémák megoldására összpontosítanak. Ha az erőforrás nem érhető el az Azure-ban nem tervezett események miatt, a hangsúly a helyreállítási folyamat során és után segítséget nyújt. 

## <a name="next-steps"></a>Következő lépések

További információ a Resource Healthról:
-  [Azure Resource Health áttekintése](Resource-health-overview.md)
-  [Az Azure Resource Health segítségével elérhető erőforrástípusok és állapotellenőrzések](resource-health-checks-resource-types.md)
