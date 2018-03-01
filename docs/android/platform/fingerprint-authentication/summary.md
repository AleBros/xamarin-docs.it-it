---
title: Guida autenticazione impronta digitale
ms.topic: article
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 20defea58ec0c09becd5a3cec1961806cb0acc1d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="fingerprint-authentication-guidance"></a>Guida autenticazione impronta digitale

## <a name="fingerprint-authentication-guidance"></a>Guida autenticazione impronta digitale

Ora che abbiamo visto i concetti e le API che circonda Android 6.0 di impronte digitali autenticazione, esaminiamo alcuni consigli generali per l'utilizzo delle API di impronta digitale.

1. **Usare le API di compatibilità della libreria di supporto Android v4** &ndash; per semplificare il codice dell'applicazione rimuovendo il controllo di API dal codice e un'applicazione per la maggior parte possibile di dispositivi di destinazione.
2. **Offrono alternative all'autenticazione impronta digitale** &ndash; autenticazione impronta digitale è un modo rapido, ideale per un'applicazione autenticare un utente, tuttavia, non è possibile presupporre che verrà sempre di lavoro o essere disponibili. È possibile che lo scanner di impronta digitale potrebbe non riuscire, l'obiettivo potrebbe essere dirty, l'utente potrebbe non aver configurato il dispositivo per utilizzare l'autenticazione delle impronte digitali o gli ID digitali poiché essersi mancanti. È anche possibile che l'utente potrebbe non desidera utilizzare l'autenticazione delle impronte digitali con l'applicazione. Per questi motivi, un'applicazione Android deve fornire un processo di autenticazione alternative, ad esempio nome utente e password.
3. **Utilizzare l'icona di impronta digitale Google** &ndash; tutte le applicazioni utilizzano la stessa icona di impronta digitale fornita da Google. L'utilizzo di un'icona standard rende più semplice per gli utenti Android di riconoscere dove le applicazioni in cui viene utilizzata l'autenticazione impronta digitale: 
    
    ![Icona di impronta digitale Android](summary-images/ic-fp-40px.png)
    
4. **Notifica all'utente** &ndash; un'applicazione deve essere visualizzato un tipo di notifica all'utente che lo scanner di impronte digitali è attivo e in attesa di un tocco o scorrere. 

## <a name="summary"></a>Riepilogo

Autenticazione impronta digitale è un ottimo modo per consentire a un'applicazione di xamarin verificare rapidamente gli utenti, rendendo più semplice per gli utenti di interagire con le funzionalità riservate, ad esempio acquisti in-app. Questa guida illustrati i concetti e il codice necessario per incorporare l'impronta digitale Android 6.0 dell'API dell'applicazione di xamarin.

Prima abbiamo parlato l'impronta digitale API le stesse, `FingerprintManager` (e `FingerprintManagerCompat`). Abbiamo esaminato come `FingerprintManager.AuthenticationCallbacks` astratta deve essere esteso da un'applicazione e utilizzato come intermediario tra l'hardware di impronta digitale e l'applicazione stessa. Quindi abbiamo esaminato il modo verificare l'integrità dei risultati delle impronte digitali scanner utilizzando un linguaggio `Cipher` oggetto. Infine, abbiamo interessate un bit sul test tramite cui viene descritto come registrare un'impronta digitale in un dispositivo e l'utilizzo **adb** per simulare un scorrere impronta digitale in un emulatore. 

Se non già stato fatto, è necessario esaminare il [applicazione di esempio](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) che accompagna questa Guida. Il [esempio di finestra di dialogo delle impronte digitali](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/) è stata portata da Java in xamarin e fornisce un altro esempio su come aggiungere l'autenticazione impronta digitale per un'applicazione Android.



## <a name="related-links"></a>Collegamenti correlati

- [App di esempio di Guida di impronta digitale](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Esempio di finestra di dialogo delle impronte digitali](https://developer.xamarin.com/samples/monodroid/android-m/FingerprintDialog/)
- [Icona di impronta digitale](https://developer.android.comhttps://developer.xamarin.com/samples/FingerprintDialog/res/drawable-hdpi/ic_fp_40px.html)
