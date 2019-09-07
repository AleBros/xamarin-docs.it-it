---
title: Supporto di .NET Standard 2,0 in Novell. Forms
description: Questo articolo illustra come convertire un'applicazione Novell. Forms in modo da usare .NET Standard 2,0. .NET Standard è una specifica di API .NET destinate a essere disponibili in tutte le implementazioni di .NET.
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: eaef607e3e6095ccc7dfb1f5831d2384d11cab5f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760052"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>Supporto di .NET Standard 2,0 in Novell. Forms

_Questo articolo illustra come convertire un'applicazione Novell. Forms in modo da usare .NET Standard 2,0._

.NET Standard è una specifica di API .NET destinate a essere disponibili in tutte le implementazioni di .NET. Semplifica la condivisione del codice tra le applicazioni desktop, le app e i giochi per dispositivi mobili e i servizi cloud, offrendo API identiche alle diverse piattaforme. Per informazioni sulle piattaforme supportate da .NET Standard, vedere [supporto dell'implementazione di .NET](/dotnet/standard/net-standard#net-implementation-support).

Le librerie di .NET Standard sono la sostituzione delle librerie di classi portabili (PCL). Tuttavia, una libreria destinata a .NET Standard è ancora una libreria di classi portabile e viene definita PCL basata su .NET Standard. Per alcuni profili PCL viene eseguito il mapping a .NET Standard versioni e per i profili che dispongono di un mapping, i due tipi di libreria saranno in grado di farvi riferimento. Per ulteriori informazioni, vedere [compatibilità PCL](/dotnet/standard/net-standard#pcl-compatibility).

Novell. Forms 2,4 consente alle applicazioni Novell. Forms di essere destinate a .NET Standard 2,0 sostituendo la libreria di classi portabile con una libreria .NET Standard 2,0. Questa operazione può essere eseguita nel modo seguente:

- Verificare che [.NET Core 2,0](https://www.microsoft.com/net/download/core) sia installato.
- Aggiornare la soluzione Novell. Forms in modo da usare Novell. Forms 2,4 o versione successiva.
- Aggiungere una libreria di .NET Standard alla soluzione destinata .NET Standard 2,0.
- Eliminare la classe aggiunta alla libreria .NET Standard.
- Aggiungere il pacchetto NuGet Novell. Forms 2,4 (o versione successiva) alla libreria .NET Standard.
- Nei progetti della piattaforma aggiungere un riferimento alla libreria .NET Standard e rimuovere il riferimento al progetto PCL che contiene la logica dell'interfaccia utente Novell. Forms.
- Copiare i file dal progetto PCL nella libreria .NET Standard.
- Rimuovere il progetto PCL che contiene la logica dell'interfaccia utente Novell. Forms.

## <a name="related-links"></a>Collegamenti correlati

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md)
