---
title: Tipi nativi per iOS e macOS
description: In questo documento viene descritto come il API unificata di Novell esegue il mapping dei tipi .NET ai tipi nativi a 32 bit e a 64 bit, in base alle esigenze, in base all'architettura di destinazione della compilazione.
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: conceptdev
ms.author: crdun
ms.date: 01/25/2016
ms.openlocfilehash: f370bc816fa72f94af8dae32ac295448f858dbb8
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290801"
---
# <a name="native-types-for-ios-and-macos"></a>Tipi nativi per iOS e macOS

Le API Mac e iOS usano tipi di dati specifici dell'architettura che sono sempre 32 bit su piattaforme a 32 bit e 64 bit su piattaforme a 64 bit.

In Objective-C, ad esempio, `NSInteger` viene eseguito il `int32_t` mapping del tipo di dati a nei `int64_t` sistemi a 32 bit e nei sistemi a 64 bit.

Per trovare la corrispondenza con questo comportamento, nell'API unificata vengono sostituiti gli utilizzi precedenti `int` di (che in .NET è definito come `System.Int32`sempre) in un nuovo tipo di dati `System.nint`:. È possibile pensare a "n" come significato "native", quindi al tipo Integer nativo della piattaforma.

Con questi nuovi tipi di dati, lo stesso codice sorgente viene compilato per le architetture a 32 bit e a 64 bit, a seconda dei flag di compilazione.

## <a name="new-data-types"></a>Nuovi tipi di dati

Nella tabella seguente vengono illustrate le modifiche apportate ai tipi di dati in modo che corrispondano a questo nuovo mondo a 32/64 bit:

|Tipo nativo|tipo di supporto a 32 bit|tipo di supporto a 64 bit|
|--- |--- |--- |
|`System.nint`|`System.Int32` (`int`)|`System.Int64` (`long`)|
|`System.nuint`|`System.UInt32` (`uint`)|`System.UInt64` (`ulong`)|
|`System.nfloat`|`System.Single` (`float`)|`System.Double` (`double`)|

Questi nomi sono stati scelti per consentire C# al codice di apparire più o meno nello stesso modo in cui sarebbe stato fatto oggi.

### <a name="implicit-and-explicit-conversions"></a>Conversioni implicite ed esplicite

La progettazione dei nuovi tipi di dati ha lo scopo di consentire a C# un singolo file di origine di utilizzare naturalmente l'archiviazione 32 o 64 bit a seconda della piattaforma host e delle impostazioni di compilazione.

A questo scopo, è necessario progettare un set di conversioni implicite ed esplicite da e verso i tipi di dati specifici della piattaforma in base ai tipi di dati a virgola mobile e integrali .NET.

Gli operatori di conversioni implicite vengono forniti quando non è possibile la perdita di dati (i valori di bit 32 vengono archiviati in uno spazio di bit 64).

Gli operatori di conversioni esplicite vengono forniti quando è possibile che si verifichi una perdita di dati (il valore 64 bit viene archiviato in un percorso di archiviazione 32 o potenzialmente 32).

`int`, `uint` e `float` sono `nuint` tutticonvertibili`nfloat` in modo implicito in ,mentre32bitsiadattanosemprea32o64bit.`nint`

`nint`, `nuint` e `nfloat` sono `ulong` tutticonvertibili`double` in modo implicito in ,mentreivaloridibit32o64rientranosemprenell'archiviodibit64.`long`

È necessario utilizzare le conversioni esplicite `nint`da `nuint` e `nfloat` in `int`e `uint` `float` , poiché i tipi nativi possono ospitare 64 bit di archiviazione.

È necessario utilizzare le conversioni esplicite `long`da `ulong` e `double` in `nint`e `nuint` `nfloat` , poiché i tipi nativi possono contenere solo 32 bit di archiviazione.

## <a name="coregraphics-types"></a>Tipi CoreGraphics

I tipi di dati Point, Size e Rectangle usati con CoreGraphics usano 32 o 64 bit a seconda del dispositivo in cui sono in esecuzione.  Quando inizialmente sono state associate le API iOS e Mac, sono state usate strutture di dati esistenti che corrispondono alle dimensioni della piattaforma host (tipi di dati `System.Drawing`in).

Quando si passa a **Unified**, sarà necessario sostituire le istanze di `System.Drawing` con le relative `CoreGraphics` controparti, come illustrato nella tabella seguente:

|Vecchio tipo in System. Drawing|Nuovo tipo di dati CoreGraphics|DESCRIZIONE|
|--- |--- |--- |
|`RectangleF`|`CGRect`|Include informazioni sul rettangolo a virgola mobile.|
|`SizeF`|`CGSize`|Include informazioni sulle dimensioni a virgola mobile (larghezza, altezza)|
|`PointF`|`CGPoint`|Include un punto a virgola mobile, informazioni sul punto (X, Y)|

I tipi di dati precedenti utilizzano float per archiviare gli elementi delle strutture di dati, mentre quello nuovo utilizza `System.nfloat`.

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Differenze rispetto a API unificata classiche](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
