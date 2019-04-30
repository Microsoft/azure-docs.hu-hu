---
title: Adja ki a sablonokat az Azure API Management |} A Microsoft Docs
description: Ismerje meg, hogyan szabhatja testre a probléma az Azure API Management a fejlesztői portál oldalainak tartalmát.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: c70e88a572bc261ad9d25f9a742b1987d0c19b44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656683"
---
# <a name="issue-templates-in-azure-api-management"></a>A probléma sablonok az Azure API Management szolgáltatásban
Az Azure API Management lehetővé teszi a fejlesztői portál oldalainak konfigurálása a tartalom-sablonok használatával tartalmának testreszabása. Használatával [DotLiquid](http://dotliquidmarkup.org/) szintaxist és a szerkesztő szerkesztőprogramban, például [DotLiquid tervezők számára](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), és a egy megadott készlete honosított [karakterlánc-erőforrások](api-management-template-resources.md#strings), [szimbóluma erőforrások](api-management-template-resources.md#glyphs), és [vezérlők lapon](api-management-page-controls.md), konfigurálhatja a lapok tartalmát, igény szerint ezek a sablonok használatával nagy rugalmasságot biztosít.  
  
 Ebben a szakaszban a sablonok lehetővé teszik a probléma a fejlesztői portál oldalainak tartalmát testreszabása.  
  
-   [A probléma listája](#IssueList)  
  
> [!NOTE]
>  Alapértelmezett mintasablonokat a következő dokumentációban szerepelnek, de módosulhatnak, folyamatos fejlesztései miatt. A fejlesztői portálon az élő alapértelmezett sablont a kívánt egyéni sablonokat lépve tekintheti meg. Sablonok használatának ismertetését lásd: [testreszabása sablonok használatával, az API Management fejlesztői portálon](api-management-developer-portal-templates.md).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="IssueList"></a> A probléma listája  
 A **problémák listája** a sablon lehetővé teszi, hogy a szervezet a probléma lista lap a fejlesztői portál testreszabása.  
  
 ![Adja ki a fejlesztői portál lista](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "APIM probléma lista fejlesztői portálon")  
  
### <a name="default-template"></a>Alapértelmezett sablon  
  
```xml
<div class="row">
  <div class="col-md-9">
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>
  </div>
</div>
<div class="row">
  <div class="col-md-12">
    {% if issues.size > 0 %}
    <ul class="list-unstyled">
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}
      {% assign replaceString0 = '{0}' %}
      {% assign replaceString1 = '{1}' %}
      {% for issue in issues %}
      <li>
        <h3>
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>
        </h3>
        <p>{{issue.description}}</p>
        <em>
          {% capture state %}{{issue.issueState}}{% endcapture %}
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}
          {{ str1 | replace : replaceString1, devName }}
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>
        </em>
      </li>
      {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    {% if canReportIssue %}
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>
    {% elsif isAuthenticated %}
    <hr />
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>
    {% else %}
    <hr />
    <p>
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}
      {{ signIntext | replace : replaceString0, link }}
    </p>
    {% endif %}
  </div>
</div>
```
  
### <a name="controls"></a>Vezérlők  
 A `Issue list` sablon előfordulhat, hogy használja a következő [vezérlők lapon](api-management-page-controls.md).  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Adatmodell  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Issues`|A gyűjtemény [probléma](api-management-template-data-model-reference.md#Issue) entitásokat.|A probléma, az aktuális felhasználó számára látható.|  
|`Paging`|[Lapozás](api-management-template-data-model-reference.md#Paging) entitás.|A lapozófájl információ az alkalmazások a gyűjteményhez.|  
|`IsAuthenticated`|logikai|Hogy az aktuális felhasználó van bejelentkezve a fejlesztői portálra.|  
|`CanReportIssues`|logikai|Az aktuális felhasználó rendelkezik-e be a problémát az engedélyeket.|  
|`Search`|string|Ez a tulajdonság elavult, és nem használható.|  
  
### <a name="sample-template-data"></a>Mintaadatok sablon  
  
```json
{
    "Issues": [
        {
            "Id": "5702b68bb16653124c8f9ba7",
            "ApiId": "570275f1b16653124c8f9ba3",
            "Title": "I couldn't figure out how to connect my application to the API",
            "Description": "I'm having trouble connecting my application to the backend API.",
            "SubscriptionDeveloperName": "Clayton",
            "IssueState": "Proposed",
            "ReportedOn": "2016-04-04T18:46:35.64",
            "Comments": null,
            "Attachments": null,
            "Services": null
        }
    ],
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 1,
        "ShowAll": false,
        "PageCount": 1
    },
    "IsAuthenticated": true,
    "CanReportIssue": true,
    "Search": null
}
```

## <a name="next-steps"></a>További lépések
Sablonok használatának ismertetését lásd: [testreszabása sablonok használatával, az API Management fejlesztői portálon](api-management-developer-portal-templates.md).
