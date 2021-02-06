---
title: ISO 27001-es bevezetési/SQL munkaterhelés-tervezeti minta üzembe helyezése
description: Az ISO 27001 App Service Environment/SQL Database munkaterhelés-tervezet lépéseinek üzembe helyezése minta, beleértve a tervrajz-összetevő paraméterének részleteit.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 60867c117c9f1fda6f8ba33333c19a7bd764d219
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627483"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Az ISO 27001 App Service Environment/SQL Database munkaterhelés tervrajzi minta üzembe helyezése

Az Azure-tervezetek üzembe helyezéséhez ISO 27001 App Service Environment/SQL Database munkaterhelés-tervezet mintáját a következő lépésekkel kell elvégezni:

> [!div class="checklist"]
> - Az [ISO 27001 Shared Services](../iso27001-shared/index.md) Blueprint minta üzembe helyezése
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free), mielőtt hozzákezd.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001 Shared Services Blueprint minta üzembe helyezése

A tervrajzi minta üzembe helyezése előtt az [ISO 27001 Shared Services](../iso27001-shared/index.md) Blueprint mintát kell központilag telepíteni a cél előfizetésre. Az ISO 27001 Shared Services Blueprint minta sikeres üzembe helyezése nélkül ez a tervrajz-minta hiányzó infrastrukturális függőségeket jelez, és az üzembe helyezés során sikertelen lesz.

> [!IMPORTANT]
> Ezt a tervrajz-mintát ugyanahhoz az előfizetéshez kell rendelni, mint az [ISO 27001 Shared Services](../iso27001-shared/index.md) Blueprint minta.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg az **ISO 27001: beadási/SQL munkaterhelés** tervrajz mintát _más minták_ alatt, és válassza **a minta használata** lehetőséget.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet az ISO 27001-es vagy az SQL-alapú számítási terv mintájának másolatához.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza az oldal tetején lévő _Összetevők_ lapot, vagy az oldal alján lévő **Következő: Összetevők** lehetőséget.

1. Tekintse át a tervmintát alkotó összetevők listáját. Számos összetevőnek olyan paraméterei vannak, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor végzett a tervminta áttekintésével.

## <a name="publish-the-sample-copy"></a>Ugyanazon másolat közzététele

A tervminta másolata létrejött a környezetében. **Piszkozat** módban jött létre, és **közzé kell tenni**, mielőtt hozzárendelhetné és üzembe helyezhetné. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás az ISO 27001 szabványból is áthelyezhető.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki az oldal tetején található **Terv közzététele** lehetőséget. A jobb oldalt megjelenő új oldalon adja meg a tervminta másolatának **verzióját**. Ez a tulajdonság akkor hasznos, ha később módosításokat végez. Adjon meg olyan **módosítási megjegyzéseket** , mint az "első verzió, amely az ISO 27001 Blueprint Sample-ből lett közzétéve." Ezután válassza a lap alján található **Közzététel** lehetőséget.

## <a name="assign-the-sample-copy"></a>Ugyanazon másolat hozzárendelése

Miután a tervezet mintájának **közzététele** sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ebben a lépésben adja meg a paramétereket, hogy a tervminta másolatának minden üzemelő példánya egyedi legyen.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. Válassza a bal oldali **Tervdefiníciók** oldalt. A szűrőkkel keresse meg a tervminta másolatát, majd válassza ki.

1. Válassza ki a Tervdefiníció oldal tetején található **Terv hozzárendelése** lehetőséget.

