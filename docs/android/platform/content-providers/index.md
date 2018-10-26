---
title: Introduzione a ContentProviders
description: Il sistema operativo Android Usa i provider di contenuti per facilitare l'accesso ai dati condivisi, ad esempio i file multimediali, i contatti e le informazioni del calendario. Questo articolo viene presentata la classe ContentProvider e vengono forniti due esempi di come usarla.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 4105200c48e41b142fc71e3a524023790b683cdb
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50105987"
---
# <a name="intro-to-contentproviders"></a>Introduzione a ContentProviders

_Il sistema operativo Android Usa i provider di contenuti per facilitare l'accesso ai dati condivisi, ad esempio i file multimediali, i contatti e le informazioni del calendario. Questo articolo viene presentata la classe ContentProvider e vengono forniti due esempi di come usarla._


## <a name="content-providers-overview"></a>Panoramica di provider di contenuti

Oggetto *ContentProvider* incapsula un archivio dati e fornisce un'API per accedere alla finestra. Il provider esista nell'ambito di un'applicazione Android che in genere fornisce anche un'interfaccia utente per la visualizzazione o gestione dei dati. Il vantaggio principale dell'uso di un provider di contenuti è l'abilitazione di altre applicazioni di accedere più facilmente i dati incapsulati con un oggetto client del provider (chiamato un' *ContentResolver*). Un provider di contenuti e un resolver di contenuto insieme, offrono un'API coerente tra le applicazioni per l'accesso ai dati che è semplice da creare e utilizzare. Tutte le applicazioni possono scegliere di usare `ContentProviders` per gestire i dati internamente e anche da esporre ad altre applicazioni.

Oggetto `ContentProvider` è necessaria anche per l'applicazione fornire suggerimenti di ricerca personalizzato o se si desidera offrire la possibilità di copiare dati complesse dall'applicazione per incollare in altre applicazioni. Questo documento viene illustrato come accedere e compilare `ContentProviders` con xamarin. Android.

La struttura di questa sezione è come segue:

- **Come funziona** &ndash; una panoramica di ciò che il `ContentProvider` è progettato per e come funziona.

- **Utilizzo di un Provider di contenuti** &ndash; riportato un esempio di accedere all'elenco di contatti.

- **Uso ContentProvider per condividere i dati** &ndash; scrivendo e dispendiosa in termini di un `ContentProvider` nella stessa applicazione.

`ContentProviders` e i cursori che operano sui dati vengono spesso usati per popolare i controlli ListView. Vedere le [questa Guida](~/android/user-interface/layouts/list-view/index.md) per altre informazioni su come usare queste classi.

`ContentProviders` esposti da Android o altre applicazioni sono un modo semplice per includere dati da altre origini nell'applicazione. Consentono di accedere e presentare i dati, ad esempio l'elenco dei contatti, foto o gli eventi del calendario all'interno dell'applicazione e consentire all'utente di interagire con tali dati.

Personalizzato `ContentProviders` sono un modo pratico per creare il pacchetto dei dati per l'uso all'interno di un'app o per l'uso da altre applicazioni (tra cui usi specifici, ad esempio ricerca personalizzata e copiare/incollare).

Gli argomenti di questa sezione forniscono alcuni semplici esempi di utilizzo e la scrittura `ContentProvider` codice.



## <a name="related-links"></a>Collegamenti correlati

- [ContactsAdapter Demo (esempio)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/ContactsAdapterDemo/)
- [SimpleContentProvider (sample)](https://developer.xamarin.com/samples/monodroid/PlatformFeatures/SimpleContentProvider)
- [Guida per sviluppatori di provider di contenuti](http://developer.android.com/guide/topics/providers/content-providers.html)
- [Riferimento alla classe ContentProvider](https://developer.xamarin.com/api/type/Android.Content.ContentProvider/)
- [Riferimento alla classe ContentResolver](https://developer.xamarin.com/api/type/Android.Content.ContentResolver/)
- [Riferimento alla classe di ListView](https://developer.xamarin.com/api/type/Android.Widget.ListView/)
- [Riferimento alla classe di CursorAdapter](https://developer.xamarin.com/api/type/Android.Widget.CursorAdapter/)
- [Riferimento alla classe UriMatcher](https://developer.xamarin.com/api/type/Android.Content.UriMatcher/)
- [Android.Provider](https://developer.xamarin.com/api/namespace/Android.Provider/)
- [Riferimento alla classe ContactsContract](https://developer.xamarin.com/api/type/Android.Provider.ContactsContract/)
