---
title: Biztonságos LDAP-riasztások feloldása a Azure AD Domain Servicesban | Microsoft Docs
description: Megtudhatja, hogyan lehet a biztonságos LDAP-vel kapcsolatos gyakori riasztásokat hibaelhárítási és megoldási Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15c1f3a1731edf7b45061646d43688b4aacc6104
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96620307"
---
# <a name="known-issues-secure-ldap-alerts-in-azure-active-directory-domain-services"></a>Ismert problémák: Secure LDAP riasztások a Azure Active Directory Domain Services

A Lightweight Directory Access Protocol (LDAP) protokollt használó alkalmazások és szolgáltatások, amelyekkel a Azure Active Directory Domain Services (Azure AD DS) kommunikálnak, [a biztonságos LDAP használatára konfigurálhatók](tutorial-configure-ldaps.md). A biztonságos LDAP működéséhez meg kell nyitni egy megfelelő tanúsítványt és szükséges hálózati portot.

Ez a cikk segítséget nyújt az Azure AD DS biztonságos LDAP-hozzáféréssel rendelkező gyakori riasztások megismerésében és megoldásában.

## <a name="aadds101-secure-ldap-network-configuration"></a>AADDS101: Secure LDAP hálózati konfiguráció

### <a name="alert-message"></a>Riasztási üzenet

*Az interneten keresztüli Secure LDAP engedélyezve van a felügyelt tartományhoz. Az 636-as porthoz való hozzáférés azonban nem áll le hálózati biztonsági csoport használatával. A felhasználók a felügyelt tartomány felhasználói fiókjait felhasználhatják az elkövetett jelszavakkal kapcsolatos támadásokra.*

### <a name="resolution"></a>Feloldás

Ha engedélyezi a biztonságos LDAP használatát, ajánlott olyan további szabályokat létrehozni, amelyek korlátozzák a bejövő LDAP-hozzáférést adott IP-címekhez. Ezek a szabályok a találgatásos támadásokkal védik a felügyelt tartományt. A hálózati biztonsági csoport frissítéséhez a 636-as TCP-port biztonságos LDAP-hozzáférésének korlátozásához hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza ki a **hálózati biztonsági csoportok** elemet.
1. Válassza ki a felügyelt tartományhoz társított hálózati biztonsági csoportot, például: *AADDS-contoso.com-NSG*, majd válassza a **bejövő biztonsági szabályok** elemet.
1. Válassza a **+ Hozzáadás** elemet a 636-es TCP-porthoz tartozó szabály létrehozásához. Ha szükséges, válassza a **speciális** lehetőséget az ablakban egy szabály létrehozásához.
1. A **forrás** mezőben válassza az *IP-címek* lehetőséget a legördülő menüből. Adja meg azokat a forrás IP-címeket, amelyekhez hozzáférést szeretne biztosítani a biztonságos LDAP-forgalomhoz.
1. Válassza *a* **cél** lehetőséget, majd adja meg a *636* értéket a **célport tartományához**.
1. Állítsa be  a protokollt *TCP* -ként, és *engedélyezze* a **műveletet** .
1. Adja meg a szabály prioritását, majd adjon meg egy nevet, például *RestrictLDAPS*.
1. Ha elkészült, válassza a **Hozzáadás** elemet a szabály létrehozásához.

A felügyelt tartomány állapota két órán belül automatikusan frissül, és eltávolítja a riasztást.

> [!TIP]
> A 636-es TCP-port nem az egyetlen olyan szabály, amely az Azure AD DS zökkenőmentes működéséhez szükséges. További információ: [Azure AD DS hálózati biztonsági csoportok és szükséges portok](network-considerations.md#network-security-groups-and-required-ports).

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Secure LDAP tanúsítvány lejárata

### <a name="alert-message"></a>Riasztási üzenet

*A felügyelt tartomány biztonságos LDAP-tanúsítványa a következő időpontban lejár: [date]].*

### <a name="resolution"></a>Feloldás

Hozzon létre egy helyettesítő Secure LDAP-tanúsítványt a [biztonságos LDAP-tanúsítvány létrehozásához](tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)szükséges lépések követésével. Alkalmazza a helyettesítő tanúsítványt az Azure AD DSra, és ossza el a tanúsítványt minden olyan ügyfél számára, amely biztonságos LDAP használatával csatlakozik.

## <a name="next-steps"></a>Következő lépések

Ha továbbra is problémákba ütközik, [Nyisson meg egy Azure-támogatási kérést][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
