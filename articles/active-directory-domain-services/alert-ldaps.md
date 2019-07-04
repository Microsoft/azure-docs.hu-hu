---
title: 'Az Azure Active Directory tartományi szolgáltatások: Hibaelhárítási Secure LDAP-konfiguráció |} A Microsoft Docs'
description: Az Azure AD Domain Services hibaelhárítási biztonságos LDAP
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: ''
editor: ''
ms.assetid: 81208c0b-8d41-4f65-be15-42119b1b5957
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 453018f486ca3fda91d8447208fe3d936722522e
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473953"
---
# <a name="azure-ad-domain-services---troubleshooting-secure-ldap-configuration"></a>Az Azure AD tartományi szolgáltatások – hibaelhárítási Secure LDAP-konfiguráció

Ez a cikk gyakori elhárítási problémákkal [a secure LDAP konfigurálása](configure-ldaps.md) az Azure AD tartományi szolgáltatásokhoz.

## <a name="aadds101-secure-ldap-network-security-group-configuration"></a>AADDS101: Secure LDAP hálózati biztonsági csoport konfigurálása

**Riasztás jelenik meg:**

*A felügyelt tartomány Secure LDAP interneten keresztül engedélyezve van. 636-os porthoz való hozzáférés le a hálózati biztonsági csoport használatával azonban nincs zárolva. Ez előfordulhat, hogy teszik elérhetővé a felhasználói fiókok jelszavát találgatásos támadások, a felügyelt tartományon.*

### <a name="secure-ldap-port"></a>Biztonságos LDAP-port

Ha a secure LDAP engedélyezve van, azt javasoljuk, hogy csak bizonyos IP-címekről érkező bejövő LDAPS hozzáférést további szabályok létrehozásával. Ezek a szabályok a tartomány elleni találgatásos támadásokkal szemben, amelyek biztonsági fenyegetést jelenthetnek. Port a 636-os lehetővé teszi, hogy a felügyelt tartomány elérését. Itt látható az NSG-t hozzáférés engedélyezése a Secure LDAP protokollhoz frissítése:

1. Keresse meg a [hálózati biztonsági csoportok lapon](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) az Azure Portalon
2. Válassza ki a tartományt a táblázatban a társított NSG-t.
3. Kattintson a **bejövő biztonsági szabályok**
4. A 636-os portot szabály létrehozása
   1. Kattintson a **Hozzáadás** a felső navigációs sávon.
   2. Válasszon **IP-címek** a forrás.
   3. Adja meg a forrásporttartományok ehhez a szabályhoz.
   4. "636" bemeneti Célporttartomány.
   5. Protokoll **TCP**.
   6. Adjon a szabálynak megfelelő nevet, leírást és prioritás. Ez a szabály prioritása nagyobb, mint az "Összes Megtagadás" szabály prioritását, lehet, ha rendelkezik ilyennel.
   7. Kattintson az **OK** gombra.
5. Győződjön meg arról, hogy a szabály létrejött-e.
6. A tartomány állapotának ellenőrzése annak biztosítása érdekében, hogy végrehajtotta a lépéseket megfelelően két órán belül.

> [!TIP]
> Az Azure AD tartományi szolgáltatásokkal való zökkenőmentes szükséges egyetlen szabály 636-os portot nem áll. További tudnivalókért látogasson el a [hálózati útmutató](network-considerations.md) vagy [hibaelhárítása NSG konfigurációs](alert-nsg.md) cikkeket.
>

## <a name="aadds502-secure-ldap-certificate-expiring"></a>AADDS502: Secure LDAP-tanúsítvány hamarosan lejár

**Riasztás jelenik meg:**

*A felügyelt tartomány secure LDAP-tanúsítványt lejár: [date]].*

**Megoldás:**

Hozzon létre egy új secure LDAP-tanúsítványt a következő témakörben ismertetett lépéseket a [secure LDAP konfigurálása](configure-ldaps.md) cikk.

## <a name="contact-us"></a>Kapcsolat
Lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [visszajelzés és támogatás](contact-us.md).
