---
title: Introduzione a ContentProviders
description: Il sistema operativo Android utilizza i provider di contenuti per facilitare l'accesso ai dati condivisi come file multimediali, contatti e informazioni del calendario. In questo articolo viene introdotta la classe ContentProvider e vengono forniti due esempi di utilizzo.
ms.prod: xamarin
ms.assetid: 6E1810AA-EB70-9AD0-1B32-D9418908CC97
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 496e5c092c79f4f71bddaad30bea6acd1d58d375
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027548"
---
# <a name="intro-to-contentproviders"></a>Introduzione a ContentProviders

_Il sistema operativo Android utilizza i provider di contenuti per facilitare l'accesso ai dati condivisi come file multimediali, contatti e informazioni del calendario. In questo articolo viene introdotta la classe ContentProvider e vengono forniti due esempi di utilizzo._

## <a name="content-providers-overview"></a>Panoramica dei provider di contenuti

Oggetto *ContentProvider* incapsula un repository di dati e fornisce un'API per accedervi. Il provider esiste come parte di un'applicazione Android che in genere fornisce anche un'interfaccia utente per la visualizzazione/gestione dei dati. Il vantaggio principale dell'utilizzo di un provider di contenuti consiste nell'consentire ad altre applicazioni di accedere facilmente ai dati incapsulati utilizzando un oggetto client del provider (denominato *ContentResolver*). Insieme, un provider di contenuti e un resolver di contenuti offrono un'API tra applicazioni coerente per l'accesso ai dati semplice da creare e usare. Qualsiasi applicazione può `ContentProviders` scegliere di utilizzare per gestire i dati internamente e anche per esporlo ad altre applicazioni.

È `ContentProvider` inoltre necessario che l'applicazione fornisca suggerimenti di ricerca personalizzati o se si desidera fornire la possibilità di copiare dati complessi dall'applicazione per incollarli in altre applicazioni. Questo documento mostra come `ContentProviders` accedere e compilare con Xamarin.Android.This document shows how to access and build with Xamarin.Android.

La struttura di questa sezione è la seguente:

- **Come funziona** &ndash; Una panoramica `ContentProvider` di ciò che è progettato per e come funziona.

- **Utilizzo di un provider** &ndash; di contenuti Esempio di accesso all'elenco Contatti.

- **Utilizzo di ContentProvider per condividere i dati** &ndash; Scrittura e utilizzo di un `ContentProvider` oggetto nella stessa applicazione.

`ContentProviders`e i cursori che operano sui loro dati vengono spesso utilizzati per popolare ListViews. Fare riferimento alla [Guida di ListViews and Adapters](~/android/user-interface/layouts/list-view/index.md) per ulteriori informazioni su come utilizzare tali classi.

`ContentProviders`esposti da Android (o altre applicazioni) sono un modo semplice per includere dati da altre origini nell'applicazione. Consentono di accedere e presentare dati quali l'elenco Contatti, le foto o gli eventi del calendario dall'interno dell'applicazione e consentono all'utente di interagire con tali dati.

Personalizzato `ContentProviders` sono un modo conveniente per creare un pacchetto dei dati per l'uso all'interno della propria app, o per l'uso da parte di altre applicazioni (compresi usi speciali come la ricerca personalizzata e copia / incolla).

Negli argomenti di questa sezione vengono forniti `ContentProvider` alcuni semplici esempi di utilizzo e scrittura di codice.

## <a name="related-links"></a>Collegamenti correlati

- [Demo di ContactsAdapter (esempio)ContactsAdapter Demo (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-contactsadapterdemo)
- [SimpleContentProvider (esempio)SimpleContentProvider (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-simplecontentprovider)
- [Guida per gli sviluppatori di provider di contenuti](https://developer.android.com/guide/topics/providers/content-providers.html)
- [Riferimento alla classe ContentProvider](xref:Android.Content.ContentProvider)
- [Riferimento alla classe ContentResolver](xref:Android.Content.ContentResolver)
- [Riferimento alla classe ListView](xref:Android.Widget.ListView)
- [Riferimento alla classe CursorAdapter](xref:Android.Widget.CursorAdapter)
- [Riferimento alla classe UriMatcherUriMatcher Class Reference](xref:Android.Content.UriMatcher)
- [Android.Provider](xref:Android.Provider)
- [Riferimento alla classe ContactsContract](xref:Android.Provider.ContactsContract)
