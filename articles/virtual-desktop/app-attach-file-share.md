---
title: Windows Virtual Desktop MSIX-alkalmazás csatolásának beállítása – Azure
description: Fájlmegosztás beállítása msix-alkalmazáshoz való csatoláshoz Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: d8aaa8d5013c426ac1ab6b367309c51be4929cee
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366402"
---
# <a name="set-up-a-file-share-for-msix-app-attach"></a>Fájlmegosztás beállítása AZ MSIX-alkalmazás csatolása számára

Minden MSIX-lemezképet egy olyan hálózati megosztáson kell tárolni, amelyet a felhasználók csak olvasási engedélyekkel rendelkező gazdagépkészletben tudnak elérni.

Az MSIX-alkalmazás csatolása nem függ a fájlmegosztás által használt tárolóalkalmazás típusától. Az MSIX-alkalmazás csatolási megosztásának szempontjai ugyanazok, mint az FSLogix-megosztásokét figyelembe véve. További információ a tárolási követelményekről: Tárolási lehetőségek FSLogix-profiltárolókhoz a [Windows Virtual Desktop.](store-fslogix-profile.md)

## <a name="performance-requirements"></a>Teljesítményre vonatkozó követelmények

Az MSIX-alkalmazás képméretkorlátai a VHD- vagy VHDX-fájlok tárolásához használt tárolótípustól, valamint a VHD-, VHSD- vagy CIM-fájlok méretkorlátozásaitól, valamint a fájlrendszertől függenek.

Az alábbi táblázat egy példát mutat be arra, hogy egy 1 GB-os MSIX-rendszerképnek hány erőforrásra van szüksége egy MSIX-alkalmazással az egyes virtuális gépekhez:

| Erőforrás             | Követelmények |
|----------------------|--------------|
| Stabil állapot I/O-i    | 1 I/O       |
| Gép rendszerindítási bejelentkezése | 10 I/O      |
| Késés              | 400 ms       |

A követelmények nagy mértékben változhatnak az MSIX-lemezképben tárolt MSIX-csomagolt alkalmazások számától függően. Nagyobb MSIX-lemezképek esetén nagyobb sávszélességet kell lefoglalni.

### <a name="storage-recommendations"></a>Tárolásra vonatkozó javaslatok

Az Azure több tárolási lehetőséget is kínál, amelyek a MISX-alkalmazások csatolása során használhatók. Javasoljuk, hogy Azure Files vagy Azure NetApp Files, mivel ezek a lehetőségek a legjobb értéket kínálják a költségek és a felügyelet többletterhelése között. Az [FSLogix-profiltárolók](store-fslogix-profile.md) tárolási lehetőségei a Windows Virtual Desktop az Azure által kínál különböző felügyelt tárolási megoldásokat hasonlítja össze az Windows Virtual Desktop.

### <a name="optimize-msix-app-attach-performance"></a>Az MSIX-alkalmazás csatolási teljesítményének optimalizálása

Az MSIX-alkalmazás csatolási teljesítményének optimalizálása érdekében javasoljuk még néhány dolgot:

- Az MSIX-alkalmazás csatolására használt tárolási megoldásnak ugyanazon az adatközponton kell lennie, mint a munkamenetgazdának.
- A teljesítménybeli szűk keresztmetszetek elkerülése érdekében zárja ki a következő VHD-, VHDX- és CIM-fájlokat a víruskereső vizsgálatokból:
   
    - <MSIXAppAttachFileShare \> \* . Vhd
    - <MSIXAppAttachFileShare \> \* . VHDX
    - \\\\storageaccount.file.core.windows.net \\ \* \* megosztás. Vhd
    - \\\\storageaccount.file.core.windows.net \\ \* \* megosztás. VHDX
    - <MSIXAppAttachFileShare>. Cim
    - \\\\storageaccount.file.core.windows.net \\ \* \* megosztás. Cim

- Válassza el az MSIX-alkalmazás csatolásának tárolóját az FSLogix-profiltárolóktól.
- A fájlmegosztás eléréséhez minden VM-rendszerfióknak és felhasználói fióknak csak olvasási jogosultsággal kell rendelkeznie.
- Minden vészhelyreállítási tervnek Windows Virtual Desktop tartalmaznia kell az MSIX-alkalmazás csatolási fájlmegosztásának replikálása a másodlagos feladatátvételi helyen. További információ a vészhelyreállításról: [Üzletmenet-folytonossági](disaster-recovery.md)és vészhelyreállítási terv beállítása.

## <a name="how-to-set-up-the-file-share"></a>A fájlmegosztás beállítása

Az MSIX-alkalmazás csatolási fájlmegosztásának telepítési folyamata nagyrészt megegyezik az [FSLogix-profilfájlmegosztások telepítési folyamatának folyamatához.](create-host-pools-user-profile.md) Ehhez azonban különböző engedélyeket kell hozzárendelni a felhasználókhoz. Az MSIX-alkalmazás csatolásához csak olvasási engedélyekre van szükség a fájlmegosztás eléréséhez.

