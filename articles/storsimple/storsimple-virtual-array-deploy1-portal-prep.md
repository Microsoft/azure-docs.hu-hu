---
title: StorSimple virtuális tömbhöz készült portál
description: A StorSimple Virtual Array üzembe helyezésének első oktatóanyaga a Azure Portal előkészítését foglalja magában.
author: alkohli
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1a3d0b05f04ad8c182d428843479160666b96ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87070315"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>StorSimple virtuális tömb üzembe helyezése – a Azure Portal előkészítése

![A virtuális tömb üzembe helyezéséhez szükséges lépéseket bemutató ábra. Az első lépés a kezdeti lépések feliratú, és ki van emelve.](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Ez az első cikk a telepítési oktatóanyagok sorozatában, amely a virtuális tömb fájlkiszolgálóként vagy iSCSI-kiszolgálóként való teljes üzembe helyezéséhez szükséges a Resource Manager-modell használatával. Ez a cikk a StorSimple Eszközkezelő szolgáltatás létrehozásához és konfigurálásához szükséges előkészületeket ismerteti a virtuális tömb kiépítés előtt. Ez a cikk a telepítési konfigurációs ellenőrzőlista és a konfiguráció előfeltételeire is hivatkozik.

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. A Kezdés előtt javasoljuk, hogy tekintse át a telepítési konfigurációhoz tartozó ellenőrzőlistát. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

A cikkben közzétett információk a StorSimple virtuális tömbök üzembe helyezésére vonatkoznak a Azure Portal és Microsoft Azure Government felhőben.

### <a name="get-started"></a>Bevezetés
Az üzembe helyezési munkafolyamat a portál előkészítését, virtuális tömb kiépítését a virtualizált környezetben, valamint a telepítés befejezését foglalja magában. Ha a StorSimple Virtual Array üzemelő példányát fájlkiszolgáló vagy iSCSI-kiszolgálóként szeretné elindítani, tekintse át a következő táblázatos erőforrásokat.

#### <a name="deployment-articles"></a>Üzembe helyezési cikkek

A StorSimple virtuális tömb üzembe helyezéséhez tekintse meg az alábbi cikkeket az előírt sorozatban.

| **#** | **Ebben a lépésben** | **Ezt...** | **És használja ezeket a dokumentumokat.** |
| --- | --- | --- | --- |
| 1. |**A Azure Portal beállítása** |Hozza létre és konfigurálja a StorSimple Eszközkezelő szolgáltatást a StorSimple virtuális tömb kiépítés előtt. |[A portál előkészítése](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**A virtuális tömb kiépítése** |Hyper-V esetén hozzon létre és kapcsolódjon egy StorSimple virtuális tömbhöz a Hyper-V-t futtató gazdagépen a Windows Server 2012 R2, a Windows Server 2012 vagy a Windows Server 2008 R2 rendszereken. <br></br> <br></br> VMware esetén a VMware ESXi 5,0, 5,5, 6,0 vagy 6,5 rendszert futtató gazdagépen hozzon létre és kapcsolódjon egy StorSimple virtuális tömbhöz.<br></br> |[Virtuális tömb kiépítése a Hyper-V-ben](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Virtuális tömb kiépítése a VMware-ben](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**A virtuális tömb beállítása** |A fájlkiszolgálón hajtsa végre a kezdeti telepítést, regisztrálja a StorSimple-fájlkiszolgáló, és fejezze be az eszköz beállítását. Ezután üzembe helyezheti az SMB-megosztásokat. <br></br> <br></br> Az iSCSI-kiszolgáló esetén hajtsa végre a kezdeti telepítést, regisztrálja az StorSimple iSCSI-kiszolgálót, és fejezze be az eszköz beállítását. Ezután kiépítheti az iSCSI-köteteket. |[Virtuális tömb beállítása fájlkiszolgálóként](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Virtuális tömb beállítása iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Most megkezdheti az Azure Portal beállítását.

## <a name="configuration-checklist"></a>Konfigurációs ellenőrzőlista

A konfigurációs ellenőrzőlista leírja azokat az információkat, amelyeket a StorSimple virtuális tömbben lévő szoftver konfigurálása előtt össze kell gyűjtenie. Az információk előre való előkészítése megkönnyíti a StorSimple-eszköz üzembe helyezésének folyamatát a környezetben. Attól függően, hogy a StorSimple virtuális tömb fájlkiszolgáló vagy iSCSI-kiszolgálóként van-e telepítve, az alábbi feladatlisták egyikére lesz szüksége.

* Töltse le a [StorSimple Virtual Array fájlkiszolgáló konfigurációs ellenőrzőlistáját](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Töltse le a [StorSimple virtuális tömb iSCSI-kiszolgálójának konfigurációs ellenőrzőlistáját](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Előfeltételek

Itt találja a StorSimple Eszközkezelő szolgáltatás, a StorSimple virtuális tömbje és az adatközpont hálózatának konfigurációs előfeltételeit.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Rendelkezik Microsoft-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* A Microsoft Azure-előfizetést engedélyezni kell a StorSimple Eszközkezelő szolgáltatáshoz.

### <a name="for-the-storsimple-virtual-array"></a>A StorSimple virtuális tömbhöz

Virtuális tömb üzembe helyezése előtt győződjön meg a következőket:

* A Hyper-V-t futtató gazdagéprendszer a Windows Server 2008 R2 vagy újabb, vagy a VMware (ESXi 5,0, 5,5, 6,0 vagy 6,5) használatával érhető el egy eszköz kiépítéséhez.
* A gazdagéprendszer a következő erőforrásokat tudja kiépíteni a virtuális tömb kiépítéséhez:
  
  * Legalább 4 mag.
  * Legalább 8 GB RAM. Ha úgy tervezi, hogy a virtuális tömböt fájlkiszolgálóként konfigurálja, a 8 GB támogatja a 2 000 000-fájlokat. 2-4 millió fájl támogatásához 16 GB RAM szükséges.
  * Egy hálózati adapter.
  * Egy 500 GB méretű virtuális lemez a rendszeradathoz.

### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az adatközpont hálózata a StorSimple-eszköz hálózati követelményeinek megfelelően van konfigurálva. További információt a [StorSimple virtuális tömb rendszerkövetelményei](storsimple-ova-system-requirements.md)című témakörben talál.
* A StorSimple Virtual Array dedikált 5 Mbps Internet-sávszélességgel (vagy több) érhető el. Ez a sávszélesség nem osztható meg más alkalmazásokkal.

## <a name="step-by-step-preparation"></a>Lépésenkénti előkészítés

A következő lépésenkénti útmutatást követve készítse elő a portált a StorSimple Eszközkezelő szolgáltatáshoz.

## <a name="step-1-create-a-new-service"></a>1. lépés: Új szolgáltatás létrehozása

A StorSimple Eszközkezelő-szolgáltatás egyetlen példánya képes több StorSimple virtuális tömb kezelésére. Az alábbi lépések végrehajtásával hozza létre a StorSimple-eszközkezelő szolgáltatás egy példányát. Ha rendelkezik meglévő StorSimple Eszközkezelő szolgáltatással a virtuális tömbök felügyeletéhez, hagyja ki ezt a lépést, és folytassa a [2. lépés: a szolgáltatás regisztrációs kulcsának beszerzésével](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással, akkor legalább egy tárfiókot létre kell hoznia, miután sikeresen létrehozott egy szolgáltatást.
> 
> * Ha nem hozott létre automatikusan egy tárfiókot, a részletes utasításokat az [Új tárfiók konfigurálása a szolgáltatáshoz](#optional-step-configure-a-new-storage-account-for-the-service) című szakaszban tekintheti meg.
> * Ha engedélyezte a tárfiók automatikus létrehozását, folytassa a [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](#step-2-get-the-service-registration-key) című szakasszal.
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>2. lépés: Szolgáltatásregisztrációs kulcs lekérése

Ha a StorSimple-eszközkezelő szolgáltatás működik és elérhető, le kell kérnie a szolgáltatásregisztrációs kulcsot. Ezzel a kulccsal regisztrálhatja és csatlakoztathatja StorSimple eszközét a szolgáltatáshoz.

Hajtsa végre a következő lépéseket az [Azure Portalon](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> A szolgáltatás regisztrációs kulcsa a StorSimple Eszközkezelő szolgáltatásban regisztrálni kívánt összes Eszközkezelő StorSimple regisztrálására szolgál.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>3. lépés: a virtuális tömb rendszerképének letöltése

A szolgáltatás regisztrációs kulcsának elvégzése után le kell töltenie a megfelelő virtuális tömb képét a virtuális tömb kiépítéséhez a gazdagép rendszerén. A virtuális tömb lemezképei operációs rendszer-specifikusak, és a Azure Portal gyorskonfigurálás lapjáról tölthetők le.

> [!IMPORTANT]
> A StorSimple virtuális tömbön futó szoftver csak a StorSimple Eszközkezelő szolgáltatással használható.
> 
> 

Hajtsa végre a következő lépéseket az [Azure Portalon](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>A virtuális tömb rendszerképének beolvasása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). 
2. A Azure Portal kattintson a **tallózás > StorSimple-eszközkezelők** elemre.
3. Válasszon ki egy meglévő StorSimple Eszközkezelő szolgáltatást. A **StorSimple Eszközkezelő** panelen kattintson az **gyorskonfigurálás** elemre. 
4. Kattintson a Microsoft letöltőközpontból letölteni kívánt rendszerképhez tartozó hivatkozásra. A rendszerképfájlok mérete körülbelül 4,8 GB.
   
   * VHDX a Hyper-V-hez a Windows Server 2012-es és újabb verzióiban
   * VHD a Hyper-V-hez Windows Server 2008 R2 és újabb rendszereken
   * VMDK VMWare ESXi 5,0, 5,5, 6,0 vagy 6,5
5. Töltse le a fájlt egy helyi meghajtóra, csomagolja ki, és jegyezze fel, hol található a kicsomagolt fájl.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Választható lépés: új Storage-fiók konfigurálása a szolgáltatáshoz

Ez a lépés nem kötelező, és csak akkor hajtható végre, ha nem engedélyezte egy Storage-fiók automatikus létrehozását a szolgáltatással.

Ha egy másik régióban kell létrehoznia egy Azure Storage-fiókot, tekintse meg [a Storage-fiók létrehozása](../storage/common/storage-account-create.md) lépésről lépésre című témakört.

Meglévő Microsoft Azure Storage-fiók hozzáadásához hajtsa végre a következő lépéseket a StorSimple Eszközkezelő szolgáltatás lapjának [Azure Portal](https://ms.portal.azure.com/) .

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>A Eszközkezelő szolgáltatással azonos Azure-előfizetéssel rendelkező Storage-fiók hitelesítő adatainak hozzáadása

1. Navigáljon a Eszközkezelő szolgáltatáshoz, válassza ki, majd kattintson rá duplán. Ekkor megnyílik az **Áttekintés** panel.
2. A **konfigurációs** szakaszban válassza ki a **Storage-fiók hitelesítő adatait** .
3. Kattintson a **Hozzáadás** parancsra.
4. A **Storage-fiók hozzáadása** panelen tegye a következőket:
   
   1. Az **előfizetés** mezőben válassza az **aktuális** lehetőséget.
   
   2. Adja meg az Azure Storage-fiók nevét.
   
   3. Az **Engedélyezés** gombra kattintva biztonságos csatornát hozhat létre a StorSimple-eszköz és a felhő közötti hálózati kommunikációhoz. Csak akkor válassza a **Letiltás** lehetőséget, ha privát felhőben működik.
   
   4. Kattintson a **Hozzáadás** parancsra. A rendszer értesítést küld arról, hogy a Storage-fiók létrehozása sikeres volt.<br></br>
   
      ![Meglévő Storage-fiók hitelesítő adatainak hozzáadása](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Következő lépés

A következő lépés egy virtuális gép kiépítése a StorSimple virtuális tömbhöz. A gazdagép operációs rendszerétől függően az alábbi témakörökben tekintheti meg a részletes utasításokat:

* [StorSimple virtuális tömb kiépítése a Hyper-V-ben](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [StorSimple virtuális tömb kiépítése a VMware-ben](storsimple-virtual-array-deploy2-provision-vmware.md)

