---
title: Hitelesítés és engedélyezés Azure Service Bus | Microsoft Docs
description: Az alkalmazások hitelesítése megosztott hozzáférés-aláírási (SAS-) hitelesítéssel Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7b287b209fbcd5bc2782505095aeae4390107803
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98060214"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus-hitelesítés és -engedélyezés
A Azure Service Bus-erőforrások hitelesítésének és engedélyezésének két módja van: az Azure Activity Directory (Azure AD) és a közös hozzáférésű aláírások (SAS). Ez a cikk részletesen ismerteti a két típusú biztonsági mechanizmus használatát. 

## <a name="azure-active-directory"></a>Azure Active Directory
Az Azure AD-integráció a Service Bus erőforrásokhoz Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít az ügyfelek erőforrásaihoz való hozzáférés részletes szabályozásához. Az Azure RBAC segítségével engedélyeket adhat egy rendszerbiztonsági tag számára, amely lehet egy felhasználó, egy csoport vagy egy egyszerű alkalmazás. A rendszerbiztonsági tag hitelesítése az Azure AD által OAuth 2,0 token visszaküldésével történik. A token használatával engedélyezhető egy Service Bus erőforrás elérésére irányuló kérés (Üzenetsor, témakör stb.).

Az Azure AD-vel történő hitelesítéssel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Hitelesítés felügyelt identitásokkal](service-bus-managed-service-identity.md)
- [Hitelesítés egy alkalmazásból](authenticate-application.md)

> [!NOTE]
> [Service Bus REST API](/rest/api/servicebus/) támogatja a OAuth-hitelesítést az Azure ad-vel.

> [!IMPORTANT]
> Az Azure AD által visszaadott OAuth 2,0 tokent használó felhasználók vagy alkalmazások engedélyezése kiváló biztonságot és egyszerű használatot biztosít a közös hozzáférésű aláírások (SAS) számára. Az Azure AD-ben nincs szükség a jogkivonatok tárolására a kódban, és kockázatos biztonsági réseket. Azt javasoljuk, hogy ha lehetséges, az Azure AD-t használja a Azure Service Bus alkalmazásaihoz. 

## <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkód
Az [sas-hitelesítés](service-bus-sas.md) lehetővé teszi a felhasználók számára, hogy hozzáférést biztosítson Service Bus erőforrásokhoz adott jogokkal. A Service Bus SAS-hitelesítése egy Service Bus erőforráson társított jogokkal rendelkező titkosítási kulcs konfigurációját foglalja magában. Az ügyfelek ezután egy SAS-token bemutatásával férhetnek hozzá ehhez az erőforráshoz, amely az elérni kívánt erőforrás-URI-t és a beállított kulccsal lejáró lejárati értéket tartalmazza.

Az SAS kulcsait Service Bus névtérben is konfigurálhatja. A kulcs az adott névtéren belüli összes üzenetküldési entitásra vonatkozik. A kulcsokat Service Bus várólistákon és témakörökben is konfigurálhatja. Az SAS [Azure Relay](../azure-relay/relay-authentication-and-authorization.md)is támogatott.

Ha SAS-t szeretne használni, konfigurálhat egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -objektumot egy névtérben, egy várólistán vagy egy témakörben. Ez a szabály a következő elemekből áll:

* *Kulcsnév*: a szabályt azonosítja.
* *PrimaryKey*: a sas-jogkivonatok aláírására/érvényesítésére használt titkosítási kulcs.
* *Értesítésiközpont*: a sas-jogkivonatok aláírására/érvényesítésére használt titkosítási kulcs.
* *Rights*: a megadható **figyelési**, **küldési** vagy **kezelési** jogosultságok gyűjteményét jelöli.

A névtér szintjén konfigurált engedélyezési szabályok hozzáférést biztosíthatnak a névtérben lévő összes entitáshoz a megfelelő kulccsal aláírt tokenekkel rendelkező ügyfelek számára. Egy Service Bus névtérben, várólistán vagy témakörön legfeljebb 12 ilyen engedélyezési szabályt állíthat be. Alapértelmezés szerint minden egyes jogosultsággal rendelkező [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) konfigurálva van minden névtérhez, amikor először kiépítik őket.

Egy entitás eléréséhez az ügyfélnek egy adott [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)létrehozott sas-tokent kell használnia. Az SAS-tokent egy olyan erőforrás-karakterlánc HMAC-SHA256 hozza létre, amely a hozzáférést igényelő erőforrás-URI-t, valamint az engedélyezési szabályhoz társított titkosítási kulccsal lejáró lejáratot tartalmaz.

A Service Bus SAS-hitelesítésének támogatása az Azure .NET SDK 2,0-as és újabb verzióiban érhető el. Az SAS a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)támogatását is támogatja. Minden olyan API-t, amely a paramétert fogadja, az SAS-kapcsolatok karakterláncok támogatását tartalmazza.

> [!IMPORTANT]
> Ha Azure Active Directory Access Controlt (más néven Access Control Service vagy ACS) használ a Service Bushoz, vegye figyelembe, hogy a metódus támogatása már korlátozott, és az alkalmazást az [sas használatára kell telepítenie](service-bus-migrate-acs-sas.md) , vagy a OAuth 2,0 hitelesítést kell használnia az Azure ad-vel (ajánlott). További információ az ACS elavult használatáról: [ebben a blogbejegyzésben](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces).

## <a name="next-steps"></a>Következő lépések
Az Azure AD-vel történő hitelesítéssel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Hitelesítés felügyelt identitásokkal](service-bus-managed-service-identity.md)
- [Hitelesítés alkalmazásból](authenticate-application.md)

Az SAS-hitelesítéssel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Hitelesítés SAS-vel](service-bus-sas.md)
