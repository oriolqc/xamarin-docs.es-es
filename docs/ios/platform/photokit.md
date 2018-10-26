---
title: PhotoKit en Xamarin.iOS
description: PhotoKit, tratar sus objetos de modelo, este documento describe cómo consultar los datos de modelo y guardar los cambios realizados en la biblioteca de fotografías.
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/14/2017
ms.openlocfilehash: d59cac9403a244ce553d84e0590b8a9c3d4d2f30
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102716"
---
# <a name="photokit-in-xamarinios"></a>PhotoKit en Xamarin.iOS

PhotoKit es un nuevo marco que permite a las aplicaciones para la biblioteca de imágenes de sistema de consulta y crear interfaces de usuario personalizadas para ver y modificar su contenido. Incluye una serie de clases que representan la imagen y activos de vídeo, así como las colecciones de recursos, como carpetas y álbumes.

## <a name="model-objects"></a>Objetos de modelo

PhotoKit representa estos activos en lo que llama a los objetos del modelo. Los objetos de modelo que representan las fotos y vídeos a sí mismos son del tipo `PHAsset`. Un `PHAsset` contiene metadatos tales como el tipo de medio del recurso y su fecha de creación.
De forma similar, el `PHAssetCollection` y `PHCollectionList` clases contienen metadatos sobre las colecciones de recursos y las listas de colección, respectivamente. Las colecciones de activos son grupos de recursos, por ejemplo, todas las fotos y vídeos de un año determinado. Del mismo modo, las listas de colección son grupos de colecciones de activos, como fotos y vídeos que se agrupan por año.

## <a name="querying-model-data"></a>Consultar datos de modelo

PhotoKit facilita a los datos del modelo de consulta a través de una variedad de métodos de captura. Por ejemplo, para recuperar todas las imágenes, se llamaría a `PFAsset.Fetch`, pasando el `PHAssetMediaType.Image` tipo de medio.

    PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);

El `PHFetchResult` instancia contendría entonces todos los `PFAsset` instancias que representan las imágenes. Para obtener las imágenes propias, usa el `PHImageManager` (o la versión de almacenamiento en caché, `PHCachingImageManager`) para realizar una solicitud para la imagen mediante una llamada a `RequestImageForAsset`. Por ejemplo, el código siguiente recupera una imagen para cada recurso en un `PHFetchResult` para mostrar en una celda de vista de colección:


    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
              var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
            imageMgr.RequestImageForAsset ((PHAsset)fetchResults [(uint)indexPath.Item], thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (), (img, info) => {
            imageCell.ImageView.Image = img;
        });
        return imageCell;
    }

Esto da como resultado una cuadrícula de imágenes, como se muestra a continuación:

![](photokit-images/image4.png "Muestra una cuadrícula de imágenes de la aplicación en ejecución")
 
## <a name="saving-changes-to-the-photo-library"></a>Guardando los cambios en la biblioteca de fotografías

Es cómo realizar consultas y leer los datos. También puede escribir los cambios de vuelta a la biblioteca. Dado que varias aplicaciones interesadas pueden interactuar con la biblioteca de fotos del sistema, puede registrar un observador para recibir una notificación de cambios mediante un PhotoLibraryObserver. A continuación, cuando los cambios entran, puede actualizar la aplicación en consecuencia. Por ejemplo, aquí es una implementación simple para volver a cargar la vista de colección anterior:

    class PhotoLibraryObserver : PHPhotoLibraryChangeObserver
    {
        readonly PhotosViewController controller;
        public PhotoLibraryObserver (PhotosViewController controller)
        
        {
            this.controller = controller;
        }
    
        public override void PhotoLibraryDidChange (PHChange changeInstance)
        {
            DispatchQueue.MainQueue.DispatchAsync (() => {
            var changes = changeInstance.GetFetchResultChangeDetails (controller.fetchResults);
            controller.fetchResults = changes.FetchResultAfterChanges;
            controller.CollectionView.ReloadData ();
            });
        }
    }
    
Para escribir realmente los cambios a atrás desde la aplicación, cree una solicitud de cambio. Cada una de las clases del modelo tiene una clase de solicitud de cambio asociada. Por ejemplo, para cambiar un PHAsset, cree un PHAssetChangeRequest. Los pasos para realizar los cambios que se reescriben a la biblioteca de fotos y se envían a los observadores como la anterior son:

-   Realizar la operación de edición.
-   Guardar los datos de imagen filtrada en una instancia de PHContentEditingOutput.
-   Realice una solicitud de cambio para publicar el formulario de los cambios en la salida de la edición.

Este es un ejemplo que escriba un cambio en una imagen que se aplica un filtro de imagen Core negras:

    void ApplyNoirFilter (object sender, EventArgs e)
    {
            
            Asset.RequestContentEditingInput (new PHContentEditingInputRequestOptions (), (input, options) => {
            
        // perform the editing operation, which applies a noir filter in this case
            var image = CIImage.FromUrl (input.FullSizeImageUrl);
            image = image.CreateWithOrientation((CIImageOrientation)input.FullSizeImageOrientation);
            var noir = new CIPhotoEffectNoir {
                Image = image
            };
            var ciContext = CIContext.FromOptions (null);
            var output = noir.OutputImage;
            var uiImage = UIImage.FromImage (ciContext.CreateCGImage (output, output.Extent));
            imageView.Image = uiImage;
        //
        // save the filtered image data to a PHContentEditingOutput instance
            var editingOutput = new PHContentEditingOutput(input);
            var adjustmentData = new PHAdjustmentData();
            var data = uiImage.AsJPEG();
            NSError error;
            data.Save(editingOutput.RenderedContentUrl, false, out error);
            editingOutput.AdjustmentData = adjustmentData;
        //
        // make a change request to publish the changes form the editing output
            PHPhotoLibrary.GetSharedPhotoLibrary.PerformChanges (() => {
                PHAssetChangeRequest request = PHAssetChangeRequest.ChangeRequest(Asset);
                request.ContentEditingOutput = editingOutput;
          },
          (ok, err) => Console.WriteLine ("photo updated successfully: {0}", ok));
      });
    }
    
Cuando el usuario selecciona el botón, se aplica el filtro:

![](photokit-images/image5.png "Un ejemplo del filtro que se va a aplicar")
 
Y gracias a la PHPhotoLibraryChangeObserver, el cambio se refleja en la vista de colección cuando el usuario navega de vuelta:

![](photokit-images/image6.png "El cambio se refleja en la vista de colección cuando el usuario navega de vuelta")
