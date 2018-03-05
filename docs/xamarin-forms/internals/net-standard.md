---
title: Supporto 2.0 standard .NET in xamarin. Forms
description: In questo articolo viene descritto come convertire un'applicazione di xamarin. Forms per usare .NET 2.0 Standard.
ms.topic: article
ms.prod: xamarin
ms.assetid: 95805355-63a7-44e7-a3c6-6487a6276ab2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 7923ccdf85ffcbbc239df9e9df751f561615baa1
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="net-standard-20-support-in-xamarinforms"></a>Supporto 2.0 standard .NET in xamarin. Forms

_In questo articolo viene descritto come convertire un'applicazione di xamarin. Forms per usare .NET 2.0 Standard._

.NET standard è una specifica di API .NET che sono destinati a essere disponibile in tutte le implementazioni di .NET. Rende più semplice condividere il codice tra le applicazioni desktop e giochi e App per dispositivi mobili e servizi cloud, riportando API identiche per le diverse piattaforme. Per informazioni sulle piattaforme supportate da .NET Standard, vedere [supporto implementazione .NET](/dotnet/standard/net-standard#net-implementation-support/).

Le librerie .NET standard sono la sostituzione per le librerie di classe portabile (PCL). Tuttavia, una libreria che ha come destinazione .NET Standard è ancora una libreria di classi Portabile e viene definita una PCL basato su .NET Standard. Vengono eseguito il mapping di alcuni profili PCL alle versioni di .NET Standard e per i profili che dispone di un mapping, i tipi della due libreria sarà in grado di fare riferimento a loro. Per ulteriori informazioni, vedere [compatibilità PCL](/dotnet/standard/net-standard#pcl-compatibility).

Xamarin. Forms 2.4 consente alle applicazioni di xamarin. Forms di destinazione .NET Standard 2.0 sostituendo la libreria di classi Portabile con una libreria .NET 2.0 Standard. Ciò può essere ottenuto come segue:

- Verificare [2.0 di .NET Core](https://www.microsoft.com/net/download/core) è installato.
- Aggiornare la soluzione xamarin. Forms per l'utilizzo di xamarin. Forms 2.4 o versioni successive.
- Aggiungere una libreria .NET Standard per la soluzione, destinato a .NET 2.0 Standard.
- Eliminare la classe che viene aggiunto alla libreria Standard di .NET.
- Aggiungere il pacchetto NuGet di xamarin. Forms 2.4 (o versione successiva) per la libreria Standard di .NET.
- Nei progetti di piattaforma, aggiungere un riferimento alla libreria Standard di .NET e rimuovere il riferimento al progetto PCL che contiene la logica dell'interfaccia utente di xamarin. Forms.
- Copiare i file dal progetto libreria di classi Portabile della libreria Standard di .NET.
- Rimuovere il progetto libreria di classi Portabile che contiene la logica dell'interfaccia utente di xamarin. Forms.


## <a name="related-links"></a>Collegamenti correlati

- [.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)
- [Opzioni di condivisione del codice](~/cross-platform/app-fundamentals/code-sharing.md)
