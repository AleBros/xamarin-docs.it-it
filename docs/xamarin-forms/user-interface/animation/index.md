---
title: "Animation in Xamarin.Forms " Description: " Xamarin.Forms include un'infrastruttura di animazione semplice per la creazione di animazioni semplici, pur essendo sufficientemente versatile da creare animazioni complesse".
ms. prod: Novell MS. AssetID: AC0B4127-ECA3-44DA-8A24-A2B10A275083 ms. Technology: Novell-Forms Author: davidbritch ms. Author: dabritch ms. Date: 07/14/2016 no-loc: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="animation-in-xamarinforms"></a>Animazione inXamarin.Forms

_Novell. Forms include una propria infrastruttura di animazione semplice per la creazione di animazioni semplici, pur essendo sufficientemente versatile per la creazione di animazioni complesse._

Le Xamarin.Forms classi di animazione sono destinate a proprietà diverse di elementi visivi, con un'animazione tipica che modifica progressivamente una proprietà da un valore a un altro in un determinato periodo di tempo. Si noti che non è presente alcuna interfaccia XAML per le Xamarin.Forms classi di animazione. Tuttavia, le animazioni possono essere incapsulate in [comportamenti](~/xamarin-forms/app-fundamentals/behaviors/index.md) e quindi a cui viene fatto riferimento da XAML.

## <a name="simple-animations"></a>[Animazioni semplici](simple.md)

La [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe fornisce metodi di estensione che possono essere usati per costruire animazioni semplici che ruotano, ridimensionano, traducono e sfumano le [`VisualElement`](xref:Xamarin.Forms.VisualElement) istanze. Questo articolo illustra la creazione e l'annullamento di animazioni con la `ViewExtensions` classe.

## <a name="easing-functions"></a>[Funzioni di interpolazione](easing.md)

Xamarin.Formsinclude una [`Easing`](xref:Xamarin.Forms.Easing) classe che consente di specificare una funzione di trasferimento che controlla il modo in cui le animazioni accelerano o rallentano durante l'esecuzione. In questo articolo viene illustrato come utilizzare le funzioni di interpolazione predefinite e come creare funzioni di interpolazione personalizzate.

## <a name="custom-animations"></a>[Animazioni personalizzate](custom.md)

La [`Animation`](xref:Xamarin.Forms.Animation) classe è il blocco predefinito di tutte le Xamarin.Forms animazioni, con i metodi di estensione nella [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) classe che crea uno o più `Animation` oggetti. Questo articolo illustra come usare la `Animation` classe per creare e annullare animazioni, sincronizzare più animazioni e creare animazioni personalizzate che animano le proprietà che non sono animate dai metodi di animazione esistenti.
