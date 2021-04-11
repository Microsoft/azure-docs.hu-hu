---
title: Komplex veszélyforrások elleni védelem Azure Cosmos DB
description: Ismerje meg, hogyan biztosítja a Azure Cosmos DB az inaktív adatok titkosítását és megvalósítását.
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: b73e7f8c13f621bc359a2ae79a725829420a3ecc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455592"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>A Azure Cosmos DB komplex veszélyforrások elleni védelme (előzetes verzió)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB komplex veszélyforrások elleni védelme további biztonsági intelligenciát biztosít, amely szokatlan és potenciálisan ártalmas kísérleteket észlel Azure Cosmos DB fiókok eléréséhez vagy kihasználásához. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelésére, akár biztonsági szakértő nélkül, akár a központi biztonsági figyelő rendszerekkel való integrációhoz.

A biztonsági riasztások akkor aktiválódnak, ha a tevékenységben rendellenességek történnek. Ezek a biztonsági riasztások integrálva vannak  [Azure Security Centerekkel](https://azure.microsoft.com/services/security-center/), és e-mailben is elküldjük az előfizetés-rendszergazdáknak, a gyanús tevékenységek részleteivel és a fenyegetések kivizsgálásával és szervizelésével kapcsolatos ajánlásokkal együtt.

> [!NOTE]
>
> * A Azure Cosmos DB komplex veszélyforrások elleni védelme jelenleg csak az SQL API esetében érhető el.
> * A Azure Cosmos DB komplex veszélyforrások elleni védelme jelenleg nem érhető el az Azure governmentben és a szuverén Felhőbeli régiókban.

A biztonsági riasztások teljes körű vizsgálatához javasolt a [diagnosztikai naplózás engedélyezése Azure Cosmos DBban](./monitor-cosmos-db.md), amely maga az adatbázison naplózza a műveleteket, beleértve a szifiliszi műveleteket az összes dokumentumon, tárolón és adatbázison.

## <a name="threat-types"></a>Fenyegetéstípusok

A Azure Cosmos DB komplex veszélyforrások elleni védelme olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához. Jelenleg a következő riasztásokat indítja el:

- **Hozzáférés szokatlan helyekről**: Ez a riasztás akkor aktiválódik, ha módosul a hozzáférési minta egy Azure Cosmos-fiókhoz, ahol valaki egy szokatlan földrajzi helyről kapcsolódott a Azure Cosmos db végponthoz. Bizonyos esetekben a riasztás jogszerű műveletet észlel, ami egy új alkalmazás vagy fejlesztői karbantartási művelet. Más esetekben a riasztás rosszindulatú műveletet észlel egy korábbi alkalmazotttól, külső támadótól stb.

- **Szokatlan adatok kinyerése**: Ez a riasztás akkor aktiválódik, ha az ügyfél szokatlan adatmennyiséget nyer ki egy Azure Cosmos db fiókból. Ez lehet annak a tünete, hogy egyes adatok kiszűrése a fiókban tárolt adatok külső adattárba történő átviteléhez.



## <a name="configure-advanced-threat-protection"></a>Az Azure Komplex veszélyforrások elleni védelem konfigurálása

A komplex veszélyforrások elleni védelem a következő szakaszokban ismertetett módon állítható be.

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. Indítsa el a Azure Portal a következő helyen:  [https://portal.azure.com](https://portal.azure.com/) .

2. A Azure Cosmos DB fiók **Beállítások** menüjében válassza a **fokozott biztonság** lehetőséget.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png" alt-text="ATP beállítása":::

3. A **speciális biztonsági** beállítások panelen:

    * Kattintson a komplex **veszélyforrások elleni védelem** lehetőségre a **beállításához.**
    * Az új vagy frissített Advanced Threat Protection-szabályzat mentéséhez kattintson a **Mentés** lehetőségre.   

### <a name="rest-api"></a>[REST API](#tab/rest-api)

A REST API-parancsokkal létrehozhat, frissíthet vagy beszerezhet egy adott Azure Cosmos DB fiók komplex veszélyforrások elleni védelmének beállítását.

* [Komplex veszélyforrások elleni védelem – létrehozás](/rest/api/securitycenter/advancedthreatprotection/create)
* [Komplex veszélyforrások elleni védelem – Get](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Használja a következő PowerShell-parancsmagokat:

* [A Komplex veszélyforrások elleni védelem engedélyezése](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Komplex veszélyforrások elleni védelem](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [A komplex veszélyforrások elleni védelem letiltása](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

### <a name="arm-template"></a>[ARM-sablon](#tab/arm-template)

Azure Resource Manager-(ARM-) sablonnal beállíthatja, hogy az Advanced Threat Protection szolgáltatás engedélyezve legyen Cosmos DB.
További információ: CosmosDB- [fiók létrehozása komplex veszélyforrások elleni védelemmel](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

A Cosmos DB a komplex veszélyforrások elleni védelem engedélyezéséhez használjon Azure Policy.

1. Indítsa el az Azure **Policy-fogalommeghatározások** lapot, és keressen rá a Cosmos db házirend komplex **veszélyforrások elleni védelmének üzembe helyezése** lehetőségre.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db.png" alt-text="Keresési szabályzat"::: 

1. Kattintson a komplex **veszélyforrások elleni védelem telepítése CosmosDB** házirendre, majd kattintson a **hozzárendelés** elemre.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png" alt-text="Előfizetés vagy csoport kiválasztása":::


1. A **hatókör** mezőben kattintson a három pontra, válasszon ki egy Azure-előfizetést vagy erőforráscsoportot, majd kattintson a **kiválasztás** elemre.

    :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png" alt-text="Házirend-definíciók lap":::


1. Adja meg a többi paramétert, majd kattintson a **hozzárendelés** elemre.




## <a name="manage-atp-security-alerts"></a>ATP biztonsági riasztások kezelése

Ha Azure Cosmos DB tevékenység anomália történik, a rendszer egy biztonsági riasztást indít el a gyanús biztonsági eseménnyel kapcsolatos információkkal. 

 Azure Security Center az aktuális [biztonsági riasztásokat](../security-center/security-center-alerts-overview.md)áttekintheti és kezelheti.  Kattintson egy adott riasztásra [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) a lehetséges okok és az ajánlott műveletek megtekintéséhez és a lehetséges fenyegetések enyhítéséhez. Az alábbi képen egy példa látható a Security Centerban megadott riasztási adatokra.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png" alt-text="Fenyegetés részletei":::

A rendszer a riasztás részleteivel és a javasolt műveletekkel kapcsolatos e-mail-értesítést is küld. Az alábbi képen egy riasztási e-mail látható.

 :::image type="content" source="./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png" alt-text="Riasztás részletei":::

## <a name="cosmos-db-atp-alerts"></a>ATP-riasztások Cosmos DB

 A Azure Cosmos DB-fiókok figyelése során generált riasztások listájának megtekintéséhez tekintse meg a Azure Security Center dokumentációjának [Cosmos db riasztások](../security-center/alerts-reference.md#alerts-azurecosmos) című szakaszát.

## <a name="next-steps"></a>Következő lépések

* További információ a [diagnosztikai naplózásról Azure Cosmos db](cosmosdb-monitor-resource-logs.md)
* További információ a [Azure Security Center](../security-center/security-center-introduction.md)