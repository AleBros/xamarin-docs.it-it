---
title: Creazione di un tema personalizzato
ms.prod: xamarin
ms.assetid: 4FE08ADC-093F-47FA-B33C-20CF08B5D7E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/01/2017
ms.openlocfilehash: b760f5e1d37f4bc8cc6af063552b974afa435d56
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="creating-a-custom-theme"></a>Creazione di un tema personalizzato

![](~/media/shared/preview.png "Questa API è attualmente in anteprima")

Oltre ad aggiungere un tema da un pacchetto Nuget (ad esempio il [luce](~/xamarin-forms/user-interface/themes/light.md) e [scuro](~/xamarin-forms/user-interface/themes/dark.md) temi), è possibile creare la propria risorsa temi dizionario che è possibile fare riferimento nell'app.

## <a name="example"></a>Esempio

I tre `BoxView`s nella [pagina temi](~/xamarin-forms/user-interface/themes/index.md) hanno il formato in base a tre classi definite nei due temi scaricabili.

Per comprendere il funzionamento di questi, il markup seguente viene creato uno stile equivalente che è possibile aggiungere direttamente il **app**.

Si noti il `Class` attributo per `Style` (anziché il [ `x:Key` ](~/xamarin-forms/user-interface/styles/inheritance.md) attributo disponibile nelle versioni precedenti di xamarin. Forms).

```xml
<ResourceDictionary>
  <!-- DEFINE ANY CONSTANTS -->
  <Color x:Key="SeparatorLineColor">#CCCCCC</Color>
  <Color x:Key="iOSDefaultTintColor">#007aff</Color>
  <Color x:Key="AndroidDefaultAccentColorColor">#1FAECE</Color>
  <OnPlatform x:TypeArguments="Color" x:Key="AccentColor">
    <On Platform="iOS" Value="{StaticResource iOSDefaultTintColor}" />
    <On Platform="Android" Value="{StaticResource AndroidDefaultAccentColorColor}" />
  </OnPlatform>
  <!--  BOXVIEW CLASSES -->
  <Style TargetType="BoxView" Class="HorizontalRule">
    <Setter Property="BackgroundColor" Value="{ StaticResource SeparatorLineColor }" />
    <Setter Property="HeightRequest" Value="1" />
  </Style>

  <Style TargetType="BoxView" Class="Circle">
    <Setter Property="BackgroundColor" Value="{ StaticResource AccentColor }" />
    <Setter Property="WidthRequest" Value="34"/>
    <Setter Property="HeightRequest" Value="34"/>
    <Setter Property="HorizontalOptions" Value="Start" />

    <Setter Property="local:ThemeEffects.Circle" Value="True" />
  </Style>

  <Style TargetType="BoxView" Class="Rounded">
    <Setter Property="BackgroundColor" Value="{ StaticResource AccentColor }" />
    <Setter Property="HorizontalOptions" Value="Start" />
    <Setter Property="BackgroundColor" Value="{ StaticResource AccentColor }" />

    <Setter Property="local:ThemeEffects.CornerRadius" Value="4" />
  </Style>
</ResourceDictionary>
```

Si noterà che il `Rounded` classe fa riferimento a un effetto personalizzato `CornerRadius`.
Il codice per questo effetto è indicato di seguito, per farvi riferimento correttamente un oggetto personalizzato `xmlns` deve essere aggiunto per il **app**dell'elemento radice:

```csharp
xmlns:local="clr-namespace:ThemesDemo;assembly=ThemesDemo"
```

### <a name="c-code-in-the-pcl-or-shared-project"></a>Codice c# nella libreria di classi Portabile o nel progetto condiviso

