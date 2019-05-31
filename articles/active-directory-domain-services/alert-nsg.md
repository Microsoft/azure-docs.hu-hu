---
title: 'Az Azure Active Directory tartományi szolgáltatások: Hibaelhárítás a hálózati biztonsági csoport konfigurálása |} A Microsoft Docs'
description: Az Azure AD tartományi szolgáltatásokhoz NSG konfigurációs hibáinak elhárítása
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: ''
editor: ''
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 743f83fd25ff897492fda7103d3db1f4b961714d
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246569"
---
# <a name="troubleshoot-invalid-networking-configuration-for-your-managed-domain"></a>A felügyelt tartomány konfigurációja érvénytelen hálózati hibaelhárítása
Ez a cikk segítséget nyújt a hibaelhárításához és megoldásához hálózati konfigurációs hibák, amelyek a következő figyelmeztető üzenet:

## <a name="alert-aadds104-network-error"></a>Alert AADDS104: Hálózati hiba
**Riasztás jelenik meg:** *A Microsoft nem tudja elérni a tartományvezérlőket, a felügyelt tartományhoz. Ez akkor fordulhat elő, ha a virtuális hálózati blokkolja a hozzáférést a felügyelt tartományhoz konfigurált hálózati biztonsági csoport (NSG). Egy másik oka az lehet, ha egy felhasználó által definiált útvonal van, amely blokkolja az internetről bejövő forgalmat.*

Érvénytelen az NSG-konfiguráció olyan hálózati hibák leggyakoribb oka az Azure AD tartományi szolgáltatásokhoz. A hálózati biztonsági csoport (NSG) a virtuális hálózat engedélyeznie kell a hozzáférést konfigurált [bizonyos portokat](network-considerations.md#ports-required-for-azure-ad-domain-services). Ezeket a portokat le vannak tiltva, ha a Microsoft nem figyelése és a felügyelt tartomány frissítése. Emellett az Azure AD-címtár és a felügyelt tartomány közötti szinkronizálásra van hatással. Az NSG létrehozásakor ne zárja be ezeket a portokat a szolgáltatás megszakadásának elkerülése érdekében.

### <a name="checking-your-nsg-for-compliance"></a>Az NSG-t a megfelelőség ellenőrzése

1. Keresse meg a [hálózati biztonsági csoportok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) oldal az Azure Portalon
2. A táblából válasszon, amelyben a felügyelt tartomány engedélyezve van az alhálózathoz társított NSG-t.
3. A **beállítások** kattintson a bal oldali panel **bejövő biztonsági szabályok**
4. Tekintse át a szabályokat a helyen, és azonosítsa, mely szabályok forgalomszűrők blokkolják a hozzáférést [ezeket a portokat](network-considerations.md#ports-required-for-azure-ad-domain-services)
5. Szerkessze az NSG-t a megfelelőség biztosítása a szabály törlése folyamatban van, szabály hozzáadása, vagy egy új NSG-t teljes mértékben létrehozása. A lépések [adjon meg egy szabályt](#add-a-rule-to-a-network-security-group-using-the-azure-portal) vagy hozzon létre egy új, a megfelelő NSG alatt van

## <a name="sample-nsg"></a>Minta NSG-t
Az alábbi táblázat mutatja be, hogy egy NSG-t, hogy az lenne a felügyelt tartomány secure miközben lehetővé teszi a figyelése, kezelése és adatok frissítése a Microsoft mintát.

![Minta NSG-t](./media/active-directory-domain-services-alerts/default-nsg.png)

>[!NOTE]
> Az Azure AD tartományi szolgáltatások a virtuális hálózatról korlátlan kimenő hozzáféréssel kell rendelkeznie. Azt javasoljuk, hogy nem minden olyan további NSG-szabályt, amely korlátozza a kimenő hozzáférést a virtuális hálózat létrehozása.

## <a name="add-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Egy szabály hozzáadása egy hálózati biztonsági csoportot az Azure portal használatával
Ha nem szeretné, ha a PowerShell segítségével, manuálisan is hozzáadhat egyetlen szabályokat az NSG-k az Azure portal használatával. A hálózati biztonsági csoport szabályainak létrehozásához hajtsa végre az alábbi lépéseket:

1. Keresse meg a [hálózati biztonsági csoportok](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) oldal az Azure Portalon.
2. A táblából válasszon, amelyben a felügyelt tartomány engedélyezve van az alhálózathoz társított NSG-t.
3. A **beállítások** kattintson a bal oldali panelen **bejövő biztonsági szabályok** vagy **kimenő biztonsági szabályok**.
4. A szabály létrehozása gombra kattintva **Hozzáadás** és kitölti az adatokat. Kattintson az **OK** gombra.
5. Ellenőrizze, hogy a szabály létrejött, a szabályok tábla megkeresésével.


## <a name="need-help"></a>Segítség
Lépjen kapcsolatba az Azure Active Directory Domain Services termékért felelős csoport [visszajelzés és támogatás](contact-us.md).
