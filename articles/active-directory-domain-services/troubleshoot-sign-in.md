---
title: A Azure AD Domain Services bejelentkezési problémáinak elhárítása | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a Azure Active Directory Domain Services gyakori felhasználói bejelentkezési problémáit és hibáit.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 85b261b8754f35c7705690d15671144b858c0a43
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96618569"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services felügyelt tartományhoz tartozó bejelentkezési problémák elhárítása

Egy olyan felhasználói fiók leggyakoribb oka, amely nem tud bejelentkezni egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartományba, például a következő helyzetekben:

* [A fiók még nincs szinkronizálva az Azure AD DSba.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Az Azure AD DS nem rendelkezik jelszó-kivonatokkal, hogy a fiók bejelentkezzen.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [A fiók ki van zárva.](#the-account-is-locked-out)

> [!TIP]
> Az Azure AD DS nem tud szinkronizálni az Azure AD-bérlőn kívüli fiókok hitelesítő adataival. A külső felhasználók nem jelentkezhetnek be az Azure AD DS felügyelt tartományba.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>A fiók még nincs szinkronizálva az Azure AD DS

A címtár méretétől függően eltarthat egy ideig, amíg a felhasználói fiókok és a hitelesítő adatok kivonatai elérhetők lesznek a felügyelt tartományokban. Nagyméretű könyvtárak esetén ez az Azure AD-ből származó első egyirányú szinkronizálás néhány órát és akár két napot is igénybe vehet. Győződjön meg arról, hogy elég sokáig vár a hitelesítés újrapróbálkozása előtt.

Olyan hibrid környezetek esetén, amelyeket a felhasználó Azure AD Connect a helyszíni címtáradatok Azure AD-ba való szinkronizálására, győződjön meg arról, hogy a Azure AD Connect legújabb verzióját futtatja, és az [azure AD DS engedélyezése után teljes szinkronizálást konfigurált Azure ad Connect][azure-ad-connect-phs]. Ha letiltja az Azure AD DS, majd újra engedélyezi, kövesse az alábbi lépéseket.

Ha továbbra is problémákba ütközik a Azure AD Connect használatával nem szinkronizált fiókok esetében, indítsa újra a Azure AD-szinkronizáló szolgáltatást. Nyisson meg egy parancssori ablakot a Azure AD Connect telepített számítógépen, majd futtassa a következő parancsokat:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Az Azure AD DS nem rendelkezik jelszó-kivonatokkal

Az Azure AD az NTLM-vagy Kerberos-hitelesítéshez szükséges formátumban nem állítja elő vagy tárolja a jelszó-kivonatokat, amíg nem engedélyezi az Azure-AD DS a bérlő számára. Biztonsági okokból az Azure AD nem tárolja a jelszó hitelesítő adatait a tiszta szöveges formában. Ezért az Azure AD nem tudja automatikusan előállítani ezeket az NTLM-vagy Kerberos-jelszó-kivonatokat a felhasználók meglévő hitelesítő adatai alapján.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Hibrid környezetek helyszíni szinkronizálással

A helyszíni AD DS-környezetből Azure AD Connectt használó hibrid környezetek esetén helyileg létrehozhatja és szinkronizálhatja a szükséges NTLM-vagy Kerberos-jelszó kivonatait az Azure AD-ben. A felügyelt tartomány létrehozása után [engedélyezze a jelszó-kivonatok szinkronizálását Azure Active Directory Domain Services][azure-ad-connect-phs]. A jelszó-kivonatolási szinkronizálás lépésének befejezése nélkül nem tud bejelentkezni a fiókba a felügyelt tartomány használatával. Ha letiltja az Azure AD DS, majd újra engedélyezi, ezeket a lépéseket újra végre kell hajtania.

További információkért lásd: [a jelszó-kivonatolási szinkronizálás működése az Azure ad DSban][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Csak felhőalapú környezetek helyszíni szinkronizálás nélkül

A helyszíni szinkronizálás nélküli felügyelt tartományoknak, csak az Azure AD-fiókoknak kell létrehozniuk a szükséges NTLM-vagy Kerberos-jelszó kivonatokat. Ha a csak felhőalapú fiók nem tud bejelentkezni, a fiók sikeresen befejeződött a jelszó módosítási folyamata az Azure AD DS engedélyezése után?

* **Nem, a jelszó nem módosult.**
    * [Módosítsa a fiók jelszavát][enable-user-accounts] a szükséges jelszó-kivonatok létrehozásához, majd várjon 15 percet, mielőtt újra bejelentkezik.
    * Ha letiltja az Azure AD DS, majd újra engedélyezi, minden fióknak újra kell követnie a lépéseket a jelszavuk módosításához és a szükséges jelszó-kivonatok létrehozásához.
* **Igen, a jelszó megváltozott.**
    * Próbáljon meg az *egyszerű felhasználónév (UPN* ) formátum használatával bejelentkezni, például `driley@aaddscontoso.com` a *sAMAccountName* formátum helyett `AADDSCONTOSO\deeriley` .
    * Előfordulhat, hogy a *sAMAccountName* automatikusan jön létre azon felhasználók számára, akiknek UPN-előtagja túl hosszú, vagy a felügyelt tartomány egy másik felhasználója. Az *UPN* formátuma garantáltan egyedi az Azure ad-bérlőn belül.

## <a name="the-account-is-locked-out"></a>A fiók ki van zárva

Egy felügyelt tartomány felhasználói fiókja zárolva van, ha a sikertelen bejelentkezési kísérletek meghatározott küszöbértéke teljesül. Ez a fiókzárolási viselkedés úgy lett kialakítva, hogy megvédje a nem kötelezően kikényszerített, automatikus digitális támadásra utaló kísérleteket.

Alapértelmezés szerint, ha 2 percen belül 5 hibás jelszóval próbálkozik, a fiók 30 percig ki lesz zárva.

További információ és a fiókzárolási problémák megoldása: fiókzárolási [problémák elhárítása az Azure ad DSban][troubleshoot-account-lockout].

## <a name="next-steps"></a>Következő lépések

Ha továbbra sem sikerül a virtuális gép csatlakoztatása a felügyelt tartományhoz, [keressen segítséget a Azure Active Directory támogatási jegyének megnyitásához][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: ./tutorial-configure-password-hash-sync.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md