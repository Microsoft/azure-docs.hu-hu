---
title: Vállalati internetes tartomány átirányítása egy Azure Traffic Manager szolgáltatásbeli tartománynévre |} A Microsoft Docs
description: Ez a cikk segít átirányítani a vállalata tartománynevét egy Traffic Manager szolgáltatásbeli tartománynevére.
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
ms.openlocfilehash: 2b9b718a61d13790de38ea82f9b479e372e9de63
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052683"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Vállalati internetes tartomány átirányítása Azure Traffic Manager-tartományra

Amikor Traffic Manager-profilt hoz létre, az Azure automatikusan hozzárendel egy DNS-nevet a profilhoz. A DNS-zónájából való név felhasználásához hozzon létre egy CNAME DNS-rekordot, amely leképezi a Traffic Manager-profiljának tartománynevét. A Traffic Manager tartományneve a Traffic Manager-profil Konfiguráció lapjának **Általános** részében tekinthető meg.

Ha például a `www.contoso.com` nevet szeretné a Traffic Manager `contoso.trafficmanager.net` DNS-neve felé irányítani, az alábbi DNS-erőforrásrekordot kell létrehoznia:

    www.contoso.com IN CNAME contoso.trafficmanager.net

A rendszer ezentúl a *www.contoso.com* tartományhoz érkező összes forgalomkérést átirányítja a *contoso.trafficmanager.net* tartománynak.

> [!IMPORTANT]
> A második szintű tartomány, például a *contoso.com*, nem irányítható á Traffic Manager-tartományra. A DNS-protokollszabványok nem engedélyezik a CNAME-rekordokat a másodlagos szintű tartománynevek esetében.

## <a name="next-steps"></a>További lépések

* [A Traffic Manager útválasztási módszerei](traffic-manager-routing-methods.md)
* [Traffic Manager – Profil letiltása, engedélyezése vagy törlése](disable-enable-or-delete-a-profile.md)
* [Traffic Manager – Végpont letiltása vagy engedélyezése](disable-or-enable-an-endpoint.md)
