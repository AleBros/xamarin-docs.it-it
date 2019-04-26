---
title: Tipi nativi per iOS e macOS
description: Questo documento descrive come API unificata di Xamarin esegue il mapping di tipi .NET ai tipi nativi a 32 e 64 bit, in base alle esigenze basata sull'architettura di destinazione di compilazione.
ms.prod: xamarin
ms.assetid: B5237770-0FC3-4B01-9E22-766B35C9A952
author: asb3993
ms.author: amburns
ms.date: 01/25/2016
ms.openlocfilehash: fc2b91a9265fcf09e4f58d5de27a1fdef9350b2d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199637"
---
# <a name="native-types-for-ios-and-macos"></a>Tipi nativi per iOS e macOS

API iOS e Mac usano i tipi di dati specifici dell'architettura sono sempre a 32 bit su piattaforme a 32 bit e a 64 bit su piattaforme a 64 bit.

Ad esempio, Objective-C esegue il mapping di `NSInteger` tipo di dati `int32_t` nei sistemi a 32 bit e a `int64_t` nei sistemi a 64 bit.

In modo che corrisponda a questo comportamento, nella nostra API unificata, si sta sostituendo l'utilizzi precedenti del `int` (che in .NET viene definito come devono essere sempre `System.Int32`) in un nuovo tipo di dati: `System.nint`. È possibile pensare "n" con il significato "nativa", quindi digitare il valore integer nativo della piattaforma.

Con questi nuovi tipi di dati, lo stesso codice sorgente viene compilato per le architetture a 32 bit e a 64 bit, a seconda del flag di compilazione.

## <a name="new-data-types"></a>Nuovi tipi di dati

Nella tabella seguente mostra le modifiche nei nostri tipi di dati per la corrispondenza di questo nuovo assetto 32 o 64 bit:

|Tipo nativo|tipo di supporto a 32 bit|tipo di supporto a 64 bit|
|--- |--- |--- |
|`System.nint`|`System.Int32` (`int`)|`System.Int64` (`long`)|
|`System.nuint`|`System.UInt32` (`uint`)|`System.UInt64` (`ulong`)|
|`System.nfloat`|`System.Single` (`float`)|`System.Double` (`double`)|

Abbiamo scelto di tali nomi per consentire il C# codice per la ricerca più o meno simile a quello che avrà un aspetto oggi stesso.

### <a name="implicit-and-explicit-conversions"></a>Conversioni implicite ed esplicite

La progettazione di nuovi tipi di dati serve per consentire a un singolo C# file di origine da utilizzare naturalmente 32 o 64 bit nell'archivio a seconda della piattaforma host e le impostazioni di compilazione.

Questa operazione necessaria per progettare un set di conversioni implicite ed esplicite da e verso i tipi di dati specifici della piattaforma per i tipi di dati integrali e a virgola mobile .NET.

Vengono forniti gli operatori di conversione implicita quando non vi è alcuna possibilità di perdita dei dati (a 32 bit i valori archiviati in uno spazio a 64 bit).

Gli operatori di conversioni esplicite vengono forniti quando è presente un potenziale perdita di dati (valore a 64 bit è in corso archiviati in un percorso di archiviazione 32 o potenzialmente 32).

 `int`, `uint` e `float` sono tutti implicitamente convertibile in `nint`, `nuint` e `nfloat` come 32 bit si adatteranno 32 o 64 bit.

 `nint`, `nuint` e `nfloat` sono tutti implicitamente convertibile in `long`, `ulong` e `double` come valori a 32 o 64 bit si adatteranno nello spazio di memorizzazione a 64 bit.

È necessario usare le conversioni esplicite da `nint`, `nuint` e `nfloat` nelle `int`, `uint` e `float` poiché i tipi nativi potrebbero contenere 64 bit di spazio di archiviazione.

È necessario usare le conversioni esplicite da `long`, `ulong` e `double` nelle `nint`, `nuint` e `nfloat` poiché i tipi nativi solo potrebbero essere in grado di contenere 32 bit di spazio di archiviazione.

## <a name="coregraphics-types"></a>Tipi CoreGraphics

I tipi di dati punto, le dimensioni e rettangolo utilizzati con CoreGraphics utilizzano 32 o 64 bit a seconda del dispositivo di che cui vengono eseguiti.  Quando sono stati originariamente associati, iOS e Mac API abbiamo utilizzato le strutture di dati esistenti che si sono verificati in modo che corrisponda le dimensioni della piattaforma host (i tipi di dati `System.Drawing`).

Quando si passa a **unificata**, sarà necessario sostituire le istanze di `System.Drawing` con loro `CoreGraphics` controparti, come illustrato nella tabella seguente:

|Tipo precedente in System. Drawing|Nuovo CoreGraphics di tipo di dati|Descrizione|
|--- |--- |--- |
|`RectangleF`|`CGRect`|Contiene informazioni sui punti di rettangolo mobile.|
|`SizeF`|`CGSize`|Contiene mobile punto le informazioni sulle dimensioni (width, height)|
|`PointF`|`CGPoint`|Contiene una virgola mobile, scegliere informazioni (X, Y)|

I valori float tipi utilizzati i dati vecchi per memorizzare gli elementi delle strutture di dati, mentre la nuova chiave uno utilizza `System.nfloat`.

## <a name="related-links"></a>Collegamenti correlati

- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Visual Studio classico differenze API unificata](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
