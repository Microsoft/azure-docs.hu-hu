---
title: Frissítés telepítése Azure Stack Edge Pro GPU-eszközön | Microsoft Docs
description: Útmutató a frissítések alkalmazásához a Azure Portal és helyi webes felhasználói felülettel Azure Stack Edge Pro GPU eszközhöz és a Kubernetes-fürthöz az eszközön
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 4590949f2feb86dc344dce87f3ff447e0e05e8ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438114"
---
# <a name="update-your-azure-stack-edge-pro-gpu"></a>A Azure Stack Edge Pro GPU frissítése 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek a frissítés telepítéséhez a Azure Stack Edge Pro-val a GPU-n keresztül a helyi webes felületen és a Azure Portal keresztül. A szoftverfrissítések vagy gyorsjavítások alkalmazásával megőrizheti Azure Stack Edge Pro-eszközét és a hozzá tartozó Kubernetes-fürtöt az eszközön naprakészen.

A cikkben ismertetett eljárás a szoftver egy másik verziójával lett elvégezve, de a folyamat a jelenlegi szoftververzió esetében is változatlan marad.

> [!IMPORTANT]
> - Az **2101** -es frissítés az aktuális frissítés, és a következőnek felel meg:
>   - Eszköz szoftverének verziója – **2.2.1473.2521**
>   - Kubernetes Server Version- **v 1.17.3**
>   - IoT Edge verziója: **0.1.0-beta10**
>    
>    A frissítés újdonságait a [kibocsátási megjegyzésekben](azure-stack-edge-gpu-2101-release-notes.md)találhatja meg.
> - Az 2101-es frissítés alkalmazásához az eszköznek 2010-at kell futnia.
> - Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. Ez a frissítés tartalmazza az eszközhöz tartozó szoftverfrissítéseket és a Kubernetes frissítéseit. Mivel az Azure Stack Edge Pro egyetlen csomópontos eszköz, a folyamatban lévő I/O-műveletek megszakadnak, és az eszköz akár 1,5 órányi állásidőt is megtapasztalhat a frissítéshez.

Az eszközön a frissítések telepítéséhez először konfigurálnia kell a frissítési kiszolgáló helyét. A frissítési kiszolgáló konfigurálása után a frissítéseket a Azure Portal felhasználói felületén vagy a helyi webes felületen keresztül is alkalmazhatja.

Ezeket a lépéseket az alábbi szakaszokban ismertetjük.

## <a name="configure-update-server"></a>Frissítési kiszolgáló konfigurálása

