---
title: A Micro Focus Enterprise Server 5,0 futtatása az Azure-beli Docker-tárolóban | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan futtathatja a Micro Focus Enterprise Server 5,0-et egy Docker-tárolóban Microsoft Azureon.
services: virtual-machines
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: 5c436eae53b16c980e9725cfef0573367d144842
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548376"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>A Micro Focus Enterprise Server 5,0 futtatása az Azure-beli Docker-tárolóban

A Micro Focus Enterprise Server 5,0 egy Docker-tárolóban is futtatható az Azure-ban. Ez az oktatóanyag bemutatja, hogyan. A vállalati kiszolgáló Windows CICS (Customer Information Control System) acctdemo bemutatóját használja.

A Docker a hordozhatóságot és az elkülönítést adja az alkalmazásokhoz. Például exportálhat egy Docker-rendszerképet egy Windows rendszerű virtuális gépről (VM) egy másikra, vagy egy adattárból egy Windows Serverre a Docker használatával. A Docker-rendszerkép az új helyen fut ugyanazzal a konfigurációval, anélkül, hogy telepítenie kellene a vállalati kiszolgálót. A rendszerkép részét képezi. A licencelési megfontolások továbbra is érvényben vannak.

Ez az oktatóanyag telepíti a **Windows 2016 Datacenter-t a containers** VM-mel az Azure Marketplace-ről. Ez a virtuális gép tartalmaz **Docker-18.09.0**. Az alábbi lépések bemutatják, hogyan helyezheti üzembe a tárolót, hogyan futtathatja, majd egy 3270-emulátorral csatlakozhat hozzá.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt tekintse át az alábbi előfeltételeket:

-   Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

