---
title: Compatibilità con le versioni precedenti del pacchetto del supporto Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/12/2017
ms.openlocfilehash: c32d666da1347b947c55209ed7c7ec31a97a70e0
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "73027294"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Compatibilità con le versioni precedenti del pacchetto del supporto Android

L'utilità dei frammenti sarà limitata senza la compatibilità con le versioni precedenti di dispositivi Android 3,0 (livello API 11). Per fornire questa funzionalità, Google ha introdotto la [libreria di supporto](https://developer.android.com/sdk/compatibility-library.html) (originariamente denominata libreria di *compatibilità Android* quando è stata rilasciata), che esegue il backporting di alcune API dalle versioni più recenti di Android alle versioni precedenti di Android. Si tratta del pacchetto di supporto Android che consente ai dispositivi che eseguono Android 1,6 (livello API 4) ad Android 2.3.3. (Livello API 10).

> [!NOTE]
> Solo i `ListFragment` e i `DialogFragment` sono disponibili tramite il pacchetto del supporto Android. Nessuna delle altre sottoclassi di frammenti, ad esempio `PreferenceFragment,` sono supportate nel pacchetto del supporto Android. Non funzioneranno nelle applicazioni pre-Android 3,0. 

## <a name="adding-the-support-package"></a>Aggiunta del pacchetto per il supporto

Il pacchetto per il supporto Android non viene aggiunto automaticamente a un'applicazione Novell. Android. Novell fornisce il [pacchetto NuGet della libreria di supporto Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) per semplificare l'aggiunta delle librerie di supporto a un'applicazione Novell. Android. Per includere i pacchetti di supporto nell'applicazione Novell. Android, includere il componente della [libreria di supporto Android V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) nel progetto Novell. Android, come illustrato nello screenshot seguente: 

[![screenshot del pacchetto della libreria di supporto Android V4 aggiunto al progetto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Dopo aver eseguito questi passaggi, è possibile usare i frammenti nelle versioni precedenti di Android. Le API dei frammenti funzioneranno nello stesso momento nelle versioni precedenti, con le eccezioni seguenti: 

- **Modificare la versione minima di android** &ndash; l'applicazione non deve più essere destinata a Android 3,0 o versione successiva, come illustrato di seguito: 

    [![screenshot della destinazione minima di Android da impostare nel manifesto Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **Estendere FragmentActivity** &ndash; le attività che ospitano i frammenti devono ora ereditare da `Android.Support.V4.App.FragmentActivity` e non da `Android.App.Activity`. 

- Gli **spazi dei nomi di aggiornamento** &ndash; le classi che ereditano da `Android.App.Fragment` ora devono ereditare da `Android.Support.V4.App.Fragment`. Rimuovere l'istruzione using "`using Android.App;`" all'inizio del file di codice sorgente e sostituirla con "`using Android.Support.V4.App`". 

- **Utilizzare SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` espone una proprietà `SupportingFragmentManager` che deve essere utilizzata per ottenere un riferimento al `FragmentManager`. Ad esempio, 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Con queste modifiche, sarà possibile eseguire un'applicazione basata su frammenti in Android 1,6 o 2. x, oltre che in Honeycomb e Ice Cream Sandwich. 

## <a name="related-links"></a>Collegamenti correlati

- [Libreria di supporto Android V4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
