---
title: Jelszó nélküli biztonsági kulcs bejelentkezés a helyszíni erőforrásokra – Azure Active Directory
description: Megtudhatja, hogyan engedélyezheti a jelszó nélküli biztonsági kulcsok bejelentkezését a helyszíni erőforrásokra Azure Active Directory használatával
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: db1b559bb4f6a1f8866116c287df5b814500210b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647472"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory"></a>Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése a helyszíni erőforrásokhoz Azure Active Directory 

Ez a dokumentum az **Azure ad** -hez csatlakoztatott és a **hibrid Azure ad-hez csatlakoztatott** Windows 10 rendszerű eszközökön a helyi erőforrásokhoz való jelszavas hitelesítés engedélyezését összpontosítja. Ez a funkció zökkenőmentes egyszeri bejelentkezést (SSO) biztosít a helyszíni erőforrásokhoz a Microsoft-kompatibilis biztonsági kulcsok használatával.

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Egyszeri bejelentkezés a helyszíni erőforrásokhoz FIDO2-kulcsok használatával

Azure Active Directory (AD) kiállíthat egy vagy több Active Directory-tartomány Kerberos-jegyét megadó jegyet (TGT). Ez a funkció lehetővé teszi, hogy a felhasználók modern hitelesítő adatokkal jelentkezzenek be a Windowsba, mint például a FIDO2 biztonsági kulcsai és a hagyományos Active Directory-alapú erőforrások A Kerberos szolgáltatási jegyeit és az engedélyezést továbbra is a helyszíni Active Directory tartományvezérlők ellenőrzik.

Létrejön egy Azure AD Kerberos-kiszolgálói objektum a helyszíni Active Directory, majd biztonságosan közzétéve Azure Active Directory. Az objektum nincs fizikai kiszolgálókhoz társítva. Ez egyszerűen egy olyan erőforrás, amelyet a Azure Active Directory használhat a Active Directory-tartomány Kerberos-TGT létrehozásához.

