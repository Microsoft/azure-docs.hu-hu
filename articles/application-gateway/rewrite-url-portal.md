---
title: URL-cím és lekérdezési karakterlánc újraírása az Azure Application Gateway-Azure Portal
description: Megtudhatja, hogyan konfigurálhat egy Azure-Application Gateway az URL-cím és a lekérdezési karakterlánc újraírására a Azure Portal használatával.
services: application-gateway
author: azhar2005
ms.service: application-gateway
ms.topic: how-to
ms.date: 4/05/2021
ms.author: azhussai
ms.openlocfilehash: b8ddc5e57b9ce56d6bce7e220bc840ba0fa43ae2
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384757"
---
# <a name="rewrite-url-with-azure-application-gateway---azure-portal"></a>URL-cím újraírása az Azure Application Gateway-Azure Portal

Ez a cikk azt ismerteti, hogyan lehet a Azure Portal használatával konfigurálni egy [Application Gateway v2 SKU](application-gateway-autoscaling-zone-redundant.md) -példányt az URL-cím újraírásához.

>[!NOTE]
> Az URL-Újraírási funkció csak a Application Gateway Standard_v2 és WAF_v2 SKU esetében érhető el. Ha az URL-cím újraírása egy WAF-kompatibilis átjárón van konfigurálva, a WAF kiértékelése az újraírásos kérelmek fejlécén és URL-címén történik. [További információk](rewrite-http-headers-url.md#using-url-rewrite-or-host-header-rewrite-with-web-application-firewall-waf_v2-sku).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="before-you-begin"></a>Előkészületek

A cikkben szereplő lépések végrehajtásához rendelkeznie kell egy Application Gateway v2 SKU-példánnyal. Az URL-cím újraírása nem támogatott a v1 SKU-ban. Ha nem rendelkezik a v2 SKU-val, hozzon létre egy [Application Gateway v2 SKU](tutorial-autoscale-ps.md) -példányt a Kezdés előtt.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

## <a name="configure-url-rewrite"></a>URL-átírás konfigurálása

Az alábbi példában, amikor a kérelem URL-címe */Article* tartalmaz, az URL-cím és az URL-cím lekérdezési karakterlánca újraírásra kerül

`contoso.com/article/123/fabrikam` -> `contoso.com/article.aspx?id=123&title=fabrikam`

1. Válassza az **összes erőforrás** lehetőséget, majd válassza ki az Application Gateway-t.

2. Válassza az **újraírások** lehetőséget a bal oldali ablaktáblán.

3. Válassza az **Újraírási készlet** elemet:

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-1.png" alt-text="Újraírási készlet hozzáadása":::

4. Adja meg az Újraírási készlet nevét, és társítsa azt egy útválasztási szabállyal:

    a. Adja meg az Újraírási készlet nevét a **név** mezőben.
    
    b. Válasszon ki egyet vagy többet a **társított útválasztási szabályok** listában felsorolt szabályok közül. Ez az átírási konfigurációnak a forrás-figyelőhöz való hozzárendelésére szolgál az útválasztási szabály segítségével. Csak azokat az útválasztási szabályokat választhatja ki, amelyek nincsenek társítva más Újraírási készletekhez. A más Újraírási készletekhez már társított szabályok szürkén jelennek meg.
    
    c. Válassza a **Tovább** gombot.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-2.png" alt-text="Hozzárendelés egy szabályhoz":::

5. Újraírási szabály létrehozása:

    a. Válassza az **Újraírási szabály hozzáadása** lehetőséget.
    
    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-3.png" alt-text="Képernyőkép, amely kiemeli az Újraírási szabály hozzáadását.":::
    
    b. Adja meg az Újraírási szabály nevét az **Újraírási szabály neve** mezőben. Adjon meg egy számot a **szabály sorszáma** mezőben.

6. Ebben a példában az URL-cím és az URL-cím lekérdezési karakterláncát csak akkor írjuk át, ha az elérési út */Article* tartalmaz. Ehhez adjon hozzá egy feltételt annak kiértékeléséhez, hogy az URL-cím */Article* tartalmaz-e

    a. Válassza a **feltétel hozzáadása** lehetőséget, majd jelölje be az **IF** utasítást tartalmazó jelölőnégyzetet a kibontásához.
    
    b. Mivel ebben a példában a minta */Article* az URL-cím elérési útja szerint szeretnénk megkeresni, az **ellenőrzési változó típusa** mezőben válassza a **kiszolgálói változó** lehetőséget.
    
    c. A **kiszolgálói változó** listában válassza a uri_path
    
    d. A **kis-és nagybetűk** megkülönböztetése területen válassza a **nem** lehetőséget.
    
    e. Az **operátor** listában válassza az **EQUAL (=)** elemet.
    
    f. Adjon meg egy reguláris kifejezési mintát. Ebben a példában a mintázatot fogjuk használni `.*article/(.*)/(.*)`
    
      a () az URL-cím elérési útjának újraírására szolgáló kifejezés összeállításakor használt alkarakterlánc rögzítésére szolgál. További információ: [itt](rewrite-http-headers-url.md#capturing).

    : Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-4.png" alt-text="Feltétel":::

 

7. Művelet hozzáadása az URL-cím és az URL elérési útjának újraírásához

   a. Az **újraírás típusa** listában válassza az **URL** lehetőséget.

   b. A **Művelettípus** listán válassza a **beállítás** lehetőséget.

   c. Az **összetevők** területen válassza az **URL-cím és URL-cím lekérdezési karakterlánc** lehetőséget.

   d. Az **URL-cím elérési útja** mezőben adja meg az elérési út új értékét. Ebben a példában a **/Article.aspx** -t fogjuk használni 

   e. Az **URL-lekérdezési karakterlánc értéke** mezőben adja meg az URL-lekérdezési karakterlánc új értékét. Ebben a példában a következőt fogjuk használni: **ID = {var_uri_path_1} &title = {var_uri_path_2}**
    
    `{var_uri_path_1}` és a `{var_uri_path_1}` feltételnek a kifejezésben való kiértékelése során rögzített alsztringek beolvasására szolgál. `.*article/(.*)/(.*)`
    
   f. Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-5.png" alt-text="Művelet":::

8. Az Újraírási készlet létrehozásához kattintson a **Létrehozás** gombra.

9. Ellenőrizze, hogy az új Újraírási készlet megjelenik-e az Újraírási készletek listájában.

    :::image type="content" source="./media/rewrite-url-portal/rewrite-url-portal-6.png" alt-text="Újraírási szabály hozzáadása":::

## <a name="verify-url-rewrite-through-access-logs"></a>URL-cím újraírásának ellenőrzése a hozzáférési naplókon keresztül

Tekintse át az alábbi mezőket a hozzáférési naplókban annak ellenőrzéséhez, hogy az URL-cím újraírása a várt módon történt-e.

* **originalRequestUriWithArgs**: Ez a mező az eredeti kérelem URL-címét tartalmazza
* **requestUri**: Ez a mező a Application Gateway Újraírási műveletét követő URL-címet tartalmazza.

További információt a hozzáférési naplók összes mezőjéről [itt](application-gateway-diagnostics.md#for-application-gateway-and-waf-v2-sku)talál.

##  <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni arról, hogyan állíthatók be a gyakori használati esetekben az újraírások, tekintse meg az [általános írási forgatókönyvek](rewrite-http-headers.md)című témakört.
