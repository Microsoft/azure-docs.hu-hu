---
title: Windows rendszerű virtuális asztali (klasszikus) gazdagép-készlet Azure Marketplace – Azure
description: Windows rendszerű virtuális asztali (klasszikus) címkészlet létrehozása az Azure Marketplace használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: fa271ba87f68d46452633d2b426c7fd805f8566b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445159"
---
# <a name="tutorial-create-a-host-pool-in-windows-virtual-desktop-classic"></a>Oktatóanyag: gazdagép létrehozása a Windows Virtual Desktopban (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../create-host-pools-azure-marketplace.md).

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy gazdagép-készletet egy Windows rendszerű virtuális asztali bérlőn egy Microsoft Azure Marketplace ajánlat használatával.

A gazdagép-készletek egy vagy több azonos virtuális gép gyűjteményei a Windows rendszerű virtuális asztali bérlői környezetekben. Mindegyik gazdagép tartalmazhatja azt az alkalmazáscsoport-csoportot, amelyet a felhasználók a fizikai asztalon lévők használatával kezelhetnek.

Az oktatóanyag feladatai a következők:

> [!div class="checklist"]
>
> * Hozzon létre egy gazdagépet a Windows Virtual Desktopban.
> * Hozzon létre egy erőforráscsoportot virtuális gépekkel egy Azure-előfizetésben.
> * Csatlakoztassa a virtuális gépeket a Active Directory tartományhoz.
> * Regisztrálja a virtuális gépeket a Windows Virtual Desktopban.

## <a name="prerequisites"></a>Előfeltételek

* Egy bérlő a virtuális asztalon. Egy korábbi [oktatóanyag](tenant-setup-azure-active-directory.md) létrehoz egy bérlőt.
* [Windows rendszerű virtuális asztali PowerShell-modul](/powershell/windows-virtual-desktop/overview/).

Ha ezt a modult futtatja, futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Az Azure Marketplace-ajánlat futtatása új címkészlet kiépítéséhez

Az Azure Marketplace ajánlatának futtatása új címkészlet kiépítéséhez:

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
1. A piactér keresési ablakában adja meg a **Windows virtuális asztal** kifejezést.
1. Válassza **a Windows virtuális asztal – címkészlet kiépítése** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

Ezután kövesse a következő szakaszban található utasításokat a megfelelő lapokra vonatkozó információk megadásához.

### <a name="basics"></a>Alapvető beállítások

Az **alapok** lapon a következő műveleteket végezheti el:

1. Válasszon egy **előfizetést**.
1. Az **erőforráscsoport** területen válassza az **új létrehozása** elemet, és adja meg az új erőforráscsoport nevét.
1. Válasszon **régiót**.
1. Adja meg a Windows rendszerű virtuális asztali bérlőn belül egyedi alkalmazáskészlet nevét.
1. Válassza az **asztal típusa** lehetőséget. Ha a **személyes** lehetőséget választja, a gazdagéphez csatlakozó összes felhasználó véglegesen hozzá van rendelve egy virtuális géphez.
1. Adja meg azokat a felhasználókat, akik bejelentkezhetnek a Windows rendszerű virtuális asztali ügyfelekbe, és hozzáférnek az asztalhoz. Vesszővel tagolt lista használata. Ha például a hozzárendelést és a hozzáférést szeretné használni `user1@contoso.com` `user2@contoso.com` , írja be a következőt: *`user1@contoso.com,user2@contoso.com`*
1. A **szolgáltatás metaadatainak helye** területen válassza ki a virtuális hálózattal megegyező helyet, amely kapcsolódik a Active Directory-kiszolgálóhoz.

   >[!IMPORTANT]
   >Ha tiszta Azure Active Directory Domain Services (Azure AD DS) és Azure Active Directory (Azure AD) megoldást használ, ügyeljen arra, hogy az Azure-AD DS azonos régióban telepítse a gazdagép-készletet, hogy elkerülje a tartományhoz való csatlakozást és a hitelesítő adatokat.

1. Válassza a Next (tovább) lehetőséget **: virtuális gépek konfigurálása**.

### <a name="configure-virtual-machines"></a>Virtuális gépek konfigurálása

A **virtuális gépek konfigurálása** lapon:

1. Fogadja el az alapértelmezett értékeket, vagy szabja testre a virtuális gépek számát és méretét.

    >[!NOTE]
    >Ha a keresett virtuálisgép-méret nem jelenik meg a méret-választóban, ez azért van, mert még nem készítettük el az Azure Marketplace eszközre.

2. Adja meg a virtuális gépek neveinek előtagját. Ha például az *előtagot* adja meg, a virtuális gépek neve **előtag-0**, **előtag-1**, és így tovább.
3. Válassza a **Tovább: virtuális gép beállításai** lehetőséget.

### <a name="virtual-machine-settings"></a>A virtuális gép beállításai

A **virtuális gép beállításai** lapon:

1. A **képforráshoz** válassza ki a forrást, és adja meg a megfelelő információkat a megkereséséhez és tárolásához. A lehetőségek különböznek a blob Storage-hoz, a felügyelt rendszerképekhez és a gyűjteményekhez.

   Ha úgy dönt, hogy nem használja a felügyelt lemezeket, válassza ki azt a Storage-fiókot, amely a *. vhd* -fájlt tartalmazza.
