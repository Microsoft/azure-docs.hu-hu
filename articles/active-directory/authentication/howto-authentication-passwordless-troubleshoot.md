---
title: Ismert problémák és hibaelhárítás a hibrid FIDO2 biztonsági kulcsaihoz – Azure Active Directory
description: Ismerkedjen meg néhány ismert problémával, valamint a jelszó nélküli hibrid FIDO2 biztonsági kulcsok a Azure Active Directory használatával történő hibaelhárításának módszereivel.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 435b27255ce58a3541d6b0d3a76bdf4080aa3962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648780"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad"></a>Hibaelhárítás a FIDO2 biztonsági kulcsainak hibrid üzembe helyezéséhez az Azure AD-ben 

Ez a cikk a hibrid Azure AD-hez csatlakoztatott eszközökre és a helyszíni erőforrásokhoz való jelszó nélküli bejelentkezésre vonatkozó gyakori kérdéseket ismerteti. Ezzel a jelszóval nem rendelkező funkcióval engedélyezheti az Azure AD-hitelesítést a Windows 10-es eszközökön a hibrid Azure AD-hez csatlakoztatott eszközökhöz a FIDO2 biztonsági kulcsainak használatával. A felhasználók modern hitelesítő adatokkal (például FIDO2 kulcsokkal) jelentkezhetnek be a Windowsba, és a hagyományos Active Directory Domain Services (AD DS) alapuló erőforrásokhoz hozzáférhetnek a helyszíni erőforrásaik zökkenőmentes egyszeri bejelentkezési (SSO) élményével.

A hibrid környezetekben a következő forgatókönyvek támogatottak:

* Jelentkezzen be a hibrid Azure AD-hez csatlakoztatott eszközökre FIDO2 biztonsági kulcsokkal, és egyszeri bejelentkezéses hozzáférést kap a helyszíni erőforrásokhoz.
* Jelentkezzen be az Azure AD-hez csatlakoztatott eszközökre FIDO2 biztonsági kulcsokkal, és egyszeri bejelentkezéses hozzáférést kap a helyszíni erőforrásokhoz.

A FIDO2 biztonsági kulcsainak és a helyszíni erőforrások hibrid elérésének megkezdéséhez tekintse meg a következő cikkeket:

