---
title: Xamarin.Mac - Documentazione correlata
description: 'Questo documento contiene i collegamenti alla documentazione pertinente per gli sviluppatori Xamarin.Mac: documentazione di Xamarin.iOS, Mac Dev Center di Apple e diverse guide che descrivono come compilare interfacce utente con Xamarin.Mac.'
ms.prod: xamarin
ms.assetid: 0a282c58-1c37-4f73-8440-85de2daf454a
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 12/02/2016
ms.openlocfilehash: 3b0e9f87505e7537f9c8aeb6f006072feffdbd57
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278903"
---
# <a name="xamarinmac-related-documentation"></a>Xamarin.Mac - Documentazione correlata

Oltre alla sezione per Mac di [Microsoft Docs](~/mac/get-started/index.md) sono disponibili tre fonti di documentazione che possono rivelarsi utili in caso di domande su Xamarin.Mac:

- [**Xamarin.iOS documentation**](~/ios/get-started/index.md) (Documentazione di Xamarin.iOS): per molte API, soprattutto al di fuori di AppKit/UIKit, le differenze tra le versioni di iOS e macOS sono minime. In molti casi per un'API iOS con nome `UIFoo`, in macOS si può trovare un'API denominata `NSFoo`. Questi esempi si trovano in genere già in C#.

- [Mac Dev Center](https://developer.apple.com/devcenter/mac/) di **Apple**: spesso un esempio di quali API chiamare in Objective-C può essere convertito in C# in modo semplice. Per altri dettagli su come eseguire questa operazione, vedere [Understanding Mac APIs](~/mac/app-fundamentals/mac-apis.md) (Informazioni sulle API Mac).

- [**Stack Overflow**](https://stackoverflow.com/): un'ottima risorsa per trovare domande e risposte dirette, ad esempio ["Come espandere automaticamente tutti i nodi in un elemento NSOutlineView"](https://stackoverflow.com/questions/519751/nsoutlineview-auto-expand-all-nodes). Questi esempi sono spesso in Objective-C e devono essere convertiti in C#, ma è presente anche un subset delle risposte in C#.

## <a name="user-interface"></a>Interfaccia utente

Quando si lavora in C# e .NET in un'applicazione Xamarin.Mac, lo sviluppatore ha accesso agli stessi controlli dell'interfaccia utente di uno sviluppatore che lavora in *Objective-C* e *Xcode*. Poiché Xamarin.Mac si integra direttamente con Xcode, lo sviluppatore può usare _Interface Builder_ di Xcode per creare e gestire le interfacce utente di un'app, oppure crearle direttamente in codice C#.

Le guide elencate di seguito offrono informazioni dettagliate sull'uso di elementi macOS in un'applicazione Xamarin.Mac:

- [Windows](~/mac/user-interface/window.md)
- [Dialogs](~/mac/user-interface/dialog.md) (Finestre di dialogo)
- [Alerts](~/mac/user-interface/alert.md) (Avvisi)
- [Menu](~/mac/user-interface/menu.md)
- [Barre degli strumenti](~/mac/user-interface/toolbar.md)
- [Table Views](~/mac/user-interface/table-view.md) (Visualizzazioni tabelle)
- [Outline Views](~/mac/user-interface/outline-view.md) (Visualizzazioni struttura)
- [Source Lists](~/mac/user-interface/source-list.md) (Elenchi di risorse)
