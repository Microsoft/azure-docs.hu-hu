---
title: Azure Service Bus hitelesítési és engedélyezési | Microsoft Docs
description: Alkalmazások hitelesítése Service Bus közös hozzáférésű jogosultság aláírása (SAS) hitelesítéssel.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: ccb526abd99be50e33c8adb918186944b7af3bd6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516654"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus-hitelesítés és -engedélyezés
Kétféleképpen hitelesítheti és engedélyezheti az erőforrásokhoz való Azure Service Bus: Azure Activity Directory (Azure AD) és közös hozzáférésű jogosultságok (SAS). Ez a cikk a két biztonsági mechanizmustípus használatának részleteit tartalmazza. 

## <a name="azure-active-directory"></a>Azure Active Directory
A Service Bus Azure AD-integrációja Azure szerepköralapú hozzáférés-vezérlést (RBAC) biztosít az ügyfelek erőforrásokhoz való hozzáférésének részletes szabályozására. Az Azure RBAC használatával engedélyeket adhat egy rendszerbiztonsági tagnak, amely lehet felhasználó, csoport vagy alkalmazás-szolgáltatásnév. Az Azure AD hitelesíti a rendszerbiztonsági biztonsági adatokat, hogy OAuth 2.0-jogkivonatot adjanak vissza. A jogkivonattal engedélyezheti, hogy egy kérés hozzáférjen egy Service Bus erőforráshoz (üzenetsorhoz, témakörhez stb.).

Az Azure AD-val való hitelesítésről a következő cikkekben talál további információt:

- [Hitelesítés felügyelt identitásokkal](service-bus-managed-service-identity.md)
- [Hitelesítés egy alkalmazásból](authenticate-application.md)

> [!NOTE]
> [Service Bus REST API](/rest/api/servicebus/) támogatja az Azure AD-val való OAuth-hitelesítést.

> [!IMPORTANT]
> A felhasználók és alkalmazások Azure AD által visszaadott OAuth 2.0-jogkivonattal való hitelesítése kiváló biztonságot és könnyű használatot biztosít a közös hozzáférésű jogosultságok (SAS) esetében. Az Azure AD-val nem kell a kódban tárolni a jogkivonatokat, és biztonsági réseket kockáztatni. Javasoljuk, hogy amikor csak lehetséges, használja az Azure AD-t Azure Service Bus alkalmazásokkal. 

## <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkód
[Az SAS-hitelesítés](service-bus-sas.md) lehetővé teszi, hogy egy felhasználónak hozzáférést adjon Service Bus erőforrásokhoz, meghatározott jogosultságokkal. A sas-Service Bus magában foglalja egy titkosítási kulcs konfigurálásán, társított jogosultságokkal egy Service Bus erőforráson. Az ügyfelek ezután hozzáférést kaphatnak az erőforráshoz egy SAS-jogkivonat bemutatja, amely az elért erőforrás URI-ját és a konfigurált kulccsal aláírt lejáratot tartalmazza.

Az SAS-kulcsok egy új Service Bus konfigurálhatóak. A kulcs az adott névtéren belüli összes üzenetkezelési entitásra vonatkozik. A kulcsokat az üzenetsorok és témakörök Service Bus is konfigurálhatja. A SAS a -ben is [Azure Relay.](../azure-relay/relay-authentication-and-authorization.md)

Az SAS használata esetén konfigurálhat egy megosztott hozzáférésű engedélyezési szabályt egy névtérre, üzenetsorra vagy témakörre. Ez a szabály a következő elemekből áll:

* *KeyName*: azonosítja a szabályt.
* *PrimaryKey:* SAS-jogkivonatok aláírásra/ellenőrzésre használt titkosítási kulcs.
* *SecondaryKey:* az SAS-jogkivonatok aláíráshoz/ellenőrzéshez használt titkosítási kulcs.
* *Jogosultságok:* a **figyelési,** küldési és kezelés **jogosultságok** gyűjteményét jelöli.

A névtér szintjén konfigurált engedélyezési szabályok hozzáférést adhatnak a névtér összes entitása számára a megfelelő kulccsal aláírt jogkivonatokat használó ügyfelek számára. Egy adott névtéren, üzenetsoron vagy témakörben legfeljebb 12 ilyen engedélyezési Service Bus konfigurálhat. Alapértelmezés szerint az összes jogosultsággal konfigurálva van egy megosztott hozzáférésű engedélyezési szabály minden névtérhez az első építéskor.

Egy entitás eléréséhez az ügyfélnek egy adott közös hozzáférésű engedélyezési szabály használatával létrehozott SAS-jogkivonatra van szüksége. Az SAS-jogkivonatot a rendszer egy olyan erőforrás-sztring HMAC-SHA256 használatával állítja elő, amely a hozzáférést igénylő erőforrás URI-ját, valamint az engedélyezési szabályhoz társított titkosítási kulccsal való lejáratot tartalmazza.

Az Azure .NET SDK 2.0 Service Bus-s és újabb verziói tartalmazzák az SAS-hitelesítés támogatását a Service Bus esetén. Az SAS támogatja a közös hozzáférésű engedélyezési szabályokat. A kapcsolati sztringet paraméterként elfogadó összes API támogatja az SAS-kapcsolati sztringeket.

> [!IMPORTANT]
> Ha az Azure Active Directory Access Control-t (más néven Access Control Service vagy ACS-t) használja az Service Bus-val, vegye figyelembe, hogy ennek a módszernek a támogatása jelenleg korlátozott, és az alkalmazást át kell minomálnia [SAS](service-bus-migrate-acs-sas.md) használatára, vagy OAuth 2.0-hitelesítést kell használnia az Azure AD-val (ajánlott). Az ACS elalasztásról ebben a [blogbejegyzésben talál további információt.](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces)

## <a name="next-steps"></a>Következő lépések
Az Azure AD-val való hitelesítésről a következő cikkekben talál további információt:

- [Hitelesítés felügyelt identitásokkal](service-bus-managed-service-identity.md)
- [Hitelesítés alkalmazásból](authenticate-application.md)

Az SAS-hitelesítéssel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Hitelesítés SAS-sel](service-bus-sas.md)
