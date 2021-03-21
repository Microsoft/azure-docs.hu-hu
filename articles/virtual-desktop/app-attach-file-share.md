---
title: Windows rendszerű virtuális asztali fájlmegosztási MSIX-alkalmazás csatlakoztatása – előzetes verzió – Azure
description: Fájlmegosztás beállítása a Windows rendszerű virtuális asztali MSIX alkalmazáshoz.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 49a350b77958901aae5e54e82d856e4f3772702e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97930786"
---
# <a name="set-up-a-file-share-for-msix-app-attach-preview"></a>Fájlmegosztás beállítása a MSIX-alkalmazás csatolásához (előzetes verzió)

> [!IMPORTANT]
> A MSIX-alkalmazás csatolása jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Minden MSIX-lemezképet egy olyan hálózati megosztáson kell tárolni, amelyet a gazdagépek a csak olvasási jogosultságokkal rendelkező gazdagépek számára tudnak elérni.

A MSIX-alkalmazás csatolása (előzetes verzió) nem rendelkezik függőségekkel a fájlmegosztás által használt tárolási háló típusával kapcsolatban. A MSIX-alkalmazás csatolási szempontjai ugyanazok, mint egy FSLogix-megosztás esetében. A tárolási követelményekkel kapcsolatos további tudnivalókért tekintse meg a [Windows rendszerű virtuális asztali FSLogix-profilok tárolási lehetőségei](store-fslogix-profile.md)című témakört.

## <a name="performance-requirements"></a>Teljesítményre vonatkozó követelmények

A MSIX alkalmazás a rendszerkép méretére vonatkozó korlátokat a rendszertől függ attól függően, hogy milyen tárolási típust használ a VHD-vagy VHDx-fájlok tárolására, valamint a VHD-, a VHSD-vagy a CIM-fájlok és a fájlrendszer méretére vonatkozó korlátozásokat.

Az alábbi táblázat azt mutatja be, hogy hány erőforrást tartalmaz egy 1 GB-os MSIX-rendszerkép egyetlen MSIX-alkalmazással az egyes virtuális gépekhez:

| Erőforrás             | Követelmények |
|----------------------|--------------|
| Állandó állapot IOPs    | 1 IOPs       |
| Számítógép rendszerindítási bejelentkezés | 10 IOPs      |
| Késés              | 400 MS       |

A követelmények nagy mértékben változhatnak attól függően, hogy hány MSIX csomagolt alkalmazást tárolnak a MSIX-rendszerképben. Nagyobb MSIX-lemezképek esetén további sávszélességet kell lefoglalnia.

### <a name="storage-recommendations"></a>Tárolásra vonatkozó javaslatok

Az Azure több tárolási lehetőséget is kínál, amelyek a MISX-alkalmazásokhoz használhatók. Javasoljuk, hogy Azure Files vagy Azure NetApp Files használjon, mivel ezek a lehetőségek a legjobb értéket nyújtják a költségek és a felügyelet terhelése között. A [Windows rendszerű virtuális asztali FSLogix-profilok tárolási lehetőségei](store-fslogix-profile.md) összehasonlítják az Azure által kínált különböző felügyelt tárolási megoldásokat a Windows rendszerű virtuális asztali környezetekben.

### <a name="optimize-msix-app-attach-performance"></a>A MSIX-alkalmazás teljesítményének optimalizálása

Íme néhány további dolog, amit érdemes optimalizálni a MSIX-alkalmazás csatlakoztatási teljesítményének optimalizálása érdekében:

- Az MSIX alkalmazáshoz használt tárolási megoldásnak ugyanabban az adatközpont-helyen kell lennie, mint a munkamenet-gazdagépeknek.
- A teljesítmény szűk keresztmetszetének elkerülése érdekében zárja ki a következő VHD-, VHDX-és CIM-fájlokat a víruskeresőből:
   
    - <MSIXAppAttachFileShare \> \* . VHD
    - <MSIXAppAttachFileShare \> \* . VHDX
    - \\\\storageaccount.file.core.windows.net- \\ megosztás \* \* . VHD
    - \\\\storageaccount.file.core.windows.net- \\ megosztás \* \* . VHDX
    - <MSIXAppAttachFileShare>. CIM
    - \\\\storageaccount.file.core.windows.net- \\ megosztás \* \* . CIM

- Válassza el a MSIX alkalmazáshoz tartozó Storage-hálót a FSLogix-profilok tárolói között.
- Minden virtuálisgép-rendszerfióknak és felhasználói fióknak csak olvasási engedéllyel kell rendelkeznie a fájlmegosztás eléréséhez.
- A Windows rendszerű virtuális asztal vész-helyreállítási terveinek tartalmaznia kell a MSIX alkalmazás csatolása a másodlagos feladatátvételi helyen. További információ a vész-helyreállításról: [az üzletmenet-folytonosság és a vész-helyreállítási terv beállítása](disaster-recovery.md).

## <a name="how-to-set-up-the-file-share"></a>A fájlmegosztás beállítása

