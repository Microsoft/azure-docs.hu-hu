---
title: Változatok az Azure API Managementban | Microsoft Docs
description: Ismerje meg az Azure API Management változatok koncepcióját.
services: api-management
documentationcenter: ''
author: johndowns
ms.service: api-management
ms.topic: article
ms.date: 06/12/2020
ms.author: jodowns
ms.custom: fasttrack-new
ms.openlocfilehash: 9a20a7966daff372cf5c0abc9b7b1dbbfd459838
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91403238"
---
# <a name="revisions-in-azure-api-management"></a>Változatok az Azure API Management

A változatok lehetővé teszik az API-k módosítását ellenőrzött és biztonságos módon. Ha módosítani kívánja a módosításokat, hozzon létre egy új változatot. Ezután szerkesztheti és tesztelheti az API-t az API-felhasználók megzavarása nélkül. Ha elkészült, végezze el az aktuális változatot. Ugyanakkor lehetőség van egy bejegyzés közzétételére is a módosítási naplóba, hogy az API-fogyasztók naprakészek maradjanak a változásokkal. A módosítási napló közzé van téve a fejlesztői portálon.

> [!NOTE]
> A fejlesztői portál nem érhető el a felhasználási szinten.

A változatokkal a következőket teheti:

- Az API-definíciók és-szabályzatok biztonságos módosítása az éles környezetbeli API-k zavarása nélkül.
- A közzététel előtt próbálja ki a módosításokat.
- Dokumentálja a módosításokat, így a fejlesztők megismerhetik az újdonságokat.
- Ha problémákat tapasztal, visszaállíthatja.

[Ismerkedjen meg a változatokkal az útmutató lépéseinek követésével.](./api-management-get-started-revise-api.md)

## <a name="accessing-specific-revisions"></a>Adott változatok elérése

Az API-k minden változatát egy speciálisan formázott URL-cím használatával lehet elérni. Fűzze hozzá `;rev={revisionNumber}` az API URL-címének végéhez, de a lekérdezési karakterlánc előtt az API adott változatának eléréséhez. Ezt az URL-címet használhatja például az API 3. változatának eléréséhez `customers` :

`https://apis.contoso.com/customers;rev=3?customerId=123`

Alapértelmezés szerint minden változat ugyanazokkal a biztonsági beállításokkal rendelkezik, mint az aktuális változat. Egy adott változatra vonatkozóan szándékosan módosíthatja a szabályzatokat, ha az egyes változatokra vonatkozóan eltérő biztonsági védelmet szeretne alkalmazni. Előfordulhat például, hogy hozzá szeretne adni egy [IP-szűrési házirendet](./api-management-access-restriction-policies.md#RestrictCallerIPs) , amely megakadályozza, hogy a külső hívók hozzáférjenek egy olyan változathoz, amely még fejlesztés alatt áll.

Egy változat offline állapotba helyezhető, ami elérhetetlenné teszi a hívók számára, még akkor is, ha az URL-címén keresztül megpróbálják elérni a változatot. A változatot kapcsolat nélküli módban is megjelölheti a Azure Portal használatával. Ha a PowerShellt használja, használhatja a `Set-AzApiManagementApiRevision` parancsmagot, és állítsa be az argumentumot a következőre: `Path` `$null` .

> [!NOTE]
> Javasoljuk, hogy offline állapotba vegye a módosításokat, ha nem használja őket tesztelésre.

## <a name="current-revision"></a>Aktuális változat

Az *aktuális* változatként egyetlen változat állítható be. Ez a változat az összes olyan API-kérelem esetében használatos, amely nem ad meg explicit változat-számot az URL-címben. Visszaállíthat egy korábbi változatot úgy, hogy az aktuális változatot állítja vissza.

Megadhat egy változatot aktuálisként a Azure Portal használatával. Ha a PowerShellt használja, használhatja a `New-AzApiManagementApiRelease` parancsmagot.

## <a name="revision-descriptions"></a>Változat leírása

A verzió létrehozásakor megadhat egy leírást a saját követési céljaihoz. A leírások nem játszhatók le az API-felhasználók számára.

Ha a jelenlegi változatot állítja be, akkor megadhat egy nyilvános változásnapló-megjegyzést is. A módosítási naplót az API-felhasználók által megtekinthető fejlesztői portál tartalmazza. A módosítási napló megjegyzéseit a PowerShell- `Update-AzApiManagementApiRelease` parancsmag használatával módosíthatja.

## <a name="versions-and-revisions"></a>Verziók és változatok

A verziók és a változatok különböző funkciók. Az egyes verziók több változattal is rendelkezhetnek, akárcsak a nem verziószámú API-k. Változatok használata nélkül is használhatja a változatokat, vagy fordítva. A verziók általában az API-verziók elválasztására szolgálnak, a módosítások azonban az API-k kisebb és nem törhető módosításaihoz is használhatók.

Ha azt tapasztalja, hogy a változat módosult, vagy ha a változatot egy béta-vagy tesztelési verzióban szeretné kialakítani, egy verziót is létrehozhat egy verzióból. A Azure Portal használatával kattintson a "verzió létrehozása a változatból" elemre a változatok lap változat helyi menüjében.
