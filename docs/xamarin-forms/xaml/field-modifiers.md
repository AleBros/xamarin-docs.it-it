---
title: Modificatori di campo XAML inXamarin.Forms
description: L'attributo dello spazio dei nomi x:FieldModifier specifica il livello di accesso per i campi generati per gli elementi XAML denominati.
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/02/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: db00f522b71a8993ef0f7f6cf5070813ce07396a
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84138125"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Modificatori di campo XAML inXamarin.Forms

L' `x:FieldModifier` attributo Namespace specifica il livello di accesso per i campi generati per gli elementi XAML denominati. I valori validi dell'attributo sono:

- `private`: specifica che il campo generato per l'elemento XAML è accessibile solo all'interno del corpo della classe in cui è dichiarato.
- `public`: specifica che il campo generato per l'elemento XAML non presenta restrizioni di accesso.
- `protected`: specifica che il campo generato per l'elemento XAML è accessibile all'interno della relativa classe e dalle istanze delle classi derivate.
- `internal`: specifica che il campo generato per l'elemento XAML è accessibile solo all'interno dei tipi nello stesso assembly.
- `notpublic`: specifica che il campo generato per l'elemento XAML è accessibile solo all'interno dei tipi nello stesso assembly.

Per impostazione predefinita, se il valore dell'attributo non è impostato, il campo generato per l'elemento sarà `private` .

> [!NOTE]
> Il valore dell'attributo può utilizzare qualsiasi combinazione di maiuscole e minuscole, in quanto verrà convertito in minuscolo da Xamarin.Forms .

Per elaborare un attributo, è necessario che siano soddisfatte le condizioni seguenti `x:FieldModifier` :

- L'elemento XAML di primo livello deve essere un oggetto valido `x:Class` .
- L'elemento XAML corrente ha un `x:Name` oggetto specificato.

Il codice XAML seguente mostra esempi di impostazione dell'attributo:

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="internal" />
<Label x:Name="publicLabel" x:FieldModifier="public" />
```

> [!IMPORTANT]
> L' `x:FieldModifier` attributo non può essere usato per specificare il livello di accesso di una classe XAML.
