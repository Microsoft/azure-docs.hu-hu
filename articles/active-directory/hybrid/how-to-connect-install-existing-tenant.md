---
title: 'Azure AD Connect: Ha már rendelkezik Azure AD-val | Microsoft Docs'
description: Ez a témakör azt ismerteti, hogyan használható a csatlakozás, ha meglévő Azure AD-Bérlővel rendelkezik.
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
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68251270b6273f5a07391138e5c7210f1c46ba5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420529"
---
# <a name="azure-ad-connect-when-you-have-an-existing-tenant"></a>Azure AD Connect: Ha meglévő Bérlővel rendelkezik
A Azure AD Connect használatának első lépései a legtöbb esetben feltételezik, hogy új Azure AD-bérlőt használ, és nincsenek felhasználók vagy más objektumok. Ha azonban egy Azure AD-Bérlővel lett elindítva, feltöltve a felhasználókkal és más objektumokkal, és most a kapcsolódást szeretné használni, akkor ez a témakör Önnek szól.

## <a name="the-basics"></a>Az alapok
Az Azure AD-beli objektumok a felhőben (Azure AD) vagy a helyszínen vannak elsajátítva. Egyetlen objektum esetében nem kezelheti a helyszíni és egyéb attribútumokat az Azure AD-ben. Minden objektumhoz tartozik egy jelző, amely azt jelzi, hogy az objektum hol van kezelve.

A helyszínen és a felhőben is kezelheti a felhasználókat. Ennek a konfigurációnak a gyakori forgatókönyve egy olyan szervezet, amely többek között a könyvelési feldolgozókat és az értékesítőket is feldolgozók. A könyvelési feldolgozók helyszíni AD-fiókkal rendelkeznek, de az értékesítők nem rendelkeznek fiókkal az Azure AD-ben. A helyszíni és az Azure AD-beli felhasználók is kezelhetők.

Ha olyan Azure AD-felhasználók felügyeletét kezdte meg, amelyek a helyszíni AD-ben is szerepelnek, és később a kapcsolódást szeretnék használni, akkor további szempontokat is figyelembe kell vennie.

## <a name="sync-with-existing-users-in-azure-ad"></a>Szinkronizálás meglévő felhasználókkal az Azure AD-ben
A Azure AD Connect telepítésekor és a szinkronizálás megkezdése után az Azure AD Sync Service (az Azure AD-ben) minden új objektumon bekerül, és megpróbál megkeresni egy meglévő objektumot. Ehhez a folyamathoz három attribútumot kell használni: **userPrincipalName**, **ProxyAddresses** és **sourceAnchor** / **immutableID**. A **userPrincipalName** és a **ProxyAddresses** egyezését a rendszer **puha egyezésnek** nevezzük. A **sourceAnchor** való egyezés a **rögzített egyezés**. A **ProxyAddresses** attribútum csak az **SMTP:** értékkel rendelkező értéket használja, amely az elsődleges e-mail-cím, amelyet a rendszer a kiértékeléshez használ.

A egyezés csak a kapcsolatból érkező új objektumok esetében lesz kiértékelve. Ha módosít egy meglévő objektumot, hogy az a fenti attribútumok bármelyikének megfelelő legyen, akkor egy hibaüzenet jelenik meg.

Ha az Azure AD olyan objektumot talál, amelyben az attribútumok értékei megegyeznek egy olyan objektum esetében, amely a kapcsolatból származik, és már megtalálható az Azure AD-ben, akkor az Azure AD-beli objektumot a kapcsolat veszi át. A korábban felhőben felügyelt objektum a helyszínen felügyelt van megjelölve. Az Azure AD-ben a helyszíni AD értékkel rendelkező összes attribútum felülíródik a helyszíni értékkel.

> [!WARNING]
> Mivel az Azure AD összes attribútumát felülírja a helyszíni érték, győződjön meg arról, hogy a helyszínen található a megfelelő adatközpont. Ha például csak a felügyelt e-mail-cím szerepel a Microsoft 365ban, és nem tartja naprakészen a helyszíni AD DSban, akkor az Azure AD-ban vagy Microsoft 365ban nem jelennek meg az AD DSban található értékek.

