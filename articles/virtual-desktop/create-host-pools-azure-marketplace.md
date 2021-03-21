---
title: Windows rendszerű virtuális asztali címkészlet Azure Portal – Azure
description: Windows rendszerű virtuális asztali címkészlet létrehozása a Azure Portal használatával.
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 03/10/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 60566b95447c1b69fb257435f45a11524ac5d8b2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617343"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Oktatóanyag: állomáslista létrehozása a Azure Portal

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). A Windows rendszerű virtuális asztallal (klasszikus) létrehozott objektumokat nem lehet felügyelni a Azure Portal.

A gazdagép-készletek egy vagy több azonos virtuális gép (VM) gyűjteményei, amelyek a Windows rendszerű virtuális asztali környezetekben találhatók. Mindegyik gazdagép tartalmazhatja azt az alkalmazáscsoport-csoportot, amelyet a felhasználók a fizikai asztalon lévők használatával kezelhetnek.

Ebből a cikkből megtudhatja, hogyan hozhat létre egy Windows rendszerű virtuális asztali környezethez tartozó gazdagépet a Azure Portal használatával. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a Windows rendszerű virtuális asztali gazdagépek létrehozásához, egy Azure-előfizetésben található virtuális gépekkel rendelkező erőforráscsoport létrehozásához, a virtuális gépek Azure Active Directory (AD) tartományhoz való csatlakoztatásához, valamint a Windows rendszerű virtuális asztali gépek regisztrálásához.

## <a name="prerequisites"></a>Előfeltételek

A gazdagépek létrehozásához a következő paramétereket kell megadnia:

- A virtuális gép rendszerképének neve
- Virtuális gép konfigurációja
- Tartomány és hálózat tulajdonságai
- Windows rendszerű virtuális asztali gazdagép-készlet tulajdonságai

Emellett a következő dolgokat is ismernie kell:

- A használni kívánt rendszerkép forrása. Ez az Azure-katalógusból vagy egy egyéni rendszerkép?
- A tartományhoz való csatlakozáshoz szükséges hitelesítő adatok.

Győződjön meg arról is, hogy regisztrálta a Microsoft. DesktopVirtualization erőforrás-szolgáltatót. Ha még nem tette meg, nyissa meg az **előfizetések** elemet, válassza ki az előfizetés nevét, majd válassza az **erőforrás-szolgáltatók** lehetőséget. Keresse meg a DesktopVirtualization, válassza a Microsoft. DesktopVirtualization, majd a regisztráció lehetőséget.

Amikor létrehoz egy Windows rendszerű virtuális asztali címkészletet a Azure Resource Manager sablonnal, létrehozhat egy virtuális gépet az Azure-katalógusból, egy felügyelt képből vagy egy nem felügyelt rendszerképből. A virtuálisgép-lemezképek létrehozásával kapcsolatos további információkért lásd: [Windows VHD vagy VHDX előkészítése az Azure-ba való feltöltéshez](../virtual-machines/windows/prepare-for-upload-vhd-image.md) , valamint [egy általánosított virtuális gép felügyelt rendszerképének létrehozása az Azure-ban](../virtual-machines/windows/capture-image-resource.md).

Ha még nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) , mielőtt elkezdené ezeket az utasításokat.

## <a name="begin-the-host-pool-setup-process"></a>A gazdagép-készlet telepítési folyamatának megkezdése

