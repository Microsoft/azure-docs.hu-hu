---
title: Az Azure külső szolgáltatási díjak értelmezése |} A Microsoft Docs
description: Ismerje meg a külső szolgáltatások, korábbi nevén a Marketplace-en a költségeket az Azure számlázását.
services: ''
documentationcenter: ''
author: adpick
manager: adpick
editor: ''
tags: billing
ms.assetid: 5e0e2a3c-d111-4054-8508-0c111c1b749b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ae9c2c975bf49725be1858ad02a1c4b90ef58a7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60370580"
---
# <a name="understand-your-azure-billing-for-external-service-charges"></a>Az Azure számlázását a külső szolgáltatási díjak
Külső szolgáltatások az Azure Marketplace-en a külső szoftvergyártók által közzétett. Ha például a SendGrid egy külső szolgáltatások, az Azure-ban is vásárolhat, de nem a Microsoft által közzétett.

Amikor üzembe helyezi egy új külső szolgáltatáshoz vagy erőforráshoz, egy figyelmeztetés jelenik meg:

![Marketplace-en beszerzési figyelmeztetés](./media/billing-understand-your-azure-marketplace-charges/marketplace-warning.PNG)

> [!NOTE]
> Külső szolgáltatások vállalatok, amelyek nem Microsoft által közzétett, de más Microsoft-termékek is kategóriába sorolt külső szolgáltatásokhoz.
> 
> 

## <a name="how-external-services-are-billed"></a>Hogyan külső szolgáltatások számlázása
- Külső szolgáltatások külön számlázzuk. Az Azure-előfizetésen belül az egyes rendeléseket kezeli azokat. Az egyes szolgáltatások a számlázási időszak van beállítva, ha a szolgáltatást vásárol. Nem tévesztendő össze az előfizetés, amelynek keretében megvásárolta, az elszámolási időszakban. Külön számlák is kap, és a megadott bankkártyára külön számlázzuk.
- Minden külső szolgáltatásnak van egy másik számlázási modellt. Bizonyos szolgáltatások a használatalapú fizetést biztosító módon elszámolással számlázzuk, míg mások havi alapján fizetési modellt használnak. Hitelkártya van szüksége az Azure-külső szolgáltatásokhoz, a számlás fizetési külső szolgáltatásokkal, nem vásárolhat.
- Havi ingyenes kreditjeinek külső szolgáltatások nem használható. Ha használ, amely tartalmazza az Azure-előfizetéssel [ingyenes krediteket](https://azure.microsoft.com/pricing/spending-limits/), azok külső szolgáltatás számlázása a nem alkalmazható. Bankkártya használatával vásárolhatja meg a külső szolgáltatásokat.

## <a name="view-external-service-spending-and-history-in-the-azure-portal"></a>Nézet külső szolgáltatás kiadások és az Azure Portalon előzményei
A külső szolgáltatások lévő minden egyes előfizetés listáját megtekintheti a [az Azure portal](https://portal.azure.com/): 

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) rendszergazdaként.
2. A központ menüben válassza ki a **előfizetések**.
   
    ![A központ menüben válassza az előfizetések](./media/billing-understand-your-azure-marketplace-charges/sub-button.png) 
3. Az a **előfizetések** panelt, válassza ki az előfizetést, amelyhez a megtekinteni kívánt, és válassza ki **külső szolgáltatások**.
   
    ![A számlázási panelen válasszon ki egy előfizetést](./media/billing-understand-your-azure-marketplace-charges/select-sub-external-services.png)
4. Megtekintheti az egyes, a külső szolgáltatás megrendeléseit, a közzétevő neve, a szolgáltatási rétegben, amelytől vásárolta, a neve az erőforrást, és a rendelés jelenlegi állapota. Korábbi számlák megtekintéséhez válasszon ki egy külső szolgáltatást.
   
    ![Válasszon ki egy külső szolgáltatást](./media/billing-understand-your-azure-marketplace-charges/external-service-blade2.png)
5. Itt megtekintheti, többek között az adó lebontása számlázási összegek múltbeli.
   
    ![Külső szolgáltatások számlázási előzmények megtekintése](./media/billing-understand-your-azure-marketplace-charges/billing-overview-blade.png)

## <a name="view-external-service-spending-for-enterprise-agreement-ea-customers"></a>Az ügyfelek a nagyvállalati szerződés (EA) kiadások külső szolgáltatás megtekintése
Nagyvállalati szerződéssel rendelkező ügyfelek tekintheti külső szolgáltatás költségeit és a nagyvállalati szerződések portáljának a jelentések letöltése. Lásd: [nagyvállalati szerződéssel rendelkező ügyfeleink az Azure Marketplace-en](https://ea.azure.com/helpdocs/azureMarketplace) a kezdéshez.

## <a name="manage-payment-methods-for-external-service-orders"></a>Külső szolgáltatás rendelések fizetési módok kezelése
A külső szolgáltatás rendelések fizetési módok frissítése a [Account Center](https://account.windowsazure.com/).

> [!NOTE]
> Ha egy munkahelyi vagy iskolai fiókkal az előfizetés vásárolt [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) módosíthatja a fizetési módot.
> 
> 

1. Jelentkezzen be a [Account Center](https://account.windowsazure.com/) és [keresse meg a **marketplace** lap](https://account.windowsazure.com/Store)
   
    ![Az account Center webhelyen jelölje ki a Marketplace-en](./media/billing-understand-your-azure-marketplace-charges/select-marketplace.png)
2. Válassza ki a kezelni kívánt külső szolgáltatást
   
    ![Válassza ki a kezelni kívánt külső szolgáltatást](./media/billing-understand-your-azure-marketplace-charges/select-ext-service.png)
3. Kattintson a **fizetési mód megváltoztatása** az oldal jobb oldalán. Ez a hivatkozás egy másik portálon is lehet kezelni a fizetési módot biztosít.
   
    ![Megrendelés összegzése](./media/billing-understand-your-azure-marketplace-charges/change-payment.PNG)
4. Kattintson a **adatainak szerkesztése** , és kövesse az utasításokat követve frissítenie kell fizetési információit.
   
    ![Válassza ki a szerkesztési adatai](./media/billing-understand-your-azure-marketplace-charges/edit-info.png)

## <a name="cancel-an-external-service-order"></a>Egy külső szolgáltatás megrendelés törlése
Biztosan megszakítja a külső szolgáltatás rendeléséhez, ha törli az erőforrás a [az Azure portal](https://portal.azure.com).

![Erőforrás törlése](./media/billing-understand-your-azure-marketplace-charges/deleteMarketplaceOrder.PNG)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