* [Jelszó nélküli biztonsági kulcsok](howto-authentication-passwordless-security-key.md)
* [Jelszó nélküli Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Jelszó nélküli helyszíni](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="known-issues"></a>Ismert problémák

* [A felhasználók nem tudnak bejelentkezni a FIDO2 biztonsági kulcsaival, mivel a Windows Hello Face túl gyors, és az alapértelmezett bejelentkezési mechanizmus](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [A felhasználók nem tudják azonnal használni a FIDO2 biztonsági kulcsait a hibrid Azure AD-hez csatlakoztatott számítógép létrehozása után](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [A felhasználók nem tudnak bejelentkezni az NTLM hálózati erőforrásba a FIDO2 biztonsági kulccsal való bejelentkezés után és a hitelesítő adatok megadásának kérése](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>A felhasználók nem tudnak bejelentkezni a FIDO2 biztonsági kulcsaival, mivel a Windows Hello Face túl gyors, és az alapértelmezett bejelentkezési mechanizmus

A Windows Hello Face a legjobb megoldás a felhasználók regisztrálására szolgáló eszköz számára. A FIDO2 biztonsági kulcsai megosztott eszközökön való használatra készültek, vagy a vállalati Windows Hello-regisztráció akadályt jelent.

Ha a Windows Hello Face megakadályozza, hogy a felhasználók kipróbálják a FIDO2 biztonsági kulcsának bejelentkezési forgatókönyvét, a felhasználók kikapcsolhatják a Hello Face bejelentkezést a **beállítások > Sign-In lehetőségre** kattintva.

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>A felhasználók nem tudják azonnal használni a FIDO2 biztonsági kulcsait a hibrid Azure AD-hez csatlakoztatott számítógép létrehozása után

A hibrid Azure AD-hez csatlakoztatott számítógép tiszta telepítésének tartományhoz való csatlakoztatása és újraindítása után be kell jelentkeznie egy jelszóval, és várnia kell, amíg a házirend szinkronizálva lett, hogy FIDO2 biztonsági kulcsot használjon a bejelentkezéshez.

Ez a viselkedés a tartományhoz csatlakoztatott eszközök ismert korlátozása, és nem a FIDO2 biztonsági kulcsaira vonatkozik.

Az aktuális állapot megtekintéséhez használja az `dsregcmd /status` parancsot. Győződjön meg arról, hogy a *AzureAdJoined* és a *DomainJoined* is *Igen értéket* mutat.

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>A felhasználók nem tudnak bejelentkezni az NTLM hálózati erőforrásba a FIDO2 biztonsági kulccsal való bejelentkezés után és a hitelesítő adatok megadásának kérése

Győződjön meg arról, hogy az erőforrás-kérelem kiszolgálásához elegendő tartományvezérlő van-e kijavítani. Annak ellenőrzéséhez, hogy látható-e a szolgáltatást futtató kiszolgáló, tekintse át a következő kimenetét: `nltest /dsgetdc:<dc name> /keylist /kdc`

Ha ezt a funkciót látja a TARTOMÁNYVEZÉRLŐvel, előfordulhat, hogy a felhasználó jelszava megváltozott a bejelentkezés óta, vagy egy másik probléma van. Gyűjtsön naplókat a Microsoft terméktámogatási csapatának hibakereséséhez a következő szakaszban leírtak szerint.

## <a name="troubleshoot"></a>Hibaelhárítás

Két területen lehet elhárítani a hibaelhárítást – a [Windows-ügyfelek problémáit](#windows-client-issues)vagy az [üzembe helyezési problémákat](#deployment-issues).

### <a name="windows-client-issues"></a>Windows-ügyfelekkel kapcsolatos problémák

Az alábbi lépéseket követve gyűjthet olyan adatokat, amelyek segítséget nyújthatnak a Windowsba való bejelentkezéssel vagy a helyszíni erőforrások Windows 10-es eszközökről való elérésével kapcsolatos hibák elhárításához:

1. Nyissa meg a **visszajelzési központ** alkalmazást. Győződjön meg arról, hogy a neve az alkalmazás bal alsó sarkában található, majd válassza az **új visszajelzési elem létrehozása** lehetőséget.

    A visszajelzési elem típusa beállításnál válassza a *probléma* elemet.

1. Válassza ki a *biztonsági és adatvédelmi* kategóriát, *majd a parancs* alkategóriáját.
1. Jelölje be a *csatolt fájlok és diagnosztika küldése a Microsoftnak a visszajelzések mellett* jelölőnégyzetet.
1. Válassza *a saját problémák* újbóli létrehozása, majd a *rögzítés elindítása* lehetőséget.
1. Zárolja és oldja fel a gépet a FIDO2 biztonsági kulccsal. Ha a probléma merül fel, próbálja meg feloldani más hitelesítő adatokkal.
1. Térjen vissza a **visszajelzési hubhoz**, válassza a **rögzítés leállítása** lehetőséget, és küldje el visszajelzését.
1. Nyissa meg a *visszajelzés* lapot, majd a *saját visszajelzés* fület. Válassza ki a legutóbb elküldött visszajelzést.
1. Kattintson a jobb felső sarokban található *megosztás* gombra a visszajelzésre mutató hivatkozás beszerzéséhez. Adja meg ezt a hivatkozást egy támogatási eset megnyitásakor, vagy válaszoljon egy meglévő támogatási esethez rendelt mérnöknek.

A rendszer a következő eseménynaplókat és beállításkulcs-adatokat gyűjti össze:

**Beállításkulcsok**

* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FIDO [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PasswordForWork\* [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Policies\PasswordForWork\* [ \* ]*

**Diagnosztikai adatok**

* Élő kernel dump
* AppX-csomag adatainak gyűjtése
* UIF környezeti fájljai

**Eseménynaplók**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>Üzembe helyezési problémák

Az Azure AD Kerberos-kiszolgáló telepítésével kapcsolatos problémák elhárításához használja az Azure AD Connecthoz tartozó új PowerShell-modult.

#### <a name="viewing-the-logs"></a>A naplók megtekintése

Az Azure AD Kerberos-kiszolgáló PowerShell-parancsmagjai ugyanazt a naplózást használják, mint a standard Azure AD Connect varázsló. Az információk vagy a hibák részleteinek a parancsmagokból való megtekintéséhez hajtsa végre a következő lépéseket:

1. A Azure AD Connect-kiszolgálón keresse meg a következőt: `C:\ProgramData\AADConnect\` . Ez a mappa alapértelmezés szerint rejtett állapotban van.
1. Nyissa meg és tekintse meg a `trace-*.log` könyvtárban található legfrissebb fájlt.

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>Az Azure AD Kerberos-kiszolgáló objektumainak megtekintése

Az Azure AD Kerberos-kiszolgáló objektumainak megtekintéséhez és a megfelelő sorrendben való ellenőrzéséhez hajtsa végre a következő lépéseket:

1. A Azure AD Connect-kiszolgálón nyissa meg a PowerShellt, és navigáljon a következőre `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. A következő PowerShell-parancsok futtatásával tekintheti meg az Azure AD Kerberos-kiszolgálót az Azure AD-től és a helyszíni AD DS.

    Cserélje le a *Corp.contoso.com* -t a helyszíni AD DS tartományának nevére.

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

A parancs kimenete az Azure AD Kerberos-kiszolgáló tulajdonságait az Azure AD-től és a helyszíni AD DS. Tekintse át a tulajdonságokat annak ellenőrzéséhez, hogy minden jó sorrendben van-e. A tulajdonságok ellenőrzéséhez használja az alábbi táblázatot.

A tulajdonságok első készlete a helyszíni AD DS környezet objektumaiból származik. A második fele (a * Cloud * * * kezdetű tulajdonságok a Kerberos-kiszolgáló objektumból származnak az Azure AD-ben:

| Tulajdonság           | Leírás  |
|--------------------|--------------|
| Id                 | A AD DS tartományvezérlő objektum egyedi *azonosítója* . |
| DomainDnsName      | A AD DS tartomány DNS-tartományneve. |
| ComputerAccount    | Az Azure AD Kerberos-kiszolgáló objektum számítógépfiók-objektuma (a tartományvezérlő). |
| Felhasználóifiók        | Az Azure AD Kerberos-kiszolgáló TGT titkosítási kulcsát birtokló letiltott felhasználói fiók objektum. Ennek a fióknak a megkülönböztető neve *CN = krbtgt_AzureAD, CN = felhasználók, <tartomány – DN>* |
| Verziószám         | Az Azure AD Kerberos-kiszolgáló TGT titkosítási kulcsának verziószáma. A verzió a kulcs létrehozásakor lesz hozzárendelve. Ekkor a rendszer a kulcs elforgatásakor minden alkalommal megnöveli a verziót. A növekmények a replikálási metaadatokon alapulnak, és valószínűleg nagyobbak lesznek, mint egy.<br /><br /> A kezdeti *verzió* például *192272* lehet. A kulcs első elforgatásakor a verzió a *212621*-as értékre léphet.<br /><br /> A legfontosabb, hogy ellenőrizze, hogy a helyszíni objektum és a *CloudKeyVersion* a Felhőbeli objektumhoz tartozó *verziószáma* azonos-e. |
| KeyUpdatedOn       | Az Azure AD Kerberos-kiszolgáló TGT titkosítási kulcsának dátuma és időpontja. |
| KeyUpdatedFrom     | Az a tartományvezérlő, ahol az Azure AD Kerberos-kiszolgáló TGT-titkosítási kulcsa utoljára frissült. |
| CloudId            | Az Azure AD-objektumból származó *azonosító* . Meg kell egyeznie a fenti *azonosítóval* . |
| CloudDomainDnsName | Az Azure AD-objektum *DomainDnsName* . Meg kell egyeznie a fenti *DomainDnsName* . |
| CloudKeyVersion    | Az Azure AD-objektum *verziószáma* . A fenti *verziónak* meg kell egyeznie. |
| CloudKeyUpdatedOn  | Az Azure AD-objektum *KeyUpdatedOn* . Meg kell egyeznie a fenti *KeyUpdatedOn* . |

## <a name="next-steps"></a>Következő lépések

A FIDO2 biztonsági kulcsainak és a helyszíni erőforrások hibrid elérésének megkezdéséhez tekintse meg a következő cikkeket:

* [Jelszó nélküli FIDO2 biztonsági kulcsok](howto-authentication-passwordless-security-key.md)
* [Jelszó nélküli Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Jelszó nélküli helyszíni](howto-authentication-passwordless-security-key-on-premises.md)