-   A Micro Focus szoftver és egy érvényes licenc (vagy próbaverziós licenc). Ha már meglévő Micro Focus-ügyfelet használ, forduljon a Micro Focus képviselőjéhez. Ellenkező esetben [próbaverziót igényelhet](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

    > [!Note] 
    > A Docker demonstrációs fájljai a vállalati kiszolgáló 5,0-es verziójában találhatók. Ez az oktatóanyag az ENT \_ Server \_ dockerfiles \_ 5,0 \_windows.zipt használja. Az első lépésekhez nyissa meg a vállalati kiszolgáló telepítési fájljához hozzáférő helyet, vagy lépjen a *Micro Focus* elemre.

-   A [vállalati kiszolgáló és a nagyvállalati fejlesztő](https://www.microfocus.com/documentation/enterprise-developer/#%22)dokumentációja.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1.  Gondoskodjon az adathordozó védelméről a ENT \_ kiszolgáló \_ dockerfiles \_ 5,0 \_windows.zip fájlból. Az ES-Docker-Prod-XXXXXXXX. mflic licencelési fájl biztonságossá tétele (a Docker-lemezképek létrehozásához szükséges).

2.  Hozza létre a virtuális gépet. Ehhez nyissa meg Azure Portalt, válassza az **erőforrás létrehozása** lehetőséget a bal felső menüből, és szűrje a *Windows Server operációs rendszer* alapján. Az eredmények között válassza a **Windows Server lehetőséget.** A következő képernyőn válassza a **Windows Server 2016 Datacenter – tárolók** lehetőséget.

    ![Azure Portal keresési eredmények képernyőképe](./media/run-image-1.png)

3.  A virtuális gép tulajdonságainak konfigurálásához válassza a példány részletei lehetőséget:

    1.  Válassza ki a virtuális gép méretét. Ebben az oktatóanyagban érdemes egy **standard DS2 \_ v3** virtuális gépet használni 2 VCPU és 16 GB memóriával.

    2.  Válassza ki azt a **régiót** és **erőforráscsoportot** , amelyre telepíteni szeretné.

    3.  A **rendelkezésre állási beállításokhoz** használja az alapértelmezett beállítást.

    4.  A **Felhasználónév** mezőbe írja be a használni kívánt rendszergazdai fiókot és a jelszót.

    5.  Győződjön meg arról, hogy a 3389-es **RDP-port** nyitva van. Csak a portot nyilvánosan elérhetővé kell tenni, hogy be lehessen jelentkezni a virtuális gépre. Ezután fogadja el az összes alapértelmezett értéket, majd kattintson a **felülvizsgálat + létrehozás** gombra.

    ![A virtuális gép létrehozása panel képernyőképe](./media/run-image-2.png)

4.  Várjon, amíg a telepítés befejeződik (néhány perc). Egy üzenet jelzi, hogy a virtuális gép létrejött.

5.  Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy a virtuális gép **Áttekintés** paneljére lépjen.

6.  A jobb oldalon válassza a **kapcsolat** lehetőséget. A jobb oldalon megjelenik a **Kapcsolódás virtuális géphez** lehetőség.

7.  Az **RDP-fájl letöltése** gombra kattintva letöltheti a távoli asztali protokoll (RDP) fájlját, amely lehetővé teszi a virtuális géphez való csatolást.

8.  Miután a fájl letöltése befejeződött, nyissa meg a fájlt, és írja be a virtuális géphez létrehozott felhasználónevet és jelszót.

    > [!Note]    
    > A bejelentkezéshez ne használja vállalati hitelesítő adatait. (Az RDP-ügyfél feltételezi, hogy ezeket szeretné használni. Nem.)

9.  Válassza a **további lehetőségek** lehetőséget, majd válassza ki a virtuális gép hitelesítő adatait.

Ezen a ponton a virtuális gép RDP-kapcsolaton keresztül fut és csatlakoztatva van. Bejelentkezett, és készen áll a következő lépésre.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Homokozó könyvtár létrehozása és a zip-fájl feltöltése

1.  Hozzon létre egy könyvtárat a virtuális gépen, ahol feltöltheti a demót és a licenceket tartalmazó fájlokat. Például **C: \\ homokozó**.

2.  Töltse fel az **ENT \_ server \_ dockerfiles \_ 5,0 \_windows.zip** és az **es-Docker-Prod-XXXXXXXX. mflic** fájlt a létrehozott könyvtárba.

3.  Bontsa ki a zip-fájl tartalmát a kinyerési folyamat által létrehozott **ENT \_ server \_ dockerfiles \_ 5,0 \_ Windows** könyvtárba. Ez a könyvtár egy információs fájlt (. html és. txt fájlt) és két alkönyvtárat, **EnterpriseServer** és **példákat** tartalmaz.

4.  Másolja az **es-Docker-Prod-XXXXXXXX. mflic** parancsot a C: \\ sandbox \\ ENT \_ kiszolgáló \_ dockerfiles \_ 5,0 \_ Windows \\ EnterpriseServer és C: \\ sandbox \\ ENT \_ Server \_ dockerfiles \_ 5,0 \_ Windows \\ példák \\ CICS könyvtárak.  
      
    > [!Note]
    > Győződjön meg arról, hogy mindkét könyvtárba másolja a licencelési fájlt. A Docker-Build lépéshez szükségük van a lemezképek megfelelő licenccel való elvégzésére.

## <a name="check-docker-version-and-create-base-image"></a>Docker-verzió keresése és alaprendszerkép létrehozása

> [!Important]  
> A megfelelő Docker-rendszerkép létrehozása két lépésből álló folyamat. Először hozza létre a vállalati kiszolgáló 5,0 alaprendszerképét. Ezután hozzon létre egy másik rendszerképet az x64 platformhoz. Bár létrehozhat egy x86-os (32 bites) rendszerképet, a 64 bites rendszerképet is használhatja.

1.  Nyisson meg egy parancssort.

2.  Győződjön meg arról, hogy a Docker telepítve van. A parancssorba írja be a következőt: **Docker Version**  
    Például a verzió 18.09.0 volt.

3.  A könyvtár módosításához írja be a következőt:  
    **CD \\ A sandbox \\ ENT \_ kiszolgáló \_ dockerfiles \_ 5,0 \_ Windows \\ EnterpriseServer**.

4.  A kezdeti alaprendszerkép létrehozási folyamatának megkezdéséhez írja be **bld.bat IacceptEULA** . Várjon néhány percet a folyamat futtatásához. Az eredmények között figyelje meg a két létrehozott rendszerképet – egyet az x64-re és egyet az x86-hoz:

    ![Képek megjelenítése Parancsablak](./media/run-image-3.png)

5.  A CICS bemutató utolsó rendszerképének létrehozásához váltson a CICS könyvtárba úgy, hogy beírja a **CD \\ sandbox \\ ent \_ kiszolgáló \_ dockerfiles \_ 5,0 \_ Windows \\ példák \\ CICS**.

6.  A rendszerkép létrehozásához írja be a következőt: **bld.bat x64**. Várjon néhány percet, amíg a folyamat elindul, és az üzenet azt jelzi, hogy a rendszerkép létrejött.

7.  Írja be a **Docker-rendszerképeket** a virtuális gépre telepített összes Docker-rendszerkép listájának megjelenítéséhez. Győződjön meg arról, hogy a **Focus/es-acctdemo** az egyik.

    ![Az es-acctdemo rendszerkép megjelenítése Parancsablak](./media/run-image-4.png)

## <a name="run-the-image"></a>A rendszerkép futtatása

1.  A vállalati kiszolgáló 5,0-es és a acctdemo-alkalmazás indításához írja be a parancssorba a következőt:

    ```
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ```

1.  Telepítsen egy 3270-es terminál-emulátort, például a [x3270](http://x3270.bgp.nu/) -t, és csatlakoztassa a 9040-es porton keresztül a futó rendszerképhez.

2.  Szerezze be a acctdemo tároló IP-címét, hogy a Docker Dynamic Host Configuration Protocol (DHCP) kiszolgálóként működjön az általa kezelt tárolók esetében:

    1.  A futó tároló AZONOSÍTÓjának beolvasása. Írja be a **Docker PS** parancsot a parancssorba, és jegyezze fel az azonosítót (ebben a példában a **22a0fe3159d0** ). Mentse a következő lépéshez.

    2.  A acctdemo tároló IP-címének lekéréséhez használja az előző lépés tároló-AZONOSÍTÓját az alábbiak szerint:

    ```
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

    Például:

    ```
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

4. Jegyezze fel a acctdemo-rendszerkép IP-címét. Például a következő kimenetben található 172.19.202.52.

    ![Képernyőkép Parancsablak IP-címet megjelenítő](./media/run-image-5.png)

5. Csatlakoztassa a lemezképet az emulátor használatával. Konfigurálja az emulátort a acctdemo-rendszerkép és a 9040-es port használatára. Itt a **172.19.202.52:9040**. A tiéd hasonló lesz. Megnyílik a **Bejelentkezés a CICS** képernyőn.

    ![Képernyőkép a CICS való bejelentkezésről](./media/run-image-6.png)

6. Jelentkezzen be a CICS régióba úgy, hogy beírja a **jelszót** a **userid** és a **SYSAD** **SYSAD** .

7. Törölje a képernyőt az emulátor kiosztás használatával. A x3270 válassza a **kiosztás** menüpontot.

8. A acctdemo alkalmazás indításához írja be a következőt: **acct**. Megjelenik az alkalmazás kezdeti képernyője.

     ![A képernyőképen az alkalmazást megjelenítő konzolablak látható.](./media/run-image-7.png)

9. Kísérletezzen a megjelenítési fiókok típusaival. Írja be például a **D** értéket a kérelemhez, a **11111** pedig a **fiókhoz**. A kipróbálható egyéb számlaszámok 22222, 33333 stb.

    ![A képernyőképen az alkalmazás különböző értékeinek szerkesztését láthatja.](./media/run-image-8.png)

10. A vállalati kiszolgáló felügyeleti konzoljának megjelenítéséhez nyissa meg a parancssort, és írja be a **Start http: 172.19.202.52:86** parancsot.

    ![Vállalati kiszolgáló felügyeleti konzolja](media/run-image-9.png)

Ennyi az egész! Most egy CICS-alkalmazást futtat és felügyel egy Docker-tárolóban.

## <a name="next-steps"></a>Következő lépések

-   [A Micro Focus Enterprise Server 5,0 és Enterprise Developer 5,0 telepítése az Azure-ban](./set-up-micro-focus-azure.md)

-   [Nagyszámítógépes alkalmazásmigrálás](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