Il codice per la creazione di un angolo di arrotondamento `BoxView` Usa [effetti](~/xamarin-forms/app-fundamentals/effects/index.md).
Raggio dell'angolo viene applicato usando un `BindableProperty` e viene implementato applicando un [effetto](~/xamarin-forms/app-fundamentals/effects/index.md). L'effetto richiesto codice specifico della piattaforma di [iOS](#ios) e [Android](#android) progetti (mostrati sotto).

```csharp
namespace ThemesDemo
{
  public static class ThemeEffects
  {
  public static readonly BindableProperty CornerRadiusProperty =
    BindableProperty.CreateAttached("CornerRadius", typeof(double), typeof(ThemeEffects), 0.0, propertyChanged: OnChanged<CornerRadiusEffect, double>);
    private static void OnChanged<TEffect, TProp>(BindableObject bindable, object oldValue, object newValue)
              where TEffect : Effect, new()
    {
        var view = bindable as View;
        if (view == null)
        {
            return;
        }

        if (EqualityComparer<TProp>.Equals(newValue, default(TProp)))
        {
            var toRemove = view.Effects.FirstOrDefault(e => e is TEffect);
            if (toRemove != null)
            {
                view.Effects.Remove(toRemove);
            }
        }
        else
        {
            view.Effects.Add(new TEffect());
        }

    }
    public static void SetCornerRadius(BindableObject view, double radius)
    {
        view.SetValue(CornerRadiusProperty, radius);
    }

    public static double GetCornerRadius(BindableObject view)
    {
        return (double)view.GetValue(CornerRadiusProperty);
    }

    private class CornerRadiusEffect : RoutingEffect
    {
        public CornerRadiusEffect()
            : base("Xamarin.CornerRadiusEffect")
        {
        }
    }
  }
}
```

<a name="ios" />

### <a name="c-code-in-the-ios-project"></a>Codice c# nel progetto iOS

```csharp
using System;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;
using CoreGraphics;
using Foundation;
using XFThemes;

namespace ThemesDemo.iOS
{
    public class CornerRadiusEffect : PlatformEffect
    {
        private nfloat _originalRadius;

        protected override void OnAttached()
        {
            if (Container != null)
            {
                _originalRadius = Container.Layer.CornerRadius;
                Container.ClipsToBounds = true;

                UpdateCorner();
            }
        }

        protected override void OnDetached()
        {
            if (Container != null)
            {
                Container.Layer.CornerRadius = _originalRadius;
                Container.ClipsToBounds = false;
            }
        }

        protected override void OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (args.PropertyName == ThemeEffects.CornerRadiusProperty.PropertyName)
            {
                UpdateCorner();
            }
        }

        private void UpdateCorner()
        {
            Container.Layer.CornerRadius = (nfloat)ThemeEffects.GetCornerRadius(Element);
        }
    }
}
```

<a name="android" />

### <a name="c-code-in-the-android-project"></a>Codice c# nel progetto Android

```csharp
using System;
using Xamarin.Forms.Platform;
using Xamarin.Forms.Platform.Android;
using Android.Views;
using Android.Graphics;

namespace ThemesDemo.Droid
{
    public class CornerRadiusEffect : BaseEffect
    {
        private ViewOutlineProvider _originalProvider;

        protected override bool CanBeApplied()
        {
            return Container != null && (int)Android.OS.Build.VERSION.SdkInt >= 21;
        }

        protected override void OnAttachedInternal()
        {
            _originalProvider = Container.OutlineProvider;
            Container.OutlineProvider = new CornerRadiusOutlineProvider(Element);
            Container.ClipToOutline = true;
        }

        protected override void OnDetachedInternal()
        {
            Container.OutlineProvider = _originalProvider;
            Container.ClipToOutline = false;
        }

        protected override void OnElementPropertyChanged(System.ComponentModel.PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (!Attached)
            {
                return;
            }

            if (args.PropertyName == ThemeEffects.CornerRadiusProperty.PropertyName)
            {
                Container.Invalidate();
            }
        }

        private class CornerRadiusOutlineProvider : ViewOutlineProvider
        {
            private Xamarin.Forms.Element _element;

            public CornerRadiusOutlineProvider(Xamarin.Forms.Element element)
            {
                _element = element;
            }

            public override void GetOutline(Android.Views.View view, Outline outline)
            {
                var pixles =
                    (float)ThemeEffects.GetCornerRadius(_element) *
                    view.Resources.DisplayMetrics.Density;

                outline.SetRoundRect(new Rect(0, 0, view.Width, view.Height), (int)pixles);
            }
        }
    }
}
```


## <a name="summary"></a>Riepilogo

È possibile creare un tema personalizzato mediante la definizione di stili per ogni controllo che richiede un aspetto personalizzato. Più stili per un controllo devono essere distinti da diverse `Class` attributi nel dizionario risorse e quindi applicare impostando il `StyleClass` attributo sul controllo.

Inoltre è possibile utilizzare uno stile [effetti](~/xamarin-forms/app-fundamentals/effects/index.md) per personalizzare l'aspetto di un controllo.

[Gli stili impliciti](~/xamarin-forms/user-interface/styles/implicit.md) (senza un `x:Key` o `Style` attributo) continuano a essere applicato a tutti i controlli che corrispondono il `TargetType`.