![Jegy beszerzése (TGT) az Azure AD-ből és AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. A felhasználó egy FIDO2 biztonsági kulccsal jelentkezik be a Windows 10-es eszközre, és hitelesíti magát az Azure AD-ben.
1. Az Azure AD ellenőrzi a felhasználó helyszíni AD-tartományának megfelelő Kerberos-kiszolgálói kulcs címtárát.
   1. Az Azure AD egy Kerberos-TGT hoz létre a felhasználó helyszíni AD-tartományához. A TGT csak a felhasználó SID-azonosítóját tartalmazza. A TGT nem tartalmaz engedélyezési adatértéket.
1. A TGT az Azure AD elsődleges frissítési jogkivonatával (PRT) együtt visszaadja az ügyfélnek.
1. Az ügyfélszámítógép kapcsolatba lép egy helyszíni AD-tartományvezérlővel, és a részleges TGT kereskedik egy teljesen formázott TGT.
1. Az ügyfélgépen már van egy Azure AD-PRT és egy teljes Active Directory TGT, és a Felhőbeli és a helyszíni erőforrások elérésére is képes.

## <a name="requirements"></a>Követelmények

A szervezeteknek el kell végezniük a [jelszó nélküli biztonsági kulcs aláírásának engedélyezése a Windows 10-es eszközökön](howto-authentication-passwordless-security-key.md) című témakör lépéseit a jelen cikkben ismertetett lépések végrehajtása előtt.

A szervezeteknek a következő szoftverekre vonatkozó követelményeket is meg kell felelniük.

- Az eszközöknek a Windows 10 2004-es vagy újabb verzióját kell futtatniuk.
- A [Azure ad Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)1.4.32.0 vagy újabb verziójának kell lennie.
  - További információ az elérhető Azure AD hibrid hitelesítési lehetőségekről: [válassza ki a megfelelő hitelesítési módszert a Azure Active Directory Hybrid Identity megoldáshoz](../hybrid/choose-ad-authn.md) , és [válassza ki, hogy melyik telepítési típust szeretné használni a Azure ad Connecthoz](../hybrid/how-to-connect-install-select-installation.md).
- A Windows Server-tartományvezérlőkhöz a következő javításokat kell telepíteni:
    - Windows Server 2016 – https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Windows Server 2019 – https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Támogatott esetek

A forgatókönyv az egyszeri bejelentkezést (SSO) is támogatja a következő esetekben:

- Olyan felhőalapú erőforrásokhoz, mint a Microsoft 365 és más SAML-kompatibilis alkalmazások.
- Helyszíni erőforrásokhoz és a webhelyekre való Windows-Integrated hitelesítéshez. Az erőforrások tartalmazhatnak olyan webhelyeket és SharePoint-webhelyeket, amelyekhez IIS-hitelesítés szükséges, illetve az NTLM-hitelesítést használó erőforrások is.

### <a name="unsupported-scenarios"></a>Nem támogatott forgatókönyvek

A következő forgatókönyvek nem támogatottak:

- Windows Server Active Directory Domain Services (AD DS) tartományhoz csatlakoztatott (csak helyszíni eszközök) központi telepítés.
- RDP-, VDI-és Citrix-forgatókönyvek biztonsági kulccsal.
- S/MIME biztonsági kulccsal.
- "Run as" (Futtatás másként) biztonsági kulcs használatával.
- Jelentkezzen be egy kiszolgálóra a biztonsági kulcs használatával.

## <a name="create-kerberos-server-object"></a>Kerberos-kiszolgáló objektum létrehozása

A rendszergazdák a Azure AD Connect kiszolgáló PowerShell-eszközeivel hozhatnak létre egy Azure AD Kerberos-kiszolgálói objektumot a helyszíni címtárban. Futtassa az alábbi lépéseket a szervezet minden olyan tartományában és erdőben, amely Azure AD-felhasználókat tartalmaz:

1. Frissítsen a Azure AD Connect legújabb verziójára. Az utasítások feltételezik, hogy már konfigurálta Azure AD Connect a hibrid környezet támogatásához.
1. Nyisson meg egy rendszergazda jogú PowerShell-parancssort a Azure AD Connect-kiszolgálón, és nyissa meg a következőt `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. A következő PowerShell-parancsok futtatásával hozzon létre egy új Azure AD Kerberos-kiszolgálói objektumot a helyszíni Active Directory tartományban és Azure Active Directory bérlőben.

> [!NOTE]
> Cserélje le a `contoso.corp.com` következő példát a helyszíni Active Directory tartománynevére.

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Az Azure AD Kerberos-kiszolgáló megtekintése és ellenőrzése

Az újonnan létrehozott Azure AD Kerberos-kiszolgálót az alábbi parancs használatával tekintheti meg és ellenőrizheti:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Ez a parancs kiírja az Azure AD Kerberos-kiszolgáló tulajdonságait. A tulajdonságok áttekintésével ellenőrizheti, hogy minden jó sorrendben van-e.

| Tulajdonság | Leírás |
| --- | --- |
| ID (Azonosító) | Az AD DS tartományvezérlő objektum egyedi azonosítója. Ezt az azonosítót más néven "slot"-nak vagy "ág-AZONOSÍTÓnak" is nevezzük. |
| DomainDnsName | A Active Directory-tartomány DNS-tartományneve. |
| ComputerAccount | Az Azure AD Kerberos-kiszolgáló objektum számítógépfiók-objektuma (a tartományvezérlő). |
| Felhasználóifiók | Az Azure AD Kerberos-kiszolgáló TGT titkosítási kulcsát birtokló letiltott felhasználói fiók objektum. Ennek a fióknak a megkülönböztető neve `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| Verziószám | Az Azure AD Kerberos-kiszolgáló TGT titkosítási kulcsának verziószáma. A verzió a kulcs létrehozásakor lesz hozzárendelve. Ekkor a rendszer a kulcs elforgatásakor minden alkalommal megnöveli a verziót. A növekmények a replikálási metaadatokon alapulnak, és valószínűleg nagyobbak. A kezdeti *verzió* például *192272* lehet. A kulcs első elforgatásakor a verzió a *212621*-as értékre léphet. A legfontosabb, hogy ellenőrizze, hogy a helyszíni objektum és a *CloudKeyVersion* a Felhőbeli objektumhoz tartozó *verziószáma* azonos-e. |
| KeyUpdatedOn | Az Azure AD Kerberos-kiszolgáló TGT titkosítási kulcsának dátuma és időpontja. |
| KeyUpdatedFrom | Az a tartományvezérlő, ahol az Azure AD Kerberos-kiszolgáló TGT-titkosítási kulcsa utoljára frissült. |
| CloudId | Az Azure AD-objektumból származó azonosító. Meg kell egyeznie a fenti AZONOSÍTÓval. |
| CloudDomainDnsName | Az Azure AD-objektum *DomainDnsName* . Meg kell egyeznie a fenti *DomainDnsName* . |
| CloudKeyVersion | Az Azure AD-objektum *verziószáma* . A fenti *verziónak* meg kell egyeznie. |
| CloudKeyUpdatedOn | Az Azure AD-objektum *KeyUpdatedOn* . Meg kell egyeznie a fenti *KeyUpdatedOn* . |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Az Azure AD Kerberos-kiszolgáló kulcsának elforgatása

Az Azure AD Kerberos-kiszolgáló titkosítási krbtgt kulcsait rendszeresen el kell forgatni. Azt javasoljuk, hogy kövesse ugyanazt az ütemtervet, amelyet az összes többi Active Directory-tartomány vezérlő krbtgt kulcsának elforgatásához használ.

> [!WARNING]
> Vannak olyan eszközök, amelyek elforgatják a krbtgt kulcsokat, azonban a dokumentumban említett eszközöket kell használniuk az Azure AD Kerberos-kiszolgáló krbtgt kulcsainak elforgatásához. Ez biztosítja, hogy a kulcsok naprakészek legyenek a helyszíni AD-ben és az Azure AD-ben is.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Az Azure AD Kerberos-kiszolgáló eltávolítása

Ha szeretné visszaállítani a forgatókönyvet, és távolítsa el az Azure AD Kerberos-kiszolgálót mind a helyszíni Active Directory, mind Azure Active Directory, futtassa a következő parancsot:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Több erdőből és több tartományból álló forgatókönyvek

Az Azure AD Kerberos-kiszolgáló objektum az Azure AD-ben *KerberosDomain* objektumként jelenik meg. Az egyes helyszíni Active Directory tartományok egyetlen *KerberosDomain* -objektumként jelennek meg az Azure ad-ben.

Például a szervezet rendelkezik egy Active Directory erdővel, és két tartománnyal rendelkezik `contoso.com` `fabrikam.com` . Ha úgy dönt, hogy engedélyezi az Azure AD számára a teljes erdő Kerberos-TGT, két *KerberosDomain* objektum van az Azure ad-ben. Egy *KerberosDomain* -objektum `contoso.com` , és egyet a következőhöz: `fabrikam.com` . Ha több Active Directory erdővel rendelkezik, az egyes erdők mindegyik tartományához egy *KerberosDomain* objektum tartozik.

Futtatnia kell a [Kerberos-kiszolgáló objektum létrehozásához](#create-kerberos-server-object) szükséges lépéseket a szervezet minden olyan tartományában és erdőben, amely tartalmazza az Azure ad-felhasználókat.

## <a name="known-behavior"></a>Ismert viselkedés

Ha a jelszó lejárt, a-vel való bejelentkezés le van tiltva. A várt érték a felhasználó számára a jelszó alaphelyzetbe állítása, mielőtt be tudná jelentkezni a következő használatával:.

## <a name="troubleshooting-and-feedback"></a>Hibaelhárítás és visszajelzés

Ha meg szeretné osztani a visszajelzéseket, vagy problémákba ütközik ezzel a szolgáltatással, ossza meg a Windows visszajelzési központ alkalmazáson keresztül a következő lépésekkel:

1. Indítsa el a **visszajelzési** központot, és ellenőrizze, hogy be van-e jelentkezve.
1. Küldjön visszajelzést a következő kategorizálás alá:
   - Kategória: biztonság és adatvédelem
   - Alkategória:
1. A naplók rögzítéséhez használja a lehetőséget a **probléma újbóli létrehozásához**.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="does-this-work-in-my-on-premises-environment"></a>Működik ez a helyszíni környezetben?

Ez a funkció nem működik tiszta helyszíni Active Directory Domain Services (AD DS) környezetben.

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>A szervezetem két faktoros hitelesítést igényel az erőforrásokhoz való hozzáféréshez. Mit tehetek a követelmény támogatásához?

A biztonsági kulcsok különféle formában jelennek meg. Vegye fel a kapcsolatot az eszköz gyártójával, és beszéljen arról, hogy az eszközük hogyan engedélyezhető PIN-kód vagy biometrikus azonosító használatával második tényezőként.

### <a name="can-admins-set-up-security-keys"></a>A rendszergazdák beállítják a biztonsági kulcsokat?

Ezen funkció általánosan elérhetővé vált ezen a lehetőségen.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Hol találhatom meg a megfelelő biztonsági kulcsokat?

[FIDO2 biztonsági kulcsok](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Mi a teendő, ha elveszítem a biztonsági kulcsot?

A kulcsokat a Azure Portalból távolíthatja el, ha a **biztonsági adatok** lapra navigál, és eltávolítja a biztonsági kulcsot.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Nem tudom azonnal használni a következőt a hibrid Azure AD-hez csatlakoztatott gép létrehozása után

Ha a hibrid Azure AD-hez csatlakoztatott gép tiszta telepítését végzi, a tartományhoz való csatlakozás és az újraindítási folyamat után be kell jelentkeznie egy jelszóval, és meg kell várnia a házirend szinkronizálását, mielőtt használni tudná a bejelentkezést.

- Az aktuális állapot ellenőrzéséhez írja `dsregcmd /status` be a parancsot egy parancssorablakba, és győződjön meg arról, hogy mind a *AzureAdJoined* , mind a *DomainJoined* *Igen értéket* jelenít meg.
- Ez a késés a tartományhoz csatlakoztatott eszközök ismert korlátozása, és nem a rendszer-specifikus.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Nem tudom beolvasni az SSO-t az NTLM hálózati erőforráshoz a parancssori felülettel való bejelentkezés után, és hitelesítő adatok kérése

Győződjön meg arról, hogy elegendő tartományvezérlő van, hogy az erőforrás-kérelem kiszolgálásához időben válaszoljon. Annak ellenőrzéséhez, hogy látható-e a szolgáltatást futtató tartományvezérlő, tekintse át a kimenetét `nltest /dsgetdc:contoso /keylist /kdc` .

## <a name="next-steps"></a>Következő lépések

[További információ a jelszóval nem rendelkező](concept-authentication-passwordless.md)
