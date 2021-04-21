---
title: Változatok az Azure API Management | Microsoft Docs
description: Az Azure-beli változatok fogalmának API Management.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new, devx-track-azurepowershell
ms.openlocfilehash: bd837faaaa986659ad9b30aa3cf853ea490cec6d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812137"
---
# <a name="revisions-in-azure-api-management"></a>Változatok az Azure API Management

A változatok lehetővé teszik, hogy szabályozott és biztonságos módon módosítsa az API-kat. Ha módosításokat szeretne, hozzon létre egy új változatot. Ezután szerkesztheti és tesztelheti az API-t anélkül, hogy zavarná az API-fogyasztókat. Amikor elkészült, a változatot aktuálisra kell tennie. Ugyanakkor közzétehet egy bejegyzést a változásnaplóban, hogy az API-fogyasztók naprakészek maradnak a változásokról. A változásnapló közzé vantéve a fejlesztői portálon.

> [!NOTE]
> A fejlesztői portál nem érhető el a használat alapján.

A változatokkal a következőt használhatja:

- Biztonságosan módosíthatja az API-definíciókat és -szabályzatokat anélkül, hogy megzavarná az éles API-t.
- A közzététel előtt próbálja ki a módosításokat.
- Dokumentálja az Ön által végrehajtott módosításokat, hogy a fejlesztők tisztában tudjanak az újdonságokkal.
- Ha problémákat talál, visszaállítást fog találni.

[A változatok első lépéseket az útmutatónk alapján sajátoljuk el.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Adott változatok elérése

Az API minden változatához hozzá lehet férni egy speciálisan formázott URL-cím használatával. Az API URL-címének végén, de a lekérdezési sztring előtt fűzve férhet hozzá `;rev={revisionNumber}` az API egy adott változatához. Ezt az URL-címet használhatja például az API 3. változatának `customers` eléréséhez:

`https://apis.contoso.com/customers;rev=3?customerId=123`

Alapértelmezés szerint minden változat ugyanazokkal a biztonsági beállításokkal rendelkezik, mint az aktuális változat. Szándékosan módosíthatja egy adott változat szabályzatát, ha azt szeretné, hogy minden változatra eltérő biztonsági beállítások vonatkoznak. Hozzáadhat például egy [IP-szűrési](./api-management-access-restriction-policies.md#RestrictCallerIPs) szabályzatot, amely megakadályozza, hogy a külső hívók hozzáférjenek egy még fejlesztés alatt áll változathoz.

A változat offline állapotba is váltható, így a hívók akkor sem férhetnek hozzá a változathoz, ha annak URL-címével próbálnak hozzáférni. A változatokat megjelölheti offline állapotúként a Azure Portal. Ha a PowerShellt használja, használhatja a parancsmagot, és a argumentumot a `Set-AzApiManagementApiRevision` következőre `Path` állíthatja: `$null` .

> [!NOTE]
> Javasoljuk, hogy a változatokat offline állapotba kell tenni, ha nem tesztelésre használja őket.

## <a name="current-revision"></a>Aktuális változat

Az aktuális változatként egyetlen *változatot is be lehet* állítani. Ez a változat lesz az összes olyan API-kérelemhez használva, amely nem ad meg explicit változatszámot az URL-címben. A változat aktuálisként való beállításával vissza lehet tért egy korábbi változatra.

A változatot aktuálisként állíthatja be a Azure Portal. Ha a PowerShellt használja, használhatja a `New-AzApiManagementApiRelease` parancsmagot.

## <a name="revision-descriptions"></a>Változatleírások

Változat létrehozásakor saját nyomkövetési célokra is meg lehet adni egy leírást. Az API-felhasználók nem játszanak le leírásokat.

Ha aktuális változatot ad meg, megadhat egy nyilvános változásnapló-megjegyzést is. A módosítási napló a fejlesztői portálon található meg, hogy az API-felhasználók megtekintsék. A változásnapló-megjegyzést a PowerShell-parancsmag használatával `Update-AzApiManagementApiRelease` módosíthatja.

## <a name="versions-and-revisions"></a>Verziók és változatok

A verziók és változatok különálló funkciók. Az egyes verziók több változatot is kaphatnak, akárcsak a nem verziószámozású API-k. A változatokat verziók használata nélkül, vagy más módon is használhatja. A verziók jellemzően az API-verziók lebontó változásokkal való elválasztásához, míg a változatok az API kisebb és nem használ feltörést nem hozó módosításaihoz használhatók.

Ha úgy találja, hogy a változatban jelentős változások vannak, vagy ha formálisan béta/teszt verzióra szeretné áttenni a változatot, változatot is létrehozhat. A Azure Portal kattintson a Változatok lap változat helyi menüjének "Verzió létrehozása változatból" parancsára.