A MSIX alkalmazás telepítési folyamata nagy mértékben megegyezik a [FSLogix-profilok telepítési folyamatával](create-host-pools-user-profile.md). Azonban különböző engedélyeket kell rendelnie a felhasználókhoz. Az MSIX alkalmazáshoz csak olvasási jogosultság szükséges a fájlmegosztás eléréséhez.

Ha a MSIX-alkalmazásait Azure Filesban tárolja, akkor a munkamenet-gazdagépek esetében az összes munkamenet-gazda virtuális gépnek hozzá kell rendelnie a Storage-fiók szerepköralapú hozzáférés-vezérlése (RBAC) és a fájlmegosztás új technológiai fájlrendszer (NTFS) engedélyeit a megosztáshoz.

| Azure-objektum                      | Szükséges szerepkör                                     | Szerepkör-függvény                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Munkamenet-gazdagép (VM számítógép-objektumok)| Storage-fájladatok SMB-megosztásának közreműködője          | Olvasás és végrehajtás, olvasás, mappa tartalmának listázása  |
| A fájlmegosztás rendszergazdái              | Storage-fájladatok SMB-megosztásának emelt szintű közreműködője | Teljes hozzáférés                                  |
| A fájlmegosztás felhasználói               | Storage-fájladatok SMB-megosztásának közreműködője          | Olvasás és végrehajtás, olvasás, mappa tartalmának listázása  |

A munkamenet-gazda virtuális gépek engedélyeinek a Storage-fiókhoz és a fájlmegosztáshoz való hozzárendeléséhez:

1. Hozzon létre egy Active Directory Domain Services (AD DS) biztonsági csoportot.

2. Adja hozzá a számítógép-fiókokat az összes munkamenet-gazda virtuális géphez a csoport tagjaiként.

3. Szinkronizálja a AD DS csoportot Azure Active Directory (Azure AD) szolgáltatásba.

4. Tárfiók létrehozása.

5. Hozzon létre egy fájlmegosztást a Storage-fiókban az Azure- [fájlmegosztás létrehozása](../storage/files/storage-how-to-create-file-share.md#create-file-share)című részben leírtak szerint.

6. Csatlakoztassa a Storage-fiókot úgy, hogy AD DS az első részben található utasításokat követve [: az Azure-fájlmegosztás AD DS hitelesítésének engedélyezése](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module).

7. Rendelje hozzá a szinkronizált AD DS csoportot az Azure AD-hez, és rendelje hozzá a tárolási fiókhoz a tárolási fájl adatsmb-megosztás közreműködői szerepkört.

8. Csatlakoztassa a fájlmegosztást bármely munkamenet-gazdagéphez a második részben található utasításokat követve, a [megosztási szintű engedélyek hozzárendeléséhez az identitáshoz](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

9. Engedélyezze az NTFS-engedélyeket a fájlmegosztás számára a AD DS csoport számára.

10. NTFS-engedélyek beállítása a felhasználói fiókokhoz. Szüksége lesz egy kiindulási egységre (OU) a AD DS arról, hogy a virtuális gépen lévő fiókok tartoznak a-hoz.

Miután hozzárendelte az identitást a tárhelyhez, kövesse a [következő lépésekben](#next-steps) szereplő cikkek utasításait, hogy más szükséges engedélyeket adjon meg a virtuális gépekhez hozzárendelt identitáshoz.

Arról is gondoskodnia kell, hogy a munkamenet-gazda virtuális gépei új technológiai fájlrendszerbeli (NTFS) engedélyekkel rendelkezzenek. Az engedélyek használatához rendelkeznie kell egy olyan operatív egység-tárolóval, amely Active Directory Domain Servicesból (AD DS) származik, és a felhasználóknak az adott operatív egység tagjainak kell lenniük.

## <a name="next-steps"></a>Következő lépések

A fájlmegosztás beállítása után a következő műveleteket kell végrehajtania:

- Megtudhatja, hogyan állíthat be Azure Active Directory Domain Services (AD DS) a [profil létrehozása Azure Files és AD DS](create-file-share.md)használatával.
- Megtudhatja, hogyan állíthatja be a Azure Files és az Azure AD DSt a [profil létrehozása a Azure Files és az azure AD DS](create-profile-container-adds.md)használatával.
- Megtudhatja, hogyan állíthat be Azure NetApp Files a MSIX alkalmazáshoz a [profil létrehozása a Azure NetApp Files és a AD DS](create-fslogix-profile-container.md)használatával.
- Megtudhatja, hogyan használhatja a virtuálisgép-alapú fájlmegosztást a [címkészlet létrehozása a gazdagéphez egy fájlmegosztás használatával](create-host-pools-user-profile.md).

Ha elkészült, néhány további erőforrást talál, amelyek hasznosak lehetnek:

- Kérje meg a közösségi kérdéseket a szolgáltatással kapcsolatban a [Windows rendszerű virtuális asztali TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
- A Windows rendszerű virtuális asztalok visszajelzéseit a [Windows Virtual Desktop visszajelzési központja](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)is elhagyhatja.
- [MSIX-alkalmazás szószedetének csatolása](app-attach-glossary.md)
- [MSIX-alkalmazás csatolása – GYIK](app-attach-faq.md)
