---
title: Funzionalità della piattaforma
description: Sfruttare le funzionalità specifiche della piattaforma con xamarin. Forms
ms.prod: xamarin
ms.assetid: 2C6CE42C-E380-4BB9-90CC-D0F4E60C4C03
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/20/2017
ms.openlocfilehash: fd46411f3662652ef26addc76f273d6071401a6f
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="platform-features"></a>Funzionalità della piattaforma

Xamarin. Forms è estendibile e consente di incorporare le funzionalità specifiche della piattaforma usando [effetti](~/xamarin-forms/app-fundamentals/effects/index.md), [renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md), il [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md)e altro ancora.

## <a name="androidandroidindexmd"></a>[Android](android/index.md)

Questa guida viene descritto come implementare materiale progettazione mediante l'aggiornamento di App Android di xamarin. Forms esistenti.

## <a name="application-indexing-and-deep-linkingdeep-linkingmd"></a>[Indicizzazione e deep linking delle applicazioni](deep-linking.md)

L'indicizzazione dell'applicazione consente alle applicazioni che, in caso contrario, verrebbero dimenticate dopo qualche utilizza rimanere rilevanti da visualizzati nei risultati della ricerca. Il collegamento completo consente alle applicazioni rispondere a un risultato di ricerca che contiene i dati dell'applicazione, in genere passando a una pagina a cui fa riferimento da un collegamento diretto.

## <a name="device-classdevicemd"></a>[Classe Device](device.md)

Come utilizzare la `Device` classe per creare il comportamento specifico della piattaforma nel codice condiviso e l'interfaccia utente (inclusi tramite XAML). Vengono inoltre illustrati `BeginInvokeOnMainThread` che è essenziale quando si modificano i controlli dell'interfaccia utente dal thread in background.

## <a name="iosiosindexmd"></a>[iOS](ios/index.md)

Alcuni stili di iOS possono essere eseguiti tramite **Info. plist** e `UIAppearance` API. Questa guida sono inclusi esempi che illustrano come includere iOS 9 funzionalità nell'app iOS di una soluzione xamarin. Forms, ad esempio la ricerca Spotlight di Core.

## <a name="gtkgtkmd"></a>[GTK](gtk.md)

Xamarin. Forms dispone ora di supporto di anteprima per le app GTK #.

## <a name="macmacmd"></a>[Mac](mac.md)

Xamarin. Forms include ora il supporto di anteprima per le app macOS.

## <a name="wpfwpfmd"></a>[WPF](wpf.md)

Xamarin. Forms dispone ora di supporto di anteprima per le app di Windows Presentation Foundation (WPF).

## <a name="native-formsnative-formsmd"></a>[Form nativi](native-forms.md)

Form native consentono di xamarin. Forms [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)-derivato pagine deve essere utilizzato da progetti nativi, xamarin. IOS, xamarin e della piattaforma UWP (Universal Windows).

## <a name="native-viewsnative-viewsindexmd"></a>[Visualizzazioni native](native-views/index.md)

Viste native da iOS, Android e la piattaforma UWP possono fare riferimento direttamente da xamarin. Forms. Le proprietà e i gestori eventi possono essere impostati nativo viste, e possono interagire con le visualizzazioni di xamarin. Forms.

## <a name="platform-specificsplatform-specificsindexmd"></a>[Specifiche di piattaforma](platform-specifics/index.md)

Le specifiche di piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza effetti o un renderer personalizzato.

## <a name="pluginspluginsmd"></a>[Plug-in](plugins.md)

Sono disponibili un'ampia gamma di plug-in open source in Github, Nuget e l'archivio di componenti di Xamarin per estendere le app xamarin. Forms.

## <a name="windowswindowsindexmd"></a>[Windows](windows/index.md)

Xamarin. Forms offre supporto per quattro diversi tipi di progetto di Windows:

* Windows Phone 8 Silverlight (originale Windows piattaforma supportata da xamarin. Forms),
* Windows Phone 8.1 (WinRT),
* Windows 8.1 (WinRT), e
* Piattaforma Windows universale (Windows 10).

In questa sezione vengono descritte le differenze tra di essi e come aggiungerli a una soluzione xamarin. Forms esistente.
