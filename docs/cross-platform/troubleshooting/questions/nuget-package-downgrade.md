---
title: Come si effettua il downgrade di un pacchetto NuGet?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 2375F833-A630-471E-B8E9-5AD2CB81F264
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 0c70859845915a821bb83b0f9d29528634b1a5de
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73013618"
---
# <a name="how-do-i-downgrade-a-nuget-package"></a>Come si effettua il downgrade di un pacchetto NuGet?

Visual Studio per Mac & Visual Studio includono funzionalità per la selezione di versioni precedenti dei pacchetti e per l'installazione automatica. simile a come funziona l'aggiornamento dei pacchetti. Questi passaggi sono descritti di seguito.

## <a name="visual-studio"></a>Visual Studio

1. Passare a **strumenti > gestione pacchetti NuGet > console di gestione pacchetti**
2. Imposta il progetto nel **progetto predefinito**
3. Usare questa sintassi:

    > Install-Package [PackageName]-Version [scheda per il menu versione]

È anche possibile copiare e incollare il comando esatto dalla pagina NuGet del pacchetto. Esempio per Xamarin.Forms: [https://www.nuget.org/packages/Xamarin.Forms/](https://www.nuget.org/packages/Xamarin.Forms/)

## <a name="visual-studio-for-mac"></a>Visual Studio per Mac

1. Nel progetto fare clic con il pulsante destro del mouse sulla cartella Pacchetti & selezionare **Aggiungi pacchetti**
2. In Searchbar è possibile utilizzare la sintassi seguente per cercare i pacchetti necessari:

    `[PackageName] version:*`

### <a name="examples"></a>Esempi 

- Elenca tutti i pacchetti Xamarin.Forms: 

    `Xamarin.Forms version:`

- Elenca tutti i pacchetti Xamarin.Forms 1.4. x: 

    `Xamarin.Forms version:1.4`

> [!NOTE]
> Se si aggiunge uno spazio tra `version:` & il numero di versione, la ricerca si comporterà come se non fosse stata specificata alcuna versione.
