---
title: Mi az Azure AD Connect Cloud Sync. | Microsoft Docs
description: Ismerteti Azure AD Connect Felhőbeli szinkronizálást.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc81e0b5a2b7e9e2e5ab12094950f71ad51c6871
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107225971"
---
# <a name="what-is-azure-ad-connect-cloud-sync"></a>Mi az Azure AD Connect Cloud Sync?
Azure AD Connect a Cloud Sync a Microsoft új ajánlása, amely a felhasználók, csoportok és névjegyek Azure AD-be való szinkronizálásához készült hibrid identitási célok teljesítésére és megvalósítására lett tervezve.  Ezt a Azure AD Connect alkalmazás helyett az Azure AD Cloud kiépítési ügynök használatával hajtja végre.  Azonban Azure AD Connect szinkronizálással is használható, és a következő előnyöket biztosítja:
    
- Azure AD-bérlők többerdős kapcsolattal való szinkronizálásának támogatása Active Directory Forest-környezetből: a gyakori forgatókönyvek közé tartozik az egyesítés & a beszerzés, ahol a beszerzett vállalat AD-erdők el vannak különítve a szülő vállalat AD-erdőkből és olyan vállalatoktól, amelyeknek történelmileg több AD-erdője van.
- Egyszerűsített telepítés kis teljesítményű kiépítési ügynökökkel: az ügynökök az AD-ből az Azure AD-ből származó hidakként működnek, és a felhőben kezelt összes szinkronizálási konfigurációval rendelkeznek. 
- Több kiépítési ügynök használható a magas rendelkezésre állású üzemelő példányok leegyszerűsítéséhez, különösen kritikus fontosságú az AD és az Azure AD közötti jelszó-kivonatolási szinkronizálást használó szervezetek számára.
- Nagyméretű csoportok támogatása akár 50 000 taggal. A nagyméretű csoportok szinkronizálásakor ajánlott csak a szervezeti egység hatókörű szűrőjét használni.


![Mi az az Azure AD Connect?](media/what-is-cloud-sync/architecture-1.png)

## <a name="how-is-azure-ad-connect-cloud-sync-different-from-azure-ad-connect-sync"></a>Miben különbözik az Azure AD Connect Cloud Sync Azure AD Connect synctől?
Azure AD Connect Cloud Sync használatával az AD-ből az Azure AD-be való kiépítés a Microsoft Online Services szolgáltatásban történik. Egy szervezetnek csak a helyszíni és a IaaS üzemeltetett környezetében kell üzembe helyeznie egy egyszerű ügynököt, amely az Azure AD és az AD közötti hidat képez. A létesítési konfiguráció az Azure AD-ben tárolódik, és a szolgáltatás részeként van kezelve.

## <a name="azure-ad-connect-cloud-sync-video"></a>Azure AD Connect Cloud Sync videó
A következő rövid videó kiváló áttekintést nyújt a Azure AD Connect Cloud Sync szolgáltatásról:

> [!VIDEO https://youtube.com/embed/mOT3ID02_YQ]


## <a name="comparison-between-azure-ad-connect-and-cloud-sync"></a>Összehasonlítás a Azure AD Connect és a Felhőbeli szinkronizálás között

Az alábbi táblázat a Azure AD Connect és a Azure AD Connect Cloud Sync összehasonlítását tartalmazza:

| Szolgáltatás | Azure Active Directory Connect szinkronizálása| Azure Active Directory Connect Cloud Sync |
|:--- |:---:|:---:|
|Csatlakozás egyetlen helyszíni AD-erdőhöz|● |● |
| Csatlakozás több helyszíni AD-erdőhöz |● |● |
| Kapcsolódás több leválasztott helyszíni AD-erdőhöz | |● |
| Egyszerűsített ügynök telepítési modellje | |● |
| Több aktív ügynök a magas rendelkezésre állás érdekében | |● |
| Kapcsolódás LDAP-címtárakhoz|●| | 
| Felhasználói objektumok támogatása |● |● |
| Csoport objektumainak támogatása |● |● |
| Kapcsolattartási objektumok támogatása |● |● |
| Eszközök objektumainak támogatása |● | |
| Alapvető Testreszabás engedélyezése az attribútumok folyamataihoz |● |● |
| Exchange Online-attribútumok szinkronizálása |● |● |
| A bővítmény attribútumainak szinkronizálása 1-15 |● |● |
| Ügyfél által definiált AD-attribútumok (címtárszolgáltatás-bővítmények) szinkronizálása |● | |
| Jelszó-kivonat szinkronizálásának támogatása |●|●|
| Pass-Through hitelesítés támogatása |●||
| Összevonás támogatása |●|●|
| Közvetlen egyszeri bejelentkezés|● |●|
| Támogatja a tartományvezérlőre történő telepítést |● |● |
| A Windows Server 2016 támogatása|● |● |
| Szűrés tartományokon/szervezeti egységeken/csoportokon |● |● |
| Objektumok szűrése attribútum értékeit |● | |
| A minimális attribútumkészletek szinkronizálásának engedélyezése (MinSync) |● |● |
| A folyamból az attribútumok eltávolításának engedélyezése az AD-ből az Azure AD-ba |● |● |
| Az attribútumfolyamok speciális testreszabásának engedélyezése |● | |
| Visszaírási-támogatás (jelszavak, eszközök, csoportok) |● | |
| Azure AD Domain Services támogatás|● | |
| [Hibrid Exchange-visszaírási](../hybrid/reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) |● | |
| Legfeljebb 150 000 objektum támogatása AD-tartományon keresztül |● |● |
| Nagyméretű csoportos támogatás – akár 50 000 taggal rendelkező csoportok |● |● |
| Tartományok közötti hivatkozások|● | |
| Igény szerinti üzembe helyezés| |● |

## <a name="next-steps"></a>Következő lépések 

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [A Cloud Sync telepítése](how-to-install.md)