1. Adja meg az egyszerű felhasználónevet és a jelszót. Ennek a fióknak a tartományi fióknak kell lennie, amely a virtuális gépeket a Active Directory tartományhoz fogja csatlakoztatni. Ugyanezt a felhasználónevet és jelszót a rendszer helyi fiókként hozza létre a virtuális gépeken. Ezeket a helyi fiókokat később is visszaállíthatja.

   >[!NOTE]
   > Ha a virtuális gépeket Azure AD DS-környezethez csatlakoztatja, győződjön meg arról, hogy a tartományhoz való csatlakozás felhasználója az [HRE DC-rendszergazdák csoport](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)tagja.
   >
   > A fióknak az Azure AD DS felügyelt tartomány vagy az Azure AD-bérlő részét is kell képeznie. Az Azure AD-bérlőhöz társított külső könyvtárak fiókjai nem tudják megfelelően hitelesíteni magukat a tartományhoz való csatlakozás során.

1. Válassza ki azt a **virtuális hálózatot** , amely a Active Directory-kiszolgálóhoz kapcsolódik, majd válasszon egy alhálózatot a virtuális gépek üzemeltetéséhez.
1. Válassza a Next (tovább) lehetőséget **: Windows virtuális asztali információk**.

### <a name="windows-virtual-desktop-tenant-information"></a>Windows rendszerű virtuális asztali bérlő adatai

A **Windows rendszerű virtuális asztali bérlő adatai** lapon:

1. A **Windows rendszerű virtuális asztali bérlői csoport neve** mezőbe írja be a bérlőt tartalmazó bérlői csoport nevét. Hagyja meg az alapértelmezett értéket, ha megadott egy bérlői csoport nevét.
1. A **Windows rendszerű virtuális asztali bérlő neve** mezőbe írja be annak a bérlőnek a nevét, ahol a gazdagépet létrehozza.
1. Adja meg, hogy milyen típusú hitelesítő adatokat kíván használni a Windows rendszerű virtuális asztali bérlői RDS-tulajdonosként való hitelesítéshez. Adja meg az egyszerű felhasználónevet vagy szolgáltatásnevet, valamint egy jelszót.

   Ha végrehajtotta az [egyszerű szolgáltatásnév és a szerepkör-hozzárendelések létrehozása a PowerShell-lel oktatóanyagot](create-service-principal-role-powershell.md), válassza az **egyszerű szolgáltatásnév** lehetőséget.

1. Az **egyszerű szolgáltatásnév** esetében az **Azure ad-bérlő azonosítója** mezőben adja meg az egyszerű szolgáltatásnevet tartalmazó Azure ad-példány bérlői rendszergazdai fiókját. Csak a jelszavas hitelesítő adatokkal rendelkező egyszerű szolgáltatások támogatottak.
1. Válassza a **Tovább: Ellenőrzés és létrehozás** lehetőségre.

## <a name="complete-setup-and-create-the-virtual-machine"></a>A telepítés befejezése és a virtuális gép létrehozása

A **felülvizsgálat és létrehozás** lapon tekintse át a telepítési adatokat. Ha módosítania kell valamit, lépjen vissza, és végezze el a módosításokat. Ha elkészült, válassza a **Létrehozás** lehetőséget a gazdagép-készlet telepítéséhez.

Attól függően, hogy hány virtuális gépet hoz létre, ez a folyamat 30 percet vagy akár több időt is igénybe vehet.

>[!IMPORTANT]
> A Windows rendszerű virtuális asztali környezet biztonságossá tételéhez az Azure-ban javasoljuk, hogy ne nyissa meg a 3389-es bejövő portot a virtuális gépeken. A Windows rendszerű virtuális asztal nem igényel olyan nyitott bejövő portot 3389, amellyel a felhasználók hozzáférhetnek a gazdagép-készlet virtuális gépei számára.
>
> Ha hibaelhárítási célból meg kell nyitnia a 3389-as portot, javasoljuk, hogy használja az igény szerinti hozzáférést. További információ: [a felügyeleti portok biztonságossá tétele az](../../security-center/security-center-just-in-time.md)igény szerinti hozzáféréssel.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Választható További felhasználók társítása az asztali alkalmazás csoportjához

Miután az Azure Marketplace befejezte a készlet létrehozását, több felhasználót is hozzárendelhet az asztali alkalmazás csoportjához. Ha még nem kívánja hozzáadni, ugorja át ezt a szakaszt.

Felhasználók társítása az asztali alkalmazás csoportjához:

1. Indítson el egy PowerShell-ablakot.

1. A következő parancs futtatásával jelentkezzen be a Windows rendszerű virtuális asztali környezetbe:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Adja hozzá a felhasználókat az asztali alkalmazás csoportjához a következő parancs használatával:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   A felhasználó UPN-nek meg kell egyeznie a felhasználó identitásával az Azure AD-ben, például: *user1@contoso.com* . Ha több felhasználót szeretne hozzáadni, futtassa az parancsot minden felhasználóhoz.

Az asztali alkalmazás csoportba felvett felhasználók a támogatott Távoli asztal ügyfelekkel bejelentkezhetnek a Windows rendszerű virtuális asztalra, és megtekinthetik a munkamenetek asztalának erőforrásait.

A jelenleg támogatott ügyfelek:

* [Távoli asztal ügyfél a Windows 7 és a Windows 10 rendszerhez](connect-windows-7-10-2019.md)
* [Windows rendszerű virtuális asztali webes ügyfél](connect-web-2019.md)

## <a name="next-steps"></a>Következő lépések

Létrehozott egy gazdagépet, és hozzárendelt felhasználókat az asztalához való hozzáféréshez. A gazdagép-készletet a RemoteApp-programokkal töltheti fel. Ha többet szeretne megtudni az alkalmazások kezeléséről a Windows Virtual Desktopban, tekintse meg ezt az oktatóanyagot:

> [!div class="nextstepaction"]
> [Alkalmazás-csoportok kezelése – oktatóanyag](manage-app-groups-2019.md)
