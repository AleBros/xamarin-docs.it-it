---
title: Garantire la compatibilità con le versioni precedenti del pacchetto di supporto Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/12/2017
ms.openlocfilehash: c32d666da1347b947c55209ed7c7ec31a97a70e0
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027294"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Garantire la compatibilità con le versioni precedenti del pacchetto di supporto Android

L'utilità dei frammenti sarebbe limitata senza compatibilità con le versioni precedenti di compatibilità con i dispositivi pre-Android 3.0 (livello API 11). Per fornire questa funzionalità, Google ha introdotto la [libreria](https://developer.android.com/sdk/compatibility-library.html) di supporto (originariamente chiamata la libreria di *compatibilità Android* quando è stato rilasciato) che esegue il backport di alcune delle API dalle versioni più recenti di Android alle versioni precedenti di Android. È il pacchetto di supporto Android che consente ai dispositivi che eseguono Android 1.6 (livello API 4) per Android 2.3.3. (livello API 10).

> [!NOTE]
> Solo `ListFragment` il `DialogFragment` e il sono disponibili tramite il pacchetto di supporto Android. Nessuna delle altre sottoclassi Fragment, `PreferenceFragment,` ad esempio l'oggetto sono supportate nel pacchetto di supporto Android. Non funzioneranno nelle applicazioni pre-Android 3.0. 

## <a name="adding-the-support-package"></a>Aggiunta del pacchetto di supporto

Il pacchetto di supporto Android non viene aggiunto automaticamente a un'applicazione Xamarin.Android.The Android Support Package is not automatically added to a Xamarin.Android application. Xamarin fornisce il [pacchetto Android Support Library v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) per semplificare l'aggiunta delle librerie di supporto a un'applicazione Xamarin.Android.Xamarin provides the Android Support Library v4 NuGet package to simplify adding the support libraries to a Xamarin.Android application. Per includere i pacchetti di supporto nell'applicazione Xamarin.Android, includere il componente Android Support Library v4 nel progetto Xamarin.Android, come illustrato nella schermata seguente:To include the support packages in your Xamarin.Android application include the [Android Support Library v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) component in your Xamarin.Android project, as illustrated in the following screenshot: 

[![Screenshot del pacchetto della libreria di supporto Android v4 aggiunto al progetto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Dopo aver eseguito questi passaggi, diventa possibile usare frammenti nelle versioni precedenti di Android.After these steps have been performed, it becomes possible to use Fragments in earlier versions of Android. Le API Frammento funzioneranno allo stesso modo ora in queste versioni precedenti, con le seguenti eccezioni: 

- **Modificare la versione minima** &ndash; di Android L'applicazione non ha più bisogno di destinazione Android 3.0 o versione successiva, come mostrato di seguito:Change the minimum Android Version The application no longer needs to target Android 3.0 or higher, as shown below: 

    [![Screenshot della destinazione Android minima impostata in Manifesto Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

- **Estendere FragmentActivity** &ndash; Le attività che ospitano `Android.Support.V4.App.FragmentActivity` i frammenti `Android.App.Activity` devono ora ereditare da e non da . 

- Le classi degli spazi `Android.App.Fragment` dei nomi `Android.Support.V4.App.Fragment` di aggiornamento che **ereditano** &ndash; da devono ora ereditare da . Rimuovere l'istruzione `using Android.App;` using " " all'inizio del file `using Android.Support.V4.App` di codice sorgente e sostituirla con " ". 

- **Use SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` espone `SupportingFragmentManager` una proprietà che deve essere `FragmentManager` utilizzata per ottenere un riferimento all'oggetto . Ad esempio: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Con questi cambiamenti in atto, sarà possibile eseguire un'applicazione basata su frammenti su Android 1.6 o 2.x così come su Honeycomb e Ice Cream Sandwich. 

## <a name="related-links"></a>Collegamenti correlati

- [Libreria di supporto Android v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
