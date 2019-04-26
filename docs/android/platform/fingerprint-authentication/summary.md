---
title: Materiale sussidiario di autenticazione tramite impronta digitale
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 535eabe07cb4f4d36e6a6f918b5717efcc99185d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023536"
---
# <a name="fingerprint-authentication-guidance"></a>Materiale sussidiario di autenticazione tramite impronta digitale

## <a name="fingerprint-authentication-guidance"></a>Materiale sussidiario di autenticazione tramite impronta digitale

Ora che abbiamo visto i concetti e le API che circondano Android 6.0 di impronte digitali l'autenticazione, verranno ora esaminati alcuni consigli generali per l'uso delle API di impronta digitale.

1. **Usare le API di compatibilità della libreria di supporto Android v4** &ndash; questo semplificherà il codice dell'applicazione rimuovendo il controllo API dal codice e consentono a un'applicazione per il massimo possibile di dispositivi di destinazione.
2. **Offrono alternative all'autenticazione con impronta digitale** &ndash; autenticazione tramite impronta digitale è un modo eccezionale e rapido per un'applicazione autenticare un utente, tuttavia, non è possibile presupporre che sarà sempre di lavoro o essere disponibili. È possibile che lo scanner di impronta digitale può avere esito negativo, l'obiettivo è forse essere dirty, l'utente potrebbe non avere configurato il dispositivo per usare l'autenticazione tramite impronta digitale o le impronte digitali poiché sono andati persi. È anche possibile che l'utente potrebbe non vuole usare l'autenticazione tramite impronta digitale con l'applicazione. Per questi motivi, un'applicazione Android deve fornire un processo di autenticazione alternativo, ad esempio nome utente e password.
3. **Utilizzare l'icona di impronta digitale di Google** &ndash; tutte le applicazioni devono usare la stessa icona di impronta digitale fornita da Google. L'uso di un'icona standard rende più semplice per gli utenti Android riconoscere in cui le App in cui viene utilizzata l'autenticazione tramite impronta digitale: 
    
    ![Icona di impronta digitale Android](summary-images/ic-fp-40px.png)
    
4. **Notifica all'utente** &ndash; un'applicazione deve visualizzare un tipo di notifica per l'utente che lo scanner di impronta digitale è attivo e in attesa di un tocco o scorrere rapidamente. 

## <a name="summary"></a>Riepilogo

Autenticazione tramite impronta digitale è un ottimo modo per consentire a un'applicazione xamarin. Android verificare rapidamente gli utenti, rendendo più semplice per gli utenti di interagire con le funzionalità sensibili, ad esempio acquisti in-app. Questa guida ha illustrato i concetti e il codice necessario per incorporare l'impronta digitale Android 6.0 dell'API nell'applicazione xamarin. Android.

Prima di tutto è stata illustrata l'API le stesse, impronta digitale `FingerprintManager` (e `FingerprintManagerCompat`). Abbiamo esaminato come la `FingerprintManager.AuthenticationCallbacks` classe astratta deve essere esteso da un'applicazione e usata come intermediario tra l'hardware di impronta digitale e l'applicazione stessa. Quindi abbiamo esaminato il modo verificare l'integrità dei risultati dello scanner di impronta digitale usando un linguaggio `Cipher` oggetto. Infine, abbiamo modificate leggermente sul test tramite cui viene descritto come registrare un'impronta digitale su un dispositivo e l'uso **adb** per simulare un passaggio del dito impronta digitale in un emulatore. 

Se non già stato fatto, è consigliabile esaminare i [applicazione di esempio](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) che accompagna questa Guida. Il [esempio di finestra di dialogo di impronte digitali](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/) è stata portata da Java in xamarin. Android e fornisce un altro esempio su come aggiungere l'autenticazione tramite impronta digitale per un'applicazione Android.



## <a name="related-links"></a>Collegamenti correlati

- [App di esempio Guida impronta digitale](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Esempio di finestra di dialogo di impronta digitale](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Icona di impronta digitale](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
