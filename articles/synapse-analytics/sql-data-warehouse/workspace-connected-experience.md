---
title: A szinapszis munkaterület funkcióinak engedélyezése dedikált SQL-készleten (korábban SQL DW)
description: Ez a dokumentum azt ismerteti, hogy az ügyfél Hogyan férhet hozzá és használhatja a meglévő SQL DW önálló példányát a munkaterületen.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 6d71d9889701ec834747e4bec1dd111157c3206e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694614"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>A szinapszis munkaterület funkcióinak engedélyezése meglévő dedikált SQL-készleten (korábban SQL DW)

Az összes SQL-adattárház-ügyfél mostantól elérheti és használhatja a meglévő dedikált SQL-készletet (korábban SQL DW-példányt) a szinapszis Studióban és munkaterületen, az automatizálás, a kapcsolatok és az eszközök befolyásolása nélkül. Ez a cikk azt mutatja be, hogy egy meglévő Azure szinapszis Analytics-ügyfél hogyan építhet ki és bővítheti meglévő elemzési megoldását azáltal, hogy kihasználja a szinapszis munkaterületen és a studión keresztül elérhető új funkciók széles választékát.   

## <a name="experience"></a>Élmény
 
Most, hogy a szinapszis-munkaterület GA új képességgel rendelkezik a DW-portál Áttekintés szakaszában, amely lehetővé teszi, hogy egy szinapszis-munkaterületet hozzon létre a meglévő dedikált SQL-készlet (korábban SQL DW) példányok számára. Ez az új funkció lehetővé teszi a meglévő adattárház-példányokat futtató logikai kiszolgáló összekapcsolását egy új szinapszis-munkaterületre. A kapcsolat biztosítja, hogy az adott kiszolgálón tárolt összes adattárház elérhető legyen a munkaterületről és a studióból, és a szinapszis partner szolgáltatásaival (kiszolgáló nélküli SQL-készlettel, Apache Spark készlettel és ADF-mel) együtt használható. Megkezdheti az erőforrások elérését és használatát, amint a kiépítési lépések befejeződtek, és a Kapcsolódás az újonnan létrehozott munkaterülethez lett létrehozva.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="Csatlakoztatott szinapszis-munkaterület":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>A szinapszis munkaterület és a Studio funkcióinak használata egy dedikált SQL-készlet (korábban SQL DW) eléréséhez és használatához
 
A következő információk lesznek érvényesek, amikor dedikált SQL DW-t (korábban SQL DW) használ a szinapszis munkaterület-funkciókkal: 
- **SQL-képességek** A szinapszis munkaterület funkciójának engedélyezése után az összes SQL-funkció továbbra is a logikai SQL Server marad. A munkaterület engedélyezése után a kiszolgálóhoz való hozzáférés az SQL erőforrás-szolgáltatón keresztül továbbra is lehetséges lesz. Az összes felügyeleti funkció a munkaterületen keresztül kezdeményezhető, és a művelet az SQL-készleteket üzemeltető logikai SQL Serveron történik. A munkaterületek engedélyezésekor nem lesznek megszakítva a meglévő automatizálás, az eszközök és a kapcsolatok.  
- **Erőforrás áthelyezése**  Ha egy erőforrást egy olyan kiszolgálóra helyez át, amelyen a szinapszis munkaterület funkció engedélyezve lesz, a kiszolgáló és a munkaterület közötti kapcsolat megszakad, és többé nem fog tudni hozzáférni a meglévő dedikált SQL-készlethez (korábbi nevén SQL DW) tartozó példányokhoz a munkaterületről. A kapcsolódás megőrzésének biztosításához ajánlott, hogy mindkét erőforrás ugyanabban az előfizetésben és erőforráscsoporthoz legyen. 
- **Figyelés** A szinapszis studión keresztül elküldött SQL-kérelmek egy olyan munkaterületen, amelyen engedélyezve van a dedikált SQL-készlet (korábbi nevén SQL DW), megtekinthetők a figyelő központban. Az összes többi figyelési tevékenységhez lépjen Azure Portal dedikált SQL-készlet (korábban SQL DW) figyelése lehetőségre. 
- A fentiekben leírtak szerint a **biztonsági** és **hozzáférés-vezérlési vezérlők** az SQL Server és a dedikált SQL-készletek (korábban SQL DW-példányok) összes felügyeleti funkciója továbbra is a logikai SQL Serveren marad. A függvények közé tartozik a tűzfalszabályok kezelése, az Azure AD-rendszergazda beállítása, valamint a dedikált SQL-készletben (korábban SQL DW) található adatokhoz való hozzáférés-vezérlés. A következő lépéseket kell megtenni annak biztosításához, hogy a dedikált SQL-készlet (korábban SQL DW) elérhető legyen, és a szinapszis munkaterületen keresztül is használható legyen. A munkaterület szerepkör-tagságok nem biztosítanak hozzáférést a felhasználóknak a dedikált SQL-készlet (korábban SQL DW) példányokban lévő adathoz. A normál [SQL-hitelesítési](sql-data-warehouse-authentication.md) házirendeket követve biztosíthatja, hogy a felhasználók hozzáférhessenek a dedikált SQL-készlet (korábban SQL DW) példányaihoz a logikai kiszolgálón. Ha a dedikált SQL-készlet (korábban SQL DW) gazdagépének felügyelt identitása már hozzá van rendelve, akkor ez a felügyelt identitás neve ugyanaz lesz, mint a munkaterület által felügyelt identitás, amelyet a rendszer automatikusan hoz létre a munkaterület-partneri szolgáltatások (például az ADF-folyamatok) támogatásához.  Két azonos nevű felügyelt identitás létezhet egy csatlakoztatott forgatókönyvben. A felügyelt identitások az Azure AD-objektumazonosítók alapján különböztethetők meg, és az SQL-felhasználók objektum-azonosítókkal történő létrehozási funkciója hamarosan elérhető lesz.

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > A kapcsolódó munkaterület, a szinapszis Studio megjeleníti a dedikált készletek nevét, amely a felhasználó Azure-beli engedélyei alapján jelenik meg. A készletekben lévő objektumok nem lesznek elérhetők, ha a felhasználó nem rendelkezik az SQL-készletekre vonatkozó engedélyekkel. 

- **Hálózati biztonság** Ha a meglévő dedikált SQL-készleten (korábban SQL DW) engedélyezte a szinapszis munkaterületet, akkor az adatszivárgás elleni védelem engedélyezve van. Hozzon létre egy felügyelt magánhálózati végponti kapcsolódást a munkaterületről a logikai SQL Serverre. Hagyja jóvá a magánhálózati végpont kapcsolódási kérelmét, hogy engedélyezze a kiszolgáló és a munkaterület közötti kommunikációt.
- **Studió** Az **adatközpontban** található SQL-készletek a munkaterület engedélyezve dedikált SQL-készlet (korábban SQL DW) az adatközpontban található elemleírás segítségével azonosítható. 
- **Új DEDIKÁLT SQL-készlet létrehozása (korábban SQL DW)** Új dedikált SQL-készletek hozhatók létre a szinapszis munkaterületen és a Studióban, miután a munkaterület funkció engedélyezve lett, és az új készlet üzembe helyezése a logikai SQL-kiszolgálón történik. Az új erőforrások a Portálon és a Studióban fognak megjelenni a kiépítés befejezésekor.      

## <a name="next-steps"></a>Következő lépések
A [szinapszis munkaterület funkcióinak](workspace-connected-create.md) engedélyezése a meglévő dedikált SQL-készleten (korábban SQL DW)
