---
title: Azure AD Connect Sync szolgáltatás árnyék-attribútumai | Microsoft Docs
description: Leírja, hogyan működnek az árnyék-attribútumok Azure AD Connect Sync szolgáltatásban.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 128303cb51b39db8442fdda71f949db17923bfa2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90088970"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Azure AD Connect Sync szolgáltatás árnyék-attribútumai
A legtöbb attribútum ugyanolyan módon jelenik meg az Azure AD-ben, mint a helyszíni Active Directory. Bizonyos attribútumok azonban bizonyos speciális kezelést is tartalmazhatnak, és az Azure AD attribútum értéke eltérő lehet, mint amit a Azure AD Connect szinkronizál.

## <a name="introducing-shadow-attributes"></a>Árnyék attribútumainak bemutatása
Egyes attribútumok két képviselettel rendelkeznek az Azure AD-ben. A helyszíni értéket és a számított értéket is tárolja a rendszer. Ezeket a további attribútumokat árnyékmásolat-attribútumoknak nevezzük. A két leggyakoribb attribútum, ahol ez a viselkedés látható, **userPrincipalName** és **proxyAddress**. Az attribútumérték módosítása akkor történik meg, ha a nem ellenőrzött tartományokat jelképező attribútumok értékei vannak. A kapcsolat szinkronizálási motorja azonban a "Shadow" attribútumban beolvassa az értéket, így a nézőpontból az attribútumot az Azure AD megerősítette.

Az árnyék attribútumai nem láthatók a Azure Portal vagy a PowerShell használatával. A koncepció megismerése pedig segít az olyan helyzetek megoldásában, amelyekben az attribútum különböző értékeket tartalmaz a helyszínen és a felhőben.

A viselkedés jobb megismeréséhez tekintse meg a fabrikam következő példáját:  
![A képernyőképen a Active Directory UPN-utótag látható, amely a hozzá tartozó, nem ellenőrzött és ellenőrzött Azure AD-tartományi értékkel rendelkezik.](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Több UPN-utótaggal rendelkeznek a helyszíni Active Directoryban, de csak egyszer voltak ellenőrizve.

### <a name="userprincipalname"></a>userPrincipalName
A felhasználók a következő attribútum-értékekkel rendelkeznek egy nem ellenőrzött tartományban:

| Attribútum | Érték |
| --- | --- |
| helyszíni userPrincipalName | lee.sperry@fabrikam.com |
| Azure AD-shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Azure AD-userPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

A userPrincipalName attribútum a PowerShell használatakor megjelenő érték.

Mivel a valós helyszíni attribútumérték az Azure AD-ben tárolódik, a fabrikam.com tartomány ellenőrzésekor az Azure AD frissíti a userPrincipalName attribútumot a shadowUserPrincipalName értékével. Ezekhez az értékekhez nem kell szinkronizálnia a Azure AD Connect módosításait.

### <a name="proxyaddresses"></a>proxyAddresses
Ugyanez a folyamat csak az ellenőrzött tartományokkal együtt fordul elő a proxyAddresses esetében is, de némi további logikával. Az ellenőrzött tartományok ellenőrzése csak a postaláda felhasználói számára történik. A levelezésre engedélyezett felhasználók vagy névjegyek egy másik Exchange-szervezetben lévő felhasználót jelölnek, és a proxyAddresses bármilyen értéket hozzáadhatnak ezekhez az objektumokhoz.

A postaláda-felhasználók számára, akár a helyszínen, akár az Exchange Online-ban, csak az ellenőrzött tartományokhoz tartozó értékek jelennek meg. Így néz ki:

| Attribútum | Érték |
| --- | --- |
| helyszíni proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

Ebben az esetben az **SMTP: \@** a (z) a (z) és a (z), De az Exchange is hozzáadta a **SIP: \@ fabrikamonline.com. Spencer.** A fabrikam nem használta a Lync/Skype szolgáltatást a helyszínen, de az Azure AD és az Exchange Online előkészíti azt.

A proxyAddresses ezt a logikát **ProxyCalc** nevezzük. A ProxyCalc a felhasználó minden módosításával meghívja a következő esetekben:

- A felhasználóhoz olyan szolgáltatási csomag van rendelve, amely tartalmazza az Exchange Online-t is, ha a felhasználó nem rendelkezik Exchange-licenccel. Ha például a felhasználó hozzá van rendelve az Office E3 SKU-hoz, de csak a SharePoint Online-hoz lett hozzárendelve. Ez akkor is igaz, ha a postaláda még mindig a helyszínen van.
- A msExchRecipientTypeDetails attribútum értéke.
- Módosítást végez a proxyAddresses vagy a userPrincipalName.

A ProxyCalc eltarthat egy ideig, hogy feldolgozza a felhasználó módosításait, és nem szinkronban van a Azure AD Connect exportálási folyamatával.

> [!NOTE]
> A ProxyCalc logikának van néhány további viselkedése a jelen témakörben nem dokumentált speciális forgatókönyvekhez. Ebben a témakörben megismerheti a viselkedést, és nem dokumentálhatja az összes belső logikát.

### <a name="quarantined-attribute-values"></a>Karanténba helyezett attribútumok értékei
A rendszer az árnyék attribútumokat is használja, ha ismétlődő attribútumok vannak megadva. További információ: [duplikált attribútum rugalmassága](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Lásd még
* [Azure AD Connect szinkronizálása](how-to-connect-sync-whatis.md)
* A helyszíni [identitások integrálása a Azure Active Directorykal](whatis-hybrid-identity.md).
