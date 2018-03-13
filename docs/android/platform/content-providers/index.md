---
title: Introduzione a ContentProviders
description: Il sistema operativo Android Usa provider di contenuti per facilitare l'accesso ai dati condivisi, ad esempio i file multimediali, contatti e le informazioni del calendario. In questo articolo viene presentata la classe di provider di contenuti e vengono forniti due esempi di come utilizzarla.
ms.topic: article
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 53408220f1bbd3b0aa97e0c54bd8f4c09847b448
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="intro-to-contentproviders"></a>Introduzione a ContentProviders

_Il sistema operativo Android Usa provider di contenuti per facilitare l'accesso ai dati condivisi, ad esempio i file multimediali, contatti e le informazioni del calendario. In questo articolo viene presentata la classe di provider di contenuti e vengono forniti due esempi di come utilizzarla._


## <a name="content-providers-overview"></a>Cenni preliminari sui provider di contenuto

Oggetto *provider di contenuti* incapsula un archivio di dati e fornisce un'API per accedere alla finestra. Il provider esista come parte di un'applicazione Android che in genere fornisce anche un'interfaccia utente per la visualizzazione o gestione dei dati. Il principale vantaggio dell'utilizzo di un provider di contenuti consente ad altre applicazioni di accedere facilmente i dati incapsulati utilizzando un oggetto client provider (chiamato un *ContentResolver*). Insieme, un provider di contenuti e il resolver di contenuto offrono un'API coerenza tra le applicazioni per l'accesso ai dati è semplice per creare e utilizzare. Qualsiasi applicazione può scegliere di utilizzare `ContentProviders` per gestire i dati internamente e anche da esporre ad altre applicazioni.

Oggetto `ContentProvider` è necessario anche per l'applicazione fornire suggerimenti di ricerca personalizzato, o se si desidera offrire la possibilità di copiare dati complessi dell'applicazione per incollare in altre applicazioni. Questo documento viene illustrato come accedere e compilare `ContentProviders` con xamarin.

La struttura di questa sezione è come segue:

- **Come funziona** &ndash; una panoramica degli elementi di `ContentProvider` è progettato per e sul funzionamento del servizio.

- **Utilizzo di un Provider di contenuti** &ndash; un esempio di accesso all'elenco di contatti.

- **Utilizzare il provider di contenuti per condividere dati** &ndash; scrittura e dispendiosa in termini di un `ContentProvider` nella stessa applicazione.

`ContentProviders` e i cursori che operano sui relativi dati vengono spesso utilizzati per popolare i controlli ListView. Fare riferimento al [Guida controlli ListView e schede](~/android/user-interface/layouts/list-view/index.md) per ulteriori informazioni su come utilizzare queste classi.

`ContentProviders` esposti da Android o altre applicazioni sono un modo semplice per includere dati da altre origini nell'applicazione. Essi consentono l'accesso e presentare i dati, ad esempio l'elenco dei contatti, foto o gli eventi del calendario all'interno dell'applicazione e consentire all'utente di interagire con i dati.

Personalizzato `ContentProviders` sono un modo pratico per creare il pacchetto dei dati per l'utilizzo all'interno di un'app o per l'uso da altre applicazioni (tra cui usi specifici quali ricerca personalizzata e copiare e incollare).

Negli argomenti di questa sezione sono alcuni semplici esempi di utilizzo e di scrittura `ContentProvider` codice.



## <a name="related-links"></a>Collegamenti correlati

- [Demo di ContactsAdapter (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider (sample)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [Guida per gli sviluppatori di provider di contenuti](http://developer.android.com/guide/topics/providers/content-providers.html)
- [Riferimento alla classe di provider di contenuti](https://developer.xamarin.com/api/type/Android.Content.ContentProvider/)
- [Riferimento alla classe ContentResolver](https://developer.xamarin.com/api/type/Android.Content.ContentResolver/)
- [Riferimento alla classe di ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Riferimento alla classe CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
- [Riferimento alla classe UriMatcher](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)
- [Android.Provider](https://developer.xamarin.com/api/namespace/Android.Provider/)
- [Riferimento alla classe ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)
