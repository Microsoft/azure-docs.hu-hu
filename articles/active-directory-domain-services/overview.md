---
title: A Azure Active Directory Domain Services áttekintése | Microsoft Docs
description: Ebből az áttekintésből megtudhatja, hogy milyen Azure Active Directory Domain Services biztosít, és hogyan használhatja azt a szervezetében, hogy identitás-szolgáltatásokat nyújtson a Felhőbeli alkalmazásokhoz és szolgáltatásokhoz.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 01/20/2021
ms.author: justinha
ms.custom: contperf-fy21q1
ms.openlocfilehash: 194a5420f38e99d45f74241f67ae8cfbbaaee8f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660997"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Mi az Azure Active Directory Domain Services?

A Azure Active Directory Domain Services (AD DS) olyan felügyelt tartományi szolgáltatásokat biztosít, mint például a tartományhoz való csatlakozás, a csoportházirend, a Lightweight Directory Access Protocol (LDAP) és a Kerberos/NTLM hitelesítés. Ezeket a tartományi szolgáltatásokat a tartományvezérlők (DCs) Felhőbeli üzembe helyezése, kezelése és javítása nélkül kell használni.

Az Azure AD DS felügyelt tartomány lehetővé teszi, hogy olyan örökölt alkalmazásokat futtasson a felhőben, amelyek nem használhatják a modern hitelesítési módszereket, vagy ha nem szeretné, hogy a címtár-keresési szolgáltatás mindig visszalépjen egy helyszíni AD DS-környezetbe. Az örökölt alkalmazásokat a helyszíni környezetből egy felügyelt tartományba helyezheti át, és nem kell a felhőben kezelnie a AD DS környezetet.

Az Azure AD DS integrálható a meglévő Azure AD-Bérlővel. Ez az integráció lehetővé teszi a felhasználók számára, hogy a meglévő hitelesítő adataik használatával bejelentkezzenek a felügyelt tartományhoz csatlakozó szolgáltatásokra és alkalmazásokra. Meglévő csoportokat és felhasználói fiókokat is használhat az erőforrásokhoz való hozzáférés biztosításához. Ezek a funkciók zökkenőmentesebb átállást biztosítanak a helyszíni erőforrások számára az Azure-hoz.

> [!div class="nextstepaction"]
> [Első lépésként hozzon létre egy Azure AD DS felügyelt tartományt a Azure Portal használatával][tutorial-create]

Tekintse meg a rövid videót, amelyből többet tudhat meg az Azure AD DSról.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4LblD]

## <a name="how-does-azure-ad-ds-work"></a>Hogyan működik az Azure AD DS?

Azure AD DS felügyelt tartomány létrehozásakor egyedi névteret határozhat meg. Ez a névtér a tartomány neve, például *aaddscontoso.com*. Ezután két Windows Server-tartományvezérlőt (DCs) telepítünk a kiválasztott Azure-régióba. A tartományvezérlők ezt a központi telepítését replikakészlet néven nevezzük.

Ezeket a tartományvezérlőket nem kell kezelnie, konfigurálnia vagy frissítenie. Az Azure platform kezeli a tartományvezérlőket a felügyelt tartomány részeként, beleértve a biztonsági mentéseket és a titkosítást a Azure Disk Encryption használatával.

A felügyelt tartomány úgy van konfigurálva, hogy egyirányú szinkronizálást végezzen az Azure AD-ből, hogy hozzáférést biztosítson a felhasználók, csoportok és hitelesítő adatok központi készletéhez. Erőforrásokat közvetlenül a felügyelt tartományban hozhat létre, de nem szinkronizálja őket az Azure AD-vel. Az Azure-ban a felügyelt tartományhoz csatlakozó alkalmazások, szolgáltatások és virtuális gépek olyan általános AD DS szolgáltatásokat használhatnak, mint például a tartományhoz való csatlakozás, a csoportházirend, az LDAP és a Kerberos/NTLM hitelesítés.

A helyszíni AD DS környezettel rendelkező hibrid környezetekben a [Azure ad Connect][azure-ad-connect] az azonosító adatokat szinkronizálja az Azure ad-vel, amelyet ezután szinkronizál a felügyelt tartományba.

![Szinkronizálás Azure AD Domain Services az Azure AD-vel és helyszíni AD DS az AD-kapcsolat használatával](./media/active-directory-domain-services-design-guide/sync-topology.png)

Az Azure AD DS az Azure AD-ből replikálja az azonosító adatokat, így az Azure AD-bérlők csak felhőben működnek, vagy egy helyszíni AD DS-környezettel szinkronizálhatók. Ugyanezek az Azure AD DS-funkciók is léteznek mindkét környezetben.

* Ha meglévő helyszíni AD DS-környezettel rendelkezik, a felhasználói fiókadatok szinkronizálásával konzisztens identitást biztosíthat a felhasználóknak. További információ: [az objektumok és a hitelesítő adatok szinkronizálása felügyelt tartományokban][synchronization].
* A csak felhőalapú környezetekhez nincs szükség hagyományos helyszíni AD DS környezetre az Azure AD DS központi identitási szolgáltatásainak használatához.

A felügyelt tartományokat kiterjesztheti úgy, hogy az Azure AD-bérlőn belüli több replikát is hozzon létre. A kópiakészlet bármely olyan Azure-régióban használható, amely támogatja az Azure AD DS-t. A különböző Azure-régiókban található további replikák földrajzi vész-helyreállítást biztosítanak az örökölt alkalmazásokhoz, ha egy Azure-régió offline állapotba kerül. A kópiakészlet jelenleg előzetes verzióban érhető el. További információ: [a replika-készletek fogalmai és funkciói a felügyelt tartományokhoz][concepts-replica-sets].

