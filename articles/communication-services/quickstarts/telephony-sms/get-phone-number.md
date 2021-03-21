---
title: Rövid útmutató – telefonszám beszerzése az Azure kommunikációs szolgáltatásokból
description: Megtudhatja, hogyan vásárolhat kommunikációs szolgáltatások telefonszámát a Azure Portal használatával.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: 390c804692029d7cdee8f78325b441592879582b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103488584"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Gyors útmutató: telefonszám beszerzése a Azure Portal használatával

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Az Azure Communication Services használatának első lépései a Azure Portal használatával vásárolhatják meg a telefonszámot.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Aktív kommunikációs szolgáltatások erőforrása.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Telefonszám beszerzése

A számok kiosztásának megkezdéséhez nyissa meg a kommunikációs szolgáltatások erőforrást a [Azure Portalon](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="A kommunikációs szolgáltatások erőforrásának főoldalát bemutató képernyőkép.":::

### <a name="getting-new-phone-numbers"></a>Új telefonszámok beolvasása

Navigáljon a **telefonszámok** panelre az erőforrás menüben.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="A kommunikációs szolgáltatások erőforrásának telefonos lapját ábrázoló képernyőkép.":::

A varázsló elindításához kattintson a **Letöltés** gombra. A **telefonszámok** panelen a varázsló végigvezeti egy olyan kérdésen, amely segít kiválasztani a forgatókönyvnek leginkább megfelelő telefonszámot.

Először ki kell választania azt az **országot/régiót** , ahol ki szeretné építeni a telefonszámot. Miután kiválasztotta az országot/régiót, ki kell választania az igényeinek legmegfelelőbb **használati esetet** .

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Képernyőfelvétel: a telefonszámok beolvasása nézet.":::

### <a name="select-your-phone-number-features"></a>Telefonszám-funkciók kiválasztása

A telefonszám beállítása két lépésből áll:

1. A [szám típusának](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services) kiválasztása
2. A [szám funkcióinak](../../concepts/telephony-sms/plan-solution.md#phone-number-features-in-azure-communication-services) kiválasztása

Két telefonszám közül választhat: **földrajzi** és **díjmentes**. Ha kiválasztott egy számot, válassza ki a szolgáltatást.

A példánkban a **kimenő hívások** és a **bejövő és kimenő SMS** **-funkciók díjmentes** számú típusát választottuk ki.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="A szolgáltatások kiválasztása nézetet bemutató képernyőkép.":::

Innen kattintson a lap alján található **Next: Numbers (tovább** ) gombra a kiépíteni kívánt telefonszám (ok) testreszabásához.

### <a name="customizing-phone-numbers"></a>Telefonszámok testreszabása

A **számok** lapon testreszabhatja a kiépíteni kívánt telefonszám (oka) t.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="A számok kiválasztása oldalt ábrázoló képernyőfelvétel":::

> [!NOTE]
> Ez a rövid útmutató a **díjmentes** számú testreszabási folyamatot mutatja be. Ha kiválasztotta a **földrajzi** számot, a felhasználói élmény némileg eltérő lehet, de a végeredmény ugyanaz lesz.

Válassza ki a **körzetszámot** az elérhető körzetszámok listájából, és adja meg a kiépíteni kívánt mennyiséget, majd kattintson a **Keresés** gombra a kiválasztott követelményeknek megfelelő számok megkereséséhez. Az igényeinek megfelelő telefonszámokat a havi költséggel együtt fogjuk megjeleníteni.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Képernyőfelvétel: a számok kiválasztása oldal a fenntartott számokkal.":::

> [!NOTE]
> A rendelkezésre állás a szám típusától, a helytől és a kiválasztott funkcióktól függ.
> A szám a tranzakció lejárta előtt rövid ideig van lefoglalva. Ha a tranzakció lejár, akkor újra ki kell választania a számokat.

A vásárlás összegzésének megtekintéséhez és a rendelés elvégzéséhez kattintson a lap alján található **következő: összefoglalás** gombra.

### <a name="place-order"></a>Megrendelés

Az összefoglalás lapon áttekintheti a telefonszámok kiépítéséhez szükséges számokat, szolgáltatásokat, telefonszámokat és a teljes havi költséget.

> [!NOTE]
> A feltüntetett díjak a **havi ismétlődő díjak** , amelyek a kiválasztott telefonszám bérletének költségeit fedezik. Ez a nézet nem része az utólagos elszámolású **költségeknek** , amelyek a hívások kezdeményezése vagy fogadása során merülnek fel. Az árlisták [itt érhetők el](../../concepts/pricing.md). Ezek a költségek a meghívott szám típusától és célhelytől függenek. Például, ha egy Seattle-beli régióból érkező hívást egy New York-beli regionális számra hív át, és az azonos számú, Egyesült királyságbeli mobil telefonszámra irányuló hívás eltérő lehet.

Végül kattintson a lap alján található **megrendelés** elemre a megerősítéshez.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Képernyőfelvétel: az összefoglalás oldal, amely tartalmazza a szám típusát, a funkciókat, a telefonszámokat és a teljes havi költséget.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Telefonszámok megkeresése a Azure Portal

Navigáljon az Azure kommunikációs erőforrásához a [Azure Portalon](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="A kommunikációs szolgáltatások erőforrásának főoldalát bemutató képernyőkép.":::

A telefonszámok kezeléséhez válassza a menüben a telefonszámok panelt.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="A kommunikációs szolgáltatások erőforrásának telefonszám lapját ábrázoló képernyőkép.":::

> [!NOTE]
> Eltarthat néhány percig, amíg a kiépített számok megjelennek ezen az oldalon.


### <a name="customizing-phone-numbers"></a>Telefonszámok testreszabása

A **számok** lapon kiválaszthatja a konfigurálni kívánt telefonszámot.

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="A frissítési funkciók lapot ábrázoló képernyőkép.":::

Válassza ki a szolgáltatásokat az elérhető lehetőségek közül, majd kattintson a **jóváhagyás** gombra a kijelölés alkalmazásához.

## <a name="troubleshooting"></a>Hibaelhárítás

Gyakori kérdések és problémák:

- Az USA-beli vásárlás csak az Egyesült Államokban támogatott. A telefonszámok megvásárlásához a következőket kell tennie:
  - A társított Azure-előfizetés számlázási címe a Egyesült Államok található. Jelenleg nem helyezhető át egy erőforrás egy másik előfizetésre.
  - A kommunikációs szolgáltatások erőforrása a Egyesült Államok adatterületen van kiépítve. Az erőforrások jelenleg nem helyezhetők át másik adathelyre.

- Ha telefonszámot szabadít fel, a telefonszámot a rendszer nem szabadítja fel, illetve nem tudja újra megvásárolni a számlázási időszak végéig.

- A kommunikációs szolgáltatások erőforrásának törlésekor a rendszer automatikusan felszabadítja az adott erőforráshoz tartozó telefonszámokat.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta a következőket:

> [!div class="checklist"]
> * Telefonszám vásárlása
> * Telefon számának kezelése
> * Telefonszám felszabadítása

> [!div class="nextstepaction"]
> [SMS küldése](../telephony-sms/send.md) 
>  [Ismerkedés a hívással](../voice-video-calling/getting-started-with-calling.md)
