---
title: Azure Stack Edge Pro GPU eszköz-hozzáférési, energiagazdálkodási és csatlakozási mód | Microsoft Docs
description: Útmutatás az Azure-ba való adatátvitelt segítő Azure Stack Edge Pro GPU-eszköz hozzáférés-, energiagazdálkodási és csatlakozási módjának kezeléséhez
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 39990a557315c3fcc79f2b9dab59f25f758ab2bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443114"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro-gpu"></a>A Azure Stack Edge Pro GPU hozzáférés-, energiagazdálkodási és csatlakozási módjának kezelése

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan kezelhető a Azure Stack Edge Pro és a GPU-eszköz hozzáférésének, teljesítményének és csatlakozási módjának kezelése. Ezek a műveletek a helyi webes felületen vagy a Azure Portalon keresztül hajthatók végre.

Ez a cikk Azure Stack Edge Pro GPU-ra, Azure Stack Edge Pro R-re és Azure Stack Edge mini R-eszközökre vonatkozik.


Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eszközök hozzáférésének kezelése
> * Erőforrás-hozzáférés kezelése
> * Kapcsolati mód kezelése
> * A Power kezelése


## <a name="manage-device-access"></a>Eszközök hozzáférésének kezelése

Az Azure Stack Edge Pro-eszköz hozzáférését az eszköz jelszavának használatával szabályozhatja. A jelszó a helyi webes felületen keresztül módosítható. Alaphelyzetbe állíthatja az eszköz jelszavát is a Azure Portalban.

Az eszközökön található adatokhoz való hozzáférést a titkosítást a REST-kulcsok is ellenőrzik.

### <a name="change-device-password"></a>Az eszköz jelszavának módosítása

Az eszköz jelszavának módosításához kövesse az alábbi lépéseket a helyi felhasználói felületen.

1. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > jelszavára**.
2. Adja meg az aktuális jelszót, majd az új jelszót. A megadott jelszónak 8 és 16 karakter közöttinek kell lennie. A jelszónak legalább hármat tartalmaznia kell a következő karakterek közül: nagybetűk, kisbetűk, számok és speciális karakterek. Erősítse meg az új jelszót.

    ![Change password](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/change-password-1.png)

3. Válassza a **jelszó módosítása** lehetőséget.
 
### <a name="reset-device-password"></a>Eszköz jelszavának alaphelyzetbe állítása

A munkafolyamat alaphelyzetbe állítása nem igényli, hogy a felhasználó felidézze a régi jelszót, és akkor is hasznos, ha a jelszó elvész. Ezt a munkafolyamatot a Azure Portal hajtja végre.

1. A Azure Portal lépjen az **áttekintés > rendszergazdai jelszó visszaállítása** elemre.

    ![Képernyőfelvétel: az eszköz alaphelyzetbe állítása jelszó kiválasztásával.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/reset-password-1.png)


