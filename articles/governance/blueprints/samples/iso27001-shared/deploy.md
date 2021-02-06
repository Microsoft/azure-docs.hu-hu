---
title: Az ISO 27001 megosztott szolgáltatások tervrajzi mintájának üzembe helyezése
description: Az ISO 27001 megosztott szolgáltatások tervezetének üzembe helyezési mintája, beleértve a tervrajz-összetevő paraméterének részleteit.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 8b28954b6145a9e6c1827107cf11aa85326dcc1f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627799"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Az ISO 27001 Shared Services Blueprint minta üzembe helyezése

Az Azure BluePrints ISO 27001 Shared Services Blueprint minta üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **Közzétettként**
> - A terv másolatának hozzárendelése egy meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free), mielőtt hozzákezd.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először a tervminta implementálásához hozzon létre egy új tervet a környezetében, és kiindulópontként használja a mintát.

1. A bal oldali panelen válassza a **Minden szolgáltatás** lehetőséget. Keresse meg és válassza ki a **Tervek** elemet.

1. A bal oldalon válassza az **Első lépések** oldalt, és válassza a _Terv létrehozása_ területen a **Létrehozás** lehetőséget.

1. Keresse meg az **ISO 27001: Shared Services** Blueprint mintát _más minták_ alatt, és válassza **a minta használata** lehetőséget.

1. Adja meg a tervminta _alapvető beállításait_:

   - **Terv neve**: adjon meg egy nevet az ISO 27001 Shared Services Blueprint minta példányának.
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
     - **Megosztott szolgáltatások alhálózati címének előtagja**: adja meg a CIDR-jelölési értéket a központilag telepített erőforrások egyesítéséhez.
     - **Megosztott szolgáltatások helye**: meghatározza, hogy az összetevők milyen helyen legyenek telepítve. Nem minden szolgáltatás érhető el minden helyen. Az ilyen szolgáltatásokat üzembe helyező összetevők paraméteres lehetőséget biztosítanak ahhoz, hogy az adott összetevő üzembe helyezése megtörténjen.
     - **Engedélyezett hely (szabályzat: terv kezdeményezése ISO 27001 esetén)**: érték, amely az erőforráscsoportok és az erőforrások megengedett helyét jelzi.
     - Virtuálisgép **-ügynökök log Analytics munkaterülete (szabályzat: az ISO 27001-es terv kezdeményezése)**: a munkaterület erőforrás-azonosítóját adja meg. Ez a paraméter egy `concat` függvényt használ az erőforrás-azonosító létrehozásához.

   - Összetevő paraméterei

     Az ebben a szakaszban megadott paraméterek arra az összetevőre érvényesek, amelyben meg lettek határozva. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza az oldal alján lévő **Hozzárendelés** lehetőséget. Létrejön a terv hozzárendelése, és megkezdődik az összetevő üzembe helyezése. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának ellenőrzéséhez nyissa meg a terv hozzárendelését.

