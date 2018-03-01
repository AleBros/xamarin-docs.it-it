---
title: "Come è possibile visualizzare le librerie sono supportate in una PCL?"
ms.topic: article
ms.prod: xamarin
ms.assetid: 14FF03BD-AF41-4DB1-B307-2349C13DE7E4
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: e05b15f0a9af7666d635ad375b6c401e95a44525
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="how-can-i-view-what-libraries-are-supported-in-a-pcl"></a>Come è possibile visualizzare le librerie sono supportate in una PCL?

- È possibile trovare una panoramica delle varie funzionalità supportate da varie piattaforme di destinazione PCL sotto il *funzionalità supportate* parte di questa pagina: [http://msdn.microsoft.com/en-us/library/gg597391.aspx](https://msdn.microsoft.com/en-us/library/gg597391.aspx)

- Un'altra opzione consiste nell'utilizzare il [.NET Portability Analyzer](https://visualstudiogallery.msdn.microsoft.com/1177943e-cfb7-4822-a8a6-e56c7905292b) per valutare se la raccolta esistente può essere convertito in un profilo della libreria di classi Portabile.

- Una terzo possibilità consiste nell'esplorare il contenuto del profilo effettivo che è possibile utilizzare. Utilizza profilo 78, ad esempio, potrebbe vedere qui: `C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETPortable\v4.5\Profile\Profile78\` e visualizzare tutti gli assembly all'interno di esso.

Indipendentemente dal metodo scelto,. si noti che alcune funzionalità deve essere scaricato tramite NuGet e la libreria Microsoft BCL.