Az alábbi videó áttekintést nyújt arról, hogyan integrálható az Azure AD DS az alkalmazásokkal és a számítási feladatokkal a Felhőbeli Identity Services biztosításához:

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

Az Azure AD DS üzembe helyezési forgatókönyvek működés közbeni megtekintéséhez tekintse meg az alábbi példákat:

* [Azure-AD DS hibrid szervezeteknél](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [Azure-AD DS csak felhőalapú szervezeteknek](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Az Azure AD DS funkciói és előnyei

A felhőben lévő alkalmazásokhoz és virtuális gépekhez az Azure AD DS teljes mértékben kompatibilis a hagyományos AD DS környezettel olyan műveletekhez, mint a tartományhoz való csatlakozás, a biztonságos LDAP (LDAPs), a Csoportházirend, a DNS-kezelés és az LDAP-kötés, valamint az olvasási támogatás. A felügyelt tartományban létrehozott objektumok esetében LDAP-írási támogatás érhető el, az Azure AD-ből nem szinkronizált erőforrások azonban nem.

Ha többet szeretne megtudni az identitási lehetőségekről, [hasonlítsa össze az azure AD DS az Azure ad-vel, az Azure-beli virtuális gépek ad DSával és a helyszíni ad DSával][compare].

Az Azure AD DS alábbi funkciói egyszerűsítik az üzembe helyezési és felügyeleti műveleteket:

* **Egyszerűsített üzembe helyezési élmény:** Az Azure AD DS a Azure Portal egyetlen varázslójának használatával engedélyezhető az Azure AD-bérlő számára.
* **Az Azure ad-vel integrált:** Az Azure AD-bérlő automatikusan elérhetővé teszi a felhasználói fiókokat, a csoporttagságok és a hitelesítő adatokat. Az Azure AD-bérlőből vagy a helyszíni AD DS környezetből származó attribútumok új felhasználóit, csoportjait vagy módosításait a rendszer automatikusan szinkronizálja az Azure AD DSba.
    * Az Azure AD-hez csatolt külső címtárakban lévő fiókok nem érhetők el az Azure AD DSban. A hitelesítő adatok nem érhetők el ezekhez a külső könyvtárakhoz, ezért nem lehet szinkronizálni a felügyelt tartományba.
* **Vállalati hitelesítő adatok/jelszavak használata:** Az Azure AD DSban lévő felhasználók jelszavai ugyanazok, mint az Azure AD-bérlőben. A felhasználók a vállalati hitelesítő adataikat a tartományhoz csatlakozó számítógépekhez használhatják, interaktív módon vagy távoli asztalon is bejelentkezhetnek, és hitelesíthetők a felügyelt tartományon.
* **NTLM-és Kerberos-hitelesítés:** Az NTLM-és Kerberos-hitelesítés támogatása lehetővé teszi a Windows-alapú hitelesítésre támaszkodó alkalmazások központi telepítését.
* **Magas rendelkezésre állás:** Az Azure AD DS több tartományvezérlőt is tartalmaz, amelyek magas rendelkezésre állást biztosítanak a felügyelt tartomány számára. Ez a magas rendelkezésre állás garantálja a szolgáltatás üzemidőét és a hibákhoz való rugalmasságot.
    * A [Azure Availability Zonest][availability-zones]támogató régiókban ezek a tartományvezérlők a további rugalmasság érdekében a zónák között is eloszthatók.
    * A [replikák][concepts-replica-sets] a korábbi alkalmazások földrajzi vész-helyreállításának biztosítására is használhatók, ha egy Azure-régió offline állapotba kerül.

A felügyelt tartomány néhány kulcsfontosságú aspektusa a következőket foglalja magában:

* A felügyelt tartomány önálló tartomány. Nem egy helyszíni tartomány kiterjesztése.
    * Szükség esetén egyirányú kimenő erdőszintű megbízhatósági kapcsolatot hozhat létre az Azure AD DS egy helyszíni AD DS környezetbe. További információ: [Az Azure AD DS erőforrás-erdővel kapcsolatos fogalmak és szolgáltatások][ forest-trusts].
* Az informatikai csapatnak nem kell a felügyelt tartományhoz tartozó tartományvezérlőket kezelnie, megjavítania vagy figyelnie.

A helyszíni AD DSt futtató hibrid környezetek esetében nem szükséges az AD-replikáció kezelése a felügyelt tartományhoz. A helyszíni címtárból származó felhasználói fiókok, csoporttagság és hitelesítő adatok az Azure AD-vel [Azure ad Connecton][azure-ad-connect]keresztül szinkronizálhatók. Ezek a felhasználói fiókok, csoporttagság és hitelesítő adatok automatikusan elérhetők a felügyelt tartományon belül.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure AD DS más identitási megoldásokkal való összehasonlításáról és a szinkronizálás működéséről, tekintse meg a következő cikkeket:

* [Az Azure AD DS összehasonlítása az Azure AD-vel, az Azure-beli virtuális gépek Active Directory Domain Services és a helyszíni Active Directory Domain Services][compare]
* [Ismerje meg, hogyan szinkronizálja Azure AD Domain Services az Azure AD-címtárral][synchronization]
* A felügyelt tartományok felügyeletének megismeréséhez tekintse meg a [felhasználói fiókok, jelszavak és felügyelet fogalmait az Azure ad DSban][administration-concepts].

Első lépésként [hozzon létre egy felügyelt tartományt a Azure Portal használatával][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md
