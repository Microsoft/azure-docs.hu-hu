---
title: Felhőalapú szolgáltatás létrehozása és üzembe helyezése (klasszikus) | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre felhőalapú szolgáltatást a gyors létrehozás módszer használatával, és hogyan töltheti fel a Cloud Service-csomagok feltöltését és üzembe helyezését az Azure-ban.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 879b86714adf50b5a4da4398389405063ac046dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743406"
---
# <a name="how-to-create-and-deploy-an-azure-cloud-service-classic"></a>Azure Cloud Service létrehozása és üzembe helyezése (klasszikus)

> [!IMPORTANT]
> Az [azure Cloud Services (bővített támogatás)](../cloud-services-extended-support/overview.md) az Azure Cloud Services termék új, Azure Resource Manager alapú üzembe helyezési modellje.Ezzel a módosítással az Azure Service Manager-alapú üzemi modellben futó Azure Cloud Services Cloud Services (klasszikus) néven lett átnevezve, és az összes új központi telepítésnek [Cloud Services (kiterjesztett támogatás)](../cloud-services-extended-support/overview.md)kell használnia.

A Azure Portal kétféle módszert biztosít a felhőalapú szolgáltatások létrehozására és üzembe helyezésére: *gyors létrehozás* és *Egyéni létrehozás*.

Ez a cikk azt ismerteti, hogyan használható a gyors létrehozás módszer egy új felhőalapú szolgáltatás létrehozásához, majd a **feltöltéshez** a Cloud Service-csomagok feltöltéséhez és üzembe helyezéséhez az Azure-ban. Ha ezt a módszert használja, a Azure Portal elérhetővé teszi az összes követelmény betöltéséhez szükséges hasznos hivatkozásokat. Ha készen áll a Cloud Service üzembe helyezésére a létrehozásakor, mindkettőt megteheti az egyéni létrehozás használatával.

> [!NOTE]
> Ha azt tervezi, hogy közzéteszi a Cloud Service-t az Azure DevOps, használja a gyors létrehozás lehetőséget, majd állítsa be az Azure DevOps-közzétételt az Azure rövid útmutatóból vagy az irányítópultból. További információkért lásd: folyamatos kézbesítés az Azure-ba az [Azure DevOps használatával][TFSTutorialForCloudService], vagy a **gyorskonfigurálás** oldal súgójában.
>
>

## <a name="concepts"></a>Alapelvek
Az Azure-ban az alkalmazások felhőalapú szolgáltatásként való üzembe helyezéséhez három összetevő szükséges:

* **Szolgáltatás definíciója**  
  A Cloud Service-definíciós fájl (. csdef) meghatározza a szolgáltatási modellt, beleértve a szerepkörök számát is.
* **Szolgáltatás konfigurációja**  
  A Cloud Service konfigurációs fájlja (. cscfg) konfigurációs beállításokat biztosít a felhőalapú szolgáltatáshoz és az egyes szerepkörökhöz, beleértve a szerepkör-példányok számát is.
* **Szolgáltatáscsomag**  
  A szervizcsomag (. cspkg) tartalmazza az alkalmazás kódját és konfigurációit, valamint a szolgáltatás definíciós fájlját.

Ezekről a szolgáltatásokról és a csomagok létrehozásáról [itt](cloud-services-model-and-package.md)tájékozódhat.

