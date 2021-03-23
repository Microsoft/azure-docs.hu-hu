---
title: Azure-támogatáskérések létrehozása
description: Azok az ügyfelek, akiknek segítségre van szükségük, a Azure Portal használhatják az önkiszolgáló megoldásokat, valamint a támogatási kérések létrehozását és kezelését.
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 395fc0d5905389ffb8f68d8bbc4c6f8b3a871d36
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774871"
---
# <a name="create-an-azure-support-request"></a>Azure-támogatáskérés létrehozása

Az Azure-ban támogatási kérelmeket, más néven támogatási jegyeket hozhat létre és kezelhet. A [Azure Portalban](https://portal.azure.com)olyan kérelmeket hozhat létre és kezelhet, amelyek a jelen cikkben szerepelnek. A kéréseket programozott módon is létrehozhatja és kezelheti az [Azure támogatási jegy REST API](/rest/api/support)vagy az [Azure CLI](/cli/azure/azure-cli-support-request)használatával.

> [!NOTE]
> A Azure Portal URL-cím kifejezetten arra az Azure-felhőre vonatkozik, ahol a szervezete telepítve van.
>
>* A kereskedelmi felhasználásra Azure Portal a következő: [https://portal.azure.com](https://portal.azure.com)
>* Németország Azure Portal: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* A Egyesült Államok Government Azure Portal a következő: [https://portal.azure.us](https://portal.azure.us)

A támogatási kérések tapasztalatai a következő három fő célkitűzésre összpontosítanak:

* **Egyszerűsítve**: a támogatási kérések beküldésével egyszerűen megtalálhatja és leegyszerűsítheti a támogatást és a hibaelhárítást.
* **Integrált**: létrehozhat egy támogatási kérést, ha egy Azure-erőforrással kapcsolatos problémát hibaelhárítás nélkül kapcsol be a környezettel.
* **Hatékony**: gyűjtsön olyan kulcsfontosságú információkat, amelyekre a támogatási szakembernek szüksége van a probléma megoldásához.

Az Azure korlátlan támogatást biztosít az előfizetés-kezeléshez, amely magában foglalja a számlázást, a kvóták módosítását és a fiókok átvitelét. Technikai támogatásért támogatási csomagra van szükség. További információ: [Támogatási csomagok összehasonlítása](https://azure.microsoft.com/support/plans).

## <a name="getting-started"></a>Első lépések

A Azure Portal Súgó és **támogatás** szolgáltatását is elérheti. A szolgáltatás a Azure Portal menüjéből, a globális fejlécből vagy a szolgáltatások erőforrás-menüjéből érhető el. Ha támogatási kérést szeretne beküldeni, rendelkeznie kell a megfelelő engedélyekkel.

### <a name="azure-role-based-access-control"></a>Azure-beli szerepköralapú hozzáférés-vezérlés

Támogatási kérelem létrehozásához [tulajdonosnak](../../role-based-access-control/built-in-roles.md#owner), [közreműködőnek](../../role-based-access-control/built-in-roles.md#contributor) kell lennie, vagy a [támogatási kérelem közreműködői](../../role-based-access-control/built-in-roles.md#support-request-contributor) szerepköréhez kell rendelni az előfizetés szintjén. Ha előfizetést nem tartalmazó támogatási kérelmet szeretne létrehozni, például egy Azure Active Directory-forgatókönyv [, rendszergazdának](../../active-directory/roles/permissions-reference.md)kell lennie.

### <a name="go-to-help--support-from-the-global-header"></a>Ugrás a Súgó + támogatás elemre a globális fejlécből

Támogatási kérelem elindítása bárhonnan a Azure Portalban:

1. Jelölje be a **?** a globális fejlécben kattintson a **Súgó és támogatás** elemre.

   ![Súgó és támogatás](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

1. Válassza az **Új támogatási kérelem** lehetőséget. Kövesse az utasításokat a problémával kapcsolatos információk megadásához. Javaslatot teszünk néhány lehetséges megoldásra, a probléma részleteinek begyűjtésére és a támogatási kérelem elküldésére és nyomon követésére.

   ![Új támogatási kérelem](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Ugrás a Súgó + támogatás erőforrás menüjéből

Támogatási kérelem elindítása a jelenleg használt erőforrás kontextusában:

1. Az erőforrás menüben, a **támogatás + hibaelhárítás** szakaszban válassza az **új támogatási kérelem** lehetőséget.

   ![Kontextusban](./media/how-to-create-azure-support-request/incontext2lower.png)

1. Az utasításokat követve adja meg nekünk a problémával kapcsolatos információkat. Amikor elindítja a támogatási kérés folyamatát az erőforrásból, egyes beállítások előre ki vannak választva.

## <a name="create-a-support-request"></a>Támogatási kérelem létrehozása

Végigvezeti a problémával kapcsolatos információk gyűjtésének lépésein, és segít a megoldásában. Az egyes lépéseket a következő szakaszokban ismertetjük.

### <a name="basics"></a>Alapvető beállítások

A támogatási kérelem folyamatának első lépése a probléma és a támogatási csomag alapvető információit gyűjti.

Az **új támogatási kérelem** **alapjai** lapon a választókkal kezdheti el a problémát. Először meg kell határoznia néhány általános kategóriát a probléma típusához, és kiválaszthatja a kapcsolódó előfizetést. Válassza ki a szolgáltatást, például a **Windows rendszerű virtuális gépeket**. Válassza ki az erőforrást, például a virtuális gép nevét. Írja le a problémát a saját szavaival, majd válassza a **probléma típusa** és a **probléma altípus** elemet a pontosabb megjelenítéshez.

![Alapvető beállítások panel](./media/how-to-create-azure-support-request/basics2lower.png)

### <a name="solutions"></a>Megoldások

Az alapszintű információk összegyűjtése után a következő megoldásokat mutatjuk be a saját kipróbálására. Bizonyos esetekben előfordulhat, hogy egy gyors diagnosztikát is futtatunk. A megoldásokat az Azure-mérnökök írták, és a leggyakoribb problémák megoldására szolgálnak.

### <a name="details"></a>Részletek

A következő lépés a problémával kapcsolatos további részletek begyűjtése. Az ebben a lépésben található részletes és részletes információk segítenek a támogatási kérelem átirányításában a megfelelő mérnöknek.

1. Ha lehetséges, mondja el nekünk, hogy mikor indította el a problémát, és hogy milyen lépéseket kell újból létrehoznia. Feltölthet egy fájlt, például egy naplófájlt vagy egy kimenetet a diagnosztika szolgáltatásból. További információ a fájlfeltöltés szolgáltatásról: [fájlfeltöltés irányelvek](how-to-manage-azure-support-request.md#file-upload-guidelines).

1. Miután megtörtént a problémával kapcsolatos összes információ, válassza ki, hogyan kérhet támogatást. A **részletek** a **támogatási módszer** szakaszban válassza ki a hatás súlyosságát. A maximális súlyossági szint a [támogatási csomagtól](https://azure.microsoft.com/support/plans)függ.

    Alapértelmezés szerint a **megosztás diagnosztikai információi** beállítás van kiválasztva. Ez lehetővé teszi, hogy az Azure-támogatás [diagnosztikai adatokat](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) gyűjtsön az Azure-erőforrásokból. Bizonyos esetekben van egy második kérdés, amely nincs alapértelmezés szerint kiválasztva, például a virtuális gép memóriájához való hozzáférés kérelmezése.

1. Adja meg az előnyben részesített kapcsolattartási módszert, amely az Önnel való kapcsolatfelvételhez és a támogatási nyelvhez nyújt segítséget.

1. Ezután végezze el a **kapcsolattartási adatok** szakaszt, hogy tudjuk, hogyan kell felvenni Önnel a kapcsolatot.

### <a name="review--create"></a>Ellenőrzés és létrehozás

Fejezze be az összes szükséges információt az egyes lapokon, majd válassza a **felülvizsgálat + létrehozás** elemet. Tekintse át a támogatáshoz küldendő adatokat. Ha szükséges, lépjen vissza bármelyik lapra, és végezze el a módosítást. Ha meggyőződött arról, hogy a támogatási kérelem elkészült, válassza a **Létrehozás** lehetőséget.

A támogatási szakember felveszi Önnel a kapcsolatot a megadott módszer használatával. További információ a kezdeti válaszidőt: [támogatás hatóköre és reagálási](https://azure.microsoft.com/support/plans/response/)idő.


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure önsegítő támogatási lehetőségeiről, tekintse meg ezt a videót:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

További információért kövesse az alábbi hivatkozásokat:

* [Azure-támogatási kérelem kezelése](how-to-manage-azure-support-request.md)
* [Azure-beli támogatási jegy REST API](/rest/api/support)
* [Küldje el nekünk visszajelzését és javaslatait](https://feedback.azure.com/forums/266794-support-feedback)
* Részvétel a [Twitteren](https://twitter.com/azuresupport)
* Segítség kérése a társaitól a [Microsoft Q&a kérdéses oldalon](/answers/products/azure)
* További információ az [Azure-támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq)