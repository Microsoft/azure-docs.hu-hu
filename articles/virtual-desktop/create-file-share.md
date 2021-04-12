---
title: Azure Files fájlmegosztás létrehozása tartományvezérlővel – Azure
description: Hozzon létre egy FSLogix-profilt tárolót egy Azure-fájlmegosztás egy meglévő Windows rendszerű virtuális asztali alkalmazáskészletben a Active Directory tartománnyal.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e859da6b3ac38ddb89c998d172c39f2549455aaa
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447930"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>Profil tároló létrehozása Azure Files és AD DS

Ebből a cikkből megtudhatja, hogyan hozhat létre egy tartományvezérlő által hitelesített Azure-fájlmegosztást egy meglévő Windows rendszerű virtuális asztali készleten. Ezt a fájlmegosztást a tárolási profilok tárolására használhatja.

Ez a folyamat Active Directory Domain Services (AD DS) szolgáltatást használ, amely egy helyszíni címtárszolgáltatás. Ha további információt szeretne arról, hogyan hozhat létre egy FSLogix-profil tárolót az Azure AD DS használatával, tekintse meg a [FSLogix-profil tárolójának létrehozása a Azure Files](create-profile-container-adds.md)használatával című témakört.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg róla, hogy a tartományvezérlő szinkronizálva van az Azure-ba, és feloldható az Azure Virtual Network (VNET) szolgáltatással, amelyhez a munkamenet-gazdagépek csatlakoznak.

## <a name="set-up-a-storage-account"></a>Storage-fiók beállítása

Először be kell állítania egy Azure Files Storage-fiókot.

Storage-fiók beállítása:

1. Jelentkezzen be az Azure Portalra.

2. Keresse meg a **Storage-fiókot** a keresősáv alatt.

3. Válassza a **+Hozzáadás** lehetőséget.

4. Adja meg a következő információkat a  **Storage-fiók létrehozása** lapon:

    - Új erőforráscsoport létrehozása.
    - Adja meg a tárfiók egyedi nevét.
    - A **helyhez** azt javasoljuk, hogy ugyanazt a helyet adja meg, mint a Windows rendszerű virtuális asztali alkalmazáskészlet.
    - A **Teljesítmény** mezőben válassza a **Standard** lehetőséget. (A IOPS követelményeitől függően. További információ: [Storage Options for FSLogix Profile containers in Windows Virtual Desktop](store-fslogix-profile.md).)
    - A **fióktípus** területen válassza a **StorageV2** vagy a **FileStorage** lehetőséget (csak akkor érhető el, ha a teljesítményszint prémium szintű).
    - A **replikáláshoz** válassza a **helyileg REDUNDÁNS tárolás (LRS)** lehetőséget.

5. Ha elkészült, válassza a **felülvizsgálat + létrehozás**, majd a **Létrehozás** lehetőséget.

Ha részletesebb konfigurációs útmutatásra van szüksége, tekintse meg a [regionális elérhetőséget](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability)ismertető témakört.

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

Ezután létre kell hoznia egy Azure-fájlmegosztást.

Fájlmegosztás létrehozása:

1. Válassza az **Erőforrás megnyitása** lehetőséget.

2. Az Áttekintés lapon válassza a **Fájlmegosztások** lehetőséget.

3. Válassza a **+ fájlmegosztás** lehetőséget, hozzon létre egy új fájlmegosztási **profilt**, majd adjon meg egy megfelelő kvótát, vagy hagyja üresen a mezőt a kvóta nélkül.

4. Válassza a **Létrehozás** lehetőséget.

## <a name="enable-active-directory-authentication"></a>Active Directory hitelesítés engedélyezése

Ezután engedélyeznie kell Active Directory (AD-) hitelesítést. Ennek a szabályzatnak az engedélyezéséhez ezt a szakasz utasításait kell követnie egy már tartományhoz csatlakoztatott gépen. A hitelesítés engedélyezéséhez kövesse az alábbi utasításokat a tartományvezérlőt futtató virtuális gépen:

1. RDP protokoll a tartományhoz csatlakoztatott virtuális gépre.

2. Az AzFilesHybrid modul telepítéséhez és a hitelesítés engedélyezéséhez kövesse az [AD DS hitelesítés engedélyezése az Azure-fájlmegosztás](../storage/files/storage-files-identity-ad-ds-enable.md) számára című témakör utasításait.

