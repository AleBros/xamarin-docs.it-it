---
title: Come il downgrade un pacchetto NuGet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: 2eec947ab748eccd45971054f2f5b232cf788cfa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>Come il downgrade un pacchetto NuGet?

Visual Studio per Mac e Visual Studio hanno caratteristiche per la selezione di versioni precedenti di pacchetti e installarli automaticamente. simile al modo in cui l'aggiornamento di pacchetti. Questi passaggi sono descritti di seguito.

## <a name="visual-studio"></a>Visual Studio
1. Passare a **strumenti > Gestione pacchetti NuGet > Console di gestione pacchetti**
2. Impostare il progetto in **progetto predefinito**
3. Usare questa sintassi:

    > Install-Package [PackageName]-versione [tab per menu versione]

È possibile anche copiare/incollare il comando esatto dalla pagina di NuGet del pacchetto. Esempio di xamarin. Forms: [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac
1. Nel progetto, fare doppio clic su nella cartella pacchetti e selezionare **Aggiungi pacchetti**
2. In searchbar, è possibile utilizzare la sintassi seguente per cercare i pacchetti richiesti:

    `[PackageName] version:*`

### <a name="examples"></a>Esempi 
- Elenca tutti i pacchetti di xamarin. Forms: 

    `Xamarin.Forms version:`
- Elenca tutti i pacchetti di 1.4.x xamarin. Forms: 

    `Xamarin.Forms version:1.4`

*Nota: Se si aggiunge uno spazio tra `version:` e il numero di versione, la ricerca si comporteranno come se è stata specificata alcuna versione.*

