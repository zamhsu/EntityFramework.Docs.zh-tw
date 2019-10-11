---
title: 建立模型 - EF6
author: divega
ms.date: 07/05/2018
ms.assetid: 4890228E-CEA1-4595-B8AD-CA81253F8767
ms.openlocfilehash: bd9843a93121f53518a307c9d2d43b68ae03369c
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182469"
---
# <a name="creating-a-model"></a>建立模型

EF 模型會儲存有關應用程式類別和屬性如何對應至資料庫資料表和資料行的詳細資料。 有兩種主要的方式可用來建立 EF 模型：

- **使用 Code First**：開發人員撰寫程式碼以指定模型。 EF 會根據實體類別和開發人員提供的其他模型組態，在執行階段產生模型和對應。

- **使用 EF Designer**：開發人員使用 EF Designer 繪製方塊和線來指定模型。 產生的模型會以 XML 的形式儲存在副檔名為 EDMX 的檔案中。 應用程式的領域物件通常是從概念模型自動產生。

## <a name="ef-workflows"></a>EF 工作流程

這兩種方法可用來將現有資料庫設為目標，或建立新的資料庫，進而產生 4 個不同的工作流程。
請找出哪一個最適合您：  

|                                           | 我只想要撰寫程式碼...                                                                                                                   | 我想要使用設計工具...                                                                                                                        |
|:------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------|
| **我要建立新的資料庫**          | [使用 **Code First** 在程式碼中定義您的模型，然後產生資料庫。](~/ef6/modeling/code-first/workflows/new-database.md)           | [使用 **Model First** 利用方塊和線定義您的模型，然後產生資料庫。](~/ef6/modeling/designer/workflows/model-first.md)   |
| **我需要存取現有資料庫** | [使用 **Code First** 建立對應至現有資料庫的程式碼架構模型。](~/ef6/modeling/code-first/workflows/existing-database.md) | [使用 **Database First** 建立對應至現有資料庫的方塊和線條模型。](~/ef6/modeling/designer/workflows/database-first.md) |

### <a name="watch-the-video-what-ef-workflow-should-i-use"></a>觀賞影片：我應該使用哪個 EF 工作流程？

這個短片說明這些差異，以及如何找出最適合您的工作流程。

**簡報者**：[Rowan Miller](https://romiller.com/)

![哪個工作流程縮圖](../media/whichworkflow-thumb.png) [WMV](https://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_winvideo_ChoseYourWorkflow.wmv) | [MP4](https://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_mp4video_ChoseYourWorkflow.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/8/F/8/8F81F4CD-3678-4229-8D79-0C63FFA3C595/HDI_ITPro_Technet_winvideo_ChoseYourWorkflow.zip)

如果您在觀看影片之後，仍然無法決定是否要使用 EF 設計工具或 Code First，請了解這兩者！

## <a name="a-look-under-the-hood"></a>深入探討

不論您是使用 Code First 還是 EF 設計工具，EF 模型一定會有數個元件：

- 應用程式的定義域物件或實體類型本身。 這通常稱為物件層

- 概念模型由定義域特定實體類型和關聯性組成，使用[實體資料模型](~/ef6/resources/glossary.md#entity-data-model)加以描述。 這一層通常以字母 "C" 來指稱，代表「概念」(_conceptual_)。

- 代表資料庫中定義的資料表、資料行和關聯性的儲存體模型。 這一層通常以字母 "S" 來指稱，代表「儲存體」(_storage_)。  

- 概念模型與資料庫結構描述之間的對應。 此對應通常稱為 "C-S" 對應。

EF 的對應引擎會利用 "C-S" 對應，將針對實體的作業 (例如建立、讀取、更新和刪除)　轉換成針對資料庫中資料表的對等作業。

概念模型和應用程式物件之間的對應通常稱為 "O-C" 對應。 相較於 "C-S" 對應，"O-C" 對應為隱含而且是一對一：概念模型中定義的實體、屬性和關聯性需要符合 .NET 物件的圖形與類型。 從 EF4 和更新版本開始，物件層可以由具有屬性的簡單物件所組成，而不需要對 EF 具有任何相依性。 這些通常稱為簡單的 CLR 物件 (POCO)，類型和屬性的對應則會根據名稱比對慣例執行。 之前，EF 3.5 的物件層中存在特定限制，例如實體必須衍生自 EntityObject 類別，以及必須包含 EF 屬性來實作 "O-C" 對應。