3.  Nyissa meg a Azure Portal, nyissa meg a Storage-fiókját, válassza a **Konfigurálás** lehetőséget, majd erősítse meg, **Active Directory (ad)** beállítást **engedélyezze**.

     > [!div class="mx-imgBorder"]
     > ![A konfiguráció oldalának képernyőképe Azure Active Directory (AD) engedélyezve.](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-windows-virtual-desktop-users"></a>Azure RBAC engedélyek kiosztása a Windows rendszerű virtuális asztali felhasználók számára

A Storage-fiókban tárolt FSLogix-profilokkal rendelkező összes felhasználónak hozzá kell rendelnie a Storage file-adat SMB-megosztás közreműködői szerepkört.

A Windows Virtual Desktop-munkamenetgazdákra bejelentkező felhasználóknak hozzáférési engedélyekre van szüksége a fájlmegosztás eléréséhez. Az Azure-fájlmegosztásokhoz való hozzáférés biztosítása magában foglalja az engedélyeknek a megosztás szintjén, valamint az NTFS-szinten történő konfigurálását, egy hagyományos Windows-megosztáshoz hasonlóan.

A megosztási szint engedélyeinek konfigurálásához rendeljen hozzá minden felhasználót a megfelelő hozzáférési engedélyekkel rendelkező szerepkörhöz. Az engedélyek az egyes felhasználókhoz vagy az Azure AD-csoportokhoz is hozzárendelhetők. További információ: [hozzáférési engedélyek kiosztása identitáshoz](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

>[!NOTE]
>Azokat a fiókokat vagy csoportokat, amelyekhez engedélyeket rendel, a tartományban kell létrehozni, és szinkronizálni kell az Azure AD-vel. Az Azure AD-ben létrehozott fiókok nem fognak működni.

Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC-) engedélyek kiosztása:

1. Nyissa meg az Azure Portalt.

2. Nyissa meg a [Storage-fiók beállítása](#set-up-a-storage-account)című ben létrehozott Storage-fiókot.

3. Válassza a **fájlmegosztás** lehetőséget, majd válassza ki a használni kívánt fájlmegosztás nevét.

4. Válassza a **Access Control (iam)** lehetőséget.

5. Válassza **a szerepkör-hozzárendelés hozzáadása** lehetőséget.

6. A **szerepkör-hozzárendelés hozzáadása** lapon válassza a **tárolási fájl adat SMB-megosztás emelt szintű közreműködő** elemet a rendszergazdai fiókhoz.

     Ha engedélyeket szeretne a felhasználókhoz rendelni az FSLogix-profiljukhoz kapcsolódóan, kövesse ugyanezeket a lépéseket. Ha azonban az 5. lépésre lép, válassza ki a **Storage file-adatsmb-megosztás közreműködője** helyet.

7. Kattintson a **Mentés** gombra.

## <a name="assign-users-permissions-on-the-azure-file-share"></a>Felhasználói engedélyek kiosztása az Azure-fájlmegosztás számára

Miután hozzárendelte az Azure RBAC-engedélyeket a felhasználókhoz, a következő lépésben konfigurálnia kell az NTFS-engedélyeket.

Első lépésként ismernie kell két dolgot a Azure Portal:

- Az UNC elérési út.
- A tárfiókkulcs.

### <a name="get-the-unc-path"></a>UNC elérési út beolvasása

A következőképpen kérheti le az UNC elérési utat:

1. Nyissa meg az Azure Portalt.

2. Nyissa meg a [Storage-fiók beállítása](#set-up-a-storage-account)című ben létrehozott Storage-fiókot.

3. Válassza a **Beállítások**, majd a **Tulajdonságok** elemet.

4. Másolja az **elsődleges Fájlszolgáltatások végpontjának** URI-ját a kívánt szövegszerkesztőbe.

5. Az URI másolása után végezze el a következő műveleteket az UNC-re való váltáshoz:

    - Eltávolítás `https://` és csere a `\\`
    - Cserélje le a Forward perjelet a `/` vissza perjelre `\` .
    - Adja hozzá az [Azure-fájlmegosztás létrehozása](#create-an-azure-file-share) az UNC végéhez létrehozott fájlmegosztás nevét.

        Például: `\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>A tárfiókkulcs lekérése

A Storage-fiók kulcsának beszerzése:

1. Nyissa meg az Azure Portalt.

2. Nyissa meg a [Storage-fiók beállítása](#set-up-a-storage-account)című ben létrehozott Storage-fiókot.

3. A **Storage-fiók** lapon válassza a **hozzáférési kulcsok** elemet.

4. Másolja a **key1** vagy a **key2** fájlt a helyi számítógép egyik fájljába.

### <a name="configure-ntfs-permissions"></a>NTFS-engedélyek konfigurálása

Az NTFS-engedélyek konfigurálása:

1. Nyisson meg egy parancssort egy tartományhoz csatlakoztatott virtuális gépen.

2. A következő parancs futtatásával csatlakoztassa az Azure-fájlmegosztást, és rendeljen hozzá egy meghajtóbetűjelet:

     ```cmd
     net use <desired-drive-letter>: <UNC-path> <SA-key> /user:Azure\<SA-name>
     ```

3. A következő parancs futtatásával tekintse át az Azure-fájlmegosztás hozzáférési engedélyeit:

    ```cmd
    icacls <mounted-drive-letter>:
    ```

    A helyére írja be annak a `<mounted-drive-letter>` meghajtónak a betűjelét, amelyhez hozzárendelte.

    Alapértelmezés szerint az *NT Authority\Authenticated-felhasználók* és a *BUILTIN\Users* is rendelkeznek bizonyos engedélyekkel. Ezek az alapértelmezett engedélyek lehetővé teszik, hogy ezek a felhasználók beolvassák a többi felhasználó profiljának tárolóit. A [tárolási engedélyek konfigurálása a profil tárolókkal és az Office-tárolókkal való használatra](/fslogix/fslogix-storage-config-ht) című témakörben ismertetett engedélyek azonban nem teszik lehetővé a felhasználók számára, hogy olvassák a többi profil tárolóját.

4. A következő parancsok futtatásával engedélyezheti, hogy a Windows rendszerű virtuális asztali felhasználók saját profilt hozzanak létre, miközben blokkolja a más felhasználóktól származó profiljaik elérését.

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - Cserélje le <csatlakoztatott-meghajtóbetűjel> a meghajtó leképezni kívánt meghajtójának betűjelére.
     - Cserélje le <felhasználó-e-mail-> a megosztáshoz hozzáférést igénylő felhasználókat tartalmazó felhasználó vagy Active Directory csoport UPN-címére.

     Például:

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>FSLogix konfigurálása a munkamenet-gazdagépen futó virtuális gépeken

Ez a szakasz bemutatja, hogyan konfigurálhatók a virtuális gépek az FSLogixszal. Ezeket az utasításokat mindig követnie kell, amikor munkamenetgazdát konfigurál. A konfigurálás megkezdése előtt kövesse a [FSLogix letöltése és telepítése](/fslogix/install-ht)című témakör utasításait. Számos lehetőség áll rendelkezésre annak biztosítására, hogy beállításkulcsok beállítása mindegyik munkamenetgazdán megtörténjen. Ezeket a beállításokat megadhatja egy rendszerképben, vagy konfigurálhat csoportszabályzatot.

FSLogix konfigurálása a munkamenetgazda virtuális gépén:

1. RDP a Windows rendszerű virtuális asztali címkészlet munkamenet-gazda virtuális gépén.

2. [Töltse le és telepítse a FSLogix](/fslogix/install-ht).

5. Kövesse a [profiltároló beállításjegyzék-beállításainak konfigurálását](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings) ismertető cikk útmutatását:

    - Navigáljon a **számítógép**  >  **HKEY_LOCAL_MACHINE**  >  **szoftver**  >  **FSLogix**.

    - Hozzon létre egy **Profiles** kulcsot.

    - Hozzon létre egy 1 értékkel rendelkező **DWORD azonosítót** .

    - Hozzon létre **VHDLocations, MULTI_SZ**.

    - Állítsa be a **VHDLocations** értékét az [UNC elérési út lekérése](#get-the-unc-path)során létrehozott UNC elérési útra.

6. Indítsa újra a virtuális gépet.

## <a name="testing"></a>Tesztelés

A FSLogix telepítése és konfigurálása után tesztelheti a telepítést úgy, hogy bejelentkezik egy olyan felhasználói fiókkal, amely hozzá van rendelve egy alkalmazáscsoport vagy egy asztal a gazdagépen. Győződjön meg arról, hogy a bejelentkezett felhasználói fiók rendelkezik engedéllyel a fájlmegosztás számára.

Ha a felhasználó korábban már bejelentkezett, akkor egy meglévő helyi profillal fog rendelkezni, amely a munkamenet során lesz használva. Ha el szeretné kerülni a helyi profil létrehozását, hozzon létre egy új felhasználói fiókot a teszteléshez, vagy használja a következő témakörben ismertetett konfigurációs módszereket [: a profile tároló konfigurálása a felhasználói profilok átirányításához](/fslogix/configure-profile-container-tutorial/).

A munkamenet engedélyeinek ellenőrzését:

1. Munkamenet elindítása a Windows rendszerű virtuális asztalon.

2. Nyissa meg az Azure Portalt.

3. Nyissa meg a [Storage-fiók beállítása](#set-up-a-storage-account)című ben létrehozott Storage-fiókot.

4. Válassza a **megosztás létrehozása** lehetőséget az Azure-fájlmegosztás létrehozása lapon.

5. Győződjön meg arról, hogy a felhasználói profilt tartalmazó mappa már szerepel a fájlokban.

További teszteléshez kövesse a következő témakör utasításait: Ellenőrizze, [hogy a profil működik](create-profile-container-adds.md#make-sure-your-profile-works)-e.

## <a name="next-steps"></a>Következő lépések

A FSLogix hibaelhárításához tekintse meg [ezt a hibaelhárítási útmutatót](/fslogix/fslogix-trouble-shooting-ht).
