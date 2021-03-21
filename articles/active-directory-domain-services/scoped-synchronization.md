---
title: Hatókörön belüli szinkronizálás a Azure AD Domain Serviceshoz | Microsoft Docs
description: Ismerje meg, hogyan konfigurálható a Azure Portal az Azure AD hatókörön belüli szinkronizálásának Azure Active Directory Domain Services felügyelt tartományhoz való konfigurálásához
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34692f5e563e4931a27ea59db84d9c88f27817da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98660898"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>Hatókörön belüli szinkronizálás konfigurálása az Azure AD-ből Azure Active Directory Domain Services a Azure Portal használatával

A hitelesítési szolgáltatások biztosításához Azure Active Directory Domain Services (Azure AD DS) szinkronizálja a felhasználókat és a csoportokat az Azure AD-ből. Hibrid környezetben a helyszíni Active Directory Domain Services (AD DS) környezet felhasználóit és csoportjait először szinkronizálhatja az Azure AD-vel az Azure AD Connect használatával, majd szinkronizálhatja az Azure AD DS felügyelt tartományával.

Alapértelmezés szerint az Azure AD-címtárból származó összes felhasználó és csoport egy felügyelt tartományba van szinkronizálva. Ha konkrét igényei vannak, ehelyett csak a megadott felhasználók szinkronizálását lehet választani.

Ez a cikk bemutatja, hogyan konfigurálhatja a hatókörön belüli szinkronizálást, majd módosíthatja vagy letilthatja a hatókörrel rendelkező felhasználók csoportját a Azure Portal használatával. [Ezeket a lépéseket a PowerShell használatával][scoped-sync-powershell]is elvégezheti.

## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Aktív Azure-előfizetés.
    * Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az előfizetéshez társított Azure Active Directory bérlő, vagy egy helyszíni címtárral vagy egy csak felhőalapú címtárral van szinkronizálva.
    * Ha szükséges, [hozzon létre egy Azure Active Directory bérlőt][create-azure-ad-tenant] , vagy [rendeljen hozzá egy Azure-előfizetést a fiókjához][associate-azure-ad-tenant].
* Egy Azure Active Directory Domain Services felügyelt tartomány engedélyezve és konfigurálva van az Azure AD-bérlőben.
    * Ha szükséges, fejezze be az oktatóanyagot [egy Azure Active Directory Domain Services felügyelt tartomány létrehozásához és konfigurálásához][tutorial-create-instance].
* Az Azure AD DS szinkronizálási hatókörének módosításához *globális rendszergazdai* jogosultságok szükségesek az Azure ad-bérlőben.

## <a name="scoped-synchronization-overview"></a>Hatókörön belüli szinkronizálás – áttekintés

Alapértelmezés szerint az Azure AD-címtárból származó összes felhasználó és csoport egy felügyelt tartományba van szinkronizálva. Ha csak néhány felhasználónak kell hozzáférnie a felügyelt tartományhoz, csak ezeket a felhasználói fiókokat lehet szinkronizálni. Ez a hatókörön belüli szinkronizálás csoport alapú. A csoport alapú hatókörű szinkronizálás konfigurálásakor a rendszer csak a megadott csoportokhoz tartozó felhasználói fiókokat szinkronizálja a felügyelt tartományba. Beágyazott csoportok nincsenek szinkronizálva, csak a kiválasztott csoportok.

A szinkronizálási hatókört a felügyelt tartomány létrehozása előtt vagy után is módosíthatja. A szinkronizálás hatókörét egy egyszerű szolgáltatásnév definiálja az alkalmazás-azonosító 2565bd9d-DA50-47d4-8B85-4c97f669dc36. A hatókör elvesztésének megelőzése érdekében ne törölje vagy módosítsa a szolgáltatásnevet. Ha véletlenül törölve van, a szinkronizálási hatókör nem állítható helyre. 

Tartsa szem előtt az alábbi figyelmeztetéseket, ha módosítja a szinkronizálási hatókört:

- Teljes szinkronizálás történik.
- A felügyelt tartományban már nem szükséges objektumok törlődnek. Az új objektumok a felügyelt tartományban jönnek létre.

További információ a szinkronizálási folyamatról: a [Azure ad Domain Services szinkronizálásának megismerése][concepts-sync].

## <a name="enable-scoped-synchronization"></a>Hatókörön belüli szinkronizálás engedélyezése

A hatókörön belüli szinkronizálás engedélyezéséhez a Azure Portal hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services** lehetőséget. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*.
1. Válassza a **szinkronizálás** lehetőséget a bal oldali menüben.
1. A *szinkronizálás típusa* beállításnál válassza a **hatókört**.
1. Válassza a **csoportok kiválasztása** lehetőséget, majd keresse meg és válassza ki a hozzáadni kívánt csoportokat.
1. Ha minden módosítás történik, válassza a **szinkronizálási hatókör mentése** lehetőséget.

A szinkronizálás hatókörének módosítása azt eredményezi, hogy a felügyelt tartomány újraszinkronizálja az összes adatot. A felügyelt tartományban már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszabb ideig is eltarthat.

## <a name="modify-scoped-synchronization"></a>Hatókörön belüli szinkronizálás módosítása

Az alábbi lépések végrehajtásával módosíthatja azon csoportok listáját, amelyek felhasználóit szinkronizálni szeretné a felügyelt tartományba:

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services** lehetőséget. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*.
1. Válassza a **szinkronizálás** lehetőséget a bal oldali menüben.
1. Csoport hozzáadásához válassza a felül a **+ csoportok kiválasztása** lehetőséget, majd válassza ki a hozzáadni kívánt csoportokat.
1. Ha el szeretne távolítani egy csoportot a szinkronizálási hatókörből, válassza ki a jelenleg szinkronizált csoportok listájából, majd válassza a **csoportok eltávolítása** lehetőséget.
1. Ha minden módosítás történik, válassza a **szinkronizálási hatókör mentése** lehetőséget.

A szinkronizálás hatókörének módosítása azt eredményezi, hogy a felügyelt tartomány újraszinkronizálja az összes adatot. A felügyelt tartományban már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszabb ideig is eltarthat.

## <a name="disable-scoped-synchronization"></a>Hatókörön belüli szinkronizálás letiltása

A csoport alapú hatókörű szinkronizálás egy felügyelt tartományon való letiltásához hajtsa végre a következő lépéseket:

1. A Azure Portal keresse meg és válassza a **Azure ad Domain Services** lehetőséget. Válassza ki a felügyelt tartományt, például *aaddscontoso.com*.
1. Válassza a **szinkronizálás** lehetőséget a bal oldali menüben.
1. Módosítsa a *szinkronizálás típusát* a **hatókörből** az **összesre**, majd válassza a **szinkronizálási hatókör mentése** lehetőséget.

A szinkronizálás hatókörének módosítása azt eredményezi, hogy a felügyelt tartomány újraszinkronizálja az összes adatot. A felügyelt tartományban már nem szükséges objektumok törlődnek, és az újraszinkronizálás hosszabb ideig is eltarthat.

## <a name="next-steps"></a>Következő lépések

További információ a szinkronizálási folyamatról: a [Azure ad Domain Services szinkronizálásának megismerése][concepts-sync].

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
