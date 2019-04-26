---
title: Modificatori di campo XAML in xamarin. Forms
description: L'attributo dello spazio dei nomi X:FieldModifier specifica il livello di accesso per i campi generati per gli elementi denominati XAML.
ms.prod: xamarin
ms.assetid: 12357CE0-3C11-4B62-947F-72DB6DFC23A2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/18/2018
ms.openlocfilehash: 8be56524ec1c5331f30418fcc29a4bd2c26ccde1
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075298"
---
# <a name="xaml-field-modifiers-in-xamarinforms"></a>Modificatori di campo XAML in xamarin. Forms

_Il `x:FieldModifier` namespace (attributo) specifica il livello di accesso per i campi generati per gli elementi denominati XAML._

## <a name="overview"></a>Panoramica

I valori validi dell'attributo sono:

- `Public` : Specifica che il campo generato per l'elemento XAML `public`.
- `NotPublic` : Specifica che il campo generato per l'elemento XAML `internal` all'assembly.

Se il valore dell'attributo non è impostato, il campo generato per l'elemento sarà `private`.

Le condizioni seguenti devono essere soddisfatti per un `x:FieldModifier` attributo da elaborare:

- Elemento di primo livello XAML deve essere un valore valido `x:Class`.
- L'elemento XAML corrente ha un `x:Name` specificato.

il XAML seguente illustra esempi di impostazione dell'attributo:

```xaml
<Label x:Name="privateLabel" />
<Label x:Name="internalLabel" x:FieldModifier="NotPublic" />
<Label x:Name="publicLabel" x:FieldModifier="Public" />
```

> [!NOTE]
> Il `x:FieldModifier` attributo non può essere utilizzato per specificare il livello di accesso di una classe XAML.
