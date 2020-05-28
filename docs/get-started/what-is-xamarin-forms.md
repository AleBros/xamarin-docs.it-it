---
title: Che cos'è Xamarin.Forms ?
description: Questo articolo presenta Xamarin.Forms le librerie correlate.
ms.prod: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b65be3cd552cf6a4fb094d59c44fa43de3deddcd
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84139191"
---
# <a name="what-is-xamarinforms"></a>Che cos'è Xamarin.Forms ?

[![Screenshot dell'applicazione di esempio Xamarin.Forms in iOS e Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin.Formsè un Framework dell'interfaccia utente open source. Xamarin.Formsconsente agli sviluppatori di compilare applicazioni Android, iOS e Windows da una singola codebase condivisa.

Xamarin.Formsconsente agli sviluppatori di creare interfacce utente in XAML con code-behind in C#. Queste interfacce vengono visualizzate come controlli nativi a prestazioni elevate in ogni piattaforma.

## <a name="who-xamarinforms-is-for"></a>Xamarin.FormsPer chi è

Xamarin.Formsper gli sviluppatori con gli obiettivi seguenti:

- Condividere il layout e la progettazione dell'interfaccia utente tra le piattaforme.
- Condividere il codice, i test e logica di business tra le piattaforme.
- Scrivere app multipiattaforma in C# con Visual Studio.

## <a name="how-xamarinforms-works"></a>Funzionamento Xamarin.Forms

![Xamarin.Formsdiagramma dell'architettura](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin.Formsfornisce un'API coerente per la creazione di elementi dell'interfaccia utente su più piattaforme. Questa API può essere implementata in XAML o C# e supporta il data binding per modelli come MVVM (Model-View-ViewModel).

In fase di esecuzione, Xamarin.Forms Usa i renderer della piattaforma per convertire gli elementi dell'interfaccia utente multipiattaforma in controlli nativi in Android, iOS e UWP. Ciò consente agli sviluppatori di ottenere l'aspetto e le prestazioni nativi continuando a usufruire dei vantaggi della condivisione del codice tra le piattaforme.

Xamarin.Formsle applicazioni sono in genere costituite da una libreria di .NET Standard condivisa e da singoli progetti di piattaforma. La libreria condivisa contiene le visualizzazioni XAML o C# e la logica di business, ad esempio servizi, modelli o altro codice. I progetti di piattaforma contengono la logica specifica della piattaforma o i pacchetti richiesti dall'applicazione.

Xamarin.FormsUSA Novell per eseguire applicazioni .NET in modalità nativa tra piattaforme diverse. Per altre informazioni su Xamarin, vedere [Che cos'è Xamarin?](~/get-started/what-is-xamarin.md)

## <a name="additional-tools"></a>Strumenti aggiuntivi

Xamarin.Formsdispone di un ampio ecosistema di pacchetti NuGet che aggiungono diverse funzionalità alle applicazioni. Questa sezione descrive alcuni pacchetti NuGet usati di frequente.

### Xamarin.Essentials

Xamarin.Essentialsè una libreria che fornisce API multipiattaforma per le funzionalità native dei dispositivi. Analogamente a Novell, Xamarin.Essentials è un'astrazione che semplifica il processo di accesso alle utilità native. Di seguito sono riportati alcuni esempi di utilità disponibili in Xamarin.Essentials :

- Informazioni dispositivo
- File system
- Accelerometro
- Dialer telefono
- Sintesi vocale
- Blocco dello schermo

Per altre informazioni, vedere [Xamarin.Essentials](~/essentials/index.md).

### <a name="shell"></a>Shell

Xamarin.FormsShell riduce la complessità dello sviluppo di applicazioni per dispositivi mobili fornendo le funzionalità fondamentali necessarie per la maggior parte delle applicazioni. Di seguito sono riportati alcuni esempi di funzionalità disponibili nella shell:

- Esperienza di navigazione comune
- Schema di navigazione basato su URI
- Gestore integrato per la ricerca

Per ulteriori informazioni, vedere [ Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/index.md) .

### <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

Xamarin.Formsfornisce un'API comune che esegue il rendering di controlli nativi su più piattaforme, ma una piattaforma specifica può avere funzionalità che non esistono in altre piattaforme. La piattaforma Android, ad esempio, include funzionalità native per lo scorrimento rapido in un elemento `ListView`, non disponibili in iOS. Xamarin.Formsle specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo in una piattaforma specifica senza creare Renderer o effetti personalizzati.

Xamarin.Formsinclude soluzioni predefinite per un'ampia gamma di funzionalità specifiche della piattaforma. Per altre informazioni, vedere:

- [Xamarin.Formsspecifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md)
- [Funzionalità specifiche della piattaforma Android](~/xamarin-forms/platform/android/index.md)
- [Funzionalità specifiche della piattaforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Funzionalità specifiche della piattaforma Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Oggetto visivo Material

Xamarin.FormsMaterial Visual viene usato per applicare le regole di progettazione del materiale alle Xamarin.Forms applicazioni. Xamarin.FormsMaterial Visual usa la proprietà Visual per applicare selettivamente renderer personalizzati all'interfaccia utente, ottenendo un'applicazione con un aspetto coerente tra iOS e Android.

Per altre informazioni, vedere [ Xamarin.Forms Material Visual](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Collegamenti correlati

- [Introduzione aXamarin.Forms](~/xamarin-forms/index.yml)
- [Xamarin.Essentials](~/essentials/index.md)
- [Xamarin.FormsShell](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin.FormsMateriale visivo](~/xamarin-forms/user-interface/visual/material-visual.md)