> [!IMPORTANT]
> Ha jelszó-szinkronizálást használ, amelyet mindig az expressz beállítások használnak, akkor a rendszer felülírja az Azure AD-ban található jelszót a helyszíni AD-ben lévő jelszóval. Ha a felhasználók különböző jelszavakat kezelhetnek, akkor tájékoztatnia kell őket arról, hogy a csatlakozás telepítésekor a helyszíni jelszót kell használniuk.

Az előző szakaszt és a figyelmeztetést figyelembe kell venni a tervezésben. Ha az Azure AD-ben számos olyan módosítást hajtott végre, amely nem tükröződik a helyszíni AD DSban, akkor meg kell terveznie, hogy miként tölthetők fel AD DS a frissített értékekkel, mielőtt szinkronizálja az objektumokat a Azure AD Connect használatával.

Ha az objektumokat egy Soft-matchtel egyeztette, akkor a **sourceAnchor** az Azure ad-ben lévő objektumhoz adja hozzá, így a rendszer a rögzített egyezést később is felhasználhatja.

>[!IMPORTANT]
> A Microsoft nyomatékosan javasolja a helyszíni fiókok szinkronizálását a Azure Active Directory meglévő rendszergazdai fiókjaival.

### <a name="hard-match-vs-soft-match"></a>Nehezen illeszkedő vs Soft-Match
A csatlakozási szolgáltatás új telepítéséhez nincs gyakorlati különbség a puha és a rögzített egyezés között. A különbség a vész-helyreállítási helyzetben van. Ha elveszítette a kiszolgálót a Azure AD Connect, akkor az új példányok elvesztése nélkül is újratelepíthetők. A rendszer a kezdeti telepítés során egy sourceAnchor rendelkező objektumot kap a csatlakozáshoz. Ezután az ügyfél (Azure AD Connect) kiértékelheti a egyezést, ami sokkal gyorsabb, mint az Azure AD-ben. A rögzített egyezést a kapcsolat és az Azure AD is kiértékeli. A rendszer csak az Azure AD által kiértékelt lágy egyezést ellenőrzi.

### <a name="other-objects-than-users"></a>Más objektumok, mint a felhasználók
A levelezésre képes csoportok és névjegyek esetében a proxyAddresses alapján lehet Soft-Matching. A rögzített egyezés nem alkalmazható, mert csak a felhasználók csak a sourceAnchor/immutableID (a PowerShell használatával) frissíthetők. A levelezésre nem alkalmas csoportok esetében jelenleg nem támogatott a Soft Match vagy a Hard Match.

### <a name="admin-role-considerations"></a>Rendszergazdai szerepkörrel kapcsolatos megfontolások
Ha meg szeretné akadályozni, hogy a nem megbízható helyszíni felhasználók a rendszergazdai szerepkörrel rendelkező Felhőbeli felhasználókkal egyezzenek meg, Azure AD Connect a rendszergazdai szerepkörrel rendelkező objektumokkal nem egyeznek meg a helyszíni felhasználói objektumokkal. Alapértelmezés szerint ez a beállítás. A viselkedés megkerülő megoldásához a következőket teheti:

1.  Távolítsa el a címtárbeli szerepköröket a csak felhőalapú felhasználói objektumból.
2.  Ha sikertelen volt a felhasználói szinkronizálás, akkor a felhőben törölje a karanténba helyezett objektumot.
3.  Indítson el egy szinkronizálást.
4.  Opcionálisan hozzáadhatja a címtárbeli szerepköröket a felhőben lévő felhasználói objektumhoz, ha a megfeleltetés megtörtént.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Új helyszíni Active Directory létrehozása az Azure AD-beli adatokból
Néhány ügyfél csak felhőalapú megoldást indít az Azure AD-vel, és nem rendelkezik helyszíni AD-vel. Később a helyszíni erőforrásokat szeretnék felhasználni, és az Azure AD-beli adatforrásokon alapuló helyszíni AD-t szeretnének létrehozni. Azure AD Connect nem tud segíteni ehhez a forgatókönyvhöz. Nem hozza létre a felhasználókat a helyszínen, és nem képes a helyi jelszó beállítására, mint az Azure AD-ben.

Ha az egyetlen ok, amiért a helyszíni AD hozzáadását tervezi a LOBs (üzletági alkalmazások) támogatásához, érdemes lehet inkább az [Azure ad tartományi szolgáltatásokat](../../active-directory-domain-services/index.yml) használni.

## <a name="next-steps"></a>Következő lépések
További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
