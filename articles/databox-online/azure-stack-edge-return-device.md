---
title: A Azure Stack Edge Pro-eszköz visszaküldése | Microsoft Docs
description: Megtudhatja, hogyan törölheti az adatait, és visszaküldheti Azure Stack Edge Pro-eszközét, majd törölheti az eszközhöz hozzárendelt erőforrást.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/26/2021
ms.author: alkohli
ms.openlocfilehash: 6c9e8938feb3c3444d01f9d37476589776707425
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101703184"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro-eszköz visszaküldése

Ez a cikk az adatok törlését és az Azure Stack Edge Pro-eszköz visszaküldését ismerteti. Az eszköz visszaadása után törölheti az eszközhöz társított erőforrást is.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Adatok törlése az eszközön lévő adatlemezekről
> * Az eszköz visszaküldésének megkezdése az Azure Portalon
> * Az eszköz becsomagolása és a pickup beosztása
> * Az erőforrás törlése Azure Portal

## <a name="erase-data-from-the-device"></a>Adatok törlése az eszközről

Az eszközön lévő adatlemezek adatainak törléséhez alaphelyzetbe kell állítania az eszközt.

Az Alaphelyzetbe állítás előtt hozzon létre egy másolatot a helyi adatmennyiségről az eszközön, ha szükséges. Az adatok az eszközről egy Azure Storage-tárolóba másolhatók. 

Az eszköz visszaküldését még az eszköz alaphelyzetbe állítása előtt is elindíthatja.

