---
title: A hibrid FIDO2 biztonsági kulcs központi telepítésére vonatkozó gyakori kérdések – Azure Active Directory
description: Ismerkedjen meg a jelszó nélküli hibrid FIDO2 biztonsági kulcsok bejelentkezésével kapcsolatos gyakori kérdésekre Azure Active Directory
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
ms.openlocfilehash: ca4943293f9474d4089267d05460d6d8766b79e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646384"
---
# <a name="deployment-frequently-asked-questions-faqs-for-hybrid-fido2-security-keys-in-azure-ad"></a>Gyakori kérdések (GYIK) a hibrid FIDO2 biztonsági kulcsaihoz az Azure AD-ben 

Ez a cikk az üzembe helyezéssel kapcsolatos gyakori kérdéseket ismerteti (GYIK) a hibrid Azure AD-hez csatlakoztatott eszközökhöz és a jelszó nélküli bejelentkezéshez a helyszíni erőforrásokhoz. Ezzel a jelszóval nem rendelkező funkcióval engedélyezheti az Azure AD-hitelesítést a Windows 10-es eszközökön a hibrid Azure AD-hez csatlakoztatott eszközökhöz a FIDO2 biztonsági kulcsainak használatával. A felhasználók modern hitelesítő adatokkal (például FIDO2 kulcsokkal) jelentkezhetnek be a Windowsba, és a hagyományos Active Directory Domain Services (AD DS) alapuló erőforrásokhoz hozzáférhetnek a helyszíni erőforrásaik zökkenőmentes egyszeri bejelentkezési (SSO) élményével.

A hibrid környezetekben a következő forgatókönyvek támogatottak:

* Jelentkezzen be a hibrid Azure AD-hez csatlakoztatott eszközökre FIDO2 biztonsági kulcsokkal, és egyszeri bejelentkezéses hozzáférést kap a helyszíni erőforrásokhoz.
* Jelentkezzen be az Azure AD-hez csatlakoztatott eszközökre FIDO2 biztonsági kulcsokkal, és egyszeri bejelentkezéses hozzáférést kap a helyszíni erőforrásokhoz.

A FIDO2 biztonsági kulcsainak és a helyszíni erőforrások hibrid elérésének megkezdéséhez tekintse meg a következő cikkeket:

* [Jelszó nélküli FIDO2 biztonsági kulcsok](howto-authentication-passwordless-security-key.md)
* [Jelszó nélküli Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Jelszó nélküli helyszíni](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="security-keys"></a>Biztonsági kulcsok

* [A szervezetem két faktoros hitelesítést igényel az erőforrásokhoz való hozzáféréshez. Mit tehetek a követelmény támogatásához?](#my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement)
* [Hol találhatók a megfelelő FIDO2 biztonsági kulcsok?](#where-can-i-find-compliant-fido2-security-keys)
* [Mi a teendő, ha elveszítem a biztonsági kulcsot?](#what-if-i-lose-my-security-key)
* [Hogyan történik az adatvédelem a FIDO2 biztonsági kulcson?](#how-is-the-data-protected-on-the-fido2-security-key)
* [Hogyan működik a FIDO2 biztonsági kulcsainak regisztrálása?](#how-does-the-registering-of-fido2-security-keys-work)
* [Van mód a rendszergazdáknak a kulcsok közvetlen kiépítésére a felhasználók számára?](#is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly)

### <a name="my-organization-requires-multi-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>A szervezetem többtényezős hitelesítést igényel az erőforrásokhoz való hozzáféréshez. Mit tehetek a követelmény támogatásához?

A FIDO2 biztonsági kulcsainak számos különböző formája van. Vegye fel a kapcsolatot az eszköz gyártójával, és beszéljen arról, hogy az eszközük hogyan engedélyezhető PIN-kód vagy biometrikus azonosító használatával második tényezőként. A támogatott szolgáltatók listáját lásd: [FIDO2 biztonsági kulcsok szolgáltatói](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="where-can-i-find-compliant-fido2-security-keys"></a>Hol találhatók a megfelelő FIDO2 biztonsági kulcsok?

A támogatott szolgáltatók listáját lásd: [FIDO2 biztonsági kulcsok szolgáltatói](concept-authentication-passwordless.md#fido2-security-key-providers).

### <a name="what-if-i-lose-my-security-key"></a>Mi a teendő, ha elveszítem a biztonsági kulcsot?

A Azure Portal kulcsok eltávolításához lépjen a **biztonsági adatok** lapra, és távolítsa el a FIDO2 biztonsági kulcsot.

### <a name="how-is-the-data-protected-on-the-fido2-security-key"></a>Hogyan történik az adatvédelem a FIDO2 biztonsági kulcson?

A FIDO2 biztonsági kulcsai rendelkeznek olyan biztonságos enklávékkal, amelyek védik a rajtuk tárolt titkos kulcsokat. A FIDO2 biztonsági kulcs is tartalmaz egy beépített anti-hammering tulajdonságot, például a Windows Hello-ben, ahol nem lehet kibontani a titkos kulcsot.

### <a name="how-does-the-registering-of-fido2-security-keys-work"></a>Hogyan működik a FIDO2 biztonsági kulcsainak regisztrálása?

A FIDO2 biztonsági kulcsainak regisztrálásával és használatával kapcsolatos további információkért lásd: [jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése](howto-authentication-passwordless-security-key.md).

### <a name="is-there-a-way-for-admins-to-provision-the-keys-for-the-users-directly"></a>Van mód a rendszergazdáknak a kulcsok közvetlen kiépítésére a felhasználók számára?

Nem, jelenleg nem.

## <a name="prerequisites"></a>Előfeltételek

* [Működik ez a funkció, ha nincs internetkapcsolat?](#does-this-feature-work-if-theres-no-internet-connectivity)
* [Mik azok a konkrét végpontok, amelyek az Azure AD-ben való megnyitáshoz szükségesek?](#what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad)
* [Hogyan azonosítani a tartományhoz való csatlakozás típusát (Azure AD-hez csatlakoztatott vagy hibrid Azure AD-hez csatlakoztatott) a Windows 10-es eszközhöz?](#how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device)
* [Mi a javaslat a kijavítani kívánt tartományvezérlők számával kapcsolatban?](#whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched)
* [Telepíthetem a FIDO2 hitelesítőadat-szolgáltatót csak helyszíni eszközön?](#can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device)
* [A FIDO2 biztonsági kulcsának aláírása nem működik a saját tartományi rendszergazda vagy más magas jogosultsági szintű fiókok esetében. miért?](#fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why)

### <a name="does-this-feature-work-if-theres-no-internet-connectivity"></a>Működik ez a funkció, ha nincs internetkapcsolat?

Az internetkapcsolat a funkció engedélyezésének előfeltétele. Amikor a felhasználó először jelentkezik be a FIDO2 biztonsági kulcsainak használatával, internetkapcsolattal kell rendelkeznie. A későbbi bejelentkezési eseményekhez a gyorsítótárazott bejelentkezésnek működnie kell, és lehetővé kell tennie a felhasználó számára az internetkapcsolat nélküli hitelesítést.

Az egységes felhasználói élmény érdekében gondoskodjon arról, hogy az eszközök rendelkezzenek internet-hozzáféréssel, és legyenek a tartományvezérlők.

### <a name="what-are-the-specific-end-points-that-are-required-to-be-open-to-azure-ad"></a>Mik azok a konkrét végpontok, amelyek az Azure AD-ben való megnyitáshoz szükségesek?

A regisztrációhoz és a hitelesítéshez a következő végpontok szükségesek:

* **. microsoftonline.com*
* **. microsoftonline-p.com*
* **. msauth.net*
* **. msauthimages.net*
* **. msecnd.net*
* **. msftauth.net*
* **. msftauthimages.net*
* **. phonefactor.net*
* *enterpriseregistration.windows.net*
* *management.azure.com*
* *policykeyservice.dc.ad.msft.net*
* *secure.aadcdn.microsoftonline-p.com*

A Microsoft online termékek használatához szükséges végpontok teljes listáját lásd: [Office 365 URL-címek és IP-címtartományok](/microsoft-365/enterprise/urls-and-ip-address-ranges).

### <a name="how-do-i-identify-the-domain-join-type-azure-ad-joined-or-hybrid-azure-ad-joined-for-my-windows-10-device"></a>Hogyan azonosítani a tartományhoz való csatlakozás típusát (Azure AD-hez csatlakoztatott vagy hibrid Azure AD-hez csatlakoztatott) a Windows 10-es eszközhöz?

A következő parancs futtatásával ellenőrizze, hogy a Windows 10-ügyfél eszközének van-e a megfelelő tartományhoz való csatlakoztatási típusa:

```console
Dsregcmd/status
```

A következő minta kimenet azt mutatja, hogy az eszköz az Azure AD-hez csatlakozik, mivel a *AzureADJoined* *Igen* értékre van állítva:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: NO
```

A következő minta kimenet azt mutatja be, hogy az eszköz hibrid Azure AD-hez csatlakozik, mivel a *DomainedJoined* is *Igen* értékre van állítva. A *tartománynév* is megjelenik:

```output
+---------------------+
| Device State        |
+---------------------+

AzureADJoined: YES
EnterpriseJoined: NO
DomainedJoined: YES
DomainName: CONTOSO
```

Windows Server 2016 vagy 2019 tartományvezérlőn győződjön meg arról, hogy a következő javítások vannak alkalmazva. Ha szükséges, futtassa Windows Update a telepítéséhez:

* Windows Server 2016 – [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 – [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

Az ügyfélgépről futtassa a következő parancsot a megfelelő tartományvezérlőhöz való csatlakozás ellenőrzéséhez a telepített javításokkal:

```console
nltest /dsgetdc:<domain> /keylist /kdc
```

### <a name="whats-the-recommendation-on-the-number-of-dcs-that-should-be-patched"></a>Mi a javaslat a kijavítani kívánt tartományvezérlők számával kapcsolatban?

Javasoljuk, hogy a Windows Server 2016-es vagy 2019-es tartományvezérlők többségét a javítást követve ellenőrizze, hogy képes-e kezelni a szervezet hitelesítési kérelmének terhelését.

Windows Server 2016 vagy 2019 tartományvezérlőn győződjön meg arról, hogy a következő javítások vannak alkalmazva. Ha szükséges, futtassa Windows Update a telepítéséhez:

* Windows Server 2016 – [KB4534307](https://support.microsoft.com/help/4534307/windows-10-update-kb4534307)
* Windows Server 2019 – [KB4534321](https://support.microsoft.com/help/4534321/windows-10-update-kb4534321)

### <a name="can-i-deploy-the-fido2-credential-provider-on-an-on-premises-only-device"></a>Telepíthetem a FIDO2 hitelesítőadat-szolgáltatót csak helyszíni eszközön?

Nem, ez a funkció nem támogatott a csak helyszíni eszközökön. A FIDO2 hitelesítőadat-szolgáltató nem jelenik meg.

### <a name="fido2-security-key-sign-in-isnt-working-for-my-domain-admin-or-other-high-privilege-accounts-why"></a>A FIDO2 biztonsági kulcsának aláírása nem működik a saját tartományi rendszergazda vagy más magas jogosultsági szintű fiókok esetében. Miért?

Az alapértelmezett biztonsági házirend nem biztosít Azure AD-engedélyt arra, hogy magas jogosultsági szintű fiókokat jelentkezzen be a helyszíni erőforrásokra.

A fiókok tiltásának feloldásához használja **Active Directory felhasználók és számítógépek beépülő** modult az Azure ad Kerberos számítógép *-objektum msDS-NeverRevealGroup* tulajdonságának módosításához *(CN = AzureADKerberos, OU = Domain Controllers, \<domain-DN> )*.

## <a name="under-the-hood"></a>technikai részletek

* [Hogyan kapcsolódik az Azure AD Kerberos a helyszíni Active Directory Domain Services környezethez?](#how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment)
* [Hol tekinthetem meg az AD-ben létrehozott és az Azure AD-ben közzétett Kerberos-kiszolgáló objektumokat?](#where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad)
* [Miért nem érhető el a helyszíni AD DS regisztrált nyilvános kulcs, így nincs függőség az interneten?](#why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet)
* [Hogyan történik a kulcsok elforgatása a Kerberos-kiszolgáló objektumon?](#how-are-the-keys-rotated-on-the-kerberos-server-object)
* [Miért van szükségünk Azure AD Connectre? A AD DS az Azure AD-ből ír vissza az adatokat?](#why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad)
* [Hogyan néz ki a HTTP-kérelem/-válasz a PRT + részleges TGT kérelmezése során?](#what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt)

### <a name="how-is-azure-ad-kerberos-linked-to-my-on-premises-active-directory-domain-services-environment"></a>Hogyan kapcsolódik az Azure AD Kerberos a helyszíni Active Directory Domain Services környezethez?

Két részből áll: a helyszíni AD DS környezetből és az Azure AD-bérlőből.

**Active Directory Domain Services (AD DS)**

Az Azure AD Kerberos-kiszolgáló egy helyszíni AD DS-környezetben, tartományvezérlő (DC) objektumként van ábrázolva. Ez a DC-objektum több objektumból tevődik fel:

* *CN = AzureADKerberos, OU = tartományvezérlők,\<domain-DN>*
    
    Egy *számítógép* -objektum, amely a AD DS Read-Only tartományvezérlőjét jelöli. Ehhez az objektumhoz nem tartozik számítógép társítva. Ehelyett egy tartományvezérlő logikai ábrázolása.

* *CN = krbtgt_AzureAD, CN = felhasználók,\<domain-DN>*

    Egy *felhasználói* objektum, amely az írásvédett tartományvezérlő Kerberos-jegyét megadó TGT-titkosítási kulcsot jelöli.

* *CN = 900274c4-b7d2-43c8-90ee-00a9f650e335, CN = AzureAD, CN = System,\<domain-DN>*

    Egy *ServiceConnectionPoint* objektum, amely az Azure ad Kerberos-kiszolgáló objektumainak metaadatait tárolja. A felügyeleti eszközök ezt az objektumot használják az Azure AD Kerberos-kiszolgáló objektumainak azonosítására és keresésére.

**Azure Active Directory**

Az Azure AD Kerberos-kiszolgáló az Azure AD-ben *KerberosDomain* -objektumként jelenik meg. Az egyes helyszíni AD DS környezetek egyetlen *KerberosDomain* -objektumként jelennek meg az Azure ad-bérlőben.

Előfordulhat például, hogy rendelkezik egy AD DS erdőben két tartománnyal, például a *contoso.com* és a *fabrikam.com*. Ha engedélyezi az Azure AD számára, hogy a teljes erdő számára biztosítson Kerberos-jegyeket (TGT), két *KerberosDomain* objektum létezik az Azure ad-ben – egy objektum a *contoso.com* -hez, egy pedig a *fabrikam.com*.

Ha több AD DS erdővel rendelkezik, az egyes erdők mindegyik tartományához egy *KerberosDomain* objektum tartozik.

### <a name="where-can-i-view-these-kerberos-server-objects-that-are-created-in-ad-ds-and-published-in-azure-ad"></a>Hol tekinthetem meg a AD DSban létrehozott és az Azure AD-ben közzétett Kerberos-kiszolgálói objektumokat?

Az összes objektum megtekintéséhez használja a Azure AD Connect legújabb verziójában található Azure AD Kerberos-kiszolgáló PowerShell-parancsmagokat.

További információk, például az objektumok megtekintésével kapcsolatos utasítások: [Kerberos-kiszolgáló objektumok létrehozása](howto-authentication-passwordless-security-key-on-premises.md#create-kerberos-server-object).

### <a name="why-cant-we-have-the-public-key-registered-to-on-premises-ad-ds-so-there-is-no-dependency-on-the-internet"></a>Miért nem érhető el a helyszíni AD DS regisztrált nyilvános kulcs, így nincs függőség az interneten?

Visszajelzéseket kaptunk a vállalati Windows Hello üzembe helyezési modelljének összetettsége miatt, ezért az üzembe helyezési modellt a tanúsítványok és a PKI használata nélkül kellett volna leegyszerűsíteni (a FIDO2 nem használ tanúsítványokat).

### <a name="how-are-the-keys-rotated-on-the-kerberos-server-object"></a>Hogyan történik a kulcsok elforgatása a Kerberos-kiszolgáló objektumon?

A többi TARTOMÁNYVEZÉRLŐhöz hasonlóan az Azure AD Kerberos-kiszolgáló titkosítási *krbtgt* kulcsait rendszeresen el kell forgatni. Azt javasoljuk, hogy az összes többi AD DS *krbtgt* -kulcs elforgatásához használja ugyanezt az ütemtervet.

> [!NOTE]
> Bár más eszközök is elforgatják a *krbtgt* kulcsokat, [a PowerShell-parancsmagok használatával el kell FORGATNI az Azure ad Kerberos-kiszolgáló *krbtgt* kulcsait](howto-authentication-passwordless-security-key-on-premises.md#rotating-the-azure-ad-kerberos-server-key) . Ez a módszer biztosítja, hogy a kulcsok frissítve legyenek a helyszíni AD DS környezetben és az Azure AD-ben is.

### <a name="why-do-we-need-azure-ad-connect-does-it-write-any-info-back-to-ad-ds-from-azure-ad"></a>Miért van szükségünk Azure AD Connectre? A AD DS az Azure AD-ből ír vissza az adatokat?

Azure AD Connect nem ír vissza adatokat az Azure AD-ből AD DSba. A segédprogram tartalmazza a PowerShell-modult, amely létrehozza a Kerberos-kiszolgáló objektumot AD DS és közzéteszi az Azure AD-ben.

### <a name="what-does-the-http-requestresponse-look-like-when-requesting-prt-partial-tgt"></a>Hogyan néz ki a HTTP-kérelem/-válasz a PRT + részleges TGT kérelmezése során?

A HTTP-kérelem egy szabványos elsődleges frissítési jogkivonat-(PRT-) kérelem. Ez a PRT-kérelem tartalmaz egy, a Kerberos-jegy kiadására vonatkozó jegyet (TGT) jelző jogcímet.

| Jogcím | Érték | Leírás                             |
|-------|-------|-----------------------------------------|
| TGT   | true  | A jogcím azt jelzi, hogy az ügyfélnek szüksége van egy TGT. |

Az Azure AD a titkosított ügyfél kulcsát és az üzenet pufferét a PRT-válaszban további tulajdonságokként ötvözi. Az adattartalom titkosítása az Azure AD-eszköz munkamenet-kulcsa alapján történik.

| Mező              | Típus   | Leírás  |
|--------------------|--------|--------------|
| tgt_client_key     | sztring | Base64 kódolású ügyfél kulcsa (titkos kulcs). Ez a kulcs a TGT elleni védelemhez használt ügyfél titka. Ebben a jelszó nélküli forgatókönyvben az ügyfél titkos kulcsát a kiszolgáló hozza létre az egyes TGT-kérések részeként, majd visszaküldi az ügyfélnek a válaszban. |
| tgt_key_type       | int    | A helyszíni AD DS a KERB_MESSAGE_BUFFERban található ügyfél-kulcshoz és Kerberos-munkamenethez használt kulcs típusa. |
| tgt_message_buffer | sztring | Base64 kódolású KERB_MESSAGE_BUFFER. |

## <a name="next-steps"></a>Következő lépések

A FIDO2 biztonsági kulcsainak és a helyszíni erőforrások hibrid elérésének megkezdéséhez tekintse meg a következő cikkeket:

* [Jelszó nélküli FIDO2 biztonsági kulcsok](howto-authentication-passwordless-security-key.md)
* [Jelszó nélküli Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [Jelszó nélküli helyszíni](howto-authentication-passwordless-security-key-on-premises.md)