2. Adja meg az új jelszót, majd erősítse meg. A megadott jelszónak 8 és 16 karakter közöttinek kell lennie. A jelszónak legalább hármat tartalmaznia kell a következő karakterek közül: nagybetűk, kisbetűk, számok és speciális karakterek. Válassza az **Alaphelyzetbe állítás** lehetőséget.

    ![2. jelszó alaphelyzetbe állítása](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

### <a name="manage-access-to-device-data"></a>Az eszközre vonatkozó adathozzáférés kezelése

Az Azure Stack Edge Pro R és a Azure Stack Edge mini R-eszközök esetében az eszköz adataihoz való hozzáférést az eszköz meghajtókon található titkosítást használó kulcsok vezérlik. Miután sikeresen konfigurálta az eszközt a titkosításhoz, az eszköz helyi felhasználói felületén elérhetővé válik a REST-nyugalmi kulcsok elforgatása lehetőség. 

Ezzel a művelettel módosíthatja a BitLocker-kötetek kulcsait, `HcsData` `HcsInternal` valamint az eszközön lévő összes öntitkosítási meghajtót.

Az alábbi lépéseket követve elforgathatja a titkosító Rest-kulcsokat.

1. Az eszköz helyi felhasználói felületén nyissa meg az **első lépések** lapot. A **Biztonság** csempén válassza a **titkosítás – Rest: a kulcsok elforgatása** lehetőséget. Ez a beállítás csak akkor érhető el, ha sikeresen beállította a REST-alapú titkosítási kulcsokat.

    ![Válassza a kulcsok elforgatása titkosításhoz – az első lépések oldalon](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-1.png)

1. Használhatja a saját BitLocker-kulcsait, vagy használhatja a rendszer által generált kulcsokat is.  

    A saját kulcs megadásához adjon meg egy 32 karakter hosszú Base-64 kódolású karakterláncot. A bemenet a következőhöz hasonló lesz, mint amikor az első alkalommal konfigurálja a titkosítást.

    ![Saját titkosítási kulcs használata](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-2.png)

    A rendszer által generált kulcsot is használhat.

    ![Rendszer által generált titkosítási kulcs használata](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-3.png)

1. Kattintson az **Alkalmaz** gombra. A rendszer elforgatja a kulcs-védőket.

    ![Az új titkosítás – nyugalmi kulcs alkalmazása](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-4.png)

1. Amikor a rendszer kéri a kulcsfájl letöltését és mentését, válassza a **letöltés és folytatás** lehetőséget. 

    ![A kulcsfájl letöltése és folytatása](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-5.png)

    Mentse a `.json` kulcsfájl biztonságos helyen. Ez a fájl az eszköz lehetséges jövőbeli helyreállításának megkönnyítésére szolgál.

    ![Képernyőfelvétel: az eszköz jelszavának alaphelyzetbe állítása párbeszédpanel.](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Erőforrás-hozzáférés kezelése

A Azure Stack Edge/Data Box Gateway, a IoT Hub és az Azure Storage-erőforrás létrehozásához az engedélyek közreműködőiként vagy magasabb szintűnek kell lennie az erőforráscsoport szintjén. Szükség van a megfelelő erőforrás-szolgáltatók regisztrálására is. Az aktiválási kulcsot és a hitelesítő adatokat is tartalmazó műveletek esetében a Microsoft Graph API-ra vonatkozó engedélyek is szükségesek. Ezeket a következő szakaszokban ismertetjük. 

### <a name="manage-microsoft-graph-api-permissions"></a>Microsoft Graph API-engedélyek kezelése

Az Azure Stack Edge Pro-eszköz aktiválási kulcsának létrehozásakor, illetve a hitelesítő adatokat igénylő műveletek elvégzéséhez engedélyekkel kell rendelkeznie Azure Active Directory Graph API. A hitelesítő adatokat igénylő műveletek a következők lehetnek:

-  Megosztás létrehozása társított Storage-fiókkal.
-  Hozzon létre egy felhasználót, aki hozzáfér az eszközön található megosztásokhoz.

Ahhoz, hogy a rendszer `User` képes legyen hozzáférni Active Directory bérlőhöz `Read all directory objects` . Nem lehet vendég felhasználó, mert nem rendelkezik engedéllyel a szolgáltatáshoz `Read all directory objects` . Ha vendég, akkor az olyan műveleteket, mint például az aktiválási kulcs létrehozása, a Azure Stack Edge Pro-eszközön létrehozott megosztás létrehozása, a felhasználó létrehozása, a peremhálózati számítási szerepkör konfigurálása, az eszköz jelszavának alaphelyzetbe állítása sikertelen lesz.

A felhasználók Microsoft Graph API-hoz való hozzáférésének biztosításával kapcsolatos további információkért lásd: [Microsoft Graph engedélyek referenciája](/graph/permissions-reference).

### <a name="register-resource-providers"></a>Erőforrás-szolgáltatók regisztrálása

Ha erőforrást szeretne kiépíteni az Azure-ban (a Azure Resource Manager modellben), szüksége lesz egy erőforrás-szolgáltatóra, amely támogatja az adott erőforrás létrehozását. Ha például egy virtuális gépet szeretne kiépíteni, az előfizetésben elérhető "Microsoft. számítási" erőforrás-szolgáltatónak kell lennie.
 
Az erőforrás-szolgáltatók az előfizetés-szinten vannak regisztrálva. Alapértelmezés szerint minden új Azure-előfizetés előre regisztrálva valamelyik gyakran használt erőforrás-szolgáltatónál. A "Microsoft. DataBoxEdge" erőforrás-szolgáltatója nem szerepel a listán.

Nem kell hozzáférési engedélyeket megadnia az előfizetési szinthez ahhoz, hogy a felhasználók olyan erőforrásokat hozzanak létre, mint például a "Microsoft. DataBoxEdge" azon erőforráscsoportok, amelyeken a tulajdonos jogosultságai vannak, feltéve, hogy az erőforrás-szolgáltatók már regisztrálva vannak.

Mielőtt megpróbál létrehozni egy erőforrást, győződjön meg arról, hogy az erőforrás-szolgáltató regisztrálva van az előfizetésben. Ha az erőforrás-szolgáltató nincs regisztrálva, meg kell győződnie arról, hogy az új erőforrást létrehozó felhasználónak elegendő jogosultsága van ahhoz, hogy regisztrálja a szükséges erőforrás-szolgáltatót az előfizetési szinten. Ha még nem tette meg, akkor a következő hibaüzenet jelenik meg:

*Az előfizetés \<Subscription name> nem rendelkezik engedéllyel az erőforrás-szolgáltató (k) regisztrálásához: Microsoft. DataBoxEdge.*


A jelenlegi előfizetésben regisztrált erőforrás-szolgáltatók listájának lekéréséhez futtassa a következő parancsot:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Azure Stack Edge Pro-eszköz esetében `Microsoft.DataBoxEdge` regisztrálni kell. A regisztráláshoz `Microsoft.DataBoxEdge` az előfizetés rendszergazdájának a következő parancsot kell futtatnia:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Az erőforrás-szolgáltatók regisztrálásával kapcsolatos további információkért lásd: [hibák elhárítása az erőforrás-szolgáltató regisztrálásához](../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="manage-connectivity-mode"></a>Kapcsolati mód kezelése

Az alapértelmezett teljes kapcsolaton kívül az eszköz részben csatlakoztatott vagy teljesen leválasztott módban is futhat. Az alábbi módok mindegyikét az alábbiak szerint írja le:

- **Teljes mértékben csatlakoztatva** – ez a normál alapértelmezett mód, amelyben az eszköz működik. Ebben a módban a Felhőbeli feltöltés és az adatok letöltése is engedélyezve van. Az eszköz kezeléséhez használhatja a Azure Portal vagy a helyi webes felhasználói felületet.

- **Részben leválasztva** – ebben a módban az eszköz nem tölthető fel, és nem tölthető le, azonban a Azure Portal használatával felügyelhető.

    Ezt a módot általában a mért műhold-hálózaton használják, és a cél a hálózati sávszélesség-fogyasztás csökkentése. Az eszköz-figyelési műveletek esetében a minimális hálózati fogyasztás továbbra is előfordulhat.

- **Leválasztva** – ebben a módban az eszköz teljesen le van választva a felhőből, és a Felhőbeli feltöltések és a letöltések is le vannak tiltva. Az eszközt csak a helyi webes felületen keresztül lehet felügyelni.

    Ez a mód általában akkor használatos, ha az eszközt offline állapotba kívánja helyezni.

Az eszköz üzemmódjának módosításához kövesse az alábbi lépéseket:

1. Az eszköz helyi webes FELÜLETén nyissa meg a **konfiguráció > a felhőben**.
2. A legördülő listából válassza ki azt a módot, amelyre az eszközt használni szeretné. Kiválaszthatja a **teljes kapcsolat**, a **részlegesen csatlakoztatott** és a **teljesen leválasztott** lehetőséget is. Az eszköz részlegesen leválasztott módban való futtatásához engedélyezze **Azure Portal felügyeletet**.

 
## <a name="manage-power"></a>A Power kezelése

A fizikai eszköz a helyi webes felhasználói felülettel állítható le vagy indítható újra. Az újraindítás előtt ajánlott offline állapotba állítani az adatkiszolgálón található megosztásokat, majd magát az eszközt is. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét.

1. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > Power** elemre.
2. A kívánt művelettől függően válassza a **Leállítás** vagy az **Újraindítás** lehetőséget.

    ![Energiaellátási beállítások](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz.

> [!NOTE]
> Ha leállítja a fizikai eszközt, a bekapcsolásához le kell küldenie az eszköz főkapcsoló gombját.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [kezelheti a megosztásokat](azure-stack-edge-manage-shares.md).