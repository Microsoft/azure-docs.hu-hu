---
title: Windows rendszerű virtuális asztali (klasszikus) felügyeleti eszköz – Azure
description: A Windows rendszerű virtuális asztali (klasszikus) felügyeleti eszközzel kapcsolatos problémák elhárítása.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6f03284103b4a2aa6900bf1ba5c50a4688c50b0e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88005482"
---
# <a name="troubleshoot-the-windows-virtual-desktop-classic-management-tool"></a>A Windows rendszerű virtuális asztali (klasszikus) felügyeleti eszköz hibáinak megoldása

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat.

Ez a cikk azokat a problémákat ismerteti, amelyek a Windows rendszerű virtuális asztali felügyeleti eszköz telepítésekor és a javításuk során fordulhatnak elő.

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>Hiba: a felügyeleti eszköz szolgáltatásai konfigurálva lettek, de az automatikus telepítés meghiúsul

Amikor sikeresen beállította a szolgáltatásokat a felügyeleti eszközhöz, de az automatikus telepítés meghiúsul, a következő hibaüzenet jelenik meg:

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

Ez általában a következő két dolog egyikét jelenti:

- A felhasználónak tulajdonosi engedélyekkel kell rendelkezniük az előfizetéshez és a globális rendszergazdához a bérlői szinten, de nem tudnak bejelentkezni az Azure-ba.
- A felhasználó Fiókbeállítások engedélyezve van a többtényezős hitelesítés.

A probléma megoldásához:

1. Győződjön meg arról, hogy az Azure Active Directory egyszerű felhasználónévhez létrehozott felhasználó a "közreműködő" előfizetési szinttel rendelkezik.
2. Jelentkezzen be <portal.azure.com> az UPN-fiókkal, és ellenőrizze a fiók beállításait, és győződjön meg arról, hogy a többtényezős hitelesítés nincs bekapcsolva. Ha be van kapcsolva, kapcsolja ki.
3. Látogasson el a Windows rendszerű virtuális asztali engedélyezési oldalára, és győződjön meg arról, hogy a kiszolgáló és az ügyfélalkalmazás beleegyezik.
4. Tekintse át a [felügyeleti eszköz üzembe helyezése](manage-resources-using-ui.md) oktatóanyagot, ha a probléma továbbra is fennáll, majd telepítse újra az eszközt.

## <a name="error-job-with-specified-id-already-exists"></a>Hiba: már létezik a megadott AZONOSÍTÓJÚ feladattípus.

Ha a felhasználó azt látja, hogy a (z) "a megadott AZONOSÍTÓJÚ feladattípus már létezik" hibaüzenet jelenik meg, akkor az a sablon telepítésekor nem tartalmaz egyedi nevet az "alkalmazásnév" paraméterben.

Ennek kijavításához telepítse újra a felügyeleti eszközt az "alkalmazás neve" paraméterrel kitöltve.

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>Késleltetett beleegyezés kérése a felügyeleti eszköz megnyitásakor

A felügyeleti eszköz központi telepítésekor előfordulhat, hogy a beleegyezés kérése azonnal nem nyílik meg. Ez azt jelenti, hogy az Azure Web App szolgáltatás a szokásosnál hosszabb időt vesz igénybe a betöltéshez. A kérést az Azure web betöltése után kell megjelennie.

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>A felhasználó nem tudja központilag telepíteni a felügyeleti eszközt az USA keleti régiójában

Ha az ügyfél az USA keleti régiójában állítja be a régiót, nem telepíthetik a felügyeleti eszközt.

Ennek kijavításához telepítse a felügyeleti eszközt egy másik régióban. Az eszköz egy másik régióban való újbóli üzembe helyezése nem érinti a felhasználói élményt.

## <a name="next-steps"></a>Következő lépések

- További információ a eszkalációs sávokról: [Hibaelhárítás áttekintése, visszajelzések és támogatás](troubleshoot-set-up-overview-2019.md).
- Ismerje meg, hogyan jelenthet problémát a Windows rendszerű virtuális asztali eszközökön a [Távoli asztali szolgáltatások ARM-sablonjaiban](https://github.com/Azure/RDS-Templates/blob/master/README.md).
- A felügyeleti eszköz központi telepítésének megismeréséhez tekintse meg a [felügyeleti eszköz üzembe helyezése](manage-resources-using-ui.md)című témakört.