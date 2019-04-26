---
title: Fornendo la compatibilità con il pacchetto di supporto per Android con le versioni precedenti
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/12/2017
ms.openlocfilehash: 48ef40ce8560fd9fbb842dde70622d968591ab98
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61022237"
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Fornendo la compatibilità con il pacchetto di supporto per Android con le versioni precedenti

L'utilità dei frammenti sarebbe limitate senza le versioni precedenti la compatibilità con i precedenti ad Android 3.0 (API livello 11) i dispositivi. Per fornire questa funzionalità, Google introduce il [libreria di supporto](https://developer.android.com/sdk/compatibility-library.html) (chiamato originariamente le *Android Compatibility Library* quando è stato rilasciato) quali backports alcune API rispetto alle versioni più recenti di Android per le versioni precedenti di Android. È il pacchetto di supporto Android che consente ai dispositivi che eseguono Android 1.6 (API livello 4) per Android 2.3.3. (Livello API 10).

> [!NOTE]
> Solo le `ListFragment` e il `DialogFragment` sono disponibili tramite il pacchetto di supporto Android. Nessuno degli altri frammenti sottoclassi, ad esempio il `PreferenceFragment,` sono supportati nel pacchetto di supporto Android. Non funzioneranno nelle 3.0 applicazioni precedenti ad Android. 


## <a name="adding-the-support-package"></a>Aggiunta del pacchetto di supporto

Il pacchetto di supporto Android non viene aggiunto automaticamente a un'applicazione xamarin. Android. Xamarin offre il [pacchetto NuGet della libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) per semplificare l'aggiunta di librerie di supporto per un'applicazione xamarin. Android. Per includere i pacchetti di supporto di xamarin. Android applicazione includono la [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) componente nel progetto xamarin. Android, come illustrato nello screenshot seguente: 

[![Pacchetto di schermata della libreria di supporto Android v4 da aggiungere al progetto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Dopo questi passaggi sono stati eseguiti, è possibile usare i frammenti in versioni precedenti di Android. Le API di frammento funzionerà ora stesso in queste versioni precedenti, con le eccezioni seguenti: 

-   **Modificare la versione minima di Android** &ndash; l'applicazione non deve più usare Android 3.0 o versione successiva, come illustrato di seguito: 

    [![Schermata di Minimum Android destinazione viene impostata nel manifesto Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

-   **Estendere FragmentActivity** &ndash; l'attività che ospitano i frammenti devono ora ereditano da `Android.Support.V4.App.FragmentActivity` e non da `Android.App.Activity` . 

-   **Aggiornare gli spazi dei nomi** &ndash; classi che ereditano da `Android.App.Fragment` ora deve ereditare da `Android.Support.V4.App.Fragment` . Rimuovere l'usando istruzione " `using Android.App;` " nella parte superiore del codice sorgente del file e sostituirlo con " `using Android.Support.V4.App` ". 

-   **Usare SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` espone una `SupportingFragmentManager` proprietà che deve essere usata per ottenere un riferimento al `FragmentManager` . Ad esempio: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Con queste modifiche nella posizione, sarà possibile eseguire un'applicazione basata sul frammento di Android 1.6 o versione 2.x che nei Honeycomb e Ice Cream Sandwich. 


## <a name="related-links"></a>Collegamenti correlati

- [NuGet della libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
