---
title: Áttekintés – Azure Files identitás-alapú hitelesítés
description: Azure Files támogatja az SMB-(Server Message Block) identitás-alapú hitelesítést a Azure Active Directory Domain Services (AD DS) és a Active Directory használatával. A tartományhoz csatlakoztatott Windows rendszerű virtuális gépek (VM-EK) az Azure AD-beli hitelesítő adatok használatával férhetnek hozzá az Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 8887243f953a7bb000033a2e56b4655b93cd7ca8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519792"
---
# <a name="overview-of-azure-files-identity-based-authentication-options-for-smb-access"></a>Az SMB-hozzáférésre vonatkozó Azure Files identitás-alapú hitelesítési beállítások áttekintése
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Ha meg szeretné tudni, hogyan engedélyezheti a helyszíni Active Directory Domain Services hitelesítést az Azure-fájlmegosztás számára, tekintse meg [a helyszíni Active Directory Domain Services hitelesítés engedélyezése SMB-en keresztül az Azure-fájlmegosztás](storage-files-identity-auth-active-directory-enable.md)számára című témakört.

Ha szeretné megtudni, hogyan engedélyezheti az Azure-AD DS hitelesítését az Azure-fájlmegosztás számára, tekintse [meg a Azure Active Directory Domain Services hitelesítés engedélyezése Azure Fileson](storage-files-identity-auth-active-directory-domain-service-enable.md)című részt.

## <a name="glossary"></a>Szószedet 
Hasznos megérteni az Azure AD tartományi szolgáltatás hitelesítésével kapcsolatos egyes kulcsfontosságú kifejezéseket az Azure-fájlmegosztás SMB szolgáltatásán keresztül:

