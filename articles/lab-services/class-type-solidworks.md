---
title: SOLIDWORKS-tesztkörnyezet beállítása a Azure Lab Serviceshoz | Microsoft Docs
description: Ismerje meg, hogyan állíthat be egy labort mérnöki tanfolyamokhoz a SOLIDWORKS használatával.
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 3a7e8c47977f0518a3a3e9f8a6fd2e57454e1c42
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626104"
---
# <a name="set-up-a-lab-for-engineering-classes-using-solidworks"></a>Tesztkörnyezet beállítása mérnöki osztályokhoz a SOLIDWORKS használatával

A [SOLIDWORKS](https://www.solidworks.com/) egy 3D-s számítógéppel segített tervezési (CAD-) környezetet biztosít a Solid Objects modellezéséhez, és különböző típusú mérnöki mezőkben használatos.  A SOLIDWORKS segítségével a mérnökök könnyedén létrehozhatják, megjeleníthetik, szimulálják és dokumentálják a terveiket.

Az egyetemek által gyakran használt licencelési lehetőség a SOLIDWORKS hálózati licencelés.   Ezzel a beállítással a felhasználók megoszthatnak egy licencelési kiszolgáló által felügyelt licencek készletét.  Ezt a típusú licencet időnként "lebegő" licencnek is nevezzük, mert csak az egyidejű felhasználók számára elegendő licenccel kell rendelkeznie.  Amikor egy felhasználó a SOLIDWORKS használatával történik, a licence visszakerül a központilag felügyelt licenckiszolgálóra, így egy másik felhasználó újra felhasználhatja azt.

Ebben a cikkben bemutatjuk, hogyan állíthat be egy, a SOLIDWORKS 2019-t és a hálózati licencelést használó osztályt.

## <a name="license-server"></a>Licenckiszolgáló

A SOLIDWORKS hálózati licenceléséhez telepíteni kell a SolidNetWork, és aktiválni kell a licenckiszolgálót.  Ez a licenckiszolgáló általában a helyi hálózaton vagy egy, az Azure-on belüli magánhálózaton található.  A SolidNetWork-licencek kiszolgálón való beállításával kapcsolatos további információkért lásd: a [License Manager telepítése és aktiválása](https://help.solidworks.com/2019/English/Installation/install_guide/t_installing_snl_lic_mgr.htm) a SOLIDWORKS telepítési útmutatójában.  Ha ezt a beállítást használja, jegyezze fel a **portszámot** és a [**sorozatszámot**](https://help.solidworks.com/2019/english/installation/install_guide/r_hid_state_serial_number.htm) , mivel ezek a későbbi lépésekben szükségesek.

A licenckiszolgáló beállítása után meg kell adni a [virtuális hálózatot (VNet)](./how-to-connect-peer-virtual-network.md) a [labor-fiókjához](./tutorial-setup-lab-account.md).  A hálózati társítást a tesztkörnyezet létrehozása előtt kell elvégezni, hogy a labor virtuális gépek hozzáférhessenek a licenckiszolgálóra és a másik megoldáshoz.

> [!NOTE]
> Győződjön meg arról, hogy a megfelelő portok meg vannak nyitva a tűzfalakon a tesztkörnyezet virtuális gépei és a licenckiszolgáló közötti kommunikáció engedélyezéséhez.  Tekintse meg például a [Windows tűzfal licencelés-kezelő számítógép portjainak módosítására](http://help.solidworks.com/2019/english/installation/install_guide/t_mod_ports_on_lic_mgr_for_firewall.htm) vonatkozó utasításokat, amelyek bemutatják, hogyan adhat hozzá bejövő és kimenő szabályokat a licenckiszolgáló tűzfalához.  Előfordulhat, hogy a tesztkörnyezet virtuális gépei számára is meg kell nyitnia a portokat.  Erről a következő cikkben ismertetett lépéseket követve további információt talál a tesztkörnyezetben a [tűzfal beállításairól](./how-to-configure-firewall-settings.md) , beleértve a tesztkörnyezet nyilvános IP-címének beszerzését.

## <a name="lab-configuration"></a>Tesztkörnyezet konfigurációja

A tesztkörnyezet beállításához Azure-előfizetésre és labor-fiókra van szükség a kezdéshez. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/). Az Azure-előfizetés beszerzése után létrehozhat egy új Labor-fiókot Azure Lab Services. Az új Labor-fiókok létrehozásával kapcsolatos további információkért tekintse meg a [labor-fiók beállítását](./tutorial-setup-lab-account.md)ismertető oktatóanyagot. Használhat meglévő labor-fiókot is.

### <a name="lab-account-settings"></a>Tesztkörnyezet-Fiókbeállítások

Engedélyezze az alábbi táblázatban ismertetett beállításokat a labor-fiókhoz. A Piactéri lemezképek engedélyezésével kapcsolatos további információkért tekintse meg a [Piactéri rendszerképek elérhetővé tétele a labor-készítők](./specify-marketplace-images.md)számára című cikket.

| Tesztkörnyezet-fiók beállítása | Utasítások |
| ------------------- | ------------ |
|Marketplace-beli rendszerkép| Engedélyezze a Windows 10 Pro-rendszerkép használatát a labor-fiókjában.|

> [!NOTE]
> A Windows 10 rendszeren kívül a SOLIDWORKS a Windows egyéb verzióit is támogatja.  A részletekért lásd a [SOLIDWORKS rendszerkövetelményeit](https://www.solidworks.com/sw/support/SystemRequirements.html) .

### <a name="lab-settings"></a>Tesztkörnyezet beállításai

A tantermi labor beállításakor használja az alábbi táblázatban szereplő beállításokat. A tantermi laborok létrehozásával kapcsolatos további információkért lásd: tantermi labor beállítása oktatóanyag.

| Tesztkörnyezet beállításai | Érték/utasítások |
| ------------ | ------------------ |
|Virtuális gép mérete| **Kis GPU (vizualizáció)**.  Ez a virtuális gép a távoli vizualizációk, a folyamatos átvitel, a játékok, a kódolók, például az OpenGL és a DirectX keretrendszerekkel való használatának legmegfelelőbb.|  
|Virtuálisgép-rendszerkép| Windows 10 Pro|

> [!NOTE]
> A kisméretű **GPU (vizualizáció)** virtuális gép mérete úgy van konfigurálva, hogy magas teljesítményű grafikus élményt engedélyezzen.  További információ erről a virtuálisgép-méretről: [tesztkörnyezet beállítása GPU-val](./how-to-setup-lab-gpu.md).

> [!WARNING]
> Ne felejtse el [, hogy a](./how-to-connect-peer-virtual-network.md) tesztkörnyezet létrehozása **előtt** a tesztkörnyezet virtuális hálózatát a virtuális hálózatra kell felvennie.

## <a name="template-virtual-machine-configuration"></a>Sablon virtuális gép konfigurációja

A jelen szakaszban ismertetett lépések bemutatják, hogyan állíthatja be a sablon virtuális gépet a SOLIDWORKS telepítési fájljainak letöltésével és az ügyfélszoftver telepítésével:

1. Indítsa el a sablon virtuális gépet, és csatlakozzon a géphez RDP használatával.

1. Töltse le a SOLIDWORKS-ügyfélszoftver telepítési fájljait. A letöltéshez két lehetősége van:
   - Letöltés a [SOLIDWORKS Customer Portalról](https://login.solidworks.com/nidp/idff/sso?id=cpenglish&sid=1&option=credential&sid=1&target=https%3A%2F%2Fcustomerportal.solidworks.com%2F).
   - Letöltés a kiszolgálók egyik könyvtárából.  Ha ezt a beállítást használta, gondoskodnia kell arról, hogy a kiszolgáló elérhető legyen a sablon virtuális gépről.  Előfordulhat például, hogy ez a kiszolgáló ugyanabban a virtuális hálózatban található, mint a labor-fiókjával.
  
    Részletekért lásd: [telepítés az egyes számítógépeken a](http://help.solidworks.com/2019/english/Installation/install_guide/c_installing_on_individual_computers.htm?id=fc149e8a968a422a89e2a943265758d3#Pg0) SOLIDWORKS – SolidWorks telepítési útmutatóban.

1. A telepítési fájlok letöltése után telepítse az ügyfélszoftvert a SOLIDWORKS Installation Manager használatával. Tekintse meg a [licenc-ügyfél telepítésének](http://help.solidworks.com/2019/english/installation/install_guide/t_installing_snl_license_client.htm) részleteit a SOLIDWORKS telepítési útmutatójában.

    > [!NOTE]
    > A **kiszolgáló hozzáadása** párbeszédpanelen a rendszer kérni fogja a licenckiszolgáló által használt **portszámot** , valamint a licenckiszolgáló nevét vagy IP-címét.

## <a name="cost"></a>Költségek

Az osztályra vonatkozó lehetséges költségbecslés. Ez a becslés nem tartalmazza a licenckiszolgáló futtatásának költségeit. 25 tanulós osztályt fogunk használni. 20 órányi ütemezett idő van. Emellett minden tanuló 10 órás kvótát kap a házi feladat vagy az ütemezett osztályon kívüli hozzárendelések számára. A kiválasztott virtuálisgép-méret kisméretű **GPU (vizualizáció)** volt, amely 160 Lab egység.

25 tanuló \* (20 ütemezett óra + 10 kvóta óra) \* 160 labor egység * 0,01 USD/óra = 1200,00 USD

>[!IMPORTANT]
> A költségbecslés csak példaként szolgál.  A díjszabással kapcsolatos aktuális információk: [Azure Lab Services díjszabása](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Következő lépések

A következő lépések közösek a laborok beállításához.

- [Sablon létrehozása és kezelése](how-to-create-manage-template.md)
- [Felhasználók hozzáadása](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kvóta beállítása](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ütemterv beállítása](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail-regisztrációs hivatkozások a tanulók számára](how-to-configure-student-usage.md#send-invitations-to-users)