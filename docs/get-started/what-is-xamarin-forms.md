---
title: Che cos'è Xamarin. Form?
description: Questo articolo introduce Xamarin. Moduli e librerie correlate.
ms.prod: xamarin
ms.assetid: C1E24DB9-3099-4F79-BB88-10AABF7D4614
author: profexorgeek
ms.author: jusjohns
ms.date: 09/18/2019
no-loc:
- Xamarin
- Xamarin.Forms
- Xamarin.Android
- Xamarin.Essentials
- Xamarin.iOS
- Xamarin.Mac
ms.openlocfilehash: c217acdc3bd5c007822cc29fc730df99e373ba01
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607841"
---
# <a name="what-is-opno-locxamarinforms"></a>Che cos'è Xamarin. Form?

[![screenshot di esempio [! OP. NO-LOC (Novell)]. Applicazione Forms in iOS e Android](what-is-xamarin-forms-images/xamarin-forms-app-cropped.png)](what-is-xamarin-forms-images/xamarin-forms-app.png#lightbox)

Xamarin. Forms è un Framework dell'interfaccia utente open source. Xamarin. I moduli consentono agli sviluppatori di compilare applicazioni Android, iOS e Windows da una singola codebase condivisa.

Xamarin. I moduli consentono agli sviluppatori di creare interfacce utente in XAML con code- C#behind in. Queste interfacce vengono visualizzate come controlli nativi performanti in ogni piattaforma.

## <a name="who-opno-locxamarinforms-is-for"></a>Chi Xamarin. Moduli per

Xamarin. I moduli sono destinati agli sviluppatori con gli obiettivi seguenti:

- Condividi layout e progettazione dell'interfaccia utente tra piattaforme diverse.
- Condividi codice, test e logica di business tra le diverse piattaforme.
- Scrivi app multipiattaforma in C# con Visual Studio.

## <a name="how-opno-locxamarinforms-works"></a>Xamarin. Moduli funziona

![[! OP. NO-LOC (Novell)]. Diagramma dell'architettura form](what-is-xamarin-forms-images/xamarin-forms-architecture.png)

Xamarin. Moduli fornisce un'API coerente per la creazione di elementi dell'interfaccia utente tra piattaforme diverse. Questa API può essere implementata in XAML o C# e supporta l'associazione dati per i modelli, ad esempio Model-View-ViewModel (MVVM).

In fase di esecuzione Xamarin. I moduli usano i renderer della piattaforma per convertire gli elementi dell'interfaccia utente multipiattaforma in controlli nativi in Android, iOS e UWP. Consente agli sviluppatori di ottenere un aspetto nativo, una sensazione e prestazioni elevate, sfruttando al contempo i vantaggi della condivisione del codice tra le diverse piattaforme.

Xamarin. Le applicazioni Forms sono in genere costituite da una libreria di .NET Standard condivisa e da singoli progetti di piattaforma. La libreria condivisa contiene XAML o C# viste e qualsiasi logica di business, ad esempio servizi, modelli o altro codice. I progetti della piattaforma contengono la logica specifica della piattaforma o i pacchetti richiesti dall'applicazione.

Xamarin. I moduli usano Xamarin per eseguire applicazioni .NET in modo nativo tra piattaforme diverse. Per ulteriori informazioni su Xamarin, vedere [che cos'è Xamarin?](~/get-started/what-is-xamarin.md).

## <a name="additional-tools"></a>Altri strumenti

Xamarin. I moduli hanno un ampio ecosistema di pacchetti NuGet che aggiungono diverse funzionalità alle applicazioni. Questa sezione descrive alcuni pacchetti NuGet usati di frequente.

### <a name="opno-locxamarinessentials"></a>Xamarin. Essentials

Xamarin. Essentials è una libreria che fornisce API multipiattaforma per le funzionalità native dei dispositivi. Come Xamarin, Xamarin. Essentials è un'astrazione che semplifica il processo di accesso alle utilità native. Di seguito sono riportati alcuni esempi di utilità disponibili in Xamarin. I concetti di base includono:

- Informazioni dispositivo
- File system
- Accelerometro
- Dialer telefonico
- Sintesi vocale
- Blocco schermo

Per ulteriori informazioni, vedere [Xamarin. Concetti](~/essentials/index.md)di base.

### <a name="shell"></a>Shell

Xamarin. La shell dei moduli riduce la complessità dello sviluppo di applicazioni per dispositivi mobili fornendo le funzionalità di base richieste dalla maggior parte delle applicazioni. Di seguito sono riportati alcuni esempi di funzionalità fornite dalla shell:

- Esperienza di navigazione comune
- Schema di navigazione basato su URI
- Gestore ricerche integrato

Per ulteriori informazioni, vedere [Xamarin. Shell moduli](~/xamarin-forms/app-fundamentals/shell/index.md)

### <a name="platform-specifics"></a>Specifiche della piattaforma

Xamarin. I moduli forniscono un'API comune che esegue il rendering di controlli nativi su più piattaforme, ma una piattaforma specifica può avere funzionalità che non esistono in altre piattaforme. Ad esempio, la piattaforma Android dispone di funzionalità native per lo scorrimento rapido in una `ListView` ma non in iOS. Xamarin. Le specifiche della piattaforma Forms consentono di utilizzare funzionalità disponibili solo in una piattaforma specifica senza creare Renderer o effetti personalizzati.

Xamarin. I moduli includono soluzioni predefinite per un'ampia gamma di funzionalità specifiche della piattaforma. Per altre informazioni, vedere:

- [Xamarin. Specifiche della piattaforma moduli](~/xamarin-forms/platform/platform-specifics/index.md)
- [Specifiche della piattaforma Android](~/xamarin-forms/platform/android/index.md)
- [specifiche della piattaforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Specifiche della piattaforma Windows](~/xamarin-forms/platform/windows/index.md)

### <a name="material-visual"></a>Oggetto visivo Material

Xamarin. Per applicare le regole di progettazione del materiale ai Xamarin, viene usato l'oggetti visivi Material form. Applicazioni Forms. Xamarin. Il materiale visivo dei form utilizza la proprietà visiva per applicare selettivamente renderer personalizzati all'interfaccia utente, ottenendo un'applicazione con un aspetto coerente tra iOS e Android.

Per ulteriori informazioni, vedere [Xamarin. Oggetti visivi Material form](~/xamarin-forms/user-interface/visual/material-visual.md)

## <a name="related-links"></a>Collegamenti correlati

- [Inizia a usare Xamarin. Form](~/xamarin-forms/index.yml)
- [Xamarin. Essentials](~/essentials/index.md)
- [Xamarin. Shell moduli](~/xamarin-forms/app-fundamentals/shell/index.md)
- [Xamarin. Oggetti visivi Material form](~/xamarin-forms/user-interface/visual/material-visual.md)