-   **Kerberos-hitelesítés**

    A Kerberos egy olyan hitelesítési protokoll, amely a felhasználó vagy a gazdagép identitásának ellenőrzésére szolgál. További információ a Kerberosról: [Kerberos-hitelesítés áttekintése](/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Server Message Block (SMB) protokoll**

    Az SMB egy iparági szabványnak megfelelő hálózati fájlmegosztás protokoll. Az SMB-t a Common Internet File System vagy CIFS néven is nevezik. Az SMB-vel kapcsolatos további információkért lásd: a [Microsoft SMB protokoll és a CIFS protokoll áttekintése](/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) a Microsoft több-bérlős felhőalapú címtár-és Identitáskezelés-kezelési szolgáltatása. Az Azure AD egyetlen megoldásban ötvözi az alapvető Directory-szolgáltatásokat, az alkalmazások hozzáférés-kezelését és az identitások védelmét. Az Azure AD-hez csatlakoztatott Windows-alapú virtuális gépek (VM-EK) nem férhetnek hozzá az Azure-fájlmegosztás Azure AD-beli hitelesítő adataival. További információ: [Mi az Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Az Azure AD DS felügyelt tartományi szolgáltatásokat biztosít, például a tartományhoz való csatlakozást, a csoportházirendeket, az LDAP-t és a Kerberos/NTLM-hitelesítést. Ezek a szolgáltatások teljes mértékben kompatibilisek a Active Directory Domain Servicesokkal. További információ: [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Helyszíni Active Directory Domain Services (AD DS)**

    A helyszíni Active Directory Domain Services (AD DS) Azure Files integrációja biztosítja a címtáradatok tárolásának módszereit, miközben elérhetővé teszi a hálózati felhasználók és a rendszergazdák számára. A biztonság integrálva van AD DS a bejelentkezési hitelesítéssel és a címtárban található objektumok hozzáférés-vezérlésével. A rendszergazdák egyetlen hálózati bejelentkezéssel kezelhetik a címtáradatokat és a szervezetet a hálózaton belül, és a jogosult hálózati felhasználók hozzáférhetnek az erőforrásokhoz a hálózat bármely pontján. A AD DS általában a helyszíni környezetben működő vállalatok fogadják el, és AD DS hitelesítő adatokat használják a hozzáférés-vezérlés identitásának. További információ: [Active Directory Domain Services Overview (áttekintés](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)).

-   **Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)**

    Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi a részletes hozzáférés-kezelést az Azure-hoz. Az Azure RBAC segítségével kezelheti az erőforrásokhoz való hozzáférést azáltal, hogy a felhasználók számára a feladatok végrehajtásához szükséges legkevesebb jogosultságot biztosít. További információ az Azure RBAC: [Mi az az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Gyakori használati helyzetek

Az identitás-alapú hitelesítés és a Windows ACL-ek támogatása a Azure Files esetében a legmegfelelőbb a következő felhasználási esetekben:

### <a name="replace-on-premises-file-servers"></a>Helyszíni fájlkiszolgálók cseréje

A szétszórt helyszíni fájlkiszolgálók elavulttá váltása és cseréje gyakori probléma, hogy minden vállalat az informatikai modernizációs folyamat során találkozik. A helyszíni AD DS hitelesítéssel rendelkező Azure-fájlmegosztás itt a legjobb megoldás, ha áttelepítheti az adatAzure Filesba. A teljes áttelepítéssel kihasználhatja a magas rendelkezésre állást és a méretezhetőség előnyeit, ugyanakkor minimalizálhatja az ügyféloldali módosításokat is. Zökkenőmentes áttelepítési élményt biztosít a végfelhasználók számára, így a meglévő tartományhoz csatlakoztatott gépek használatával továbbra is hozzáférhetnek az adataikkal.

### <a name="lift-and-shift-applications-to-azure"></a>Alkalmazások átemelése és átirányítása az Azure-ba

Ha az alkalmazásokat a felhőbe emeli és áthelyezi, ugyanazt a hitelesítési modellt szeretné megőrizni az adataihoz. Ahogy kiterjesztjük az Identity-alapú hozzáférés-vezérlési élményt az Azure-fájlmegosztás számára, nem kell módosítania az alkalmazást a modern hitelesítési módszerekre, és fel kell gyorsítani a felhőbe való bevezetést. Az Azure-fájlmegosztás lehetőséget biztosít az Azure AD DS vagy a helyszíni AD DS integrálására a hitelesítéshez. Ha a terve a 100%-os felhőbe kerül, és a Felhőbeli infrastruktúrák kezelésének minimalizálására törekszik, az Azure AD DS jobban illeszkednek a teljes körűen felügyelt tartományi szolgáltatásokhoz. Ha AD DS-képességekkel való teljes kompatibilitásra van szüksége, érdemes megfontolnia, hogy a virtuális gépeken a saját üzemeltetésű tartományvezérlők használatával kiterjessze a AD DS környezetét a felhőbe. Mindkét esetben rugalmasságot biztosítunk az üzleti igényeknek megfelelő tartományi szolgáltatások kiválasztásához.

### <a name="backup-and-disaster-recovery-dr"></a>Biztonsági mentés és vész-helyreállítás (DR)

Ha az elsődleges tárterületet a helyszínen tartja, az Azure-fájlmegosztás ideális tárolóhelyként szolgálhat a Backup vagy a DR számára az üzletmenet folytonosságának javítása érdekében. Az Azure-fájlmegosztás használatával biztonsági mentést készíthet az adatokról a meglévő fájlkiszolgálók alapján, miközben megőrizheti a Windows DACL-kiszolgálókat. DR-forgatókönyvek esetén konfigurálhat egy olyan hitelesítési lehetőséget, amely támogatja a megfelelő hozzáférés-vezérlés kényszerítést a feladatátvétel során.

## <a name="supported-scenarios"></a>Támogatott esetek

Az alábbi táblázat összefoglalja az Azure AD DS és a helyszíni AD DS támogatott Azure-fájlmegosztás hitelesítési forgatókönyveit. Azt javasoljuk, hogy válassza ki azt a tartományi szolgáltatást, amelyet az ügyfél-környezethez az Azure Fileshoz való integrációhoz fogadott. Ha már AD DS a helyszíni vagy az Azure-ban, ahol az eszközei tartományhoz csatlakoznak az AD-hez, válassza az Azure-fájlmegosztás hitelesítésének AD DS kihasználása lehetőséget. Hasonlóképpen, ha már elvégezte az Azure AD DS használatát, használja ezt az Azure-fájlmegosztás hitelesítéséhez.


|Azure AD DS-hitelesítés  | Helyszíni AD DS hitelesítés  |
|---------|---------|
|Az Azure AD DS csatlakoztatott Windows rendszerű gépek az Azure AD hitelesítő adataival érhetik el az Azure-fájlmegosztást az SMB protokollon keresztül.     |A helyszíni AD DS csatlakoztatott vagy az Azure AD DShoz csatlakoztatott Windows rendszerű gépek képesek hozzáférni az Azure-fájlmegosztás számára a helyszíni Active Directory hitelesítő adataival, amelyek az SMB-en keresztül szinkronizálva vannak az Azure AD-vel. Az ügyfélnek a AD DSnak kell lennie.        |

### <a name="restrictions"></a>Korlátozások

- Az Azure AD DS és a helyszíni AD DS hitelesítés nem támogatja a számítógép-fiókok hitelesítését. Ehelyett érdemes lehet szolgáltatás-bejelentkezési fiókot használni.
- Sem az Azure AD DS hitelesítés, sem a helyszíni AD DS hitelesítés nem támogatott az Azure AD-hez csatlakoztatott eszközökön vagy az Azure AD-ban regisztrált eszközökön.
- Az Azure-fájlmegosztás csak az alábbi tartományi szolgáltatások egyikén támogatja az identitás alapú hitelesítést: [Azure Active Directory Domain Services (Azure AD DS)](#azure-ad-ds) vagy helyszíni [Active Directory Domain Services (AD DS)](#ad-ds).
- A hálózati fájlrendszer (NFS) nem támogatja az olyan identitás-alapú hitelesítési módszert, amely előzetes verzióban érhető el.

## <a name="advantages-of-identity-based-authentication"></a>Az identitás-alapú hitelesítés előnyei
A Azure Files identitás-alapú hitelesítése számos előnyt kínál a megosztott kulcsos hitelesítés használata esetén:

-   **Terjessze ki a hagyományos, identitás-alapú fájlmegosztás-hozzáférési élményt a felhőbe helyszíni AD DS és Azure AD DS**  
    Ha azt tervezi, hogy a felhőbe helyezi át az alkalmazást, és lecseréli a hagyományos fájlkiszolgálók Azure-fájlmegosztás használatával történő cseréjére, akkor előfordulhat, hogy az alkalmazás a fájlok adatainak eléréséhez helyszíni AD DS vagy Azure AD DS hitelesítő adatokkal hitelesíti magát. A Azure Files a helyszíni AD DS vagy az Azure AD DS hitelesítő adataival is támogatja az Azure-fájlmegosztás SMB-kapcsolaton keresztüli elérését a helyszíni AD DS vagy az Azure AD DS tartományhoz csatlakoztatott virtuális gépek használatával.

-   **Az Azure-fájlmegosztás részletes hozzáférés-vezérlésének betartatása**  
    Engedélyeket adhat meg egy adott identitáshoz a megosztás, a könyvtár vagy a fájl szintjén. Tegyük fel például, hogy több csapattal rendelkezik egyetlen Azure-fájlmegosztás használatával a projektek együttműködéséhez. Megadhatja, hogy az összes csapat hozzáférjen a nem bizalmas címtárakhoz, miközben a bizalmas pénzügyi adatokat tartalmazó címtárakhoz való hozzáférést csak a pénzügyi csapatának korlátozza. 

-   **A Windows ACL-ek (más néven NTFS) biztonsági mentése az adataival együtt**  
    Az Azure-fájlmegosztást használhatja a meglévő helyszíni fájlmegosztás biztonsági mentésére. Azure Files megőrzi az ACL-eket az adataival együtt, amikor biztonsági másolatot készít az Azure-fájlmegosztás SMB protokollon keresztüli megosztásáról.

## <a name="how-it-works"></a>Működés

Az Azure file shares szolgáltatás a Kerberos protokollt használja a helyszíni AD DS vagy az Azure AD DS való hitelesítéshez. Ha az ügyfélen futó felhasználóhoz vagy alkalmazáshoz tartozó identitás megpróbál hozzáférni az Azure-fájlmegosztás adataihoz, a rendszer a kérést a tartományi szolgáltatásba küldi, vagy AD DS vagy az Azure AD DS az identitás hitelesítéséhez. Ha a hitelesítés sikeres, egy Kerberos-tokent ad vissza. Az ügyfél olyan kérelmet küld, amely tartalmazza a Kerberos-tokent, és az Azure-fájlmegosztás ezt a tokent használja a kérelem engedélyezéséhez. Az Azure-fájlmegosztás csak a Kerberos-tokent kapja meg, és nem fér hozzá a hitelesítő adatokhoz.

Ahhoz, hogy engedélyezni lehessen az identitás-alapú hitelesítést az Azure-fájlmegosztás esetében, először be kell állítania a tartományi környezetet.

### <a name="ad-ds"></a>AD DS

Helyszíni AD DS hitelesítéshez be kell állítania az AD-tartományvezérlőket és a tartományhoz való csatlakozást a gépekhez vagy virtuális gépekhez. Az Azure-beli virtuális gépeken és a helyszínen is üzemeltetheti a tartományvezérlőket. Mindkét esetben a tartományhoz csatlakoztatott ügyfeleknek a tartományi szolgáltatásnak kell megfelelniük, ezért a tartományi szolgáltatás vállalati hálózatán vagy virtuális hálózatán (VNET) belül kell lenniük.

Az alábbi ábra a helyszíni AD DS hitelesítését ábrázolja az Azure-fájlmegosztás SMB-kapcsolaton keresztüli használatával. A helyszíni AD DS az Azure AD-be kell szinkronizálni Azure AD Connect Sync használatával. Csak a helyszíni AD DS és az Azure AD-ben található hibrid felhasználók hitelesíthetők és jogosultak az Azure file share-hozzáférésre. Ennek az az oka, hogy a megosztási szint engedély az Azure AD-ben jelölt identitásra van konfigurálva, ahol a könyvtár/fájl szintű engedély kényszerítve van a AD DS. Győződjön meg arról, hogy megfelelően konfigurálja az engedélyeket ugyanahhoz a hibrid felhasználóhoz.

:::image type="content" source="media/storage-files-active-directory-overview/Files-on-premises-AD-DS-Diagram.png" alt-text="A helyszíni AD DS az Azure-fájlmegosztás SMB-kapcsolaton keresztüli hitelesítését ábrázoló diagram.":::

### <a name="azure-ad-ds"></a>Azure AD DS

Az Azure AD DS hitelesítéshez engedélyeznie kell a Azure AD Domain Services és a tartományhoz való csatlakozást azokhoz a virtuális gépekhez, amelyeken a fájl adatait el szeretné érni. A tartományhoz csatlakoztatott virtuális gépnek ugyanabban a virtuális hálózatban (VNET) kell lennie, mint az Azure AD DS. 

Az alábbi ábra az Azure-AD DS az SMB-en keresztüli Azure-fájlmegosztás hitelesítéséhez használt munkafolyamatot ábrázolja. Az Azure-fájlmegosztás-hitelesítéshez hasonló mintát mutat a helyszíni AD DS hitelesítéshez. Két fő különbség van:

- Először nem kell létrehoznia az identitást az Azure AD DSban a Storage-fiók képviseletéhez. Ezt az engedélyezési folyamat hajtja végre a háttérben.

- Másodszor, az Azure AD-ben meglévő összes felhasználó hitelesíthető és engedélyezett. A felhasználó csak Felhőbeli vagy hibrid lehet. Az Azure AD-ből az Azure AD DS-ra való szinkronizálást a platform felügyeli anélkül, hogy felhasználói konfigurációra lenne szükség. Az ügyfélnek azonban tartományhoz kell tartoznia az Azure AD DShoz, nem lehet az Azure AD-hez csatlakoztatva vagy regisztrálni. 

:::image type="content" source="media/storage-files-active-directory-overview/Files-Azure-AD-DS-Diagram.png" alt-text="Ábra":::

### <a name="enable-identity-based-authentication"></a>Identitás-alapú hitelesítés engedélyezése

Az identitás-alapú hitelesítést engedélyezheti az Azure-AD DS vagy a helyszíni AD DS Azure-fájlmegosztás számára az új és meglévő Storage-fiókokban is. Csak egy tartományi szolgáltatás használható a fájl-hozzáférési hitelesítéshez a Storage-fiókban, amely a fiókban lévő összes fájlmegosztás esetében érvényes. Részletes útmutató a fájlmegosztás Azure-beli hitelesítéshez való beállításához AD DS a cikkben [Azure Active Directory Domain Services hitelesítés engedélyezése a Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) és a helyszíni AD DS útmutatása a másik cikkben, a helyszíni [Active Directory Domain Services hitelesítés engedélyezése az SMB-en keresztül az Azure-fájlmegosztás](storage-files-identity-auth-active-directory-enable.md)számára.

### <a name="configure-share-level-permissions-for-azure-files"></a>Megosztási szintű engedélyek konfigurálása Azure Fileshoz

Ha az Azure AD DS vagy a helyszíni AD DS hitelesítés engedélyezve van, használhatja az Azure beépített szerepköreit, vagy egyéni szerepköröket konfigurálhat az Azure AD-identitásokhoz, valamint hozzáférési jogokat rendelhet a Storage-fiókokban lévő összes fájlmegosztási megosztáshoz. A hozzárendelt engedély lehetővé teszi, hogy a megadott identitás csak a megosztáshoz kapjon hozzáférést, semmi más, még a gyökérkönyvtárban sem. Az Azure-fájlmegosztás esetében továbbra is külön kell konfigurálnia a címtár-vagy a fájl szintű engedélyeket.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>A Azure Files könyvtár-vagy fájl szintű engedélyeinek konfigurálása

Az Azure-fájlmegosztás a könyvtár és a fájl szintjén is kikényszeríti a Windows-fájlok szabványos engedélyeit, beleértve a gyökérkönyvtárat is. A címtár vagy a fájl szintű engedélyek konfigurációja az SMB és a REST protokollon keresztül is támogatott. Csatlakoztassa a célfájl megosztását a virtuális gépről, és konfigurálja az engedélyeket a Windows fájlkezelő, a Windows [icacls](/windows-server/administration/windows-commands/icacls)vagy a [set-ACL](/powershell/module/microsoft.powershell.security/get-acl) parancs használatával.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Rendszergazdai jogosultságok használata a Storage-fiók kulcsaként

A Storage-fiók kulcsával rendelkező felhasználók hozzáférhetnek az Azure-fájlmegosztás rendszergazdai engedélyeivel. Rendszergazdai jogosultságok az összes hozzáférés-vezérlési korlátozás mellőzése.

> [!IMPORTANT]
> Ajánlott biztonsági eljárásunk a Storage-fiókok kulcsai megosztásának elkerülése és az identitás-alapú hitelesítés kihasználása, amikor csak lehetséges.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Címtár és fájl ACL-ek megőrzése az Azure-fájlmegosztásba való adatimportáláskor

Azure Files támogatja az adatok Azure-fájlmegosztásba való másolásakor a címtár vagy a fájl szintű ACL-ek megőrzését. Az ACL-eket egy könyvtárra vagy fájlra másolhatja az Azure-fájlmegosztás Azure File Sync vagy közös fájl-áthelyezési eszközkészlet használatával. Használhatja például a [robocopyt](/windows-server/administration/windows-commands/robocopy) a `/copy:s` jelölővel az adatmásoláshoz, valamint az ACL-eket egy Azure-fájlmegosztás eléréséhez. Az ACL-ek alapértelmezés szerint megmaradnak, és az ACL-ek megőrzése érdekében nem szükséges az identitás-alapú hitelesítés engedélyezése a Storage-fiókban.

## <a name="pricing"></a>Díjszabás
A Storage-fiókjában nem található további szolgáltatás, amely lehetővé teszi az identitás-alapú hitelesítés használatát az SMB protokollon keresztül. A díjszabással kapcsolatos további információkért tekintse meg a [Azure Files díjszabását](https://azure.microsoft.com/pricing/details/storage/files/) és a [Azure ad Domain Services díjszabását](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Következő lépések
Az SMB-en keresztüli Azure Files-és identitás-alapú hitelesítéssel kapcsolatos további információkért tekintse meg a következő forrásokat:

- [Azure Files üzembe helyezésének tervezése](storage-files-planning.md)
- [Helyszíni Active Directory Domain Services hitelesítés engedélyezése SMB-en keresztül az Azure-fájlmegosztás esetében](storage-files-identity-auth-active-directory-enable.md)
- [Azure Active Directory Domain Services hitelesítés engedélyezése Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Gyakori kérdések](storage-files-faq.md)