> [!WARNING]
> Az Azure Blueprints szolgáltatás és a beépített tervminták **ingyenesek**. Az Azure-erőforrások [díjszabása termékalapú](https://azure.microsoft.com/pricing/). A [díjkalkulátorral](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a tervminta által üzembe helyezett erőforrások futtatásának költségét.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a terv összetevő-paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|\[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux-VM Scale sets (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája|Választható Az alapértelmezett érték _["None"]_.|
|\[Előzetes verzió \] : log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Linux operációs rendszert futtató virtuálisgép-rendszerképek listája|Választható Az alapértelmezett érték _["None"]_.|
|\[Előzetes verzió \] : log Analytics ügynök központi telepítése Windows VM Scale sets (VMSS)|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája|Választható Az alapértelmezett érték _["None"]_.|
|\[Előzetes verzió \] : log Analytics ügynök üzembe helyezése Windows rendszerű virtuális gépeken|Szabályzat-hozzárendelés|Nem kötelező: a hatókörbe felvenni kívánt Windows operációs rendszert futtató virtuálisgép-rendszerképek listája|Választható Az alapértelmezett érték _["None"]_.|
|Engedélyezett erőforrástípusok|Szabályzat-hozzárendelés|Engedélyezett erőforrástípusok|Az üzembe helyezhető erőforrástípusok listája. Ez a lista a megosztott szolgáltatásokban üzembe helyezett összes erőforrás-típusból tevődik össze.|
|Engedélyezett tárfiók-termékváltozatok|Szabályzat-hozzárendelés|Engedélyezett tárolási SKU-ket|A diagnosztikai naplók Storage-fiókjának SKU-ban engedélyezett listája. Az alapértelmezett érték a _következő: ["Standard_LRS"]_.|
|Engedélyezett virtuális gépek SKU-i|Szabályzat-hozzárendelés|A telepítendő virtuálisgép-SKU-ket tartalmazó lista. Az alapértelmezett érték a következő: _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Tervezet kezdeményezés ISO 27001|Szabályzat-hozzárendelés|A diagnosztikai naplók naplózására szolgáló erőforrástípusok|A naplózni kívánt erőforrástípusok listája, ha a diagnosztikai napló beállítása nincs engedélyezve. Elfogadható értékek találhatók [Azure monitor diagnosztikai naplók sémái](../../../../azure-monitor/platform/resource-logs-schema.md#service-specific-schemas)között.|
|Log Analytics erőforráscsoport|Erőforráscsoport|Name|**Zárolva** – összefűzi a **szervezet nevét** , `-sharedsvsc-log-rg` hogy az erőforráscsoport egyedi legyen.|
|Log Analytics erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Log Analytics sablon|Resource Manager-sablon|Szolgáltatási szint|Beállítja a Log Analytics munkaterület szintjét. Az alapértelmezett érték a _PerNode_.|
|Log Analytics sablon|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Log Analytics sablon|Resource Manager-sablon|Hely|A Log Analytics munkaterület létrehozásához használt régió. Az alapértelmezett érték az _USA 2. nyugati_ régiója.|
|Hálózati erőforráscsoport|Erőforráscsoport|Name|**Zárolva** – összefűzi a **szervezet nevét** , `-sharedsvcs-net-rg` hogy az erőforráscsoport egyedi legyen.|
|Hálózati erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Azure Firewall sablon|Resource Manager-sablon|Azure tűzfal magánhálózati IP-címe|Az [Azure tűzfal](../../../../firewall/overview.md)magánhálózati IP-címének konfigurálása. Ez az érték a megosztott szolgáltatások alhálózatán alapértelmezett útválasztási táblázatként is használatos. A **Azure Firewall alhálózati** CIDR definiált jelölésnek kell lennie. Az alapértelmezett érték a _10.0.4.4_.|
|Azure Firewall sablon|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Hálózati biztonsági csoport sablonja|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Virtual Network címzési előtag|A virtuális hálózat CIDR-jelölése. Az alapértelmezett érték a _10.0.0.0/16_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Virtual Network DDoS elleni védelem engedélyezése|A DDoS Protection beállítása a virtuális hálózathoz. Az alapértelmezett érték _true (igaz_).|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Megosztott szolgáltatások alhálózati címének előtagja|A megosztott szolgáltatások alhálózatának CIDR-jelölése. Az alapértelmezett érték a _10.0.0.0/24_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|DMZ alhálózati címének előtagja|A DMZ-alhálózat CIDR-jelölése. Az alapértelmezett érték a _10.0.1.0/24_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Alhálózat Application Gateway-címének előtagja|Az Application Gateway-alhálózat CIDR-jelölése. Az alapértelmezett érték a _10.0.2.0/24_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Virtual Network átjáró alhálózati címének előtagja|A virtuális hálózati átjáró alhálózatának CIDR-jelölése. Az alapértelmezett érték a _10.0.3.0/24_.|
|Virtual Network és útválasztási táblázat sablonja|Resource Manager-sablon|Alhálózat Azure Firewall-címének előtagja|Az [Azure tűzfal](../../../../firewall/overview.md) alhálózatának CIDR-jelölése. Tartalmaznia kell az **Azure tűzfal magánhálózati IP-** paraméterét.|
|Key Vault erőforráscsoport|Erőforráscsoport|Name|**Zárolva** – összefűzi a **szervezet nevét** , `-sharedsvcs-kv-rg` hogy az erőforráscsoport egyedi legyen.|
|Key Vault erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Key Vault sablon|Resource Manager-sablon|Jumpbox-rendszergazdai Felhasználónév|A Jumpbox tartozó Felhasználónév. Meg kell egyeznie a **Jumpbox-sablonban** szereplő tulajdonság értékével. Az alapértelmezett érték a _JB-Admin-User_.|
|Key Vault sablon|Resource Manager-sablon|Jumpbox rendszergazdai SSH-kulcs vagy jelszó|A Jumpbox lévő fiók kulcsa vagy jelszava. Meg kell egyeznie a **Jumpbox-sablonban** szereplő tulajdonság értékével. Nincs alapértelmezett érték, és nem hagyható üresen.|
|Key Vault sablon|Resource Manager-sablon|Tartományi rendszergazda felhasználóneve|A Active Directory virtuális gép eléréséhez és más virtuális gépek tartományhoz való csatlakoztatásához használt Felhasználónév. Meg kell egyeznie a **tartományi rendszergazda felhasználói** tulajdonság értékével **Active Directory Domain Services sablonban**. Az alapértelmezett érték a _tartományi rendszergazda – felhasználó_.|
|Key Vault sablon|Resource Manager-sablon|Tartományi rendszergazda jelszava|A tartományi rendszergazda felhasználói jelszava. Nincs alapértelmezett érték, és nem hagyható üresen.|
|Key Vault sablon|Resource Manager-sablon|HRE objektum azonosítója|Annak a fióknak a HRE-azonosítója, amelyhez hozzáférést kell adni a Key Vault-példányhoz. Nincs alapértelmezett érték, és nem hagyható üresen. Ha meg szeretné keresni ezt az értéket a Azure Portal, keresse meg és válassza a "felhasználók" lehetőséget a _szolgáltatások_ területen. A _Name (név_ ) mező használatával szűrheti a fióknevet, és kiválaszthatja a fiókot. A _felhasználói profil_ lapon válassza az _objektumazonosító_ melletti "kattintson ide a másoláshoz" ikont.  |
|Key Vault sablon|Resource Manager-sablon|Napló megőrzési ideje (nap)|Adatok megőrzése napokban. Az alapértelmezett érték a _365_.|
|Key Vault sablon|Resource Manager-sablon|Key Vault SKU|A létrehozott Key Vault SKU-jának meghatározása. Az alapértelmezett érték a _prémium_.|
|Jumpbox erőforráscsoport|Erőforráscsoport|Name|**Zárolva** – összefűzi a **szervezet nevét** , `-sharedsvcs-jb-rg` hogy az erőforráscsoport egyedi legyen.|
|Jumpbox erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Jumpbox-sablon|Resource Manager-sablon|Jumpbox-rendszergazdai Felhasználónév|A Jumpbox virtuális gépek eléréséhez használt Felhasználónév. Meg kell egyeznie a tulajdonság értékével **Key Vault sablonban**. Az alapértelmezett érték a _JB-Admin-User_.|
|Jumpbox-sablon|Resource Manager-sablon|Jumpbox rendszergazdai jelszava (Key Vault erőforrás-azonosító)|A Key Vault erőforrás-azonosítója. Használja a "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" kifejezést, és cserélje le az `{subscriptionId}` előfizetés-azonosítóra és `{orgName}` a **szervezet neve** Blueprint paraméterre.|
|Jumpbox-sablon|Resource Manager-sablon|Jumpbox rendszergazdai jelszava (Key Vault Secret Name)|A Jumpbox-rendszergazda felhasználóneve. Az értéknek meg kell egyeznie **Key Vault sablon** tulajdonságában a **Jumpbox rendszergazdai felhasználóneve**.|
|Jumpbox-sablon|Resource Manager-sablon|Jumpbox operációs rendszer|Meghatározza a Jumpbox virtuális gép operációs rendszerét. Az alapértelmezett érték a _Windows_.|
|Active Directory Domain Services erőforráscsoport|Erőforráscsoport|Name|**Zárolva** – összefűzi a **szervezet nevét** , `-sharedsvcs-adds-rg` hogy az erőforráscsoport egyedi legyen.|
|Active Directory Domain Services erőforráscsoport|Erőforráscsoport|Hely|**Zárolt** – a terv paramétert használja.|
|Active Directory Domain Services sablon|Resource Manager-sablon|Tartományi rendszergazda felhasználóneve|A Jumpbox HOZZÁADÁSához használt Felhasználónév. Meg kell egyeznie a tulajdonság értékével **Key Vault sablonban**. Az alapértelmezett érték a _Add-admin-User_.|
|Active Directory Domain Services sablon|Resource Manager-sablon|Tartományi rendszergazdai jelszó (Key Vault erőforrás-azonosító)|A Key Vault erőforrás-azonosítója. Használja a "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" kifejezést, és cserélje le az `{subscriptionId}` előfizetés-azonosítóra és `{orgName}` a **szervezet neve** Blueprint paraméterre.|
|Active Directory Domain Services sablon|Resource Manager-sablon|Tartományi rendszergazda jelszava (Key Vault titkos kulcs neve)|A tartományi rendszergazda felhasználóneve. Az értéknek meg kell egyeznie **Key Vault sablon** tulajdonságának **tartományi rendszergazdai felhasználóneve**.|
|Active Directory Domain Services sablon|Resource Manager-sablon|Tartománynév|A minta által létrehozott Active Directory neve. Az alapértelmezett érték a _contoso.com_.|
|Active Directory Domain Services sablon|Resource Manager-sablon|Tartományi rendszergazda felhasználó|A rendszergazdai AD-fiókhoz tartozó Felhasználónév és az eszközök AD-tartományhoz való csatlakoztatása. Meg kell egyeznie az **ad admin username** tulajdonság értékével **Key Vault sablonban**. Az alapértelmezett érték a _tartományi rendszergazda – felhasználó_.|
|Active Directory Domain Services sablon|Resource Manager-sablon|Tartományi rendszergazda jelszava|Adja meg a Jelszó tárolásának Key Vault részleteit. Nincs alapértelmezett érték, és nem hagyható üresen.|

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekintette az ISO 27001 Shared Services Blueprint-minta üzembe helyezésének lépéseit, tekintse meg az alábbi cikkeket az architektúra és a vezérlés leképezésének megismeréséhez:

> [!div class="nextstepaction"]
> [ISO 27001 megosztott szolgáltatások tervrajza – áttekintés](./index.md) 
>  [ISO 27001 megosztott szolgáltatások tervrajza – vezérlés leképezése](./control-mapping.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
