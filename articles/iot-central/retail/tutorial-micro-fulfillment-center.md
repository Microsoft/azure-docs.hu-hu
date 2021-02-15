---
title: A Micro-beteljesülő központ alkalmazás-sablonjának oktatóanyaga | Microsoft Docs
description: Oktatóanyag az Azure-beli Micro-teljesítési központ alkalmazási sablonról IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 01/09/2020
ms.openlocfilehash: ab4d609b1638b47ad73b6f1143adee2870577305
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390470"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Oktatóanyag: üzembe helyezés és útmutató a Micro-teljesítési központ alkalmazás sablonja

Ebben az oktatóanyagban a kiskereskedelmi megoldás létrehozásához az Azure IoT Central Micro-beteljesülés Center alkalmazás sablonját használhatja.

Ez az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Az alkalmazás sablonjának üzembe helyezése
> * Az alkalmazás sablonjának használata

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag-Sorozat elvégzéséhez szüksége lesz egy Azure-előfizetésre. Igény szerint ingyenes 7 napos próbaverziót is használhat. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Alkalmazás létrehozása 
Ebben a szakaszban új Azure IoT Central alkalmazást hoz létre egy sablonból. Ezt az alkalmazást az oktatóanyag-sorozatban fogja használni teljes megoldás létrehozásához.

Új Azure IoT Central-alkalmazás létrehozása:

1. Nyissa meg az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyét.
1. Ha rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal. Ellenkező esetben jelentkezzen be Microsoft-fiók használatával:

   ![Képernyőkép Microsoft-fiók bejelentkezési párbeszédpanelről](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget.

1. Válassza a **kereskedelmi** lehetőséget.  A kiskereskedelmi oldalon számos kereskedelmi alkalmazás-sablon látható.

Az előzetes verziójú funkciókat használó új, Micro-beteljesülő központ alkalmazás létrehozása:  
1. Válassza ki a **Micro-beteljesülés központ** alkalmazás sablonját. Ez a sablon az oktatóanyagban használt összes eszközhöz tartalmaz sablonokat. A sablon egy operátori irányítópultot is biztosít a teljesítési központban lévő figyelési feltételekhez, valamint a robot-szolgáltatók feltételeihez. 

    ![Képernyőfelvétel az Azure IoT Central a IoT-alkalmazás felépítése oldalon](./media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png)
    
1. Igény szerint válasszon egy felhasználóbarát **nevet**. Az alkalmazás sablonja a kitalált vállalati Northwind kereskedőkön alapul. 

    >[!NOTE]
    >Ha felhasználóbarát alkalmazás-nevet használ, továbbra is egyedi értéket kell használnia az alkalmazás URL-címéhez.

1. Ha Azure-előfizetéssel rendelkezik, adja meg a címtárat, az Azure-előfizetést és a régiót. Ha nem rendelkezik előfizetéssel, engedélyezheti a 7 napos ingyenes próbaidőszakot, és elvégezheti a szükséges kapcsolattartási adatokat.  

    További információ a címtárakról és az előfizetésekről: [alkalmazás létrehozása](../core/quick-deploy-iot-central.md) – rövid útmutató.

1. Válassza a **Létrehozás** lehetőséget.

    ![Az Azure IoT Central új alkalmazás oldalának képernyőképe](./media/tutorial-micro-fulfillment-center-app/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Az alkalmazás végigvezeti 

Az alkalmazás sablonjának sikeres üzembe helyezését követően a **Northwind Traders Micro-beteljesülő központ irányítópultja** látható. A Northwind Traders egy fiktív kiskereskedő, amely az Azure IoT Central alkalmazásban felügyelt, mikro-teljesítési központtal rendelkezik. Ezen az operátor irányítópulton megtekintheti a sablonban lévő eszközök információit és telemetria, valamint parancsokat, feladatokat és műveleteket is végrehajthat. Az irányítópult logikailag két szakaszra oszlik. A bal oldalon nyomon követheti a környezeti feltételeket a beteljesülés struktúrán belül, és a jobb oldalon nyomon követheti a robot-szolgáltatók állapotát a létesítményen belül.  

Az irányítópulton a következőket teheti:
   * Tekintse meg az eszköz telemetria, például a kivételezések számát, a feldolgozott megrendelések számát és a tulajdonságokat, például a struktúra rendszer állapotát.  
   * Megtekintheti a beteljesülés struktúrán belüli robot-szolgáltatók alapszintű tervét és helyét.
   * Trigger parancsok, például a vezérlőrendszer alaphelyzetbe állítása, a szolgáltató belső vezérlőprogram frissítése és a hálózat újrakonfigurálása.

     ![Képernyőfelvétel a Northwind Traders Micro-teljesítési központ irányítópultjának felső feléről.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard1.png)
   * Tekintse meg az irányítópult azon példáját, amelyet az operátor a teljesítési központban lévő feltételek figyelésére használhat. 
   * Figyelje az átjáró eszközön futó hasznos adatok állapotát a teljesítési központban.    

     ![Képernyőkép a Northwind Traders Micro-beteljesülő központ irányítópultjának alsó feléről.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard2.png)

## <a name="device-template"></a>Eszköz sablonja
Ha az eszközbeállítások lapot választja, akkor láthatja, hogy a sablonhoz két különböző típusú eszköz tartozik: 
   * **Robot Carrier**: Ez a sablon a megfelelőségi struktúrában üzembe helyezett, működő robot-szolgáltató definícióját jelöli, és megfelelő tárolási és lekérési műveleteket végez. Ha kijelöli a sablont, láthatja, hogy a robot adatokat küld, például a hőmérsékletet és a tengely pozícióját, valamint tulajdonságokat, például a robot Carrier állapotát. 
   * **Szerkezeti feltételek figyelése**: ez az eszköz egy olyan gyűjteményt képvisel, amely lehetővé teszi a környezeti feltételek, valamint a különböző peremhálózati munkaterheléseket üzemeltető átjárók számára a teljesítési központ bekapcsolását. Az eszköz telemetria adatokat küld, például a hőmérsékletet, a kivételezések számát és a megrendelések számát. Emellett adatokat küld a környezetben futó számítási feladatok állapotáról és állapotáról. 

     ![Micro-beteljesülés Center-eszközök sablonjai](./media/tutorial-micro-fulfillment-center-app/device-templates.png)

Ha kiválasztja az eszközcsoport fület, azt is láthatja, hogy ezekhez az eszközökhöz automatikusan vannak létrehozva az erőforráscsoportok.

## <a name="rules"></a>Szabályok
A **szabályok** lapon megjelenik egy minta szabály, amely szerepel az alkalmazás sablonjában a robot Carrier hőmérsékleti feltételeinek figyeléséhez. Ezt a szabályt használhatja arra, hogy figyelmeztesse a kezelőt, ha a létesítmény egy adott robotja túlmelegszik, és offline állapotba kell helyezni a karbantartás során. 

A minta szabály mint inspiráció használatával határozhatja meg az üzleti funkcióknak megfelelő szabályokat.

![Képernyőfelvétel a szabályok lapról](./media/tutorial-micro-fulfillment-center-app/rules.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az alkalmazás sablonját. Nyissa meg az **adminisztrációs**  >  **alkalmazás beállításait**, és válassza a **Törlés** lehetőséget.

![Képernyőkép a Micro-beteljesülés központ Alkalmazásbeállítások oldaláról](./media/tutorial-micro-fulfillment-center-app/delete.png)

## <a name="next-steps"></a>Következő lépések

További információk:

> [!div class="nextstepaction"]
> [Micro-teljesítési központ megoldási architektúrája](./architecture-micro-fulfillment-center.md)
