---
title: Supporto pre-Nido d'API Android tramite pacchetti di supporto
ms.topic: article
ms.prod: xamarin
ms.assetid: DACD0C14-5DDF-7BDE-6844-80550D301307
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/15/2018
ms.openlocfilehash: 9ffa3733908affb8a6f3cc5a1ae2e83c5a15f0f0
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="supporting-pre-honeycomb-android-using-support-packages"></a>Supporto pre-Nido d'API Android tramite pacchetti di supporto

Il *pacchetto di supporto Android* è costituito da librerie che porta indietro tra la nuova API &ndash; , ad esempio frammenti &ndash; alle versioni precedenti di Android. Aggiungendo il pacchetto di supporto Android, è possibile eseguire l'applicazione nei dispositivi Android 2.3, come illustrato da schermate riportate di seguito:

![Schermata della procedura dettagliata frammenti](supporting-pre-honeycomb-images/00.png)

![Schermata dei dettagli attività](supporting-pre-honeycomb-images/01.png)

<a name="Adding_the_Support_Package" />

## <a name="adding-the-support-package"></a>Aggiunta del pacchetto di supporto

Il pacchetto di supporto Android non viene aggiunto automaticamente a un'applicazione di xamarin. Xamarin offre il [pacchetto NuGet di libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) per semplificare l'aggiunta di raccolte di supporto per un'applicazione di xamarin.
Per includere i pacchetti di supporto del xamarin applicazione includono il [libreria di supporto Android v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) componente nel progetto xamarin, come illustrato nella schermata seguente:

![Aggiunta del pacchetto di libreria di supporto Android v4](supporting-pre-honeycomb-images/02.png)

Dopo aver aggiunto il pacchetto, modificare il framework di destinazione per Android 2.2 o versione successiva:

![Schermata di modifica del livello di API di Framework di destinazione](supporting-pre-honeycomb-images/03.png)

Inoltre, assicurarsi che la versione minima di Android è destinato lo stesso livello di API:

![Schermata di impostazione della versione minima di Android](supporting-pre-honeycomb-images/04.png)


<a name="Change_MainActivity_to_derive_from_FragmentActivity" />

### <a name="change-mainactivity-to-derive-from-fragmentactivity"></a>Modificare MainActivity da cui derivare FragmentActivity

Qualsiasi attività che utilizza frammenti deve ereditare da `Xamarin.Support.V4.App.FragmentActivity`. Questa classe è una parte essenziale del pacchetto di supporto perché permette di frammenti deve essere ospitato da attività, indipendentemente dalla versione di Android. `MainActivity` richiede solo una piccola modifica, è necessario ora ereditano da `Android.Support.V4.App.FragmentActivity`:

```csharp
[Activity(Label = "Fragments Walkthrough", MainLauncher = true, Icon = "@drawable/launcher")]
public class MainActivity : Android.Support.V4.App.FragmentActivity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       SetContentView(Resource.Layout.activity_main);
   }
}
```

<a name="Change_DetailsActivity_to_derive_from_FragmentActivity" />

### <a name="change-detailsactivity-to-derive-from-fragmentactivity"></a>Modificare DetailsActivity da cui derivare FragmentActivity

`DetailsActivity` deve inoltre essere modificata da un `Activity` per un `FragmentActivity`. Come `FragmentManager` è incompatibile con le versioni di pre-Nido d'API di Android, il pacchetto di supporto Android include una classe wrapper, `SupportFragmentManager`, che garantisce la compatibilità con le versioni precedenti. Ogni `FragmentActivity` ha un `SupportFragmentManager` , proprietà e `DetailsActivity` viene modificata per utilizzare il `SupportFragmentManager` anziché il `FragmentManager`:

```csharp
[Activity(Label = "Details Activity")]
public class DetailsActivity : Android.Support.V4.App.FragmentActivity
{
   protected override void OnCreate(Bundle bundle)
   {
       base.OnCreate(bundle);
       var index = Intent.Extras.GetInt("index", 0);
       var details = DetailsFragment.NewInstance(index);
       var fragmentTransaction = SupportFragmentManager.BeginTransaction(); // Notice the change from FragmentManager to SupportFragmentManager
       fragmentTransaction.Add(Android.Resource.Id.Content, details);
       fragmentTransaction.Commit();
   }
}
```

Al termine si queste modifiche, è ora disponibile un'applicazione che è possibile eseguire in Android versione 1.6 e versioni successive e che usa i frammenti per regolare l'interfaccia utente per le dimensioni di questo dispositivo di destinazione.


## <a name="related-links"></a>Collegamenti correlati

- [Android supporta libreria v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4)
