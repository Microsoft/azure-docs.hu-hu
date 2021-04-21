---
title: Hozzáférési kulcs rotálása az Azure SignalR Service esetében
description: Áttekintés arról, hogy az ügyfélnek miért kell rendszeresen elforgatni a hozzáférési kulcsokat, és hogyan kell megtennie a Azure Portal grafikus felhasználói felület és az Azure CLI használatával.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: cd03720b65883c5f43b11837d4a88a705209fb10
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764098"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Hozzáférési kulcs rotálása az Azure SignalR Service esetében

Minden Azure SignalR Service példány rendelkezik egy elsődleges és egy másodlagos kulcs nevű hozzáférési kulcspárral. Ezek a SignalR-ügyfelek hitelesítésére használhatók, amikor kéréseket küldnek a szolgáltatásnak. A kulcsok a példányvégpont URL-címéhez vannak társítva. Tartsa biztonságban a kulcsokat, és rendszeresen váltogatja őket. Két hozzáférési kulcsot biztosítunk Önnek, így az egyik kulccsal fenntarthatja a kapcsolatokat a másik újragenerálásával.

## <a name="why-rotate-access-keys"></a>Miért érdemes a hozzáférési kulcsokat elforgatni?

Biztonsági okokból és a megfelelőségi követelmények miatt rendszeresen váltogatja a hozzáférési kulcsokat.

## <a name="regenerate-access-keys"></a>Hozzáférési kulcsok újragenerálása

1. [A(Azure Portal,](https://portal.azure.com/)és jelentkezzen be a hitelesítő adataival.

1. Keresse meg **a** kulcsokat a Azure SignalR Service példányban az újragenerálni kívánt kulcsokkal.

1. A **navigációs menüben** válassza a Kulcsok lehetőséget.

1. Válassza az **Elsődleges kulcs újragenerálása vagy** a Másodlagos kulcs **újragenerálása lehetőséget.**

   Létrejön és megjelenik egy új kulcs és a megfelelő kapcsolati sztring.

   ![Kulcsok újragenerálása](media/signalr-howto-key-rotation/regenerate-keys.png)

A kulcsokat az Azure CLI használatával is [újragenerálhatja.](/cli/azure/signalr/key#az_signalr_key_renew)

## <a name="update-configurations-with-new-connection-strings"></a>Konfigurációk frissítése új kapcsolati sztringekkel

1. Másolja ki az újonnan létrehozott kapcsolati sztringet.

1. Frissítse az összes konfigurációt az új kapcsolati sztring használatára.

1. Szükség szerint indítsa újra az alkalmazást.

## <a name="forced-access-key-regeneration"></a>Kényszerített hozzáférési kulcs újragenerálása

Azure SignalR Service bizonyos helyzetekben kötelezően szükséges lehet a hozzáférési kulcs újragenerálása. A szolgáltatás e-mailben és portálértesítéssel értesíti az ügyfeleket. Ha ezt a kommunikációt kapja, vagy egy hozzáférési kulcs miatt szolgáltatáshibába ütközik, váltsa át a kulcsokat az útmutató utasításait követve.

## <a name="next-steps"></a>Következő lépések

Biztonsági gyakorlatként a hozzáférési kulcsokat rendszeresen váltogathatja.

Ebben az útmutatóban megtanulta, hogyan lehet újragenerálni a hozzáférési kulcsokat. Folytassa a következő oktatóanyagokkal, amelyek az OAuth-hitelesítést vagy a hitelesítést Azure Functions.

> [!div class="nextstepaction"]
> [Integrálás ASP.NET alapvető identitással](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Kiszolgáló nélküli, hitelesítést is lehetővé tő valós idejű alkalmazás összeállítása](./signalr-tutorial-authenticate-azure-functions.md)