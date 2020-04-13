---
title: Cos'è Xamarin.Forms?
description: Questo articolo presenta Xamarin.Forms e le librerie correlate.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
ms.openlocfilehash: aaceb6089a5b7e5f0551dafe9ef1fe50d01433d9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "77374023"
---
# <a name="what-is-xamarinforms"></a>Cos'è Xamarin.Forms?

[![Screenshot dell'applicazione Xamarin.Forms di esempio in iOS e Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin.Forms è un framework di interfaccia utente open source. Xamarin.Forms consente agli sviluppatori di compilare applicazioni Android, iOS e Windows da una singola codebase condivisa.

Xamarin.Forms consente agli sviluppatori di creare interfacce utente in XAML con code-behind in C#. Queste interfacce vengono visualizzate come controlli nativi a prestazioni elevate in ogni piattaforma.

## <a name="who-xamarinforms-is-for"></a>Destinatari di Xamarin.Forms

Xamarin.Forms è destinato agli sviluppatori con gli obiettivi seguenti:

- Condividere il layout e la progettazione dell'interfaccia utente tra le piattaforme.
- Condividere il codice, i test e logica di business tra le piattaforme.
- Scrivere app multipiattaforma in C# con Visual Studio.

## <a name="how-xamarinforms-works"></a>Funzionamento di Xamarin.Forms

![Diagramma dell'architettura di Xamarin.Forms](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin.Forms fornisce un'API coerente per la creazione di elementi dell'interfaccia utente su più piattaforme. Questa API può essere implementata in XAML o C# e supporta il data binding per modelli come MVVM (Model-View-ViewModel).

In fase di esecuzione, Xamarin.Forms utilizza i renderer della piattaforma per convertire gli elementi dell'interfaccia utente multipiattaforma in controlli nativi in Android, iOS e UWP. Ciò consente agli sviluppatori di ottenere l'aspetto e le prestazioni nativi continuando a usufruire dei vantaggi della condivisione del codice tra le piattaforme.

Le applicazioni Xamarin.Forms sono in genere costituite da una libreria .NET Standard condivisa e da singoli progetti di piattaforma. La libreria condivisa contiene le visualizzazioni XAML o C# e la logica di business, ad esempio servizi, modelli o altro codice. I progetti di piattaforma contengono la logica specifica della piattaforma o i pacchetti richiesti dall'applicazione.

Xamarin.Forms usa Xamarin per eseguire le applicazioni .NET in modalità nativa tra piattaforme diverse. Per altre informazioni su Xamarin, vedere [Che cos'è Xamarin?](~/get-started/what-is-xamarin.md)

## <a name="additional-tools"></a>Strumenti aggiuntivi

Xamarin.Forms include un ampio ecosistema di pacchetti NuGet che aggiungono funzionalità diverse alle applicazioni. Questa sezione descrive alcuni pacchetti NuGet usati di frequente.

### <a name="xamarinessentials"></a>Xamarin.Essentials

Xamarin.Essentials è una libreria che fornisce API multipiattaforma per le funzionalità native dei dispositivi. Come Xamarin, Xamarin.Essentials è un'astrazione che semplifica il processo di accesso alle utilità native. Di seguito sono riportati alcuni esempi di utilità disponibili in Xamarin.Essentials:

- Informazioni dispositivo
- File system
- Accelerometro
- Dialer telefono
- Sintesi vocale
- Blocco dello schermo

Per altre informazioni, vedere [Xamarin.Essentials](~/essentials/index.md).

### <a name="shell"></a>Shell

La shell di Xamarin.Forms riduce la complessità dello sviluppo di applicazioni per dispositivi mobili offrendo le principali funzionalità richieste dalla maggior parte delle applicazioni. Di seguito sono riportati alcuni esempi di funzionalità disponibili nella shell:

- Esperienza di navigazione comune
- Schema di navigazione basato su URI
- Gestore integrato per la ricerca

Per altre informazioni, vedere [Shell di Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

Xamarin.Forms fornisce un'API comune che esegue il rendering dei controlli nativi su più piattaforme, ma una piattaforma specifica può avere funzionalità che non esistono in altre piattaforme. La piattaforma Android, ad esempio, include funzionalità native per lo scorrimento rapido in un elemento `ListView`, non disponibili in iOS. Le funzionalità specifiche della piattaforma di Xamarin.Forms consentono di utilizzare funzionalità disponibili solo in una piattaforma specifica senza creare renderer o effetti personalizzati.

Xamarin.Forms include soluzioni predefinite per un'ampia gamma di funzionalità specifiche delle piattaforme. Per altre informazioni, vedere:

- [Funzionalità specifiche delle piattaforme di Xamarin.Forms](~/xamarin-forms/platform/platform-specifics/index.md)
- [Funzionalità specifiche della piattaforma Android](~/xamarin-forms/platform/android/index.md)
- [Funzionalità specifiche della piattaforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Funzionalità specifiche della piattaforma Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Oggetto visivo Material

L'oggetto visivo Material di Xamarin.Forms viene usato per applicare le regole di Material Design alle applicazioni Xamarin.Forms. L'oggetto visivo Material di Xamarin.Forms utilizza la proprietà Visual per applicare selettivamente i renderer personalizzati all'interfaccia utente, generando un'applicazione con un aspetto coerente in iOS e in Android.

Per altre informazioni, vedere [Oggetto visivo Material di Xamarin.Forms](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a Xamarin.Forms](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Shell di Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Oggetto visivo Material di Xamarin.Forms](~/xamarin-forms/user-interface/visual/material-visual.md)
