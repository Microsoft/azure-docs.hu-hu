---
title: IT-szolgáltatásmenedzsmenti csatoló – biztonságos exportálás Azure Monitor – Azure-konfigurációk
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure-t úgy, hogy a ITSM-termékek/szolgáltatások a biztonságos exportálással való összekapcsolásához Azure Monitor a ITSM-munkaelemek központilag figyeléséhez
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 8eb9430e3d280c52cf84c61f0a44cb12152ac054
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037540"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>Az Azure konfigurálása a ITSM-eszközök biztonságos exportálással történő összekapcsolásához

Ez a cikk arról nyújt tájékoztatást, hogyan konfigurálhatja az Azure-t a "biztonságos exportálás" használatához.
A "biztonságos exportálás" használatához kövesse az alábbi lépéseket:

1. [Az alkalmazás regisztrálása az Azure AD-ben.](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [Egyszerű szolgáltatásnév definiálása.](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [Hozzon létre egy biztonságos webhook-műveleti csoportot.](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. Konfigurálja a partneri környezetet.
    A biztonságos exportálás a következő ITSM eszközökkel támogatja a kapcsolatokat:
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [BMC Helix](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>Regisztrálás Azure Active Directory

Az alábbi lépéseket követve regisztrálja az alkalmazást az Azure AD-ben:

1. Kövesse az [alkalmazás a Microsoft identitásplatformján való regisztrálását](../../active-directory/develop/quickstart-register-app.md) ismertető cikkben található lépéseket.
2. Az Azure AD-ben válassza az **alkalmazás közzététele** lehetőséget.
3. Adja **meg** az **alkalmazás azonosítójának URI** azonosítóját.

   [![Képernyőkép az I D-alkalmazás U R I értékének beállításáról.](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad.png)](media/itsm-connector-secure-webhook-connections-azure-configuration/azure-ad-expand.png#lightbox)
4. Kattintson a **Mentés** gombra.

## <a name="define-service-principal"></a>Egyszerű szolgáltatásnév megadása

A műveleti csoport szolgáltatás egy első féltől származó alkalmazás, ezért jogosult a hitelesítési jogkivonatok beszerzésére a HRE alkalmazásból a szolgáltatással való hitelesítéshez.
Választható lépésként megadhatja az alkalmazási szerepkört a létrehozott alkalmazás jegyzékfájljában, amely lehetővé teszi a további korlátozást, így csak bizonyos, adott szerepkörrel rendelkező alkalmazások küldhetnek üzeneteket. Ezt a szerepkört ezután hozzá kell rendelni a műveleti csoport egyszerű szolgáltatásához (bérlői rendszergazdai jogosultságok szükségesek).

Ez a lépés ugyanazokkal a PowerShell- [parancsokkal](../alerts/action-groups.md#secure-webhook-powershell-script)végezhető el.

## <a name="create-a-secure-webhook-action-group"></a>Biztonságos webhook műveletcsoport létrehozása

Miután az alkalmazás regisztrálva lett az Azure AD-ben, létrehozhat munkaelemeket a ITSM eszközön az Azure-riasztások alapján, a biztonságos webhook művelettel a műveleti csoportokban.

A műveleti csoportok moduláris és újrahasznosítható módszert biztosítanak az Azure-riasztások műveleteinek elindításához. A műveleti csoportokat metrikus riasztásokkal, műveletnapló riasztásokkal és Azure Log Analytics riasztásokkal is használhatja a Azure Portal.
A műveleti csoportokkal kapcsolatos további információkért lásd: [műveleti csoportok létrehozása és kezelése a Azure Portalban](../alerts/action-groups.md).

Webhook egy művelethez való hozzáadásához kövesse az alábbi utasításokat a biztonságos webhookhoz:

1. A [Azure Portal](https://portal.azure.com/)keresse meg és válassza a **figyelő** elemet. A **figyelő** ablaktábla egyetlen nézetben összesíti az összes figyelési beállítást és az adatait.
2. Válassza a **riasztások**  >  **kezelése műveletek** lehetőséget.
3. Válassza a [műveleti csoport hozzáadása](../alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal)lehetőséget, és töltse ki a mezőket.
4. Írjon be egy nevet a **műveleti csoport neve** mezőbe, és adjon meg egy nevet a **rövid név** mezőben. A rendszer a rövid nevet használja a műveletcsoport teljes neve helyett, amikor értesítéseket küld a csoport használatával.
5. Válassza a **biztonságos webhook** lehetőséget.
6. Válassza ki az alábbi adatokat:
   1. Válassza ki a regisztrált Azure Active Directory példány objektumazonosítóát.
   2. Az URI esetében illessze be a [ITSM eszköz-környezetből](#configure-the-itsm-tool-environment)másolt webhook URL-címét.
   3. Állítsa be **az általános riasztási séma engedélyezése** **beállítást igen** értékre. 

   Az alábbi képen egy minta biztonságos webhook-művelet konfigurációja látható:

   ![A biztonságos webhook műveletet bemutató képernyőkép.](media/itsm-connector-secure-webhook-connections-azure-configuration/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>A ITSM eszköz környezetének konfigurálása

A konfiguráció két lépést tartalmaz:

1. Szerezze be a biztonságos exportálás definíciójának URI azonosítóját.
2. Definíciók a ITSM eszköz folyamata alapján.

## <a name="next-steps"></a>Következő lépések

* [ServiceNow biztonságos exportálási konfiguráció](./itsmc-secure-webhook-connections-servicenow.md)
* [A BMC biztonságos exportálásának konfigurációja](./itsmc-secure-webhook-connections-bmc.md)
