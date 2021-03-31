---
title: A Azure AD Connect által szinkronizált attribútumok | Microsoft Docs
description: Felsorolja a Azure Active Directory szinkronizált attribútumokat.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ec05c4160c6502904644bf7035bda0bed66cc33
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94413190"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect szinkronizálási szolgáltatás: Az Azure Active Directoryval szinkronizált attribútumok
Ez a témakör a Azure AD Connect Sync által szinkronizált attribútumokat sorolja fel.  
Az attribútumok a kapcsolódó Azure AD-alkalmazás szerint vannak csoportosítva.

## <a name="attributes-to-synchronize"></a>Szinkronizálandó attribútumok
Gyakori kérdés a *szinkronizálandó attribútumok listája*. Az alapértelmezett és ajánlott módszer az alapértelmezett attribútumok megtartása, így a teljes GAL (globális címlista) a felhőben is létrehozható, és a Microsoft 365 munkaterhelések összes funkciójának beolvasása. Bizonyos esetekben vannak olyan attribútumok, amelyeket a szervezet nem szeretne szinkronizálni a felhővel, mivel ezek az attribútumok bizalmas személyes adatokat tartalmaznak, például a következő példában:  
![helytelen attribútumok](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

Ebben az esetben Kezdje a témakörben található attribútumok listájával, és azonosítsa azokat az attribútumokat, amelyek személyes adatokat tartalmaznak, és nem szinkronizálhatók. Ezután törölje ezeket az attribútumokat a telepítés során az [Azure ad-alkalmazás és az attribútumok szűrésével](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> Az attribútumok kiválasztásakor óvatosnak kell lennie, és csak azokat az attribútumokat kell kijelölnie, amelyek nem szinkronizálhatók. A többi attribútum kijelölésének visszavonása negatív hatással lehet a funkciókra.
>
>

## <a name="microsoft-365-apps-for-enterprise"></a>Vállalati alkalmazások Microsoft 365
| Attribútum neve | User | Megjegyzés |
| --- |:---:| --- |
| accountEnabled |X |Meghatározza, hogy engedélyezve van-e a fiók. |
| CN |X | |
| displayName |X | |
| objectSID |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| pwdLastSet |X |mechanikai tulajdonság. Annak ismerete, hogy mikor kell érvényteleníteni a már kiállított jogkivonatokat. A jelszó-kivonatolási szinkronizálás, az átmenő hitelesítés és az összevonás egyaránt használja. |
|samAccountName|X| |
| sourceAnchor |X |mechanikai tulajdonság. Megváltoztathatatlan azonosító a HOZZÁADÁSok és az Azure AD közötti kapcsolat fenntartásához. |
| usageLocation |X |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X |Az UPN a felhasználó bejelentkezési azonosítója. A legtöbb esetben ugyanaz, mint a [mail] érték. |

## <a name="exchange-online"></a>Exchange Online
| Attribútum neve | User | Kapcsolattartó | Group | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy engedélyezve van-e a fiók. |
| asszisztens |X |X | | |
| altRecipient |X | | |Ehhez Azure AD Connect build 1.1.552.0 vagy későbbi verzió szükséges. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| cég |X |X | | |
| Országhívószám |X |X | | |
| Részleg |X |X | | |
| leírás | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| érték facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| információ |X |X |X |Ezt az attribútumot jelenleg nem használják a csoportok. |
| Monogram |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| tag | | |X | |
| mobil |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Elérhető a Azure AD Connect verzió 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Ezt az attribútumot jelenleg nem használják az Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Ezt az attribútumot jelenleg nem használják az Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Ezt az attribútumot jelenleg nem használják az Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Ezt az attribútumot jelenleg nem használják az Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Ezt az attribútumot jelenleg nem használják az Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| objectSID |X | |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| személyhívó |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |mechanikai tulajdonság. Annak ismerete, hogy mikor kell érvényteleníteni a már kiállított jogkivonatokat. A jelszó-szinkronizálás és az összevonás is használja. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Megváltoztathatatlan azonosító a HOZZÁADÁSok és az Azure AD közötti kapcsolat fenntartásához. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |csak egyszer szinkronizálva az Azure AD-ből az Exchange Online-ba, amely után az Exchange Online az attribútumhoz tartozó szolgáltatói forrás lesz, és a későbbi módosítások nem szinkronizálhatók a helyszíni rendszerből. További információ: ([kb](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)).|
| cím |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |Az UPN a felhasználó bejelentkezési azonosítója. A legtöbb esetben ugyanaz, mint a [mail] érték. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| Attribútum neve | User | Kapcsolattartó | Group | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy engedélyezve van-e a fiók. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| cég |X |X | | |
| Országhívószám |X |X | | |
| Részleg |X |X | | |
| leírás |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| érték facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| HomePhone |X |X | | |
| információ |X |X |X | |
| monogramja |X |X | | |
| Érték ipphone |X |X | | |
| l |X |X | | |
| Levelezés |X |X |X | |
| mailnickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| tag | | |X | |
| middleName |X |X | | |
| mobil |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| személyhívó |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |Ezt az attribútumot jelenleg nem használják a SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanikai tulajdonság. Annak ismerete, hogy mikor kell érvényteleníteni a már kiállított jogkivonatokat. A jelszó-kivonatolási szinkronizálás, az átmenő hitelesítés és az összevonás egyaránt használja. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Megváltoztathatatlan azonosító a HOZZÁADÁSok és az Azure AD közötti kapcsolat fenntartásához. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |csak egyszer szinkronizálva az Azure AD-ből az Exchange Online-ba, amely után az Exchange Online az attribútumhoz tartozó szolgáltatói forrás lesz, és a későbbi módosítások nem szinkronizálhatók a helyszíni rendszerből. További információ: ([kb](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)).|
| cím |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |mechanikai tulajdonság. A felhasználó országa/régiója
. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X | | |Az UPN a felhasználó bejelentkezési azonosítója. A legtöbb esetben ugyanaz, mint a [mail] érték. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Csapatok és Skype vállalati online verzió
| Attribútum neve | User | Kapcsolattartó | Group | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy engedélyezve van-e a fiók. |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| cég |X |X | | |
| Részleg |X |X | | |
| leírás |X |X |X | |
| displayName |X |X |X | |
| érték facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| HomePhone |X |X | | |
| Érték ipphone |X |X | | |
| l |X |X | | |
| Levelezés |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| tag | | |X | |
| mobil |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP – ApplicationOptions |X | | | |
| msRTCSIP – DeploymentLocator |X |X | | |
| msRTCSIP-line |X |X | | |
| msRTCSIP – OptionFlags |X |X | | |
| msRTCSIP – OwnerUrn |X | | | |
| msRTCSIP – PrimaryUserAddress |X |X | | |
| msRTCSIP – UserEnabled |X |X | | |
| objectSID |X | |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanikai tulajdonság. Annak ismerete, hogy mikor kell érvényteleníteni a már kiállított jogkivonatokat. A jelszó-kivonatolási szinkronizálás, az átmenő hitelesítés és az összevonás egyaránt használja. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Megváltoztathatatlan azonosító a HOZZÁADÁSok és az Azure AD közötti kapcsolat fenntartásához. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |csak egyszer szinkronizálva az Azure AD-ből az Exchange Online-ba, amely után az Exchange Online az attribútumhoz tartozó szolgáltatói forrás lesz, és a későbbi módosítások nem szinkronizálhatók a helyszíni rendszerből. További információ: ([kb](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange)).|
| cím |X |X | | |
| usageLocation |X | | |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X | | |Az UPN a felhasználó bejelentkezési azonosítója. A legtöbb esetben ugyanaz, mint a [mail] érték. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Attribútum neve | User | Kapcsolattartó | Group | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy engedélyezve van-e a fiók. |
| CN |X | |X |Köznapi név vagy alias. Leggyakrabban a [mail] érték előtagja. |
| displayName |X |X |X |Az a név, amely a nevet jelöli, gyakran a felhasználóbarát név (vezetéknév). |
| Levelezés |X |X |X |teljes e-mail-cím. |
| tag | | |X | |
| objectSID |X | |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| proxyAddresses |X |X |X |mechanikai tulajdonság. Az Azure AD által használt. A felhasználó összes másodlagos e-mail-címét tartalmazza. |
| pwdLastSet |X | | |mechanikai tulajdonság. Annak ismerete, hogy mikor kell érvényteleníteni a már kiállított jogkivonatokat. |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Megváltoztathatatlan azonosító a HOZZÁADÁSok és az Azure AD közötti kapcsolat fenntartásához. |
| usageLocation |X | | |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X | | |Ez az egyszerű felhasználónév a felhasználó bejelentkezési azonosítója. A legtöbb esetben ugyanaz, mint a [mail] érték. |

## <a name="intune"></a>Intune
| Attribútum neve | User | Kapcsolattartó | Group | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy engedélyezve van-e a fiók. |
| c |X |X | | |
| CN |X | |X | |
| leírás |X |X |X | |
| displayName |X |X |X | |
| Levelezés |X |X |X | |
| mailnickname |X |X |X | |
| tag | | |X | |
| objectSID |X | |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanikai tulajdonság. Annak ismerete, hogy mikor kell érvényteleníteni a már kiállított jogkivonatokat. A jelszó-kivonatolási szinkronizálás, az átmenő hitelesítés és az összevonás egyaránt használja. |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Megváltoztathatatlan azonosító a HOZZÁADÁSok és az Azure AD közötti kapcsolat fenntartásához. |
| usageLocation |X | | |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X | | |Az UPN a felhasználó bejelentkezési azonosítója. A legtöbb esetben ugyanaz, mint a [mail] érték. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Attribútum neve | User | Kapcsolattartó | Group | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy engedélyezve van-e a fiók. |
| c |X |X | | |
| CN |X | |X | |
| co |X |X | | |
| cég |X |X | | |
| Országhívószám |X |X | | |
| leírás |X |X |X | |
| displayName |X |X |X | |
| érték facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manager |X |X | | |
| tag | | |X | |
| mobil |X |X | | |
| objectSID |X | |X |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |mechanikai tulajdonság. Annak ismerete, hogy mikor kell érvényteleníteni a már kiállított jogkivonatokat. A jelszó-kivonatolási szinkronizálás, az átmenő hitelesítés és az összevonás egyaránt használja. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Megváltoztathatatlan azonosító a HOZZÁADÁSok és az Azure AD közötti kapcsolat fenntartásához. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| cím |X |X | | |
| usageLocation |X | | |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X | | |Az UPN a felhasználó bejelentkezési azonosítója. A legtöbb esetben ugyanaz, mint a [mail] érték. |

## <a name="3rd-party-applications"></a>harmadik féltől származó alkalmazások
Ez a csoport az általános számítási feladatokhoz vagy alkalmazásokhoz szükséges minimális attribútumként használt attribútumok halmaza. Használható olyan munkaterheléshez, amely nem szerepel egy másik szakaszban vagy nem Microsoft-alkalmazásban. A következőkhöz explicit módon kell használni:

* Yammer (csak felhasználó használatos)
* [Hibrid vállalatközi (B2B) intézményközi együttműködési forgatókönyvek, például a SharePoint](/sharepoint/create-b2b-extranet)

Ez a csoport olyan attribútumok összessége, amelyek akkor használhatók, ha az Azure AD-címtárat nem használják a Microsoft 365, a Dynamics vagy az Intune támogatásához. Az alapattribútumok kis készlete van. Vegye figyelembe, hogy az egyszeri bejelentkezés vagy bizonyos külső alkalmazások üzembe helyezéséhez az itt ismertetett attribútumok mellett az attribútumok szinkronizálásának konfigurálására is szükség van. Az alkalmazásra vonatkozó követelményeket a [SaaS-alkalmazás oktatóanyaga](../saas-apps/tutorial-list.md) ismerteti minden alkalmazáshoz.

| Attribútum neve | User | Kapcsolattartó | Group | Megjegyzés |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Meghatározza, hogy engedélyezve van-e a fiók. |
| CN |X | |X | |
| displayName |X |X |X | |
| employeeID |X |  |  | |
| givenName |X |X | | |
| Levelezés |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| tag | | |X | |
| objectSID |X | | |mechanikai tulajdonság. Az Azure AD és az AD közötti szinkronizálás fenntartásához használt AD felhasználói azonosító. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mechanikai tulajdonság. Annak ismerete, hogy mikor kell érvényteleníteni a már kiállított jogkivonatokat. A jelszó-kivonatolási szinkronizálás, az átmenő hitelesítés és az összevonás egyaránt használja. |
| sn |X |X | | |
| sourceAnchor |X |X |X |mechanikai tulajdonság. Megváltoztathatatlan azonosító a HOZZÁADÁSok és az Azure AD közötti kapcsolat fenntartásához. |
| usageLocation |X | | |mechanikai tulajdonság. A felhasználó országa/régiója. Licenc-hozzárendeléshez használatos. |
| userPrincipalName |X | | |Az UPN a felhasználó bejelentkezési azonosítója. A legtöbb esetben ugyanaz, mint a [mail] érték. |

## <a name="windows-10"></a>Windows 10
Egy Windows 10 tartományhoz csatlakoztatott számítógép (eszköz) szinkronizál néhány attribútumot az Azure AD-vel. A forgatókönyvekkel kapcsolatos további információkért lásd: [tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure ad-hez Windows 10-es élményekhez](../devices/hybrid-azuread-join-plan.md). Ezek az attribútumok mindig szinkronizálva vannak, és a Windows 10 nem jelenik meg olyan alkalmazásként, amellyel törölheti a kijelölést. A rendszer a tartományhoz csatlakoztatott Windows 10 rendszerű számítógépeket a userCertificate attribútum kitöltésével azonosítja.

| Attribútum neve | Eszköz | Megjegyzés |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |A tartományhoz csatlakoztatott számítógépek hardcoded értéke. |
| displayName |X | |
| MS-DS-CreatorSID |X |Más néven registeredOwnerReference. |
| objectGUID |X |Más néven deviceID. |
| objectSID |X |Más néven onPremisesSecurityIdentifier. |
| operatingSystem |X |Más néven deviceOSType. |
| operatingSystemVersion |X |Más néven deviceOSVersion. |
| userCertificate |X | |

A **felhasználóhoz** tartozó attribútumok a többi kiválasztott alkalmazás mellett is szerepelnek.  

| Attribútum neve | User | Megjegyzés |
| --- |:---:| --- |
| domainFQDN |X |Más néven dnsDomainName. Például adja meg a contoso.com nevet. |
| domainNetBios |X |Más néven netBiosName. Például: CONTOSO. |
| msDS-KeyCredentialLink |X |Ha a felhasználó regisztrálva van a vállalati Windows Hello-ben. | 

## <a name="exchange-hybrid-writeback"></a>Hibrid Exchange-visszaírási
Ezek az attribútumok visszakerülnek az Azure AD-ből a helyszíni Active Directoryba, ha engedélyezi az **Exchange Hybrid** engedélyezését. Az Exchange-verziótól függően kevesebb attribútum is szinkronizálható.

| Attribútum neve (helyszíni AD) | Attribútum neve (felhasználói felület összekapcsolása) | User | Kapcsolattartó | Group | Megjegyzés |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| MS-DS-External-Directory-Object-ID |X | | |Az Azure AD cloudAnchor származtatva. Ez az attribútum az Exchange 2016 és a Windows Server 2016 AD újdonsága. |
| msExchArchiveStatus| MS-árfolyam-ArchiveStatus |X | | |Online Archive: lehetővé teszi, hogy az ügyfelek archiválják az e-maileket. |
| msExchBlockedSendersHash| MS-árfolyam-BlockedSendersHash |X | | |Szűrés: a helyszíni szűrést és az online biztonságos és letiltott küldő adatait írja vissza az ügyfelektől. |
| msExchSafeRecipientsHash| MS-árfolyam-SafeRecipientsHash  |X | | |Szűrés: a helyszíni szűrést és az online biztonságos és letiltott küldő adatait írja vissza az ügyfelektől. |
| msExchSafeSendersHash| MS-árfolyam-SafeSendersHash  |X | | |Szűrés: a helyszíni szűrést és az online biztonságos és letiltott küldő adatait írja vissza az ügyfelektől. |
| msExchUCVoiceMailSettings| MS-árfolyam-UCVoiceMailSettings |X | | |Egyesített üzenetküldés engedélyezése (UM) – online hangposta: a Microsoft Lync Server integrációja azt jelzi, hogy a Lync-kiszolgáló a helyszínen, hogy a felhasználónak van hangposta a online szolgáltatások. |
| msExchUserHoldPolicies| MS-árfolyam-UserHoldPolicies |X | | |Peres eljárás: lehetővé teszi a Cloud Services számára, hogy meghatározza, mely felhasználók tartoznak a peres eljárás hatálya alá. |
| proxyAddresses| proxyAddresses |X |X |X |Csak az Exchange Online x500-címe van beszúrva. |
| publicDelegates| MS-árfolyam-nyilvános delegált  |X | | |Lehetővé teszi, hogy az Exchange Online-postaláda SendOnBehalfTo jogokat biztosítson a helyszíni Exchange-postaládával rendelkező felhasználók számára. Ehhez Azure AD Connect build 1.1.552.0 vagy későbbi verzió szükséges. |

## <a name="exchange-mail-public-folder"></a>Exchange-levelezés nyilvános mappája
Ezek az attribútumok szinkronizálva vannak a helyszíni Active Directory az Azure AD-be, amikor engedélyezi az **Exchange-levelezés nyilvános mappájának** engedélyezését.

| Attribútum neve | PublicFolder | Megjegyzés |
| --- | :---:| --- |
| displayName | X |  |
| Levelezés | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Eszközvisszaíró
Az eszközök objektumai a Active Directoryban jönnek létre. Ezek az objektumok lehetnek az Azure AD-hez vagy a tartományhoz csatlakoztatott Windows 10 rendszerű számítógépekhez csatlakoztatott eszközök.

| Attribútum neve | Eszköz | Megjegyzés |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| megkülönböztető név |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Csak Windows Server 2016 AD-sémával |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Jegyzetek
* Alternatív azonosító használata esetén a userPrincipalName helyszíni attribútuma szinkronizálva lesz az Azure AD attribútum onPremisesUserPrincipalName. A másodlagos azonosító attribútum, például a levelezés, szinkronizálva van az Azure AD-attribútum userPrincipalName.
* A fenti listában az objektumtípus **felhasználója** az **InetOrgPerson** objektumra is érvényes.

## <a name="next-steps"></a>Következő lépések
További információ a [Azure ad Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).