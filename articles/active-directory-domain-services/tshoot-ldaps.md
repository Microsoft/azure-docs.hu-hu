---
title: A Secure LDAP hibáinak megoldása a Azure AD Domain Servicesban | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani egy Azure Active Directory Domain Services felügyelt tartomány biztonságos LDAP (LDAPs) megoldását
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: dc4a8e6783c699cd81ecab342f5611e564577fad
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91967324"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>A Azure Active Directory Domain Services felügyelt tartományhoz való biztonságos LDAP-kapcsolati problémák elhárítása

A Lightweight Directory Access Protocol (LDAP) protokollt használó alkalmazások és szolgáltatások, amelyekkel a Azure Active Directory Domain Services (Azure AD DS) kommunikálnak, [a biztonságos LDAP használatára konfigurálhatók](tutorial-configure-ldaps.md). A biztonságos LDAP működéséhez meg kell nyitni egy megfelelő tanúsítványt és szükséges hálózati portot.

Ez a cikk segítséget nyújt a biztonságos LDAP-hozzáférésekkel kapcsolatos hibák elhárításához az Azure AD DSban.

## <a name="common-connection-issues"></a>Gyakori csatlakozási hibák

Ha nem sikerül az Azure AD DS felügyelt tartományhoz való kapcsolódás biztonságos LDAP használatával, tekintse át a következő hibaelhárítási lépéseket. Az egyes hibaelhárítási lépések után próbálkozzon újra a felügyelt tartományhoz való kapcsolódással:

* A biztonságos LDAP-tanúsítvány kiállítói láncának megbízhatónak kell lennie az ügyfélen. A megbízhatóság létrehozásához hozzáadhatja a legfelső szintű hitelesítésszolgáltatót (CA) a megbízható főtanúsítvány-tárolóhoz az ügyfélen.
    * Győződjön meg róla, hogy [exportálja és alkalmazza a tanúsítványt az ügyfélszámítógépekre][client-cert].
* Ellenőrizze, hogy a felügyelt tartományhoz tartozó biztonságos LDAP-tanúsítvány rendelkezik-e a DNS-névvel a *tulajdonos* vagy a *tulajdonos alternatív nevek* attribútumában.
    * Tekintse át a [biztonságos LDAP-tanúsítványokra vonatkozó követelményeket][certs-prereqs] , és szükség esetén hozzon létre egy helyettesítő tanúsítványt.
* Győződjön meg arról, hogy az LDAP-ügyfél, például a *ldp.exe* a DNS-név használatával csatlakozik a biztonságos LDAP-végponthoz, nem pedig az IP-címhez.
    * A felügyelt tartományra alkalmazott tanúsítvány nem tartalmazza a szolgáltatás IP-címeit, csak a DNS-neveket.
* Keresse meg azt a DNS-nevet, amelyhez az LDAP-ügyfél csatlakozik. A felügyelt tartományon a biztonságos LDAP-t a nyilvános IP-címen kell feloldani.
    * Ha a DNS-név a belső IP-címhez lett feloldva, frissítse a DNS-rekordot a külső IP-cím feloldásához.
* A külső kapcsolatok esetében a hálózati biztonsági csoportnak tartalmaznia kell egy olyan szabályt, amely engedélyezi az internetről a 636-as TCP-portra irányuló forgalmat.
    * Ha a biztonságos LDAP használatával tud csatlakozni a felügyelt tartományhoz közvetlenül a virtuális hálózathoz csatlakoztatott erőforrásokról, de külső kapcsolatok nélkül, akkor győződjön meg arról, hogy [létrehoz egy hálózati biztonsági csoport szabályt a biztonságos LDAP-forgalom engedélyezéséhez][ldaps-nsg].

## <a name="next-steps"></a>Következő lépések

Ha továbbra is problémákba ütközik, [Nyisson meg egy Azure-támogatási kérést][azure-support] további hibaelhárítási segítségért.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