1. Adja meg a tervhozzárendelés paraméterértékeit:

   - Alapvető beállítások

     - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, mindegyikhez létrejön egy hozzárendelés a beírt paraméterekkel.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Módosítsa igény szerint, vagy hagyja meg az eredetit.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További információ: [felügyelt identitások az Azure-erőforrásokhoz](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezetének megfelelő tervzárolási beállítást. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Tervparaméterek

     Az ebben a szakaszban meghatározott paramétereket a tervdefinícióban lévő számos összetevő használja a konzisztencia érdekében.

     - **Szervezet neve**: adjon meg egy rövid nevet a szervezetnek. Ez a tulajdonság elsősorban az erőforrások elnevezésére használatos.
     - **Megosztott szolgáltatás előfizetési azonosítója**: előfizetés azonosítója, ahol az [ISO 27001 Shared Services](../iso27001-shared/index.md) Blueprint minta van hozzárendelve.
     - **Alapértelmezett alhálózat-címzési előtag**: a virtuális hálózat alapértelmezett alhálózatának CIDR-jelölése.
       Az alapértelmezett érték a _10.1.0.0/16_.
     - **Munkaterhelés helye**: meghatározza, hogy az összetevők milyen helyen legyenek telepítve. Nem minden szolgáltatás érhető el minden helyen. Az ilyen szolgáltatásokat üzembe helyező összetevők paraméteres lehetőséget biztosítanak ahhoz, hogy az adott összetevő üzembe helyezése megtörténjen.

   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget. Létrejön a terv hozzárendelése, és megkezdődik az összetevő üzembe helyezése. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg a terv hozzárendelését.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervminták **ingyenesek**. Az Azure-erőforrások [díjszabása termékalapú](https://azure.microsoft.com/pricing/). A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a tervminta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a terv összetevő-paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Log Analytics erőforráscsoport|Erőforráscsoport|Name|**Zárolva** – összefűzi a **szervezet nevét** , `-workload-log-rg` hogy az erőforráscsoport egyedi legyen.|
|Log Analytics erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Log Analytics sablon|Resource Manager-sablon|Szolgáltatási szint|Beállítja a Log Analytics munkaterület szintjét. Az alapértelmezett érték a _PerNode_.|
|Log Analytics sablon|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Log Analytics sablon|Resource Manager-sablon|Hely|A Log Analytics munkaterület létrehozásához használt régió. Az alapértelmezett érték az _USA 2. nyugati_ régiója.|
|Hálózati erőforráscsoport|Erőforráscsoport|Name|**Zárolva** – összefűzi a **szervezet nevét** , `-workload-net-rg` hogy az erőforráscsoport egyedi legyen.|
|Hálózati erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Hálózati biztonsági csoport sablonja|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Azure tűzfal magánhálózati IP-címe|Az [Azure tűzfal](../../../../firewall/overview.md)magánhálózati IP-címének konfigurálása. Az _ISO 27001: Shared Services_ összetevő-paraméterben definiált CIDR-jelölésnek kell lennie **Azure Firewall alhálózati címek előtagja**. Az alapértelmezett érték a _10.0.4.4_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Megosztott szolgáltatások előfizetés-azonosítója|A számítási feladatok és a megosztott szolgáltatások közötti VNET-társítást engedélyező érték.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Virtual Network címzési előtag|A virtuális hálózat CIDR-jelölése. Az alapértelmezett érték a _10.1.0.0/16_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Alhálózat alapértelmezett címének előtagja|A virtuális hálózat alapértelmezett alhálózatának CIDR-jelölése. Az alapértelmezett érték a _10.1.0.0/16_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|IP-cím HOZZÁADÁSával|Az első virtuális gép IP-címe. Ezt az értéket használja a rendszer egyéni VNET DNS-ként.|
|Key Vault erőforráscsoport|Erőforráscsoport|Name|**Zárolva** – összefűzi a **szervezet nevét** , `-workload-kv-rg` hogy az erőforráscsoport egyedi legyen.|
|Key Vault erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Key Vault sablon|Resource Manager-sablon|HRE objektum azonosítója|Annak a fióknak a HRE-azonosítója, amelyhez hozzáférést kell adni a Key Vault-példányhoz. Nincs alapértelmezett érték, és nem hagyható üresen. Ha meg szeretné keresni ezt az értéket a Azure Portal, keresse meg és válassza a "felhasználók" lehetőséget a _szolgáltatások_ területen. A _Name (név_ ) mező használatával szűrheti a fióknevet, és kiválaszthatja a fiókot. A _felhasználói profil_ lapon válassza az _objektumazonosító_ melletti "kattintson ide a másoláshoz" ikont.|
|Key Vault sablon|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Key Vault sablon|Resource Manager-sablon|Key Vault SKU|A létrehozott Key Vault SKU-jának meghatározása. Az alapértelmezett érték a _prémium_.|
|Key Vault sablon|Resource Manager-sablon|Azure SQL Server-rendszergazdai Felhasználónév|Az Azure SQL Server eléréséhez használt Felhasználónév. Meg kell egyeznie a tulajdonság értékével **Azure SQL Database sablonban**. Az alapértelmezett érték az _SQL-Admin-User_.|
|Azure SQL Database erőforráscsoport|Erőforráscsoport|Name|**Zárolva** – összefűzi a **szervezet nevét** , `-workload-azsql-rg` hogy az erőforráscsoport egyedi legyen.|
|Azure SQL Database erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Azure SQL Database sablon|Resource Manager-sablon|Azure SQL Server-rendszergazdai Felhasználónév|Az Azure-SQL Server felhasználóneve. Meg kell egyeznie a tulajdonság értékével **Key Vault sablonban**. Az alapértelmezett érték az _SQL-Admin-User_.|
|Azure SQL Database sablon|Resource Manager-sablon|Azure SQL Server rendszergazdai jelszó (Key Vault erőforrás-azonosító)|A Key Vault erőforrás-azonosítója. Használja a "/subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" kifejezést, és cserélje le az `{subscriptionId}` előfizetés-azonosítóra és `{orgName}` a **szervezet neve** Blueprint paraméterre.|
|Azure SQL Database sablon|Resource Manager-sablon|Azure SQL Server rendszergazdai jelszó (Key Vault titkos kulcs neve)|A SQL Server-rendszergazda felhasználóneve. Az értéknek meg kell egyeznie **Key Vault sablon** tulajdonság **Azure SQL Server rendszergazdai Felhasználónév**.|
|Azure SQL Database sablon|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Azure SQL Database sablon|Resource Manager-sablon|HRE rendszergazdai objektum azonosítója|Azon felhasználó HRE-azonosítója, amely Active Directory-rendszergazdaként lesz hozzárendelve. Nincs alapértelmezett érték, és nem hagyható üresen. Ha meg szeretné keresni ezt az értéket a Azure Portal, keresse meg és válassza a "felhasználók" lehetőséget a _szolgáltatások_ területen. A _Name (név_ ) mező használatával szűrheti a fióknevet, és kiválaszthatja a fiókot. A _felhasználói profil_ lapon válassza az _objektumazonosító_ melletti "kattintson ide a másoláshoz" ikont.|
|Azure SQL Database sablon|Resource Manager-sablon|HRE-rendszergazdai bejelentkezés|Jelenleg a Microsoft-fiókok (például a live.com vagy a outlook.com) nem állíthatók be rendszergazdaként. Csak a szervezeten belüli felhasználók és biztonsági csoportok állíthatók be rendszergazdaként. Nincs alapértelmezett érték, és nem hagyható üresen. Ha meg szeretné keresni ezt az értéket a Azure Portal, keresse meg és válassza a "felhasználók" lehetőséget a _szolgáltatások_ területen. A _Name (név_ ) mező használatával szűrheti a fióknevet, és kiválaszthatja a fiókot. A _felhasználói profil_ lapon másolja a _felhasználónevet_.|
|App Service Environment erőforráscsoport|Erőforráscsoport|Name|**Zárolva** – összefűzi a **szervezet nevét** , `-workload-ase-rg` hogy az erőforráscsoport egyedi legyen.|
|App Service Environment erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|App Service Environment sablon|Resource Manager-sablon|Tartománynév|A minta által létrehozott Active Directory neve. Az alapértelmezett érték a _contoso.com_.|
|App Service Environment sablon|Resource Manager-sablon|Bemutató hely|App Service Environment hely. Az alapértelmezett érték az _USA 2. nyugati_ régiója.|
|App Service Environment sablon|Resource Manager-sablon|Application Gateway napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az ISO 27001 App Service Environment/SQL Database munkaterhelés-tervezet üzembe helyezésének lépéseit, tekintse meg az alábbi cikkeket az architektúra és a vezérlés leképezésének megismeréséhez:

> [!div class="nextstepaction"]
> [ISO 27001 app Service Environment/SQL Database munkaterhelés tervezete – áttekintés](./index.md) 
>  [ISO 27001 app Service Environment/SQL Database munkaterhelés-tervezet – vezérlés leképezése](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
