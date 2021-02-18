---
title: Azure AD-bérlő létrehozása az Azure Red Hat OpenShift
description: Ebből a témakörből megtudhatja, hogyan hozhat létre Azure Active Directory (Azure AD) bérlőt a Microsoft Azure Red Hat OpenShift-fürt üzemeltetéséhez.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: b98f02adeb850f16127658c7d02d44754512e216
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635009"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>Azure AD-bérlő létrehozása az Azure Red Hat OpenShift

> [!IMPORTANT]
> Az Azure Red Hat OpenShift 3,11 2022. június 30-án megszűnik. Az új Azure Red Hat OpenShift 3,11-fürtök létrehozásának támogatása az 2020. november 30-ig folytatódik. A biztonsági rések elkerülése érdekében a rendszer leállítja a fennmaradó Azure Red Hat OpenShift 3,11-fürtöket a kivonulást követően.
> 
> Kövesse ezt az útmutatót [egy Azure Red Hat OpenShift 4-fürt létrehozásához](tutorial-create-cluster.md).
> Ha konkrét kérdései vannak, vegye [fel velünk a kapcsolatot](mailto:arofeedback@microsoft.com).

Microsoft Azure Red Hat OpenShift olyan [Azure Active Directory (Azure ad)](../active-directory/develop/quickstart-create-new-tenant.md) bérlőt igényel, amelyben létre kívánja hozni a fürtöt. A *bérlő* az Azure ad dedikált példánya, amelyet a szervezet vagy az alkalmazás fejlesztője kap, amikor kapcsolatot hoz létre a Microsofttal az Azure-ra, Microsoft Intunera vagy Microsoft 365re való regisztráláskor. Minden Azure AD-bérlő különálló, és elkülönül a többi Azure AD-bérlőtől, és saját munkahelyi és iskolai identitásokkal és alkalmazás-regisztrációval rendelkezik.

Ha még nem rendelkezik Azure AD-Bérlővel, az alábbi utasításokat követve hozhat létre egyet.

## <a name="create-a-new-azure-ad-tenant"></a>Új Azure AD-bérlő létrehozása

Bérlő létrehozása:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) az Azure Red Hat OpenShift-fürthöz társítandó fiók használatával.
2. A [Azure Active Directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) panel megnyitásával hozzon létre egy új bérlőt (más néven új *Azure Active Directory*).
3. Adja meg a **szervezet nevét**.
4. Adja meg a **kezdeti tartománynevet**. Ehhez hozzá kell fűzni a *onmicrosoft.com* . Itt újra felhasználhatja a *szervezet nevének* értékét.
5. Válassza ki azt az országot vagy régiót, ahol létre kívánja hozni a bérlőt.
6. Kattintson a **Létrehozás** lehetőségre.
7. Miután létrehozta az Azure AD-bérlőt, válassza a **kattintson ide az új címtár-hivatkozás kezeléséhez** . Az új bérlő nevét a Azure Portal jobb felső sarkában kell megjeleníteni:  

    ![Képernyőkép a bérlő nevét bemutató portálról a jobb felső sarokban][tenantcallout]  

8. Jegyezze fel a *bérlő azonosítóját* , hogy később meg tudja határozni, hová hozza létre az Azure Red Hat OpenShift-fürtöt. A portálon ekkor megjelenik az új bérlő Azure Active Directory áttekintés panelje. Válassza a **Tulajdonságok** lehetőséget, és másolja a **címtár-azonosító** értékét. Erre az értékre `TENANT` az [Azure Red Hat OpenShift-fürt létrehozása](tutorial-create-cluster.md) című oktatóanyagban talál.

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>Források

Az [Azure ad-Bérlővel](../active-directory/develop/quickstart-create-new-tenant.md)kapcsolatos további információkért tekintse meg [Azure Active Directory dokumentációját](../active-directory/index.yml) .

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan hozhat létre egyszerű szolgáltatásnevet, hogyan hozhat létre egy ügyfél-titkos és hitelesítési visszahívási URL-címet, és hogyan hozhat létre új Active Directory felhasználót az Azure Red Hat OpenShift-fürtön futó alkalmazások teszteléséhez.

[Azure AD-alkalmazásobjektum és -felhasználó létrehozása](howto-aad-app-configuration.md)