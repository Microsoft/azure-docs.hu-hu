---
title: Hozzáférési kulcs rotálása az Azure SignalR Service esetében
description: Annak áttekintése, hogy az ügyfélnek miért kell rendszeresen elforgatnia a hozzáférési kulcsokat, és hogyan kell ezt megtennie a Azure Portal grafikus felhasználói felülettel és az Azure CLI-vel.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1f80742d48fc46c8c5e337794100ca5901818c88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102184389"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Hozzáférési kulcs rotálása az Azure SignalR Service esetében

Minden Azure Signaler szolgáltatás-példányhoz tartozik egy elsődleges és egy másodlagos kulcs nevű hozzáférési kulcs. A rendszer a Signaler-ügyfelek hitelesítésére használja a szolgáltatásra irányuló kérelmek esetén. A kulcsok a példány végpontjának URL-címével vannak társítva. Tartsa biztonságban a kulcsait, és rendszeresen forgassa őket. Két hozzáférési kulccsal van ellátva, így a kapcsolatok egy kulcs használatával kezelhetők, miközben újragenerálja a másikat.

## <a name="why-rotate-access-keys"></a>Miért érdemes a hozzáférési kulcsokat elforgatni?

Biztonsági okokból és megfelelőségi követelmények esetén a hozzáférési kulcsok rendszeres elforgatása.

## <a name="regenerate-access-keys"></a>Hozzáférési kulcsok újragenerálása

1. Lépjen a [Azure Portalra](https://portal.azure.com/), és jelentkezzen be a hitelesítő adataival.

1. Keresse meg a **kulcsok** szakaszt az Azure signaler szolgáltatás példányában az újragenerálni kívánt kulcsokkal.

1. A navigációs menüben válassza a **kulcsok** lehetőséget.

1. Válassza az **elsődleges kulcs újragenerálása** vagy a **másodlagos kulcs újragenerálása** elemet.

   A rendszer létrehoz és megjelenít egy új kulcsot és a hozzá tartozó kapcsolódó karakterláncot.

   ![Kulcsok újragenerálása](media/signalr-howto-key-rotation/regenerate-keys.png)

A kulcsokat az [Azure CLI](/cli/azure/signalr/key#az-signalr-key-renew)használatával is újragenerálhatja.

## <a name="update-configurations-with-new-connection-strings"></a>Konfigurációk frissítése új kapcsolatok sztringekkel

1. Másolja az újonnan létrehozott kapcsolatok sztringjét.

1. Frissítse az összes konfigurációt az új kapcsolódási karakterlánc használatára.

1. Szükség szerint indítsa újra az alkalmazást.

## <a name="forced-access-key-regeneration"></a>Kényszerített hozzáférési kulcs újragenerálása

Az Azure Signaler szolgáltatás bizonyos helyzetekben kikényszerítheti a kötelező hozzáférési kulcs újragenerálását. A szolgáltatás értesítést küld az ügyfeleknek e-mailben és a portálon keresztül. Ha egy hozzáférési kulcs miatt ezt a kommunikációt vagy a szolgáltatás meghibásodását tapasztalja, forgassa el a kulcsokat az útmutató utasításait követve.

## <a name="next-steps"></a>Következő lépések

A hozzáférési kulcsok rendszeres elforgatása jó biztonsági gyakorlatként.

Ebben az útmutatóban megtanulta, hogyan lehet újragenerálni a hozzáférési kulcsokat. Folytassa a következő oktatóanyagokkal a OAuth vagy a Azure Functions használatával történő hitelesítéssel kapcsolatban.

> [!div class="nextstepaction"]
> [Integrálás a ASP.NET Core Identity szolgáltatással](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Kiszolgáló nélküli valós idejű alkalmazás létrehozása hitelesítéssel](./signalr-tutorial-authenticate-azure-functions.md)