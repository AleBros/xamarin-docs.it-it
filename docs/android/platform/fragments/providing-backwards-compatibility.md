---
title: Fornisce la compatibilità con il pacchetto di supporto Android
ms.prod: xamarin
ms.assetid: 7511D2F8-2B4F-4200-C74E-E967153B2E8D
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 06/12/2017
ms.openlocfilehash: 07aec6f5fb66d4efcc114f92f0fb85d5b5b99c6f
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="providing-backwards-compatibility-with-the-android-support-package"></a>Fornisce la compatibilità con il pacchetto di supporto Android

L'utilità dei frammenti sarà limitato con le versioni precedenti senza la compatibilità con pre-Android dispositivi (API livello 11) 3.0. Per fornire questa funzionalità, introdotta Google il [libreria di supporto](http://developer.android.com/sdk/compatibility-library.html) (denominato originariamente il *Android Compatibility Library* quando è stato rilasciato) quale backports alcune delle API di versioni più recenti di Android alle versioni precedenti di Android. È il pacchetto di supporto Android che consente ai dispositivi che eseguono Android 2.3.3 1.6 Android (API livello 4). (Livello API 10).

> [!NOTE]
> Solo il `ListFragment` e `DialogFragment` sono disponibili tramite il pacchetto di supporto Android. Nessuno degli altri frammenti sottoclassi, ad esempio il `PreferenceFragment,` sono supportati nel pacchetto di supporto Android. Non funzionerà in 3.0 applicazioni pre-Android. 


## <a name="adding-the-support-package"></a>Aggiunta del pacchetto di supporto

Il pacchetto di supporto Android non viene aggiunto automaticamente a un'applicazione di xamarin. Xamarin offre il [pacchetto NuGet di libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) per semplificare l'aggiunta di raccolte di supporto per un'applicazione di xamarin. Per includere i pacchetti di supporto del xamarin applicazione includono il [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) componente nel progetto xamarin, come illustrato nella schermata seguente: 

[![Pacchetto di schermata della libreria di supporto Android v4 da aggiungere al progetto](providing-backwards-compatibility-images/02-sml.png)](providing-backwards-compatibility-images/02.png#lightbox)

Questi passaggi sono stati eseguiti, diventa possibile utilizzare frammenti nelle versioni precedenti di Android. Le API di frammento funzionerà ora la stessa in queste versioni precedenti, con le eccezioni seguenti: 

-   **Modificare la versione minima di Android** &ndash; l'applicazione non richiede più la destinazione Android 3.0 o versione successiva, come illustrato di seguito: 

    [![Schermata di minimo Android destinazione viene impostata nel manifesto Android](providing-backwards-compatibility-images/03-sml.png)](providing-backwards-compatibility-images/03.png#lightbox)

-   **Estendere FragmentActivity** &ndash; l'attività che ospitano frammenti ora deve ereditare da `Android.Support.V4.App.FragmentActivity` e non da `Android.App.Activity` . 

-   **Aggiornare gli spazi dei nomi** &ndash; le classi che ereditano da `Android.App.Fragment` ora deve ereditare da `Android.Support.V4.App.Fragment` . Rimuovere l'usando istruzione " `using Android.App;` " nella parte superiore del codice sorgente del file e sostituirlo con " `using Android.Support.V4.App` ". 

-   **Utilizzare SupportFragmentManager** &ndash; `Android.Support.V4.App.FragmentActivity` espone un `SupportingFragmentManager` proprietà che deve essere utilizzato per ottenere un riferimento di `FragmentManager` . Ad esempio: 

```csharp
FragmentTransaction fragmentTx = this.SupportingFragmentManager.BeginTransaction();
DetailsFragment detailsFrag = new DetailsFragment();
fragmentTx.Add(Resource.Id.fragment_container, detailsFrag);
fragmentTx.Commit();
```

Con queste modifiche sul posto, sarà possibile eseguire un'applicazione basata sul frammento di in Android versione 1.6 o 2. x, nonché sui Nido d'API e Sandwich gelato. 


## <a name="related-links"></a>Collegamenti correlati

- [Supporto Android libreria v4 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
