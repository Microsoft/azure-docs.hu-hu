---
title: A replika-készletek fogalmai a Azure AD Domain Serviceshoz | Microsoft Docs
description: Megtudhatja, hogy mely replikák vannak a Azure Active Directory Domain Servicesban, és hogyan biztosítanak redundanciát az Identity Services szolgáltatást igénylő alkalmazások számára.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: justinha
ms.openlocfilehash: 8eb1560887c08c3f64fa599c39e5577242d2a1e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689062"
---
# <a name="replica-sets-concepts-and-features-for-azure-active-directory-domain-services"></a>A replika a Azure Active Directory Domain Services kapcsolatos fogalmakat és szolgáltatásokat tartalmazza

Azure Active Directory Domain Services (Azure AD DS) felügyelt tartomány létrehozásakor egyedi névteret határozhat meg. Ez a névtér a tartománynevet, például a *aaddscontoso.com* és két tartományvezérlőt (DCS) helyezi üzembe a kiválasztott Azure-régióban. A tartományvezérlők ezt a központi telepítését replikakészlet néven nevezzük.

A felügyelt tartományokat kiterjesztheti úgy, hogy az Azure AD-bérlőn belüli több replikát is hozzon létre. A kópiakészlet bármely olyan Azure-régióban használható, amely támogatja az Azure AD DS-t. A különböző Azure-régiókban található további replikák földrajzi vész-helyreállítást biztosítanak az örökölt alkalmazásokhoz, ha egy Azure-régió offline állapotba kerül.

> [!NOTE]
> A replikák nem lehetővé teszik több egyedi felügyelt tartomány központi telepítését egyetlen Azure-bérlőben. Mindegyik replikakészlet ugyanazokat az adathalmazokat tartalmazza.

## <a name="how-replica-sets-work"></a>A replika-készletek működése

A felügyelt tartományok, például a *aaddscontoso.com* létrehozásakor létrejön egy kezdeti replikakészlet. A további replika-készletek ugyanazt a névteret és konfigurációt használják. Az Azure AD DS módosításai, beleértve a konfigurációt, a felhasználói identitást és a hitelesítő adatokat, a csoportokat, a csoportházirend-objektumokat, a számítógép-objektumokat és az egyéb módosításokat a felügyelt tartomány összes másodpéldányára alkalmazza AD DS replikáció használatával.

Az egyes replikákat egy virtuális hálózatban hozza létre. Minden virtuális hálózatot a felügyelt tartomány replikáját futtató összes többi virtuális hálózatnak meg kell adni. Ez a konfiguráció létrehoz egy háló hálózati topológiát, amely támogatja a címtár-replikációt. A virtuális hálózatok több replikát is támogatnak, ha mindegyik replikakészlet más virtuális alhálózatban található.

Az összes replikakészlet ugyanahhoz a Active Directory-helyhez kerül. Ennek eredményeképpen a rendszer az összes módosítást a helyen belüli replikáció használatával propagálja a gyors konvergenciához.

> [!NOTE]
> Nem lehet külön helyeket definiálni, és replikációs beállításokat definiálni a replikák között.

A következő ábrán egy felügyelt tartomány látható két replikával. Az első replikakészlet a tartományi névtérrel lett létrehozva. A rendszer a következő után hozza létre a másodpéldányt:

![Példa felügyelt tartomány diagramja két replikával](./media/concepts-replica-sets/two-replica-set-example.png)

> [!NOTE]
> A replika-készletek biztosítják a hitelesítési szolgáltatások rendelkezésre állását azokon a régiókban, amelyekben egy replikakészlet van beállítva. Ahhoz, hogy egy alkalmazás földrajzi redundancia legyen, ha van regionális leállás, a felügyelt tartományon alapuló alkalmazás-platformnak a másik régióban is szerepelnie kell.
>
> Az alkalmazás működéséhez szükséges egyéb szolgáltatások, például az Azure-beli virtuális gépek vagy az Azure-App Services rugalmassága nem a replikák által biztosított. Az egyéb alkalmazás-összetevők rendelkezésre állási tervének figyelembe kell vennie az alkalmazást alkotó szolgáltatások rugalmassági funkcióit.

Az alábbi példa egy felügyelt tartományt mutat be három replikával a rugalmasság további biztosításához és a hitelesítési szolgáltatások rendelkezésre állásának biztosításához. Mindkét példában az alkalmazás munkaterhelései ugyanabban a régióban találhatók, mint a felügyelt tartományi replika:

![Példaként szolgáló felügyelt tartomány ábrázolása három replikával](./media/concepts-replica-sets/three-replica-set-example.png)

## <a name="deployment-considerations"></a>Telepítési szempontok

A felügyelt tartományokhoz tartozó alapértelmezett SKU a *vállalati* SKU, amely több replikát is támogat. Ha a *standard* SKU-ra módosította a további replikákat, [frissítse a felügyelt tartományt](change-sku.md) *nagyvállalati* vagy *prémium* szintűre.

A replikakészlet-készletek maximális száma négy, beleértve a felügyelt tartomány létrehozásakor létrehozott első replikát is.

Az egyes replikák számlázása a tartományi konfigurációs SKU alapján történik. Ha például olyan felügyelt tartománya van, amely a *vállalati* SKU-t használja, és három replikakészlet van, akkor az előfizetése óránként lesz elszámolva mindhárom replikakészlet esetében.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="can-i-create-a-replica-set-in-subscription-different-from-my-managed-domain"></a>Létrehozhatok olyan replikát az előfizetésben, amely eltér a felügyelt tartománytól?

Nem. A replikának a felügyelt tartományhoz tartozó előfizetésben kell lennie.

### <a name="how-many-replica-sets-can-i-create"></a>Hány replikakészlet hozható létre?

Legfeljebb négy replikakészlet hozható létre – a felügyelt tartomány kezdeti replikája, valamint három további replikakészlet is.

### <a name="how-does-user-and-group-information-get-synchronized-to-my-replica-sets"></a>Hogyan történik a felhasználók és a csoportok adatainak szinkronizálása a saját replikákkal?

Az összes replikakészlet a háló virtuális hálózati társításával csatlakozik egymáshoz. Az egyik replikakészlet az Azure AD-ből fogadja a felhasználók és a csoportok frissítéseit. Ezeket a módosításokat a rendszer replikálja a többi replikára a helyen lévő AD DS replikáció használatával a meglevő hálózaton keresztül.

A helyszíni AD DShoz hasonlóan a kiterjesztett leválasztott állapot is zavart okozhat a replikálásban. Mivel a kiszolgált virtuális hálózatok nem tranzitívak, a replikák tervezési követelményeinek teljes mértékben rácsvonal hálózati topológiára van szükségük.

### <a name="how-do-i-make-changes-in-my-managed-domain-after-i-have-replica-sets"></a>A replikák beállítása után Hogyan módosításokat végezni a felügyelt tartományban?

A felügyelt tartományon belüli változások ugyanúgy működnek, mint korábban. [Hozzon létre és használjon felügyeleti virtuális gépet a felügyelt tartományhoz csatlakoztatott RSAT-eszközökkel](tutorial-create-management-vm.md). A felügyelt tartományhoz tetszőleges számú felügyeleti virtuális gépet csatlakoztathat.

## <a name="next-steps"></a>Következő lépések

A replikakészlet megkezdéséhez [hozzon létre és konfiguráljon egy Azure AD DS felügyelt tartományt][tutorial-create-advanced]. Üzembe helyezéskor [hozzon létre és használjon további replikákat][create-replica-set].

<!-- LINKS - INTERNAL -->
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-replica-set]: tutorial-create-replica-set.md
