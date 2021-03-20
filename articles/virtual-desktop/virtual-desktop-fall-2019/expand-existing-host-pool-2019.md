---
title: Meglévő Windowsos virtuális asztali (klasszikus) címkészlet kibontása új munkamenet-gazdagépekkel – Azure
description: Meglévő címkészlet kibontása új munkamenet-gazdagépekkel a Windows Virtual Desktopban (klasszikus).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61cf28b0f1ebee6a0312ec3f23f22b01c6c4919e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88009171"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts-in-windows-virtual-desktop-classic"></a>Meglévő gazdagép kibontása új munkamenet-gazdagépekkel a Windows Virtual Desktopban (klasszikus)

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali (klasszikus) verzióra vonatkozik, amely nem támogatja Azure Resource Manager Windows rendszerű virtuális asztali objektumokat. Ha Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../expand-existing-host-pool.md).

A gazdagépen belüli használat során előfordulhat, hogy a meglévő gazdagépet új munkamenet-gazdagépekkel kell bővíteni az új terhelés kezeléséhez.

Ebből a cikkből megtudhatja, hogyan bővítheti a meglévő gazdagépeket új munkamenet-gazdagépekkel.

## <a name="what-you-need-to-expand-the-host-pool"></a>Mire van szükség a gazdagép kibontásához

Mielőtt elkezdené, győződjön meg arról, hogy létrehozta a gazdagép-készletet és a munkamenet-gazda virtuális gépeket (VM) az alábbi módszerek egyikének használatával:

- [Azure Marketplace-ajánlat](create-host-pools-azure-marketplace-2019.md)
- [GitHub Azure Resource Manager sablon](create-host-pools-arm-template.md)
- [Gazdagépcsoport létrehozása a PowerShell-lel](create-host-pools-powershell-2019.md)

A következő információkra is szüksége lesz a gazdagép és a munkamenet-gazda virtuális gépek első létrehozásakor:

- VM-méret,-rendszerkép és-név előtagja
- Tartományhoz való csatlakozás és a Windows virtuális asztali bérlői rendszergazdai hitelesítő adatai
- Virtuális hálózat neve és alhálózatának neve

A következő három szakaszban három módszer használható a címkészlet kibontására. Bármely, az Ön számára kényelmes üzembe helyezési eszközzel elvégezhető.

>[!NOTE]
>Az üzembe helyezési fázisban hibaüzenetek jelennek meg az előző munkamenet-gazdagép virtuálisgép-erőforrásaihoz, ha éppen leállnak. Ezek a hibák azért történnek, mert az Azure nem tudja futtatni a PowerShell DSC bővítményt annak ellenőrzéséhez, hogy a munkamenet-gazda virtuális gépek megfelelően vannak-e regisztrálva a meglévő gazdagépen Ezeket a hibákat nyugodtan figyelmen kívül hagyhatja, vagy elkerülheti a hibákat úgy, hogy a telepítési folyamat megkezdése előtt elindítja az összes munkamenet-gazda virtuális gépet a meglévő gazdagép-készletben.

## <a name="redeploy-from-azure"></a>Újraüzembe helyezés az Azure-ból

Ha már létrehozott egy gazdagép-készletet és egy munkamenet-gazdagép virtuális gépet az [Azure Marketplace-ajánlat](create-host-pools-azure-marketplace-2019.md) vagy a [GitHub Azure Resource Manager-sablon](create-host-pools-arm-template.md)segítségével, akkor a Azure Portalból is újratelepítheti ugyanazt a sablont. A sablon újbóli üzembe helyezése automatikusan visszaadja az eredeti sablonba beírt összes információt, kivéve a jelszavakat.

A következőképpen telepítheti újra a Azure Resource Manager sablont a gazdagépek kibontásához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. A Azure Portal tetején található keresősáv alatt keresse meg az **erőforráscsoportok** kifejezést, és válassza ki az elemet a **szolgáltatások** területen.
3. Keresse meg és válassza ki azt az erőforráscsoportot, amelyet a gazdagép-készlet létrehozásakor hozott létre.
4. A böngésző bal oldalán lévő panelen válassza a **központi telepítések** lehetőséget.
5. Válassza ki a gazdagép-készlet létrehozási folyamatának megfelelő központi telepítését:
     - Ha az eredeti alkalmazáskészletet az Azure Marketplace-ajánlattal hozta létre, válassza ki az **RDS. WVD-kiépítés-gazdagép-készletet**.
     - Ha az eredeti címkészletet a GitHub Azure Resource Manager sablonnal hozta létre, válassza ki a **Microsoft. template** nevű központi telepítést.