Ha MSIX-alkalmazásait az Azure Files-ban tárolja, akkor a munkamenetgazdákhoz minden munkamenetgazda virtuális géphez hozzá kell rendelnie tárfiók szerepköralapú hozzáférés-vezérlési (RBAC) és fájlmegosztási Új technológiai fájlrendszer (NTFS) engedélyeket a megosztáson.

| Azure-objektum                      | Szükséges szerepkör                                     | Szerepköri függvény                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Munkamenetgazda (virtuális gép számítógép-objektumai)| Storage-fájladatok SMB-megosztásának közreműködője          | Read and Execute, Read, List folder contents (Mappa tartalmának olvasása és végrehajtása, olvasása, listása)  |
| Fájlmegosztás rendszergazdái              | Storage-fájladatok SMB-megosztásának emelt szintű közreműködője | Teljes hozzáférés                                  |
| Fájlmegosztáson található felhasználók               | Storage-fájladatok SMB-megosztásának közreműködője          | Read and Execute, Read, List folder contents (Mappa tartalmának olvasása és végrehajtása, olvasása, listása)  |

Munkamenetgazda virtuális gépek engedélyeinek hozzárendelése a tárfiókhoz és a fájlmegosztáshoz:

1. Hozzon létre Active Directory Domain Services (AD DS) biztonsági csoportot.

2. Vegye fel a munkamenetgazda virtuális gépek számítógépfiókját a csoport tagjaiként.

3. Szinkronizálja az AD DS csoportot Azure Active Directory (Azure AD) használatával.

4. Tárfiók létrehozása.

5. Hozzon létre egy fájlmegosztást a tárfiók alatt az Azure-fájlmegosztás [létrehozása útmutatását követve.](../storage/files/storage-how-to-create-file-share.md#create-file-share)

6. Csatlakozzon a tárfiókhoz a AD DS az első rész utasításait követve: engedélyezze AD DS [azure-fájlmegosztások hitelesítését.](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module)

7. Rendelje hozzá a szinkronizált AD DS csoportot az Azure AD-hez, és rendelje hozzá a tárfiókhoz a Storage-fájladatok SMB-megosztási közreműködője szerepkört.

8. Csatlakoztassa a fájlmegosztást bármely munkamenetgazda számára a következő részben található utasításokat követve: megosztási szintű engedélyek hozzárendelése egy [identitáshoz.](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)

9. Adjon NTFS-engedélyeket a fájlmegosztásnak a AD DS számára.

10. Állítson be NTFS-engedélyeket a felhasználói fiókokhoz. Szüksége lesz egy olyan üzemeltetési egységre (OU), amely abból a AD DS amelybe a virtuális gép fiókjai tartoznak.

Miután hozzárendelt egy identitást a tárolóhoz, a [](#next-steps) Következő lépések cikkben található utasításokat követve adjon meg más szükséges engedélyeket a virtuális gépekhez rendelt identitáshoz.

Arról is meg kell győződni, hogy a munkamenetgazda virtuális gépei new Technology File System (NTFS) engedélyekkel rendelkezik. Rendelkeznie kell egy operatívegység-tárolóval, amely a Active Directory Domain Services -től (AD DS) származik, és a felhasználóknak ennek az üzemeltetési egységnek a tagjainak kell lennie az engedélyek használhatja.

## <a name="next-steps"></a>Következő lépések

A fájlmegosztás beállítása után a következő további dolgokat kell még megtennie:

- A profiltárolók Azure Active Directory Domain Services (AD DS) beállítását a Profiltároló létrehozása Azure Files [és a AD DS.](create-file-share.md)
- A profiltárolók Azure Files és Azure AD DS beállítását [a Profiltároló](create-profile-container-adds.md)létrehozása a Azure Files és a Azure AD DS.
- Az MSIX-Azure NetApp Files beállítását a Profiltároló létrehozása a Azure NetApp Files és a [AD DS.](create-fslogix-profile-container.md)
- A virtuálisgép-alapú fájlmegosztások használatát a Profiltároló létrehozása gazdagépkészlethez [fájlmegosztás használatával.](create-host-pools-user-profile.md)

Ha végzett, íme néhány további erőforrás, amelyek hasznosak lehetnek:

- A funkcióval kapcsolatos kérdéseit a [TechCo Windows Virtual Desktop fel.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)
- Visszajelzést is küldhet a Windows Virtual Desktop a [Windows Virtual Desktop visszajelzési központban.](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)
- [MSIX-alkalmazás csatolása – szószedet](app-attach-glossary.md)
- [MSIX-alkalmazás csatolása – GYIK](app-attach-faq.md)
