---
title: A tartományhoz való csatlakozás hibáinak megoldása – Azure AD Domain Services | Microsoft Docs
description: Ismerje meg, hogy miként lehet elhárítani a gyakori problémákat, amikor egy virtuális géphez próbál csatlakozni, vagy egy alkalmazást csatlakoztat Azure Active Directory Domain Serviceshoz, és nem tud csatlakozni a felügyelt tartományhoz, illetve nem végezheti el a hitelesítést.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 86d6ca79a12e4706f558e92c3c83c5bddaa99b3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618603"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services felügyelt tartományhoz való csatlakozással kapcsolatos problémák elhárítása

Amikor megpróbál csatlakozni egy virtuális géphez (VM), vagy egy alkalmazást Azure Active Directory Domain Services (Azure AD DS) felügyelt tartományhoz csatlakoztat, hibaüzenetet kaphat arról, hogy ezt nem tudja elvégezni. A tartományhoz való csatlakozással kapcsolatos problémák elhárításához tekintse át a következő pontok problémáit:

* Ha nem kap hitelesítési kérést, a virtuális gép vagy az alkalmazás nem tud csatlakozni az Azure AD DS felügyelt tartományhoz.
    * A [tartományhoz való csatlakozással kapcsolatos csatlakozási problémák](#connectivity-issues-for-domain-join)elhárításának megkezdése.
* Ha a hitelesítés során hibaüzenet jelenik meg, akkor a felügyelt tartományhoz való csatlakozás sikeres.
    * A [hitelesítő adatokkal kapcsolatos problémák](#credentials-related-issues-during-domain-join)elhárításának megkezdése a tartományhoz való csatlakozás során.

## <a name="connectivity-issues-for-domain-join"></a>A tartományhoz való csatlakozáshoz kapcsolódó csatlakozási problémák

Ha a virtuális gép nem találja a felügyelt tartományt, általában hálózati kapcsolatok vagy konfigurációs problémák vannak. A probléma megkereséséhez és megoldásához tekintse át a következő hibaelhárítási lépéseket:

1. Győződjön meg arról, hogy a virtuális gép felügyelt tartományhoz van csatlakoztatva, vagy egy társ virtuális hálózat. Ha nem, a virtuális gép nem találja és nem tud csatlakozni a tartományhoz a csatlakozáshoz.
    * Ha a virtuális gép nem csatlakozik ugyanahhoz a virtuális hálózathoz, győződjön meg arról, hogy a virtuális hálózati társ-vagy VPN-kapcsolat *aktív* vagy *csatlakoztatva* van, hogy a forgalom megfelelően működjön.
1. Próbálja meg pingelni a tartományt a felügyelt tartomány tartományneve (például `ping aaddscontoso.com` ) használatával.
    * Ha a pingelési válasz sikertelen, próbálja meg pingelni a tartomány IP-címeit a portál áttekintés lapján a felügyelt tartományhoz, például: `ping 10.0.0.4` .
    * Ha sikeresen Pingeli az IP-címet, de a tartományt nem, a DNS helytelenül van konfigurálva. Győződjön meg arról, hogy [konfigurálta a felügyelt tartomány DNS-kiszolgálóit a virtuális hálózathoz][configure-dns].
1. Próbálja meg kiüríteni a DNS-feloldó gyorsítótárát a virtuális gépen, például: `ipconfig /flushdns` .

### <a name="network-security-group-nsg-configuration"></a>Hálózati biztonsági csoport (NSG) konfigurációja

Felügyelt tartomány létrehozásakor a rendszer a sikeres tartományi művelethez is létrehoz egy hálózati biztonsági csoportot a megfelelő szabályokkal. Ha további hálózati biztonsági csoportokra vonatkozó szabályokat szerkeszt vagy hoz létre, előfordulhat, hogy véletlenül letiltja a kapcsolódási és hitelesítési szolgáltatások biztosításához szükséges portokat az Azure AD DS számára. Ezek a hálózati biztonsági csoportokra vonatkozó szabályok olyan problémákat okozhatnak, mint például a jelszó-szinkronizálás nem fejeződik be, a felhasználók nem tudnak bejelentkezni, vagy a tartományhoz való csatlakozással kapcsolatos problémák.

Ha továbbra is fennáll a kapcsolódási problémák, tekintse át a következő hibaelhárítási lépéseket:

1. A felügyelt tartomány állapotának ellenőrzését a Azure Portal. Ha a *AADDS001* riasztása van, a hálózati biztonsági csoport szabálya blokkolja a hozzáférést.
1. Tekintse át a [szükséges portokat és a hálózati biztonsági csoportra vonatkozó szabályokat][network-ports]. Győződjön meg arról, hogy a virtuális GÉPRE vagy a hálózati portok blokkolásához használt virtuális hálózatra nem vonatkoznak hálózati biztonsági csoportra vonatkozó szabályok.
1. A hálózati biztonsági csoport konfigurációs problémáinak elhárítása után a *AADDS001* riasztás eltűnik az állapot lapról körülbelül 2 órával. A hálózati kapcsolat már elérhető, próbálja meg újra tartományhoz csatlakoztatni a virtuális gépet.

## <a name="credentials-related-issues-during-domain-join"></a>Hitelesítő adatokkal kapcsolatos problémák a tartományhoz való csatlakozás során

Ha olyan párbeszédpanelt kap, amely hitelesítő adatokat kér a felügyelt tartományhoz való csatlakozáshoz, a virtuális gép az Azure Virtual Network használatával tud csatlakozni a tartományhoz. A tartományhoz való csatlakozás folyamata nem sikerül a tartományhoz való hitelesítéskor, vagy a tartományhoz való csatlakozás engedélyezéséhez a hitelesítő adatok használatával.

A hitelesítő adatokkal kapcsolatos problémák elhárításához tekintse át a következő hibaelhárítási lépéseket:

1. Próbálja meg az UPN formátumot használni a hitelesítő adatok megadásához, például: `dee@contoso.onmicrosoft.com` . Győződjön meg arról, hogy az UPN helyesen van konfigurálva az Azure AD-ben.
    * Előfordulhat, hogy a fiókhoz tartozó *sAMAccountName* automatikusan létrejön, ha több felhasználó rendelkezik UGYANAZZAL az UPN-előtaggal a bérlőben, vagy ha az UPN-előtag túl hosszú. Ezért előfordulhat, hogy a fiók *sAMAccountName* -formátuma eltér a helyszíni tartományban várttól vagy használattól.
1. Próbáljon meg egy olyan felhasználói fiók hitelesítő adatait használni, amely a felügyelt tartomány részét képezi, hogy a virtuális gépeket a felügyelt tartományhoz csatlakoztassa.
1. Győződjön meg arról, hogy [engedélyezte a jelszó-szinkronizálást][enable-password-sync] , és elég sokáig várt a jelszó-szinkronizálás kezdeti befejezéséhez.

## <a name="next-steps"></a>Következő lépések

A tartományhoz való csatlakozás művelet részeként a Active Directory folyamatok mélyebb megismeréséhez tekintse meg a [csatlakoztatási és hitelesítési problémák][join-authentication-issues]című témakört.

Ha továbbra sem sikerül a virtuális gép csatlakoztatása a felügyelt tartományhoz, [keressen segítséget a Azure Active Directory támogatási jegyének megnyitásához][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