1. A helyi webes kezelőfelületen lépjen a **konfigurációs**  >  **frissítési kiszolgáló** elemre.
   
    ![Frissítések konfigurálása 1](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. A **frissítési kiszolgáló típusának kiválasztása** területen a legördülő listából válassza ki a Microsoft Update kiszolgáló (alapértelmezett) vagy a Windows Server Update Services lehetőséget.  
   
    Ha a Windows Server Update Services frissítését, akkor a kiszolgáló URI-JÁT kell megadnia. Az URI-kiszolgáló a kiszolgálóhoz csatlakozó összes eszközön telepíti a frissítéseket.

    ![Frissítések konfigurálása 2](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    A WSUS-kiszolgáló a frissítések felügyeletére és terjesztésére szolgál a felügyeleti konzolon keresztül. A WSUS-kiszolgáló a szervezeten belüli többi WSUS-kiszolgáló frissítési forrása is lehet. A frissítés forrásaként szolgáló WSUS-kiszolgálót felsőbb rétegbeli kiszolgálónak nevezik. A WSUS implementációjában a hálózaton legalább egy WSUS-kiszolgálónak képesnek kell lennie csatlakozni a Microsoft Updatehoz az elérhető frissítési információk beszerzéséhez. Rendszergazdaként a hálózati biztonság és konfiguráció alapján határozhatja meg, hogy hány más WSUS-kiszolgáló csatlakozik közvetlenül a Microsoft Updatehoz.
    
    További információ: [Windows Server Update Services (WSUS)](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Javasoljuk, hogy telepítse a frissítéseket a Azure Portalon keresztül. Az eszköz naponta egyszer automatikusan frissítéseket keres. Ha a frissítések elérhetők, megjelenik egy értesítés a portálon. Ezt követően letöltheti és telepítheti a frissítéseket.

> [!NOTE]
> Győződjön meg arról, hogy az eszköz kifogástalan állapotban van, és az állapota **online** állapotba kerül, mielőtt folytatná a frissítések telepítését.

1. Ha a frissítések elérhetők az eszközhöz, egy értesítés jelenik meg. Válassza ki az értesítést, vagy a felső menüsorban **frissítse az eszközt**. Ez lehetővé teszi az eszköz szoftverfrissítések alkalmazását.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. A **Device Updates (eszközök frissítése** ) panelen győződjön meg arról, hogy áttekintette a kibocsátási megjegyzések új szolgáltatásaihoz társított licencfeltételeket.

    Választhat, hogy **letölti és telepíti** a frissítéseket, vagy csak **letölti** a frissítéseket. A letöltött frissítések telepítését későbbre halaszthatja.

    ![A szoftver verziója a 2. frissítés után](./media/azure-stack-edge-gpu-install-update/portal-update-2-a.png)    

    Ha le szeretné tölteni és telepíteni kívánja a frissítéseket, ellenőrizze, hogy a frissítések telepítése automatikusan megtörtént-e a letöltés befejeződése után.

    ![A szoftver verziója a 3. frissítés után](./media/azure-stack-edge-gpu-install-update/portal-update-2-b.png)

3. A frissítések letöltése megkezdődik. Ekkor megjelenik egy értesítés arról, hogy a letöltés folyamatban van.

    ![A szoftver verziója a 4-es frissítés után](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    Egy értesítési szalagcím is megjelenik a Azure Portalban. Ez a letöltési folyamatra utal.

    ![A szoftver verziója az 5. frissítés után](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    Kiválaszthatja ezt az értesítést, vagy az **eszköz frissítése** lehetőség kiválasztásával megtekintheti a frissítés részletes állapotát.

    ![Szoftver verziója a 6. frissítés után](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)


4. A letöltés befejezését követően az értesítési szalagcím frissíti a befejezést. Ha úgy dönt, hogy letölti és telepíti a frissítéseket, a telepítés automatikusan elindul.

    ![Szoftververzió a 7. frissítés után](./media/azure-stack-edge-gpu-install-update/portal-update-6.png)

    Ha úgy döntött, hogy csak a frissítések letöltését választotta, válassza ki az értesítést az **eszköz frissítései** panel megnyitásához. Válassza a **Telepítés** gombot.
  
    ![Szoftververzió a 8. frissítés után](./media/azure-stack-edge-gpu-install-update/portal-update-7.png)

5. Ekkor megjelenik egy értesítés arról, hogy a telepítés folyamatban van.

    ![Szoftververzió a 9-es frissítés után](./media/azure-stack-edge-gpu-install-update/portal-update-8.png)
 
    A portál egy tájékoztató riasztást is megjelenít, amely jelzi, hogy a telepítés folyamatban van. Az eszköz offline állapotba kerül, és karbantartási üzemmódban van.
   
    ![A szoftver verziója a 10. frissítés után](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Mivel ez egy 1 csomópontos eszköz, az eszköz a frissítések telepítése után újraindul. Az újraindítás során a kritikus riasztás azt jelzi, hogy az eszköz szívverése megszakadt.

    ![A szoftver verziója a 11. frissítés után](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Válassza ki a riasztást a megfelelő eszköz eseményének megtekintéséhez.
    
    ![Szoftver verziója a 12. frissítés után](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)


7. Az újraindítás után az eszköz ismét a karbantartási módba kerül, és megjelenik egy tájékoztató riasztás, amely azt jelzi, hogy.

    Ha a felső menüsávban az **eszköz frissítése** lehetőséget választja, láthatja a frissítések előrehaladását.   

8. A frissítések telepítése után az eszköz állapota **online** állapotra frissül. 

    ![A 13-as frissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-14.png)

    A felső menüsávban válassza az **eszközök frissítései** lehetőséget. Ellenőrizze, hogy a frissítés telepítése sikeres volt-e, és hogy az eszköz szoftverének verziója megfelel-e.

    ![A szoftver verziója a 14. frissítés után](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

<!--9. You will again see a notification that updates are available. These are the Kubernetes updates. Select the notification or select **Update device** from the top command bar.

    ![Software version after update 15](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)

10. Download the Kubernetes updates. You can see that the package size is different when compared to the previous update package.

    ![Software version after update 16](./media/azure-stack-edge-gpu-install-update/portal-update-17.png)

    The process of installation is identical to that of device updates. First the updates are downloaded.

    ![Software version after update 17](./media/azure-stack-edge-gpu-install-update/portal-update-18.png)    
    
11. Once the updates are downloaded, you can then install the updates. 

    ![Software version after update 18](./media/azure-stack-edge-gpu-install-update/portal-update-19.png)

    As the updates are installed, the device is put into maintenance mode. The device does not restart for the Kubernetes updates. -->

Ha az eszköz szoftver-és Kubernetes frissítése sikeresen megtörtént, a szalagcím-értesítés eltűnik. Az eszközön már az eszköz szoftver-és Kubernetes legújabb verziója érhető el.


## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

* A frissítés vagy a gyorsjavítás letöltése
* A frissítés vagy a gyorsjavítás telepítése

Ezeket a lépéseket a következő szakaszokban részletesen ismertetjük.


### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

A frissítés letöltéséhez hajtsa végre a következő lépéseket. A frissítést a Microsoft által biztosított helyről vagy a Microsoft Update katalógusból töltheti le.


Az alábbi lépések végrehajtásával töltse le a frissítést a Microsoft Update-katalógusból.

1. Indítsa el a böngészőt, és navigáljon a gombra [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

    ![Keresés a katalógusban](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. A Microsoft Update-katalógus keresőmező mezőjébe írja be a letölteni kívánt frissítés gyorsjavításának vagy használati feltételeinek a tudásbázisát (KB). Adja meg például a **Azure stack Edge Pro** kifejezést, majd kattintson a **Keresés** gombra.
   
    A frissítési lista **Azure stack Edge 2101-es frissítésként** jelenik meg.
   
    <!--![Search catalog 2](./media/azure-stack-edge-gpu-install-update/download-update-2-b.png)-->

4. Válassza a **Letöltés** lehetőséget. Két fájl tölthető le *SoftwareUpdatePackage.exe* és *Kubernetes_Package.exe* utótagokkal, amelyek megfelelnek az eszköz szoftverfrissítések és a Kubernetes frissítéseinek. Töltse le a fájlokat a helyi rendszer egyik mappájába. A mappát átmásolhatja egy olyan hálózati megosztásra is, amely elérhető az eszközről.

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg az alábbiakról:

 - A frissítés vagy a gyorsjavítás helyileg, a gazdagépen vagy egy hálózati megosztáson keresztül érhető el.
 - Az eszköz állapota Kifogástalan, ahogy a helyi webes felhasználói felület **Áttekintés** lapján látható.

   ![eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png)

Az eljárás végrehajtása körülbelül 20 percet vesz igénybe. A frissítés vagy a gyorsjavítás telepítéséhez hajtsa végre a következő lépéseket.

1. A helyi webes kezelőfelületen lépjen a **karbantartási**  >  **szoftverfrissítés** elemre. Jegyezze fel a futtatott szoftver verzióját. 
   
   ![2. eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Adja meg a frissítési fájl elérési útját. Ha hálózati megosztásra helyezi, tallózással is megkeresheti a frissítési telepítési fájlt. Válassza ki *SoftwareUpdatePackage.exe* utótaggal rendelkező szoftverfrissítési fájlt.

   ![3. eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-3-a.png)

3. Kattintson az **Alkalmaz** gombra.

   ![4. eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz. Mivel az eszköz egyetlen csomópontos eszköz, a frissítés alkalmazása után az eszköz újraindul, és leállási idő van. 
   
   ![5. eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület ebben az időtartamban nem érhető el.
   
6. Az újraindítás után a rendszer a **bejelentkezési** oldalra kerül. Annak ellenőrzéséhez, hogy az eszköz szoftvere frissítve lett-e, a helyi webes felületen lépjen a **karbantartási**  >  **szoftverfrissítés** lapra. A jelenlegi kiadásban a megjelenő szoftververzió **Azure stack Edge 2101**. 

   <!--![update device 6](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png)-->

7. Most frissíti a Kubernetes szoftver verzióját. Ismételje meg a fenti lépéseket. Adja meg a Kubernetes frissítési fájljának elérési útját az *Kubernetes_Package.exe* utótaggal.  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)-->

8. Válassza a **frissítés alkalmazása** lehetőséget.

   ![7. eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz.

10. A Kubernetes frissítésének sikeres telepítése után a rendszer nem módosítja a megjelenő szoftvereket a **karbantartási**  >  **szoftverfrissítés** során.


## <a name="next-steps"></a>Következő lépések

További információ [az Azure stack Edge Pro felügyeletéről](azure-stack-edge-manage-access-power-connectivity-mode.md).