Az új címkészlet létrehozásának megkezdéséhez:

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com/) webhelyen.
   
   >[!NOTE]
   > Ha bejelentkezik a US Gov portálra, ugorjon a [https://portal.azure.us/](https://portal.azure.us/) helyet.

2. Adja meg a **Windows rendszerű virtuális asztalt** a keresőmezőbe, majd keresse meg és válassza ki a **Windows rendszerű virtuális asztali** szolgáltatások elemet.

3. A **Windows rendszerű virtuális asztal** áttekintése lapon válassza **a gazdagép létrehozása** lehetőséget.

4. Az **alapvető beállítások** lapon válassza ki a megfelelő előfizetést a Project Details (projekt részletei) területen.

5. Vagy válassza az új létrehozása lehetőséget az új erőforráscsoport **létrehozásához** , vagy válasszon ki egy meglévő erőforráscsoportot a legördülő menüből.

6. Adjon egyedi nevet a gazdagép-készletnek.

7. A hely mezőben válassza ki azt a régiót, ahol létre szeretné hozni az alkalmazáskészletet a legördülő menüből.

   A kiválasztott régiókkal társított Azure földrajz a gazdagép-készlet és a hozzá kapcsolódó objektumok metaadatait fogja tárolni. Győződjön meg arról, hogy a földrajzi helyen belüli régiókat választja, amelyekben a szolgáltatás metaadatait tárolni szeretné.

     > [!div class="mx-imgBorder"]
     > ![A Azure Portal az USA keleti régiójában kiválasztott Location (hely) mezőt bemutató képernyőkép. A mező mellett a "metaadatok az USA keleti régiójában lesznek tárolva."](media/portal-location-field.png)
  
   >[!NOTE]
   > Ha az USA-n kívüli [támogatott régióban](data-locations.md) szeretné létrehozni a gazdagépet, újra kell regisztrálnia az erőforrás-szolgáltatót. Az Újraregisztrálás után a legördülő menüben a másik régiót kell megtekinteni a hely kiválasztásához. Ismerje meg, hogyan regisztrálhat újra a [gazdagép-készlet létrehozásával](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects) kapcsolatos hibaelhárítási cikkben.

8. Az alkalmazáskészlet típusa területen válassza ki, hogy a gazdagép **személyes** vagy **készletezési** készlettel rendelkezik-e.

    - Ha a **személyes** lehetőséget választja, válassza az **automatikus** vagy a **közvetlen** lehetőséget a hozzárendelés típusa mezőben.

      > [!div class="mx-imgBorder"]
      > ![A hozzárendelés típusa mező legördülő menüjének képernyőképe. A felhasználó automatikus beállítást adott meg.](media/assignment-type-field.png)

9.  Ha a **készletezett** lehetőséget választja, adja meg a következő adatokat:

     - A **munkamenetek** maximális száma lehetőségnél adja meg, hogy legfeljebb hány felhasználót szeretne terheléselosztással elválasztani egyetlen munkamenet-gazdagépre.
     - **Terheléselosztási algoritmus** esetén a használati minta alapján válassza a szélesség – első vagy a mélység – első lehetőséget.

       > [!div class="mx-imgBorder"]
       > ![A hozzárendelés típus mezőjének képernyőképe a "készletezett" beállítással. A felhasználó fölé viszi a kurzort a szélesség – először a terheléselosztás legördülő menüben.](media/pooled-assignment-type.png)

10. Válassza a Next (tovább) lehetőséget **: Virtual Machines >**.

11. Ha már létrehozott egy virtuális gépet, és az új gazdagépet szeretné használni, válassza a **nem** lehetőséget, válassza a **tovább: munkaterület >** lehetőséget, és ugorjon a [munkaterület-információk](#workspace-information) szakaszra. Ha új virtuális gépeket szeretne létrehozni, és regisztrálja őket az új gazdagép-készletben, válassza az **Igen** lehetőséget.

Most, hogy elvégezte az első részt, térjünk át a telepítési folyamat következő részére, ahol létrehozjuk a virtuális gépet.

## <a name="virtual-machine-details"></a>Virtuális gép adatai

Most, hogy az első részen vagyunk, be kell állítania a virtuális gépet.

A virtuális gép beállítása a gazdagép-készlet telepítési folyamatán belül:

1. Az **erőforráscsoport** területen válassza ki azt az erőforráscsoportot, amelyben létre szeretné hozni a virtuális gépeket. Ez lehet egy másik erőforráscsoport, mint a gazdagéphez használt készlet.

2. Ezt követően adjon meg egy **előtagot** a virtuális gépek számára a telepítési folyamat által létrehozott névvel. Az utótag a 0 értéktől `-` kezdődő számmal lesz ellátva.

3. Válassza ki azt a **virtuális gépet** , amelyben létre szeretné hozni a virtuális gépeket. Megegyeznek a gazdagéphez kiválasztott régióval, vagy eltérőek lehetnek.
   
4. Ezután válassza ki az igényeinek leginkább megfelelő rendelkezésre állási lehetőséget. Ha többet szeretne megtudni arról, hogy melyik lehetőségről van szó, tekintse meg az Azure-beli [virtuális gépek rendelkezésre állási lehetőségeit](../virtual-machines/availability.md) , valamint [a gyakori kérdéseket](faq.md#which-availability-option-is-best-for-me).
   
   > [!div class="mx-imgBorder"]
   > [A rendelkezésre állási zóna legördülő menüjének képernyőképe. A "rendelkezésre állási zóna" beállítás ki van emelve.](media/availability-zone.png)

5. Ezután válassza ki a virtuális gép létrehozásához használni kívánt rendszerképet. Kiválaszthatja a **Katalógus vagy a** **tárolási blob** lehetőséget.

    - Ha a katalógus lehetőséget **választja, válassza ki a** javasolt rendszerképek egyikét a legördülő menüből:

      - Windows 10 Enterprise multi-session, 1909-es verzió
      - Windows 10 Enterprise multi-session, Version 1909 + Microsoft 365 alkalmazások
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise multi-session, 2004-es verzió
      - Windows 10 Enterprise multi-session, Version 2004 + Microsoft 365 alkalmazások

      Ha nem látja a kívánt rendszerképet, válassza az **összes** rendszerkép megjelenítése lehetőséget, amely lehetővé teszi egy másik rendszerkép kiválasztását a katalógusban vagy a Microsoft és más kiadók által biztosított lemezképben. Győződjön meg arról, hogy a kiválasztott rendszerkép a [támogatott operációsrendszer-lemezképek](overview.md#supported-virtual-machine-os-images)egyike.

      > [!div class="mx-imgBorder"]
      > ![A piactér képernyőképe a Microsoft által megjelenített rendszerképek listájáról.](media/marketplace-images.png)

      Megnyithatja **az elemeket** , és kiválaszthat egy már feltöltött egyéni rendszerképet is.

      > [!div class="mx-imgBorder"]
      > ![Képernyőkép a saját elemek lapról.](media/my-items.png)

    - Ha a **Storage blob** lehetőséget választja, saját rendszerképét a Hyper-V használatával vagy egy Azure-beli virtuális gépen is létrehozhatja. Mindössze annyit kell tennie, hogy megadja a rendszerkép helyét a Storage-blobban URI-ként.
   
   A rendszerkép helye független a rendelkezésre állási lehetőségtől, de a rendszerkép zóna rugalmassága határozza meg, hogy a rendszerkép használható-e a rendelkezésre állási zónával. Ha kijelöl egy rendelkezésre állási zónát a rendszerkép létrehozása közben, győződjön meg róla, hogy a katalógusból származó képet használ a zóna rugalmassága beállítással. Ha többet szeretne megtudni arról, hogy melyik zóna rugalmassági beállítását érdemes használni, tekintse meg [a gyakori kérdéseket](faq.md#which-availability-option-is-best-for-me).

6. Ezután válassza ki a használni kívánt **virtuálisgép-méretet** . Megtarthatja az alapértelmezett méretet is, vagy a méret **módosításához válassza a méret módosítása** lehetőséget. Ha a **méret módosítása** lehetőséget választja, a megjelenő ablakban válassza ki a munkaterheléshez megfelelő virtuális gép méretét.

7. A **virtuális gépek száma** területen adja meg a gazdagéphez létrehozni kívánt virtuális gépek számát.

    >[!NOTE]
    >A telepítési folyamat akár 400 virtuális GÉPET is létrehozhat a gazdagép beállítása közben, és minden egyes virtuálisgép-beállítási folyamat négy objektumot hoz létre az erőforráscsoporthoz. Mivel a létrehozási folyamat nem ellenőrzi az előfizetési kvótát, győződjön meg arról, hogy a megadott virtuális gépek száma az Azure-beli virtuális gépen, valamint az erőforráscsoport és az előfizetés API-korlátain belül van. A gazdagép-készlet létrehozása után további virtuális gépeket adhat hozzá.

8. Válassza ki, hogy a virtuális gépek milyen operációsrendszer-lemezeket szeretnének használni: standard SSD, prémium SSD vagy standard HDD.

9. A hálózat és biztonság területen válassza ki azt a **virtuális hálózatot** és **alhálózatot** , ahol a létrehozni kívánt virtuális gépeket el szeretné helyezni. Győződjön meg arról, hogy a virtuális hálózat tud csatlakozni a tartományvezérlőhöz, mert a virtuális hálózaton belüli virtuális gépeket a tartományhoz kell csatlakoztatnia. A kiválasztott virtuális hálózat DNS-kiszolgálóit úgy kell konfigurálni, hogy a tartományvezérlő IP-címét használják.

10. Válassza ki a kívánt biztonsági csoport típusát: **Alapszintű**, **speciális** vagy **nincs**.

    Ha az **alapszintű** lehetőséget választja, ki kell választania, hogy meg kívánja-e nyitni a bejövő portokat. Ha az **Igen** lehetőséget választja, akkor a bejövő kapcsolatok engedélyezéséhez válasszon a szabványos portok listájából.

    >[!NOTE]
    >A nagyobb biztonság érdekében javasoljuk, hogy ne nyissa meg a nyilvános bejövő portokat.

    > [!div class="mx-imgBorder"]
    > ![A biztonsági csoport oldalának képernyőképe, amely megjeleníti az elérhető portok listáját a legördülő menüben.](media/available-ports.png)

    Ha a **speciális** lehetőséget választja, válasszon ki egy már konfigurált, meglévő hálózati biztonsági csoportot.

11. Ezután válassza ki, hogy szeretné-e a virtuális gépeket egy adott tartományhoz és szervezeti egységhez csatlakoztatni. Ha az **Igen** lehetőséget választja, adja meg azt a tartományt, amelyhez csatlakozni szeretne. Opcionálisan hozzáadhat egy adott szervezeti egységet, amelyben a virtuális gépeket be szeretné állítani. Ha a **nem** lehetőséget választja, a virtuális gépek az **ad-tartományhoz való csatlakozás UPN**-utótagjának megfelelő tartományhoz fognak csatlakozni.

    - Szervezeti egység megadásakor ügyeljen arra, hogy a teljes elérési utat (megkülönböztető név) és idézőjelek nélkül használja.

12. A tartományi rendszergazda fiók területen adja meg a kiválasztott virtuális hálózat Active Directory-tartomány rendszergazdájához tartozó hitelesítő adatokat. Ennek a fióknak nincs engedélyezve a többtényezős hitelesítés (MFA). Azure Active Directory Domain Services (Azure AD DS) tartományhoz való csatlakozáskor a fióknak az Azure AD DC-rendszergazdák csoport tagjaként kell lennie, és a fiók jelszavának az Azure-AD DS kell működnie.

13. Válassza a **Tovább: munkaterület >** elemet.

Ezzel készen áll arra, hogy elindítsa a gazdagép készletének következő fázisát: az alkalmazás csoportjának regisztrálása munkaterületre.

## <a name="workspace-information"></a>Munkaterület-információk

A gazdagép-készlet telepítési folyamata alapértelmezés szerint létrehoz egy asztali alkalmazáscsoport-csoportot. Ahhoz, hogy a gazdagép a kívánt módon működjön, közzé kell tennie ezt az erőforráscsoportot felhasználók vagy felhasználói csoportok számára, és regisztrálnia kell az alkalmazást egy munkaterületen.

Az asztali alkalmazás csoportjának regisztrálása munkaterületre:

1. Válassza az **Igen** lehetőséget.

   Ha a **nem** lehetőséget választja, akkor később regisztrálhatja az alkalmazást, de azt javasoljuk, hogy a munkaterület regisztrációját a lehető leghamarabb végezze el, amint a gazdagép-készlet megfelelően működik.

2. Ezután adja meg, hogy szeretne-e új munkaterületet létrehozni, vagy válasszon a meglévő munkaterületekről. Csak abban a helyen létrehozott munkaterületek lesznek regisztrálva az alkalmazásban, ahol a gazdagép-készletet is létrehozták.

3. Opcionálisan kiválaszthatja a **következőt: címkék >**.

    Itt hozzáadhat címkéket, így a metaadatokkal csoportosíthatja az objektumokat, hogy könnyebb legyen a rendszergazdák számára.

4. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet.

     >[!NOTE]
     >A felülvizsgálat + ellenőrzési folyamat nem ellenőrzi, hogy a Jelszó megfelel-e a biztonsági szabványoknak, vagy ha az architektúra helyes, ezért az ilyen dolgok bármelyikével kapcsolatos problémákat meg kell vizsgálnia.

5. Tekintse át az üzemelő példány adatait, és győződjön meg róla, hogy minden helyesnek tűnik. Ha elkészült, válassza a **Létrehozás** lehetőséget. Ez elindítja a telepítési folyamatot, amely a következő objektumokat hozza létre:

     - Az új gazdagép-készletet.
     - Egy asztali alkalmazás csoportja.
     - Munkaterület, ha úgy dönt, hogy létrehoz egy munkaterületet.
     - Ha úgy dönt, hogy regisztrálja az asztali alkalmazás csoportját, a rendszer a regisztrációt végzi.
     - Virtuális gépek, ha úgy dönt, hogy létrehozza őket, amelyek a tartományhoz csatlakoznak, és regisztrálva vannak az új gazdagép-készletben.
     - Egy Azure Resource Management-sablonhoz tartozó letöltési hivatkozás a konfiguráció alapján.

Ezt követően már készen is van!

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>A Azure Resource Manager sablon futtatása új címkészlet kiépítéséhez

Ha inkább automatikus folyamatot használ, [töltse le a Azure Resource Manager sablont](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates) az új címkészlet kiépítéséhez.

>[!NOTE]
>Ha automatizált eljárást használ a környezet összeállításához, a konfigurációs JSON-fájl legújabb verziójára lesz szüksége. [Itt](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list)megtalálhatja a JSON-fájlt.

## <a name="next-steps"></a>Következő lépések

Most, hogy elvégezte a gazdagép készletét, feltöltheti azt a RemoteApp-programok használatával. Ha többet szeretne megtudni a Windows rendszerű virtuális asztali alkalmazások kezeléséről, tekintse meg a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Alkalmazás-csoportok kezelése – oktatóanyag](./manage-app-groups.md)
