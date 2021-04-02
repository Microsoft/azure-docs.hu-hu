---
title: Egy Azure AD Domain Services SKU-jának módosítása | Microsoft Docs
description: Megtudhatja, hogyan módosítható a Azure AD Domain Services felügyelt tartomány SKU-szintje, ha az üzleti követelmények megváltoznak
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 320bd87aa78d26cee44c48f27365febd1dd426ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96620290"
---
# <a name="change-the-sku-for-an-existing-azure-active-directory-domain-services-managed-domain"></a>Meglévő Azure Active Directory Domain Services felügyelt tartomány SKU-jának módosítása

Azure Active Directory Domain Services (Azure AD DS) esetében a rendelkezésre álló teljesítmény és szolgáltatások az SKU típusán alapulnak. A szolgáltatásbeli különbségek közé tartozik a biztonsági mentés gyakorisága vagy az egyirányú kimenő erdő megbízhatósági kapcsolatainak maximális száma.

A felügyelt tartomány létrehozásakor ki kell választania egy SKU-t, és az üzleti igényeknek megfelelően át lehet váltani a SKU-ra a felügyelt tartomány telepítése után. Az üzleti követelmények változásai több gyakori biztonsági mentésre vagy további erdőszintű megbízhatósági kapcsolatok létrehozására is igénybe vehetnek. A különböző SKU-i korlátokkal és díjszabással kapcsolatos további információkért lásd az [azure AD DS SKU-fogalmakat][concepts-sku] és az [Azure AD DS díjszabási][pricing] oldalát.

Ez a cikk bemutatja, hogyan módosíthatja az SKU-t egy meglévő Azure AD DS felügyelt tartományhoz a Azure Portal használatával.

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, végezze el az oktatóanyagot [egy felügyelt tartomány létrehozásához és konfigurálásához][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>SKU-változások korlátai

A felügyelt tartomány üzembe helyezése után akár feljebb, akár lejjebb is módosíthatja a SKU-t. Ha azonban erőforrás-erdőt használ, és egyirányú kimenő erdőszintű megbízhatósági kapcsolatot hozott létre az Azure AD DS egy helyszíni AD DS környezetbe, az SKU-változási művelet bizonyos korlátozásokat tartalmaz. A *prémium* és a *vállalati* SKU korlátozza a létrehozható megbízhatósági kapcsolatok számát. Nem válthat olyan SKU-ra, amelynek a maximális korlátja alacsonyabb, mint a jelenleg konfigurálva.

Például:

* Ha két erdőszintű megbízhatósági kapcsolatot hozott létre a *prémium* SKU-ra, akkor nem lehet a *standard* SKU-ra váltani. A *standard* SKU nem támogatja az erdőszintű megbízhatósági kapcsolatot.
* Ha pedig hét megbízhatósági kapcsolatot hozott létre a *prémium* SKU-ra, akkor nem módosítható a *vállalati* SKU-ra. A *vállalati* SKU legfeljebb öt megbízhatósági kapcsolatot támogat.

További információ ezekről a korlátokról: [Azure AD DS SKU-funkciók és korlátozások][concepts-sku].

## <a name="select-a-new-sku"></a>Új SKU kiválasztása

Felügyelt tartomány SKU-jának a Azure Portal használatával történő módosításához hajtsa végre a következő lépéseket:

1. A Azure Portal tetején keresse meg és válassza a **Azure ad Domain Services** lehetőséget. Válassza ki a felügyelt tartományt a listából, például *aaddscontoso.com*.
1. Az Azure AD DS lap bal oldali menüjében válassza a **beállítások > SKU** lehetőséget.

    ![Válassza ki az SKU menüpontot az Azure AD DS felügyelt tartományhoz a Azure Portal](media/change-sku/overview-change-sku.png)

1. A legördülő menüben válassza ki a felügyelt tartományhoz használni kívánt SKU-t. Ha rendelkezik erőforrás-erdővel, a *szabványos* SKU-t nem választhatja erdőszintű megbízhatóságként, csak a *vállalati* SKU-ban vagy annál újabb verzióban.

    Válassza ki a kívánt SKU-t a legördülő menüből, majd válassza a **Mentés** lehetőséget.

    ![Válassza ki a szükséges SKU-t a Azure Portal legördülő menüjében.](media/change-sku/change-sku-selection.png)

Az SKU típusának módosításához egy-két percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

Ha rendelkezik erőforrás-erdővel, és további megbízhatósági kapcsolatokat szeretne létrehozni az SKU módosítása után, tekintse [meg a kimenő erdő megbízhatóságának létrehozása helyszíni tartományhoz az Azure AD DS-ban][create-trust]című témakört.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
