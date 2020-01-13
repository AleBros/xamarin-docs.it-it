---
title: Introduzione a ContentProviders
description: Il sistema operativo Android usa i provider di contenuti per facilitare l'accesso ai dati condivisi, ad esempio i file multimediali, i contatti e le informazioni sul calendario. In questo articolo viene presentata la classe ContentProvider e vengono forniti due esempi di utilizzo.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 496e5c092c79f4f71bddaad30bea6acd1d58d375
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027548"
---
# <a name="intro-to-contentproviders"></a>Introduzione a ContentProviders

_Il sistema operativo Android usa i provider di contenuti per facilitare l'accesso ai dati condivisi, ad esempio i file multimediali, i contatti e le informazioni sul calendario. In questo articolo viene presentata la classe ContentProvider e vengono forniti due esempi di utilizzo._

## <a name="content-providers-overview"></a>Panoramica sui provider di contenuti

Un *ContentProvider* incapsula un repository di dati e fornisce un'API per accedervi. Il provider è incluso in un'applicazione Android che in genere fornisce anche un'interfaccia utente per la visualizzazione e la gestione dei dati. Il vantaggio principale dell'utilizzo di un provider di contenuti è consentire ad altre applicazioni di accedere facilmente ai dati incapsulati utilizzando un oggetto client del provider (denominato *ContentResolver*). Insieme, un provider di contenuti e un resolver del contenuto offrono un'API tra applicazioni coerente per l'accesso ai dati semplice da compilare e utilizzare. Qualsiasi applicazione può scegliere di utilizzare `ContentProviders` per gestire i dati internamente, nonché esporli ad altre applicazioni.

Un `ContentProvider` è necessario anche per l'applicazione per fornire suggerimenti di ricerca personalizzati o per fornire la possibilità di copiare dati complessi dall'applicazione per incollarli in altre applicazioni. Questo documento illustra come accedere e compilare `ContentProviders` con Xamarin.Android.

La struttura di questa sezione è la seguente:

- Il **funzionamento di &ndash; una** Panoramica della progettazione e del funzionamento dei `ContentProvider`.

- L' **utilizzo di un provider di contenuti** &ndash; un esempio di accesso all'elenco contatti.

- **Utilizzo di ContentProvider per condividere i dati** &ndash; la scrittura e l'utilizzo di un `ContentProvider` nella stessa applicazione.

`ContentProviders` e i cursori che operano sui dati vengono spesso utilizzati per popolare ListView. Per ulteriori informazioni sull'utilizzo di tali classi, fare riferimento alla [Guida di ListView e adapter](~/android/user-interface/layouts/list-view/index.md) .

`ContentProviders` esposti da Android (o altre applicazioni) sono un modo semplice per includere dati da altre origini nell'applicazione. Consentono di accedere ai dati e di presentarli, ad esempio l'elenco contatti, le foto o gli eventi del calendario dall'interno dell'applicazione, e di consentire all'utente di interagire con tali dati.

`ContentProviders` personalizzati sono un modo pratico per comprimere i dati da usare all'interno dell'app o per l'uso da parte di altre applicazioni (inclusi gli usi speciali come la ricerca personalizzata e la copia e incolla).

Negli argomenti di questa sezione vengono forniti alcuni semplici esempi di utilizzo e scrittura del codice `ContentProvider`.

## <a name="related-links"></a>Collegamenti correlati

- [Demo di ContactsAdapter (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [SimpleContentProvider (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [Guida per gli sviluppatori di provider di contenuti](https://developer.android.com/guide/topics/providers/content-providers.html)
- [Riferimento alla classe ContentProvider](xref:Android.Content.ContentProvider)
- [Riferimento alla classe ContentResolver](xref:Android.Content.ContentResolver)
- [Riferimento alla classe ListView](xref:Android.Widget.ListView)
- [Riferimento alla classe CursorAdapter](xref:Android.Widget.CursorAdapter)
- [Riferimento alla classe UriMatcher](xref:Android.Content.UriMatcher)
- [Android. provider](xref:Android.Provider)
- [Riferimento alla classe ContactsContract](xref:Android.Provider.ContactsContract)
