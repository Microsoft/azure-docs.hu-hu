---
title: Hálózati biztonsági csoportra vonatkozó riasztások feloldása az Azure AD DSban | Microsoft Docs
description: Útmutató a hálózati biztonsági csoport konfigurációs értesítéseinek hibaelhárításához és megoldásához Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: justinha
ms.openlocfilehash: 5b48d326efad889adbcf25d487ee27b8200f558f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693911"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Ismert problémák: hálózati konfigurációval kapcsolatos riasztások Azure Active Directory Domain Services

Ahhoz, hogy az alkalmazások és a szolgáltatások megfelelően kommunikáljanak egy Azure Active Directory Domain Services (Azure AD DS) felügyelt tartománnyal, bizonyos hálózati portoknak nyitva kell lenniük, hogy lehetővé tegyék a forgalom áramlását. Az Azure-ban hálózati biztonsági csoportok használatával szabályozhatja a forgalom áramlását. Egy Azure AD DS felügyelt tartomány állapota riasztást jelenít meg, ha a szükséges hálózati biztonsági csoportra vonatkozó szabályok nincsenek érvényben.

Ez a cikk segít megérteni és elhárítani a hálózati biztonsági csoport konfigurációs problémáinak gyakori riasztásait.

## <a name="alert-aadds104-network-error"></a>Riasztás AADDS104: hálózati hiba

### <a name="alert-message"></a>Riasztási üzenet

*A Microsoft nem tudja elérni a tartományvezérlőket ehhez a felügyelt tartományhoz. Ez akkor fordulhat elő, ha a virtuális hálózaton konfigurált hálózati biztonsági csoport (NSG) blokkolja a hozzáférést a felügyelt tartományhoz. Egy másik lehetséges ok, ha van egy felhasználó által megadott útvonal, amely blokkolja az internetről érkező bejövő forgalmat.*

A hálózati biztonsági csoportra vonatkozó szabályok érvénytelenek az Azure-AD DS hálózati hibáinak leggyakoribb okai. A virtuális hálózathoz tartozó hálózati biztonsági csoportnak engedélyeznie kell az adott portokhoz és protokollokhoz való hozzáférést. Ha ezek a portok le vannak tiltva, az Azure platform nem tudja figyelni vagy frissíteni a felügyelt tartományt. Az Azure AD-címtár és az Azure AD DS közötti szinkronizálás is hatással van. Ügyeljen arra, hogy az alapértelmezett portok nyitva maradjanak a szolgáltatás megszakadásának elkerülése érdekében.

## <a name="default-security-rules"></a>Alapértelmezett biztonsági szabályok

A rendszer a következő alapértelmezett bejövő és kimenő biztonsági szabályokat alkalmazza a felügyelt tartomány hálózati biztonsági csoportjára. Ezek a szabályok megőrzik az Azure AD DS védelmét, és lehetővé teszik az Azure platform számára a felügyelt tartomány figyelését, kezelését és frissítését.

### <a name="inbound-security-rules"></a>Bejövő biztonsági szabály

| Prioritás | Name | Port | Protokoll | Forrás | Cél | Művelet |
|----------|------|------|----------|--------|-------------|--------|
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Bármelyik | Engedélyezés |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Bármelyik | <sup>1</sup> . megtagadás |
| 65000    | AllVnetInBound | Bármelyik | Bármelyik | VirtualNetwork | VirtualNetwork | Engedélyezés |
| 65001    | AllowAzureLoadBalancerInBound | Bármelyik | Bármelyik | AzureLoadBalancer | Bármelyik | Engedélyezés |
| 65500    | DenyAllInBound | Bármelyik | Bármelyik | Bármelyik | Bármelyik | Megtagadás |


<sup>1</sup> Hibakereséshez nem kötelező. Engedélyezze, ha szükséges a speciális hibaelhárításhoz.

> [!NOTE]
> A [biztonságos LDAP konfigurálása][configure-ldaps]esetén további szabályt is használhat, amely engedélyezi a bejövő forgalmat. Ez a további szabály a megfelelő LDAP-kommunikációhoz szükséges.

### <a name="outbound-security-rules"></a>Kimenő biztonsági szabályok

| Prioritás | Name | Port | Protokoll | Forrás | Cél | Művelet |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Bármelyik | Bármelyik | VirtualNetwork | VirtualNetwork | Engedélyezés |
| 65001    | AllowAzureLoadBalancerOutBound | Bármelyik | Bármelyik |  Bármelyik | Internet | Engedélyezés |
| 65500    | DenyAllOutBound | Bármelyik | Bármelyik | Bármelyik | Bármelyik | Megtagadás |

>[!NOTE]
> Az Azure AD DS a virtuális hálózat nem korlátozott kimenő hozzáférését igényli. Nem javasoljuk, hogy hozzon létre olyan további szabályokat, amelyek korlátozzák a kimenő hozzáférést a virtuális hálózat számára.

## <a name="verify-and-edit-existing-security-rules"></a>Meglévő biztonsági szabályok ellenőrzése és szerkesztése

A meglévő biztonsági szabályok ellenőrzéséhez és az alapértelmezett portok megnyitásához végezze el a következő lépéseket:

1. A Azure Portal keresse meg és válassza ki a **hálózati biztonsági csoportok** elemet.
1. Válassza ki a felügyelt tartományhoz társított hálózati biztonsági csoportot, például: *AADDS-contoso.com-NSG*.
1. Az **Áttekintés** lapon megjelenik a meglévő bejövő és kimenő biztonsági szabályok.

    Tekintse át a bejövő és kimenő szabályokat, és hasonlítsa össze az előző szakaszban szereplő kötelező szabályok listájával. Szükség esetén válassza ki, majd törölje a szükséges forgalmat blokkoló egyéni szabályokat. Ha a szükséges szabályok bármelyike hiányzik, adjon hozzá egy szabályt a következő szakaszban.

    Miután hozzáadta vagy törli a szükséges forgalmat engedélyező szabályokat, a felügyelt tartomány állapota két órán belül automatikusan frissül, és eltávolítja a riasztást.

### <a name="add-a-security-rule"></a>Biztonsági szabály hozzáadása

Hiányzó biztonsági szabály hozzáadásához hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza ki a **hálózati biztonsági csoportok** elemet.
1. Válassza ki a felügyelt tartományhoz társított hálózati biztonsági csoportot, például: *AADDS-contoso.com-NSG*.
1. A bal oldali panel **Beállítások** területén kattintson a *bejövő biztonsági szabályok* vagy a *kimenő biztonsági szabályok* elemre attól függően, hogy melyik szabályt kell felvennie.
1. Válassza a **Hozzáadás** lehetőséget, majd hozza létre a szükséges szabályt a port, a protokoll, az irány stb. alapján. Ha elkészült, kattintson **az OK gombra**.

A biztonsági szabály hozzáadására és megjelenítésére néhány percet vesz igénybe a lista.

## <a name="next-steps"></a>Következő lépések

Ha továbbra is problémákba ütközik, [Nyisson meg egy Azure-támogatási kérést][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
