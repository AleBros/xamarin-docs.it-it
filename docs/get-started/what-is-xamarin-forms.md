---
title: Cos'è Xamarin.Forms?
description: Questo articolo presenta Novell. Forms e le librerie correlate.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
ms.openlocfilehash: 8bd530e743330ab8058f13cb7ba09f95a30ee886
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256581"
---
# <a name="what-is-xamarinforms"></a>Cos'è Xamarin.Forms?

[![Screenshot dell'applicazione Novell. Forms di esempio in iOS e Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Novell. Forms è un Framework dell'interfaccia utente open source. Novell. Forms consente agli sviluppatori di compilare applicazioni Android, iOS e Windows da una singola codebase condivisa.

Novell. Forms consente agli sviluppatori di creare interfacce utente in XAML con code- C#behind in. Queste interfacce vengono visualizzate come controlli nativi performanti in ogni piattaforma.

## <a name="who-xamarinforms-is-for"></a>Chi Novell. Forms è per

Novell. Forms è destinata agli sviluppatori con gli obiettivi seguenti:

- Condividi layout e progettazione dell'interfaccia utente tra piattaforme diverse.
- Condividi codice, test e logica di business tra le diverse piattaforme.
- Scrivi app multipiattaforma in C# con Visual Studio.

## <a name="how-xamarinforms-works"></a>Funzionamento di Novell. Forms

![Diagramma dell'architettura di Novell. Forms](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Novell. Forms fornisce un'API coerente per la creazione di elementi dell'interfaccia utente su più piattaforme. Questa API può essere implementata in XAML o C# e supporta l'associazione dati per i modelli, ad esempio Model-View-ViewModel (MVVM).

In fase di esecuzione, Novell. Forms usa i renderer della piattaforma per convertire gli elementi dell'interfaccia utente multipiattaforma in controlli nativi in Android, iOS e UWP. Consente agli sviluppatori di ottenere un aspetto nativo, una sensazione e prestazioni elevate, sfruttando al contempo i vantaggi della condivisione del codice tra le diverse piattaforme.

Le applicazioni Novell. Forms sono in genere costituite da una libreria di .NET Standard condivisa e da singoli progetti di piattaforma. La libreria condivisa contiene XAML o C# viste e qualsiasi logica di business, ad esempio servizi, modelli o altro codice. I progetti della piattaforma contengono la logica specifica della piattaforma o i pacchetti richiesti dall'applicazione.

Novell. Forms utilizza Novell per eseguire applicazioni .NET in modalità nativa tra piattaforme diverse. Per ulteriori informazioni su Novell, vedere [che cos'è Novell?](~/get-started/what-is-xamarin.md).

## <a name="additional-tools"></a>Strumenti aggiuntivi

Novell. Forms include un ampio ecosistema di pacchetti NuGet che aggiungono diverse funzionalità alle applicazioni. Questa sezione descrive alcuni pacchetti NuGet usati di frequente.

### <a name="xamarinessentials"></a>Xamarin.Essentials

Novell. Essentials è una libreria che fornisce API multipiattaforma per le funzionalità native dei dispositivi. Analogamente a Novell, Novell. Essentials è un'astrazione che semplifica il processo di accesso alle utilità native. Di seguito sono riportati alcuni esempi di utilità disponibili in Novell. Essentials:

- Informazioni sul dispositivo
- File system
- Accelerometro
- Dialer telefonico
- Sintesi vocale
- Blocco schermo

Per altre informazioni, vedere [Novell. Essentials](~/essentials/index.md).

### <a name="shell"></a>Shell

La shell Novell. Forms riduce la complessità dello sviluppo di applicazioni per dispositivi mobili fornendo le funzionalità di base richieste dalla maggior parte delle applicazioni. Di seguito sono riportati alcuni esempi di funzionalità fornite dalla shell:

- Esperienza di navigazione comune
- Schema di navigazione basato su URI
- Gestore ricerche integrato

Per altre informazioni, vedere [Shell Novell. Forms](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>Specifiche della piattaforma

Novell. Forms fornisce un'API comune che esegue il rendering di controlli nativi su più piattaforme, ma una piattaforma specifica può avere funzionalità che non esistono in altre piattaforme. Ad esempio, la piattaforma Android dispone di funzionalità native per lo scorrimento rapido in `ListView` un, ma non iOS. Le specifiche della piattaforma Novell. Forms consentono di utilizzare funzionalità disponibili solo in una piattaforma specifica senza creare Renderer o effetti personalizzati.

Novell. Forms include soluzioni predefinite per un'ampia gamma di funzionalità specifiche della piattaforma. Per altre informazioni, vedere:

- [Specifiche della piattaforma Novell. Forms](~/xamarin-forms/platform/platform-specifics/index.md)
- [Specifiche della piattaforma Android](~/xamarin-forms/platform/android/index.md)
- [specifiche della piattaforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Specifiche della piattaforma Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Oggetto visivo Material

Il materiale visivo Novell. Forms viene usato per applicare le regole di progettazione del materiale alle applicazioni Novell. Forms. Novell. Forms Visual Material Usa la proprietà Visual per applicare selettivamente renderer personalizzati all'interfaccia utente, ottenendo un'applicazione con un aspetto coerente tra iOS e Android.

Per altre informazioni, vedere [Visual Material Novell. Forms Visual](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione a Novell. Forms](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Shell di Xamarin.Forms](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Oggetti visivi del materiale Novell. Forms](~/xamarin-forms/user-interface/visual/material-visual.md)
