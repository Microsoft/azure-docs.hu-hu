---
title: Áttekintés – helyszíni AD DS hitelesítés az Azure-fájlmegosztás számára
description: Ismerje meg az Azure-fájlmegosztás Active Directory Domain Services (AD DS) hitelesítését. Ez a cikk a támogatási forgatókönyveket, a rendelkezésre állást, valamint azt ismerteti, hogy az engedélyek hogyan működnek a AD DS és az Azure Active Directory között.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 03/15/2021
ms.author: rogarana
ms.openlocfilehash: 26932d05cd3d2ef7704b48463c895e05524d87f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103472141"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Áttekintés – helyszíni Active Directory Domain Services hitelesítés SMB-en keresztül az Azure-fájlmegosztás esetében

[Azure Files](storage-files-introduction.md)   támogatja a kiszolgálói üzenetblokk (SMB) szolgáltatáson keresztüli identitás-alapú hitelesítést a következő két típusú tartományi szolgáltatáson keresztül: helyszíni Active Directory Domain Services (AD DS) és Azure Active Directory Domain Services (Azure AD DS). Javasoljuk, hogy tekintse át a [működéséről szóló szakaszt](./storage-files-active-directory-overview.md#how-it-works) , és válassza ki a megfelelő tartományi szolgáltatást a hitelesítéshez. A beállítás a választott tartományi szolgáltatástól függően eltérő. Ezek a cikksorozatok a helyszíni AD DS engedélyezésére és konfigurálására összpontosítanak az Azure-fájlmegosztás segítségével történő hitelesítéshez.

Ha még nem ismeri az Azure-fájlmegosztás újdonságait, javasoljuk, hogy olvassa el a [tervezési útmutatót](storage-files-planning.md) , mielőtt elolvassa a következő cikksorozatot.

## <a name="supported-scenarios-and-restrictions"></a>Támogatott forgatókönyvek és korlátozások

- AD DS Azure Files helyszíni AD DS hitelesítéshez használt identitásokat szinkronizálni kell az Azure AD-vel. A jelszó-kivonat szinkronizálása nem kötelező. 
- A Azure File Sync által felügyelt Azure-fájlmegosztás használatát támogatja.
- Támogatja a Kerberos-hitelesítést az AD-vel az RC4-HMAC és [AES 256 titkosítással](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). Az AES 256 titkosítási támogatása jelenleg <= 15 karakter hosszúságú Storage-fiókokra korlátozódik. Az AES 128 Kerberos-titkosítás még nem támogatott.
- Támogatja az egyszeri bejelentkezési élményt.
- Csak a Windows 7 vagy Windows Server 2008 R2 rendszernél újabb verziójú operációs rendszert futtató ügyfelek esetében támogatott.
- Csak azon AD-erdőben támogatott, amelyhez a Storage-fiók regisztrálva van. Alapértelmezés szerint csak egyetlen erdő AD DS hitelesítő adataival érheti el az Azure-fájlmegosztást. Ha egy másik erdőből kell hozzáférnie az Azure-fájlmegosztás számára, győződjön meg arról, hogy a megfelelő erdőszintű megbízhatósági kapcsolat van konfigurálva. a részletekért tekintse meg a [gyakori kérdéseket](storage-files-faq.md#ad-ds--azure-ad-ds-authentication) .
- A nem támogatja a AD DS-ben létrehozott számítógépfiókok hitelesítését.
- A nem támogatja a hálózati fájlrendszer (NFS) fájlmegosztás elleni hitelesítést.

Ha engedélyezi a AD DS az Azure-fájlmegosztás SMB protokollon keresztül történő engedélyezését, a AD DS csatlakoztatott gépek az Azure-fájlmegosztást meglévő AD DS hitelesítő adataival csatlakoztathatók. Ez a funkció AD DS környezettel engedélyezhető, amely a helyszíni gépeken vagy az Azure-ban üzemeltethető.

## <a name="videos"></a>Videók

A Azure Files AD-hitelesítés néhány gyakori felhasználási esethez való beállításának elősegítése érdekében két videót tettünk közzé a következő forgatókönyvek lépésenkénti útmutatójában:

| Helyszíni fájlkiszolgálók cseréje Azure Files (beleértve a fájlokhoz és az AD-hitelesítéshez tartozó magánhálózati kapcsolaton keresztül történő telepítést) | A Azure Files használata a Windows rendszerű virtuális asztali profil tárolójának használatával (beleértve az AD-hitelesítés és a FsLogix-konfiguráció beállítását is)  |
|-|-|
| [![Képernyőfelvétel a helyszíni fájlkiszolgálók cseréjéről – kattintson a lejátszáshoz.](./media/storage-files-identity-auth-active-directory-enable/replace-on-prem-server-thumbnail.png)](https://www.youtube.com/watch?v=jd49W33DxkQ) | [![A using Azure Files használata a profil tárolójában – kattintson a lejátszáshoz.](./media/storage-files-identity-auth-active-directory-enable/files-ad-ds-fslogix-thumbnail.png)](https://www.youtube.com/watch?v=9S5A1IJqfOQ) |


## <a name="prerequisites"></a>Előfeltételek 

Az Azure-fájlmegosztás AD DS hitelesítésének engedélyezése előtt győződjön meg arról, hogy végrehajtotta a következő előfeltételeket: 

- Válassza ki vagy hozza létre [AD DS-környezetét](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) , és [szinkronizálja azt az Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) -be Azure ad Connect használatával. 

    A szolgáltatást egy új vagy meglévő helyszíni AD DS környezetben is engedélyezheti. A hozzáféréshez használt identitásokat szinkronizálni kell az Azure AD-vel. Az Azure AD-bérlőt és az elérni kívánt fájlmegosztást ugyanahhoz az előfizetéshez kell társítani.

- Tartomány – helyszíni vagy Azure-beli virtuális gép csatlakoztatása helyszíni AD DShoz. További információ a tartományhoz való csatlakozásról: [számítógép csatlakoztatása tartományhoz](/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain).

    Ha a számítógép nincs tartományhoz csatlakoztatva egy AD DShoz, akkor továbbra is használhatja az AD hitelesítő adatokat a hitelesítéshez, ha a számítógép az AD-tartományvezérlővel rendelkezik.

- Válasszon ki vagy hozzon létre egy Azure Storage-fiókot.  Az optimális teljesítmény érdekében javasoljuk, hogy telepítse a Storage-fiókot ugyanabban a régióban, mint az ügyfél, amelyről a megosztást szeretné elérni. Ezután [csatlakoztassa az Azure-fájlmegosztást](storage-how-to-use-files-windows.md) a Storage-fiók kulcsával. A Storage-fiók kulcsának csatlakoztatása ellenőrzi a kapcsolódást.

    Győződjön meg arról, hogy a fájlmegosztást tartalmazó Storage-fiók még nincs konfigurálva az Azure AD DS-hitelesítéshez. Ha Azure Files Azure AD DS hitelesítés engedélyezve van a Storage-fiókon, akkor azt a helyszíni AD DS használata előtt le kell tiltani. Ez azt jelenti, hogy az Azure AD DS-környezetben konfigurált meglévő ACL-eket újra kell konfigurálni a megfelelő engedélyek kényszerítéséhez.


    Ha a Azure Fileshoz való csatlakozással kapcsolatos problémákat tapasztal, tekintse meg [a hibaelhárítási eszközt, amelyet Azure Files csatlakoztatási hibákhoz tettünk közzé a Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)rendszeren. Az 445-as port blokkolása esetén [útmutatást](./storage-files-faq.md#on-premises-access) is biztosítunk a forgatókönyvek megoldásához. 


- Az Azure-fájlmegosztás AD DS hitelesítésének engedélyezése és konfigurálása előtt végezze el a megfelelő hálózati konfigurációt. További információért tekintse meg [Azure Files hálózatkezelési megfontolásokat](storage-files-networking-overview.md) .

## <a name="regional-availability"></a>Regionális elérhetőség

Az AD DS-hitelesítés Azure Files az [összes Azure-beli nyilvános, kínai és gov régióban](https://azure.microsoft.com/global-infrastructure/locations/)elérhető.

## <a name="overview"></a>Áttekintés

Ha azt tervezi, hogy engedélyezi a fájlmegosztás hálózati konfigurációját, javasoljuk, hogy olvassa el a [hálózatkezelési megfontolásokat](./storage-files-networking-overview.md) ismertető cikket, és a AD DS hitelesítés engedélyezése előtt fejezze be a kapcsolódó konfigurációt.

Az Azure-fájlmegosztás AD DS hitelesítésének engedélyezése lehetővé teszi az Azure-fájlmegosztás hitelesítését a helyszíni AD DS hitelesítő adataival. Továbbá lehetővé teszi az engedélyek jobb kezelését a részletes hozzáférés-vezérlés engedélyezéséhez. Ehhez szükség van a helyszíni AD DS és az Azure AD közötti, AD-kapcsolaton keresztüli identitások szinkronizálására. A megosztási szint hozzáférését az Azure AD-be szinkronizált identitásokkal szabályozhatja, miközben a fájl-és megosztási szinthez való hozzáférést a helyszíni AD DS hitelesítő adataival kezelheti.

Ezután kövesse az alábbi lépéseket a AD DS-hitelesítés Azure Files beállításához: 

1. [Első rész: AD DS hitelesítés engedélyezése a Storage-fiókban](storage-files-identity-ad-ds-enable.md)

1. [Második rész: a megosztás hozzáférési engedélyeinek kiosztása az Azure AD-identitáshoz (felhasználó, csoport vagy szolgáltatásnév), amely szinkronban van a cél AD-identitással](storage-files-identity-ad-ds-assign-permissions.md)

1. [Harmadik rész: a Windows ACL-ek konfigurálása a címtárakhoz és a fájlokhoz](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [Negyedik rész: Azure-fájlmegosztás csatlakoztatása a AD DShoz csatlakoztatott virtuális géphez](storage-files-identity-ad-ds-mount-file-share.md)

1. [A Storage-fiók identitásának jelszavának frissítése AD DS](storage-files-identity-ad-ds-update-password.md)

Az alábbi ábra a teljes munkafolyamatot mutatja be, amely lehetővé teszi az Azure AD-hitelesítés használatát az SMB-en keresztül az Azure-fájlmegosztás számára. 

![Fájlok AD munkafolyamat-diagramja](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

Az Azure-fájlmegosztás eléréséhez használt identitásokat szinkronizálni kell az Azure AD-vel, hogy az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md) modellje segítségével kényszerítse a megosztási szintű fájlok engedélyeit. A meglévő fájlkiszolgálók által átvitt fájlok/könyvtárak [Windows-stílusú DACL-listái](/previous-versions/technet-magazine/cc161041(v=msdn.10)) megmaradnak és érvényben lesznek. Ez zökkenőmentes integrációt biztosít a vállalati AD DS környezettel. Amikor az Azure-fájlmegosztás használatával cseréli le a helyszíni fájlkiszolgálók szolgáltatást, a meglévő felhasználók az Azure-fájlmegosztást az aktuális ügyfelektől egyszeri bejelentkezéssel érhetik el, a használatban lévő hitelesítő adatok módosítása nélkül.  

## <a name="next-steps"></a>Következő lépések

Ha engedélyezni szeretné a helyszíni AD DS hitelesítést az Azure-fájlmegosztás számára, folytassa a következő cikkel:

[Első rész: AD DS hitelesítés engedélyezése a fiókhoz](storage-files-identity-ad-ds-enable.md)
