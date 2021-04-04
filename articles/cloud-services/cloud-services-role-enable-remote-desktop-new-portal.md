---
title: A portál használata a szerepkörök Távoli asztalának engedélyezéséhez
description: Azure Cloud Service-alkalmazás konfigurálása távoli asztali kapcsolatok engedélyezéséhez
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 8fa0d3c0e29c53e6fe9cb32ddf02168686be1efe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743253"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-classic"></a>Távoli asztali kapcsolat engedélyezése az Azure Cloud Services (klasszikus) szerepkörhöz

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Távoli asztal lehetővé teszi az Azure-ban futó szerepkör asztalának elérését. Az alkalmazással kapcsolatos problémák elhárításához és diagnosztizálásához Távoli asztal-kapcsolatban is használhatja a szolgáltatást.

A fejlesztés során engedélyezheti a Távoli asztal kapcsolatát, ha a szolgáltatás definíciójában lévő Távoli asztal modulokat is engedélyezi, vagy engedélyezheti a Távoli asztal a Távoli asztal bővítménnyel. Az előnyben részesített módszer az Távoli asztal bővítmény használata, mivel a Távoli asztal még az alkalmazás telepítése után is engedélyezheti, anélkül, hogy újból üzembe kellene helyeznie az alkalmazást.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Távoli asztal konfigurálása a Azure Portal

A Azure Portal a Távoli asztal bővítmény megközelítését használja, így Távoli asztal még az alkalmazás telepítése után is engedélyezheti. A felhőalapú szolgáltatás **Távoli asztal** beállításai lehetővé teszik a távoli asztal engedélyezését, a virtuális gépekhez való kapcsolódáshoz használt helyi rendszergazdai fiók, a hitelesítésben használt tanúsítvány és a lejárati dátum beállításának módosítását.

1. Kattintson a **Cloud Services** elemre, válassza ki a Cloud Service nevét, majd válassza a **Távoli asztal** lehetőséget.

    ![a rendszerkép a Cloud Services távoli asztalát mutatja](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Válassza ki, hogy szeretné-e engedélyezni a Távoli asztal egy adott szerepkörhöz vagy az összes szerepkörhöz, majd módosítsa az kapcsoló értékét az **engedélyezve** értékre.

3. Adja meg a Felhasználónév, a jelszó, a lejárat és a tanúsítvány kötelező mezőit.

    ![Cloud Services Távoli asztal](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Az összes szerepkör-példány újra lesz indítva, amikor először engedélyezi Távoli asztal, majd **az OK** (pipa) lehetőséget választja. Az újraindítás megakadályozása érdekében a jelszó titkosításához használt tanúsítványt telepíteni kell a szerepkörre. Az újraindítás elkerüléséhez [töltsön fel egy tanúsítványt a Cloud Service](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) -be, majd térjen vissza erre a párbeszédpanelre.

4. A **szerepkörök** területen válassza ki a frissíteni kívánt szerepkört, vagy válassza az **összes** lehetőséget az összes szerepkörhöz.

5. A konfigurációs frissítések befejezését követően válassza a **Mentés** lehetőséget. Néhány percet is igénybe vehet, mielőtt a szerepkör-példányok megkapják a kapcsolatokat.

## <a name="remote-into-role-instances"></a>Távoli szerepkör-példányok

Ha Távoli asztal engedélyezve van a szerepkörökön, közvetlenül a Azure Portal kezdeményezheti a kapcsolatokat:

1. A **példányok beállításainak** megnyitásához kattintson a **példányok** elemre.
2. Válasszon Távoli asztal konfigurált szerepkör-példányt.
3. Kattintson a **Kapcsolódás** gombra a szerepkör-példányhoz tartozó RDP-fájl letöltéséhez.

    ![Cloud Services – Távoli asztal képe](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Kattintson a **Megnyitás** , majd a **Kapcsolódás** elemre a távoli asztal kapcsolat indításához.

>[!NOTE]
> Ha a felhőalapú szolgáltatás egy NSG mögött található, előfordulhat, hogy olyan szabályokat kell létrehoznia, amelyek engedélyezik a forgalmat a **3389** -es és a **20000**-es porton.  Távoli asztal a **3389**-es portot használja.  A Cloud Service-példányok terheléselosztás alatt állnak, így nem lehet közvetlenül szabályozni, hogy melyik példányt kívánja csatlakozni.  A *RemoteForwarder* és a *REMOTEACCESS* ügynök az RDP-forgalmat kezeli, és lehetővé teszi az ügyfél számára, hogy RDP-cookie-t küldjön, és megadhat egy egyedi példányt a kapcsolódáshoz.  A *RemoteForwarder* és a *remoteaccess* -ügynökök megkövetelik, hogy a **20000**-es port nyitva legyen, ami blokkolható, ha van NSG.

## <a name="additional-resources"></a>További források

[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)