## <a name="prepare-your-app"></a>Az alkalmazás előkészítése
A Cloud Service üzembe helyezése előtt létre kell hoznia a Cloud Service-csomagot (. cspkg) az alkalmazás kódjából és egy Cloud Service-konfigurációs fájlból (. cscfg). Az Azure SDK eszközöket biztosít ezeknek a szükséges központi telepítési fájloknak a előkészítéséhez. Az SDK-t az [Azure letöltések](https://azure.microsoft.com/downloads/) oldaláról telepítheti abban a nyelven, amelyben az alkalmazás kódját szeretné fejleszteni.

A Service-csomagok exportálása előtt a felhőalapú szolgáltatások három funkciójának speciális konfigurációra van szüksége:

* Ha olyan felhőalapú szolgáltatást szeretne üzembe helyezni, amely Transport Layer Security (TLS) protokollt (korábbi nevén SSL (SSL)) használ az adattitkosításhoz, [konfigurálja az alkalmazást](cloud-services-configure-ssl-certificate-portal.md#modify) a TLS-hez.
* Ha Távoli asztal kapcsolatokat kíván konfigurálni a szerepkör-példányokhoz, [konfigurálja a szerepköröket](cloud-services-role-enable-remote-desktop-new-portal.md) a távoli asztalhoz.
* Ha szeretné beállítani a felhőalapú szolgáltatás részletes figyelését, engedélyezze Azure Diagnostics a Cloud Service-hez. A *minimális figyelés* (az alapértelmezett figyelési szint) a gazdagép operációs rendszereiből összegyűjtött teljesítményszámlálókat használja a szerepkör-példányok (virtuális gépek) számára. A *részletes figyelés* további mérőszámokat gyűjt a szerepkör példányain belüli teljesítményadatok alapján, így az alkalmazások feldolgozásakor felmerülő problémák alaposabb elemzését is lehetővé teszi. A Azure Diagnostics engedélyezéséről a [diagnosztika engedélyezése az Azure-ban](cloud-services-dotnet-diagnostics.md)című témakörben talál további információt.

Ha felhőalapú szolgáltatást szeretne létrehozni webes szerepkörök vagy feldolgozói szerepkörök központi telepítésével, [létre kell hoznia a szervizcsomagot](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Előkészületek
* Ha még nem telepítette az Azure SDK-t, kattintson az **Azure SDK telepítése** lehetőségre az [Azure downloads oldalának](https://azure.microsoft.com/downloads/)megnyitásához, majd töltse le az SDK-t arra a nyelvre, amelyben a kódot fejleszteni szeretné. (Ezt később is megteheti.)
* Ha bármelyik szerepkör-példányhoz tanúsítvány szükséges, hozza létre a tanúsítványokat. A Cloud Serviceshez titkos kulccsal rendelkező. pfx fájl szükséges. A tanúsítványokat feltöltheti az Azure-ba a Cloud Service létrehozása és üzembe helyezése során.

## <a name="create-and-deploy"></a>Létrehozás és üzembe helyezés
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/).
2. Kattintson az **erőforrás létrehozása > a számítás** elemre, majd görgessen le, és kattintson a **Cloud Service** elemre.

    ![Felhőbeli service1 közzététele](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. Az új **Cloud Service** ablaktáblán adja meg a **DNS-név** értékét.
4. Hozzon létre egy új **erőforráscsoportot** , vagy válasszon ki egy meglévőt.
5. Válasszon egy **helyet**.
6. Kattintson a **csomag** elemre. Ekkor megnyílik a **csomag feltöltése** panel. Töltse ki a kötelező mezőket. Ha bármelyik szerepkör egyetlen példányt tartalmaz, akkor is győződjön meg arról, **hogy az üzembe helyezés akkor is, ha egy vagy több szerepkör egyetlen példányt tartalmaz** .
7. Győződjön meg arról, hogy a **Start Deployment** beállítás van kiválasztva.
8. Kattintson **az OK** gombra, amely a **csomag feltöltése** panelt fogja lezárva.
9. Ha nem rendelkezik a hozzáadni kívánt tanúsítványokkal, kattintson a **Létrehozás** gombra.

    ![Felhőbeli service2 közzététele](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Tanúsítvány feltöltése
Ha a központi telepítési csomag [tanúsítvány használatára lett konfigurálva](cloud-services-configure-ssl-certificate-portal.md#modify), akkor most feltöltheti a tanúsítványt.

1. Válassza a **tanúsítványok** lehetőséget, majd a **tanúsítványok hozzáadása** PANELEN válassza ki a TLS/SSL-tanúsítvány. pfx fájlját, majd adja meg a tanúsítvány **jelszavát** .
2. Kattintson a **tanúsítvány csatolása** elemre, majd kattintson az **OK** gombra a **tanúsítványok hozzáadása** panelen.
3. Kattintson a **create (létrehozás** ) elemre a **Cloud Service** ablaktáblán. Ha az üzemelő példány elérte a **kész** állapotot, folytassa a következő lépésekkel.

    ![Felhőbeli service3 közzététele](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Ellenőrizze, hogy az üzemelő példány sikeresen befejeződött-e
1. Kattintson a Cloud Service-példányra.

    Az állapotnak a szolgáltatás **futását** kell mutatnia.
2. Az **Essentials** területen kattintson a **webhely URL-címére** a Cloud Service egy böngészőben való megnyitásához.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: ./cloud-services-choose-me.md

## <a name="next-steps"></a>Következő lépések
* [A felhőalapú szolgáltatás általános konfigurációja](cloud-services-how-to-configure-portal.md).
* Konfigurálja az [Egyéni tartománynevet](cloud-services-custom-domain-name-portal.md).
* [A felhőalapú szolgáltatás kezelése](cloud-services-how-to-manage-portal.md).
* Konfigurálja a [TLS/SSL-tanúsítványokat](cloud-services-configure-ssl-certificate-portal.md).