Alaphelyzetbe állíthatja az eszközt a helyi webes felületen vagy a PowerShellben. PowerShell-utasításokért lásd: [az eszköz alaphelyzetbe állítása](./azure-stack-edge-connect-powershell-interface.md#reset-your-device).


[! Belefoglalás] [adatok alaphelyzetbe állítása az eszközről](../../includes/azure-stack-edge-device-reset.md)

> [!NOTE]
> - Ha új eszközre cserél vagy frissít, javasoljuk, hogy csak az új eszköz megérkezése után állítsa alaphelyzetbe az eszközt.
> - Az eszköz alaphelyzetbe állítása csak az eszközön lévő összes helyi adatmezőt törli. A felhőben tárolt adatok nem törlődnek, és gyűjti a [díjakat](https://azure.microsoft.com/pricing/details/storage/). Ezeket az eszközöket külön törölni kell egy felhőalapú tároló-felügyeleti eszköz (például [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)) használatával.

## <a name="initiate-device-return"></a>Eszköz visszaküldésének kezdeményezése

A visszatérési folyamat elindításához hajtsa végre az alábbi lépéseket.

1. Lépjen a Azure Stack Edge Pro/Data Box Gateway erőforrásra Azure Portal. Az **áttekintésben** nyissa meg a parancssáv a jobb oldali ablaktáblán, majd válassza az **eszköz visszaküldése** lehetőséget. 

    ![1. eszköz visszaküldése](media/azure-stack-edge-return-device/return-device-1.png)  

2. A **visszalépési eszköz** panelen, az **alapszintű részletek** területen:

    1. Adja meg az eszköz sorozatszámát. Az eszköz sorozatszámának beszerzéséhez nyissa meg az eszköz helyi webes FELÜLETét, és válassza az **Áttekintés** lehetőséget.  
    
       ![1. eszköz sorozatszáma](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Adja meg a szolgáltatási címke számát. A szolgáltatás címkéjének száma öt vagy több karaktert tartalmazó azonosító, amely egyedi az eszközön. A szolgáltatás címkéje az eszköz jobb alsó sarkában található (az eszközre való szembenézés során). Húzza ki az információs címkét (ez egy kiugró címke panel). Ez a panel olyan rendszerinformációkat tartalmaz, mint a szolgáltatási címke, a hálózati adapterek, a MAC-címek stb. 
    
       ![1. szolgáltatási címke száma](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. A legördülő listából válassza ki a visszatérés okát.

       ![Eszköz visszaküldése 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. A **szállítás részletei** területen:

    1. Adja meg a nevét, a vállalat nevét és a teljes céges címeket. Adjon meg egy munkahelyi telefonszámot, beleértve a körzetszámot és egy e-mail-azonosítót az értesítéshez.
    2. Ha visszatérési szállítási mezőbe van szüksége, kérheti. Az **Igen** kérdésre adandó válaszhoz **üres mezőt kell visszaadnia**.

    ![Visszaadott eszköz 3](media/azure-stack-edge-return-device/return-device-3.png)

4. Tekintse át az **adatvédelmi feltételeket**, és jelölje be a jelölőnégyzetet a megjegyzésben, amelyet áttekintett, és fogadja el az adatvédelmi feltételeket.

5. Válassza a **visszatérés kezdeményezése** lehetőséget.

    ![4. visszaadott eszköz](media/azure-stack-edge-return-device/return-device-4.png) 

6. Az eszköz visszaküldési részleteinek rögzítése után e-mailben értesítheti az Azure Stack Edge Pro műveleti csapatát. Használhatja az e-mail-alkalmazást, feltéve, hogy az e-mail-alkalmazás telepítve van és konfigurálva van. Az Adatmásolást is elvégezheti, és e-mailben is elküldheti azokat.

    ![Eszköz visszaküldése 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Miután az Azure Stack Edge Pro Operations csapata megkapja az e-mailt, elküld egy fordított szállítólevél címkét. Ha ezt a címkét kapja, beállíthatja az eszköz felvételét a szállítóval. 

## <a name="schedule-a-pickup"></a>Pickup beosztása

Az alábbi lépéseket követve ütemezhet egy felvételt.

1. Állítsa le az eszközt. A helyi webes KEZELŐFELÜLETen lépjen a **karbantartás > energiagazdálkodási beállítások** elemre.
2. Válassza a **Leállítás** lehetőséget. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz. További információ: [Manage Power](../databox-gateway/data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Húzza ki az energiaellátási kábeleket, és távolítsa el az összes hálózati kábelt az eszközről.
4. Készítse elő a szállítólevél-csomagot saját vagy az Azure-ból kapott üres mező használatával. Helyezze el az eszközt a dobozba, és az eszközzel szállított tápkábeleket.
5. Helyezze az Azure-ból kapott szállítási címkét a csomagba.
6. Egyeztessen egy csomagfelvételi időpontot helyi szolgáltatójával. Ha visszaadja az eszközt az USA-ban, akkor a fuvarozó a UPS vagy a FedEx lehet. Felvétel beosztása a UPS-sel:

    1. Hívja fel a helyi UPS-t (ország-/régióspecifikus ingyenes szám).
    2. A hívásban adja meg a fordított szállítólevél nyomon követésének számát a nyomtatott címkén látható módon.
    3. Ha a nyomkövetési szám nincs feltüntetve, a UPS-nek külön díjat kell fizetnie a felvétel során.

    A felvétel ütemezése helyett a Azure Stack Edge Pro-t is elhúzhatja a legközelebbi legördülő helyen.

## <a name="delete-the-resource"></a>Erőforrás törlése

Miután az eszközt megkapta az Azure-adatközpontban, a rendszer megkeresi az eszközt a károsodás vagy az illetéktelen módosítás jeleinek ellenőrzéséhez.

- Ha az eszköz érintetlenül érkezik, és jó állapotban van, akkor az adott erőforrás számlázási mérőszáma leáll. Azure Stack Edge Pro operatív csapata felveszi Önnel a kapcsolatot, hogy erősítse meg az eszköz visszaadását. Ezután törölheti az eszközhöz társított erőforrást a Azure Portal.
- Ha az eszköz jelentősen megsérült, a bírságok vonatkozhatnak. További részletekért tekintse [meg az elveszett vagy sérült eszköz](https://azure.microsoft.com/pricing/details/databox/edge/) és a [termék használati feltételeivel](https://www.microsoft.com/licensing/product-licensing/products)kapcsolatos gyakori kérdéseket.  


Az eszközt törölheti a Azure Portalban:

- Miután elvégezte a megrendelés elvégzését, és még az eszköz előkészítése előtt a Microsoft.
- Miután visszaadott egy eszközt a Microsoftnak, és a Azure Stack Edge Pro Operations csapata meghívott annak megerősítésére, hogy az eszköz vissza lett-e vonva. Az operatív csapat addig nem hívja meg a hívást, amíg a visszaadott eszköz át nem adja a fizikai ellenőrzést az Azure-adatközpontban.

Ha aktiválta az eszközt egy másik előfizetéshez vagy helyhez, a Microsoft egy munkanapon belül áthelyezi a rendelését az új előfizetésre vagy helyre. A megrendelés áthelyezése után törölheti ezt az erőforrást.


A következő lépésekkel törölheti az eszközt és az erőforrást Azure Portalban.

1. A Azure Portal lépjen az erőforráshoz, majd az **Áttekintés** elemre. A parancssorban válassza a **Törlés** lehetőséget.

    ![Törlés kiválasztása](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Az **eszköz törlése** panelen írja be a törölni kívánt eszköz nevét, és válassza a **Törlés** lehetőséget.

    ![Törlés megerősítése](media/azure-stack-edge-return-device/delete-resource-2.png)

Az eszköz és a hozzá tartozó erőforrás sikeres törlése után értesítést kap.


## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [szerezhet be helyettesítő Azure stack Edge Pro-eszközt](azure-stack-edge-replace-device.md).