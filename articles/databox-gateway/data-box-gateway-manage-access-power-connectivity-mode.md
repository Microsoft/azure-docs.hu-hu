---
title: Eszköz-hozzáférés, energiaellátás és kapcsolódási mód Azure Data Box Gateway
description: Útmutató az adatok Azure-ba való átvitelét segítő Azure Data Box Gateway eszköz hozzáférésének, teljesítményének és csatlakozási módjának kezeléséhez
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: c4e2894d193309c169adbea96491e0754d479a8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786808"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>A Azure Data Box Gateway hozzáférésének, teljesítményének és kapcsolati módjának kezelése

Ez a cikk azt ismerteti, hogyan kezelhető a Azure Data Box Gateway hozzáférési, energiagazdálkodási és kapcsolódási módja. Ezek a műveletek a helyi webes felületen vagy a Azure Portalon keresztül hajthatók végre. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Eszközök hozzáférésének kezelése
> * Kapcsolati mód kezelése
> * A Power kezelése

## <a name="manage-device-access"></a>Eszközök hozzáférésének kezelése

A Data Box Gateway eszköz hozzáférését az eszköz jelszavának használatával szabályozhatja. A jelszó a helyi webes felületen keresztül módosítható. Alaphelyzetbe állíthatja az eszköz jelszavát is a Azure Portalban.

### <a name="change-device-password"></a>Az eszköz jelszavának módosítása

Az eszköz jelszavának módosításához kövesse az alábbi lépéseket a helyi felhasználói felületen.

1. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > jelszavának módosítása** elemre.
2. Adja meg az aktuális jelszót, majd az új jelszót. A megadott jelszónak 8 és 16 karakter közöttinek kell lennie. A jelszónak legalább hármat tartalmaznia kell a következő karakterek közül: nagybetűk, kisbetűk, számok és speciális karakterek. Erősítse meg az új jelszót.

    ![Change password](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Kattintson a **jelszó módosítása** gombra.
 
### <a name="reset-device-password"></a>Eszköz jelszavának alaphelyzetbe állítása

A munkafolyamat alaphelyzetbe állítása nem igényli, hogy a felhasználó felidézze a régi jelszót, és akkor is hasznos, ha a jelszó elvész. Ezt a munkafolyamatot a Azure Portal hajtja végre.

1. A Azure Portal lépjen az **áttekintés > rendszergazdai jelszó visszaállítása** elemre.

    ![Új jelszó létrehozása](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Adja meg az új jelszót, majd erősítse meg. A megadott jelszónak 8 és 16 karakter közöttinek kell lennie. A jelszónak legalább hármat tartalmaznia kell a következő karakterek közül: nagybetűk, kisbetűk, számok és speciális karakterek. Kattintson az **Alaphelyzetbe állítás** gombra.

    ![2. jelszó alaphelyzetbe állítása](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Erőforrás-hozzáférés kezelése

Azure Data Box Gateway-, IoT Hub-és Azure Storage-erőforrás létrehozásához a közreműködőként vagy annál magasabb szintű jogosultságokkal kell rendelkeznie az erőforráscsoport szintjén. Szükség van a megfelelő erőforrás-szolgáltatók regisztrálására is. Az aktiválási kulcsot és a hitelesítő adatokat is tartalmazó műveletek esetében a Azure Active Directory Graph APIra vonatkozó engedélyek is szükségesek. Ezeket a következő szakaszokban ismertetjük.

### <a name="manage-microsoft-graph-api-permissions"></a>Microsoft Graph API-engedélyek kezelése

Az eszközhöz tartozó aktiválási kulcs létrehozásakor, illetve a hitelesítő adatokat igénylő műveletek elvégzéséhez engedélyekkel kell rendelkeznie Microsoft Graph API-hoz. A hitelesítő adatokat igénylő műveletek a következők lehetnek:

-  Megosztás létrehozása társított Storage-fiókkal.
-  Hozzon létre egy felhasználót, aki hozzáfér az eszközön található megosztásokhoz.

Rendelkeznie kell `User` hozzáféréssel a Active Directory bérlőn, hogy Ön is hozzáférhessen `Read all directory objects` . A vendég felhasználó nem rendelkezik jogosultsággal a következőhöz: `Read all directory objects` . Ha vendég, olyan műveleteket, mint például az aktiválási kulcs generálása, a megosztás létrehozása az eszközön, és a felhasználó létrehozása sikertelen lesz.

A felhasználók Microsoft Graph API-hoz való hozzáférésének biztosításával kapcsolatos további információkért lásd: [Microsoft Graph engedélyek referenciája](/graph/permissions-reference).

### <a name="register-resource-providers"></a>Erőforrás-szolgáltatók regisztrálása

Ha erőforrást szeretne kiépíteni az Azure-ban (a Azure Resource Manager modellben), szüksége lesz egy erőforrás-szolgáltatóra, amely támogatja az adott erőforrás létrehozását. Ha például egy virtuális gépet szeretne kiépíteni, az előfizetésben elérhető "Microsoft. számítási" erőforrás-szolgáltatónak kell lennie.
 
Az erőforrás-szolgáltatók az előfizetés-szinten vannak regisztrálva. Alapértelmezés szerint minden új Azure-előfizetés előre regisztrálva valamelyik gyakran használt erőforrás-szolgáltatónál. A "Microsoft. DataBoxEdge" erőforrás-szolgáltatója nem szerepel a listán.

Az előfizetési szinten nem kell hozzáférési engedélyeket megadnia ahhoz, hogy a felhasználók olyan erőforrásokat hozzanak létre, mint például a "Microsoft. DataBoxEdge" azon erőforráscsoportok, amelyekhez tulajdonosi jogosultságuk van, feltéve, hogy az adott erőforrásokhoz tartozó erőforrás-szolgáltatók már regisztrálva vannak.

Mielőtt megpróbál létrehozni egy erőforrást, győződjön meg arról, hogy az erőforrás-szolgáltató regisztrálva van az előfizetésben. Ha az erőforrás-szolgáltató nincs regisztrálva, meg kell győződnie arról, hogy az új erőforrást létrehozó felhasználónak elegendő jogosultsága van ahhoz, hogy regisztrálja a szükséges erőforrás-szolgáltatót az előfizetési szinten. Ha még nem tette meg, akkor a következő hibaüzenet jelenik meg:

*Az előfizetés \<Subscription name> nem rendelkezik engedéllyel az erőforrás-szolgáltató (k) regisztrálásához: Microsoft. DataBoxEdge.*


A jelenlegi előfizetésben regisztrált erőforrás-szolgáltatók listájának lekéréséhez futtassa a következő parancsot:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Data Box Gateway eszköz esetében regisztrálni kell `Microsoft.DataBoxEdge` . A regisztráláshoz `Microsoft.DataBoxEdge` az előfizetés rendszergazdájának a következő parancsot kell futtatnia:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Az erőforrás-szolgáltatók regisztrálásával kapcsolatos további információkért lásd: [hibák elhárítása az erőforrás-szolgáltató regisztrálásához](../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="manage-connectivity-mode"></a>Kapcsolati mód kezelése

Az alapértelmezett normál mód mellett az eszköz részlegesen leválasztott vagy leválasztott módban is futtatható:

- **Részben leválasztva** – ebben a módban az eszköz nem tud adatokkal feltölteni a megosztásokat. Azonban a Azure Portal használatával felügyelhető.

    Ez a mód általában a hálózati sávszélesség-használat minimalizálására szolgál a mért műhold-hálózaton. Az eszköz-figyelési műveletek esetében a minimális hálózati fogyasztás továbbra is előfordulhat.

- **Leválasztva** – ebben a módban az eszköz teljesen le van választva a felhőből, és a Felhőbeli feltöltések és letöltések is le vannak tiltva. Az eszközt csak a helyi webes felületen keresztül lehet felügyelni.

    Ez a mód általában akkor használatos, ha az eszközt offline állapotba kívánja helyezni.

Az eszköz üzemmódjának módosításához kövesse az alábbi lépéseket:

1. Az eszköz helyi webes FELÜLETén nyissa meg a **konfigurációs > a felhő beállításait**.
2. Tiltsa le a **Felhőbeli feltöltést és letöltést**.
3. Az eszköz részlegesen leválasztott módban való futtatásához engedélyezze **Azure Portal felügyeletet**.

    ![Kapcsolati mód](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Az eszköz leválasztott módban való futtatásához tiltsa le a **Azure Portal felügyeletet**. Most az eszközt csak a helyi webes felületen keresztül lehet felügyelni.

    ![2. kapcsolati mód](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>A Power kezelése

A virtuális eszköz a helyi webes felhasználói felületen állítható le vagy indítható újra. Javasoljuk, hogy az eszköz újraindítása előtt a megosztások offline állapotba kerüljön a gazdagépen, majd az eszközön. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét.

1. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > energiagazdálkodási beállítások** elemre.
2. A kívánt művelettől függően kattintson a **Leállítás** vagy az **Újraindítás** lehetőségre.

    ![Energiaellátási beállítások](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz.

> [!NOTE]
> Ha leállítja a virtuális eszközt, akkor az eszközt a hipervizor-kezelői felületen keresztül kell elindítania.