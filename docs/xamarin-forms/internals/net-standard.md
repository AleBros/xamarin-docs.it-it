---
title: .NET standard 2.0 il supporto di xamarin. Forms
description: Questo articolo illustra come convertire un'applicazione xamarin. Forms per l'uso di .NET Standard 2.0. .NET standard è una specifica di API .NET che devono essere disponibili in tutte le implementazioni di .NET.
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: c3e46592bb8760ff85eaeb5dce119897a97dfe89
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293285"
---
# <a name="net-standard-20-support-in-xamarinforms"></a>.NET standard 2.0 il supporto di xamarin. Forms

_Questo articolo illustra come convertire un'applicazione xamarin. Forms per l'uso di .NET Standard 2.0._

.NET standard è una specifica di API .NET che devono essere disponibili in tutte le implementazioni di .NET. Rende più semplice condividere codice tra le applicazioni desktop, App per dispositivi mobili e giochi e servizi cloud, portando le API identiche per le diverse piattaforme. Per informazioni sulle piattaforme supportate da .NET Standard, vedere [supporto di implementazione .NET](/dotnet/standard/net-standard#net-implementation-support).

Librerie .NET standard sono la sostituzione per le librerie di classi portabile (PCL). Tuttavia, una libreria con destinazione .NET Standard è ancora una libreria di classi Portabile e si intende una libreria di classi Portabile .NET Standard basato su. Vengono eseguito il mapping di determinati profili PCL per le versioni di .NET Standard e per i profili che dispone di un mapping, i tipi della due libreria sarà in grado di fare riferimento a loro. Per altre informazioni, vedere [compatibilità PCL](/dotnet/standard/net-standard#pcl-compatibility).

Xamarin. Forms 2.4 consente alle applicazioni xamarin. Forms di destinazione .NET Standard 2.0, sostituendo la libreria di classi Portabile con una libreria .NET Standard 2.0. Ciò può essere ottenuto come segue:

- Assicurarsi [.NET Core 2.0](https://www.microsoft.com/net/download/core) è installato.
- Aggiornare la soluzione xamarin. Forms per l'uso di xamarin. Forms 2.4 o versioni successive.
- Aggiungere una libreria .NET Standard per la soluzione, che ha come destinazione .NET Standard 2.0.
- Eliminare la classe che viene aggiunto alla libreria .NET Standard.
- Aggiungere il pacchetto NuGet di xamarin. Forms 2,4 (o versione successiva) per la libreria .NET Standard.
- Nei progetti di piattaforma, aggiungere un riferimento alla libreria .NET Standard e rimuovere il riferimento al progetto libreria di classi Portabile che contiene la logica dell'interfaccia utente di xamarin. Forms.
- Copiare i file dal progetto libreria di classi Portabile della libreria .NET Standard.
- Rimuovere il progetto libreria di classi Portabile che contiene la logica dell'interfaccia utente di xamarin. Forms.


## <a name="related-links"></a>Collegamenti correlati

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md)