6. Válassza az **újratelepítés** lehetőséget.

     >[!NOTE]
     >Ha a sablon nem kerül automatikusan újratelepítésre az **újratelepítési** lehetőség kiválasztásakor, válassza a **sablon** lehetőséget a böngésző bal oldalán lévő panelen, majd válassza a **telepítés** lehetőséget.

7. Válassza ki azt az erőforráscsoportot, amely a meglévő gazdagép-készletben lévő aktuális munkamenet-gazda virtuális gépeket tartalmazza.

     >[!NOTE]
     >Ha olyan hibaüzenetet lát, amely azt jelzi, hogy egy másik erőforráscsoportot választ, még ha a beírt érték is helyes, válasszon másik erőforráscsoportot, majd válassza ki az eredeti erőforráscsoportot.

8. Adja meg a következő URL-címet a *_artifactsLocation* számára: `https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`
9. Adja meg a *Rdsh-példányok számának* megadásához használni kívánt munkamenet-gazdagépek új teljes számát. Ha például öt munkamenet-gazdagépről nyolcra bővíti a gazdagép-készletet, írja be a **8** értéket.
10. Adja meg ugyanazt a meglévő tartományi jelszót, amelyet a meglévő tartományi UPN-hez használt. Ne változtassa meg a felhasználónevet, mert a sablon futtatásakor hibaüzenetet fog okozni.
11. Adja meg ugyanazt a bérlői rendszergazdai jelszót, amelyet a *bérlői rendszergazdai UPN-hez vagy az alkalmazás-azonosítóhoz* megadott felhasználóhoz vagy alkalmazás-azonosítóhoz használt. Ha újra, ne módosítsa a felhasználónevet.
12. Fejezze be a küldést a címkészlet kibontásához.

## <a name="run-the-azure-marketplace-offering"></a>Az Azure Marketplace-ajánlat futtatása

Kövesse az [alkalmazáskészlet létrehozása az Azure Marketplace](create-host-pools-azure-marketplace-2019.md) -en című témakör utasításait, amíg el nem éri [Az Azure Marketplace-ajánlat futtatását az új címkészlet](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool)kiépítéséhez. Amikor erre a pontra kerül, meg kell adnia az alábbi adatokat az egyes lapokon:

### <a name="basics"></a>Alapvető beállítások

Az ebben a szakaszban szereplő összes értéknek egyeznie kell azzal, amit a gazdagép és a munkamenet-gazda virtuális gépei első létrehozásakor adott meg, kivéve az *alapértelmezett asztali felhasználók* számára:

1.    Az *előfizetés* mezőben válassza ki azt az előfizetést, amelybe először létrehozta a gazdagép-készletet.
2.    Az *erőforráscsoport* mezőben válassza ki ugyanazt az erőforráscsoportot, ahol a meglévő gazdagép-készlethez tartozó virtuális gépek találhatók.
3.    A *régió* mezőben válassza ki azt a régiót, ahol a meglévő gazdagép-munkamenet-gazdagép virtuális gépei találhatók.
4.    A *Hostpool neve* mezőbe írja be a meglévő címkészlet nevét.
5.    Az *asztal típusa* mezőben válassza ki azt az asztal-típust, amely megegyezik a meglévő gazdagép-készlettel.
6.    Az *alapértelmezett asztali felhasználók beállításnál* adja meg azokat a további felhasználókat, akik be kívánnak jelentkezni a Windows rendszerű virtuális asztali ügyfelekbe, és az Azure Marketplace-ajánlat befejezése után férnek hozzá az asztalhoz. Ha például hozzá kívánja rendelni user3@contoso.com és hozzáférést szeretne user4@contoso.com adni, írja be a következőt: user3@contoso.com user4@contoso.com .
7.    Válassza a Next (tovább) lehetőséget **: virtuális gép konfigurálása**.

>[!NOTE]
>Az *alapértelmezett asztali felhasználók* kivételével minden mezőnek pontosan egyeznie kell a meglévő gazdagép-készletben megadott beállításokkal. Ha eltérés van, akkor az új gazdagépet fog eredményezni.

### <a name="configure-virtual-machines"></a>Virtuális gépek konfigurálása

Az ebben a szakaszban szereplő összes paraméternek meg kell egyeznie a gazdagép és a munkamenet-gazda virtuális gépek első létrehozásakor megadott értékekkel, kivéve a virtuális gépek teljes számát. A virtuális gépek száma a kibontott gazdagép-készletben lévő virtuális gépek száma lesz:

1. Válassza ki a virtuálisgép-méretet, amely megfelel a meglévő munkamenet-gazda virtuális gépeknek.

    >[!NOTE]
    >Ha a keresett virtuálisgép-méret nem jelenik meg a virtuálisgép-méret választóban, ennek az az oka, hogy még nem készítettük üzembe az Azure Marketplace eszközre. A virtuális gép méretének igényléséhez hozzon létre egy kérést, vagy egy meglévő kérelmet a [Windows virtuális asztali UserVoice fórumában](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

2. Szabja testre a *használati profilt*, az *összes felhasználót* és a virtuálisgép-paraméterek *számát* , hogy kiválassza a gazdagép készletében lévő összes munkamenet-gazdagép számát. Ha például öt munkamenet-gazdagépről nyolcra bővíti a gazdagép-készletet, ezeket a beállításokat a 8 virtuális gép számára konfigurálja.
3. Adja meg a virtuális gépek neveinek előtagját. Ha például a "prefix" nevet adja meg, akkor a virtuális gépek "előtag-0", "előtag-1" és így tovább.
4. Válassza a **Tovább: virtuális gép beállításai** lehetőséget.

### <a name="virtual-machine-settings"></a>A virtuális gép beállításai

Az ebben a szakaszban szereplő összes paraméternek meg kell egyeznie a gazdagép és a munkamenet-gazda virtuális gépek első létrehozásakor megadott értékekkel:

1. A *képforrásra* és a *rendszerkép operációsrendszer-verziójára* vonatkozóan adja meg ugyanazokat az adatokat, amelyeket a gazdagép első létrehozásakor adott meg.
2. Az *ad-tartományhoz való JOIN UPN* és a társított jelszavak esetében adja meg ugyanazt az információt, amelyet akkor adott meg, amikor a gazdagépet először hozta létre a virtuális gépekhez a Active Directory tartományhoz való csatlakozáshoz. A rendszer ezeket a hitelesítő adatokat fogja használni helyi fiók létrehozásához a virtuális gépeken. Ezeket a helyi fiókokat alaphelyzetbe állíthatja, hogy később megváltoztassák a hitelesítő adataikat.
3. A virtuális hálózat adatainál válassza ki ugyanazt a virtuális hálózatot és alhálózatot, ahol a meglévő gazdagép-készlethez tartozó virtuális gépek találhatók.
4. Válassza a Next (tovább) lehetőséget **: konfigurálja a Windows rendszerű virtuális asztali adatokat**.

### <a name="windows-virtual-desktop-information"></a>Windows rendszerű virtuális asztali információk

Az ebben a szakaszban szereplő összes paraméternek meg kell egyeznie a gazdagép és a munkamenet-gazda virtuális gépek első létrehozásakor megadott értékekkel:

1. A *Windows rendszerű virtuális asztali bérlői csoport neve* mezőbe írja be a bérlőt tartalmazó bérlői csoport nevét. Hagyja meg az alapértelmezett értéket, ha megadott egy bérlői csoport nevét.
2. A *Windows rendszerű virtuális asztali bérlő neve* mezőbe írja be annak a bérlőnek a nevét, ahol a gazdagépet létrehozza.
3. Megadhatja ugyanazokat a hitelesítő adatokat, amelyeket a gazdagép és a munkamenet-gazda virtuális gépek első létrehozásakor használt. Ha egyszerű szolgáltatásnevet használ, adja meg annak a Azure Active Directory-példánynak az AZONOSÍTÓját, amelyben a szolgáltatásnév található.
4. Válassza a **Next (tovább): felülvizsgálat + létrehozás** elemet.

## <a name="run-the-github-azure-resource-manager-template"></a>A GitHub Azure Resource Manager-sablon futtatása

Kövesse a [Azure Resource Manager sablon futtatása új címkészlet kiépítési céljára](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool) című témakör utasításait, és adja meg az összes azonos paraméter-értéket, kivéve a *Rdsh-példányok számát*. A sablon futtatása után adja meg a gazdagép-készletben használni kívánt munkamenet-gazda virtuális gépek számát. Ha például öt munkamenet-gazdagépről nyolcra bővíti a gazdagép-készletet, írja be a **8** értéket.

## <a name="next-steps"></a>Következő lépések

Most, hogy kibontotta a meglévő gazdagép-készletet, bejelentkezhet egy Windows rendszerű virtuális asztali ügyfélbe, és tesztelheti őket egy felhasználói munkamenet részeként. Csatlakozhat egy munkamenethez a következő ügyfelek bármelyikével:

- [Kapcsolódás a Windows asztali ügyféllel](connect-windows-7-10-2019.md)
- [Kapcsolódás a webügyféllel](connect-web-2019.md)
- [Kapcsolódás az Android-ügyféllel](connect-android-2019.md)
- [Kapcsolódás a macOS-ügyfélhez](connect-macos-2019.md)
- [Kapcsolódás az iOS-ügyfélhez](connect-ios-2019.md)
