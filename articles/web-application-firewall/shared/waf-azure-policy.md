---
title: Azure webalkalmazási tűzfal és Azure Policy
description: Az Azure webalkalmazási tűzfal (WAF) és a Azure Policy együttes használata segíthet a szervezeti szabványok betartatásában és a WAF-erőforrások megfelelőségének felmérésében
author: tremansdoerfer
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: rimansdo
ms.openlocfilehash: 7798d7e960286d4f8aa971eb2eb0b03d24bd6360
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97589457"
---
# <a name="azure-web-application-firewall-and-azure-policy"></a>Azure webalkalmazási tűzfal és Azure Policy

Az Azure webalkalmazási tűzfal (WAF) és a Azure Policy együttes használata segíthet a szervezeti szabványok betartatásában és a WAF-erőforrások megfelelőségének felmérésében. Az Azure Policy egy irányítási eszköz, amely összesített nézetet biztosít a környezet általános állapotának kiértékeléséhez, és lehetővé teszi, hogy az erőforrás-és a házirendek részletessége alapján részletezhető legyen. Az Azure Policy emellett a meglévő erőforrások tömeges szervizelése és az új erőforrások automatikus szervizelése révén is lehetővé teszi az erőforrások megfelelőségét.

## <a name="azure-policy-for-web-application-firewall"></a>Webalkalmazási tűzfal Azure Policy

A WAF-erőforrások kezeléséhez számos beépített Azure Policy definíció található. A szabályzat-definíciók és funkcióik részletezése a következő:

1. A **webalkalmazási tűzfalat (WAF) engedélyezni kell az Azure bejárati ajtó szolgáltatásához**: az Azure bejárati szolgáltatásait akkor értékeli ki a rendszer, ha az erőforrás-létrehozási WAF létezik. A házirendnek három hatása van: naplózás, megtagadás és letiltás. A naplózás nyomon követi, ha egy Azure bejárati ajtó szolgáltatás nem rendelkezik WAF, és lehetővé teszi a felhasználók számára, hogy az Azure bejárati ajtó szolgáltatás nem felel meg a követelményeknek. A Megtagadás megakadályozza, hogy az Azure bejárati ajtó szolgáltatás ne hozzon létre egy WAF, ha nincs csatlakoztatva. A Letiltva beállítás kikapcsolja ezt a házirendet.

2. A **webalkalmazási tűzfalat (WAF) engedélyezni kell a Application Gateway esetében**: az Application Gateway kiértékelése akkor történik meg, ha az erőforrás-LÉTREHOZÁShoz WAF van. A házirendnek három hatása van: naplózás, megtagadás és letiltás. A naplózás nyomon követi, ha egy Application Gateway nem rendelkezik WAF, és lehetővé teszi, hogy a felhasználók lássák, milyen Application Gateway nem felel meg a követelményeknek. A Megtagadás megakadályozza, hogy a rendszer ne hozzon létre Application Gateway, ha nincs csatolva WAF. A Letiltva beállítás kikapcsolja ezt a házirendet.

3. **A webalkalmazási tűzfalnak (WAF) a megadott módot kell használnia az Azure bejárati ajtó szolgáltatásához**: az "észlelés" vagy a "megelőzés" üzemmód használatát az Azure-beli előtérben elérhető összes webalkalmazási tűzfalra vonatkozó házirendben aktívnak kell lennie. A házirendnek három hatása van: naplózás, megtagadás és letiltás. A naplózás nyomon követi, ha egy WAF nem felel meg a megadott módnak. A Megtagadás megakadályozza, hogy a rendszer ne hozzon létre WAF, ha az nem megfelelő módban van. A Letiltva beállítás kikapcsolja ezt a házirendet.

4. **A webalkalmazási tűzfalnak (WAF) a megadott Application Gateway módot kell használnia**: az "észlelés" vagy a "megelőzés" üzemmód használatát a Application Gateway összes webalkalmazási tűzfal házirendje esetében aktívnak kell lennie. A házirendnek három hatása van: naplózás, megtagadás és letiltás. A naplózás nyomon követi, ha egy WAF nem felel meg a megadott módnak. A Megtagadás megakadályozza, hogy a rendszer ne hozzon létre WAF, ha az nem megfelelő módban van. A Letiltva beállítás kikapcsolja ezt a házirendet.

## <a name="launch-an-azure-policy"></a>Azure Policy elindítása

1.  Az Azure kezdőlapján írja be a házirend kifejezést a keresősáv mezőbe, és kattintson a Azure Policy ikonra.

2.  A Azure Policy szolgáltatás **Létrehozás** területén válassza a **hozzárendelések** lehetőséget.

:::image type="content" source="../media/waf-azure-policy/policy-home.png" alt-text="Hozzárendelések lap Azure Policy belül":::

3.  A hozzárendelések lapon válassza a felül a **házirend hozzárendelése** ikont.

:::image type="content" source="../media/waf-azure-policy/assign-policy.png" alt-text="Az alapbeállítások lap a szabályzat kiosztása lapon":::

4.  A házirend-hozzárendelési lap alapjai lapon frissítse a következő mezőket:
    1.  **Hatókör**: válassza ki, hogy az Azure-előfizetések és-erőforráscsoportok milyen hatással legyenek a szabályzat-definícióra.
    2.  **Kizárások**: válassza ki a hatókörből azokat az erőforrásokat, amelyeket ki szeretne zárni a szabályzat-hozzárendelésből.
    3.  **Házirend-definíció**: válassza ki azt a házirend-definíciót, amely a hatókörre vonatkozik a kizárásokkal. Írja be a "webalkalmazási tűzfal" kifejezést a keresőmezőbe a megfelelő webalkalmazási tűzfal Azure Policy kiválasztásához.

:::image type="content" source="../media/waf-azure-policy/policy-listing.png" alt-text="Képernyőkép, amely az &quot;elérhető definíciók&quot; oldal &quot;házirend-definíciók&quot; lapját jeleníti meg.":::

5.  Válassza a **Parameters (paraméterek** ) fület, és frissítse a szabályzat-hozzárendelés paramétereit. A paraméter nevének további tisztázásához vigye a kurzort a paraméter neve melletti információs ikonra a további pontosításhoz.

6.  Válassza a **felülvizsgálat + létrehozás** lehetőséget a szabályzat-hozzárendelés véglegesítéséhez. A szabályzat-hozzárendelés körülbelül 15 percet vesz igénybe, amíg az új erőforrások esetében nem aktív.
