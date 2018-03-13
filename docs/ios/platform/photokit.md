---
title: PhotoKit
description: "Kit de fotos permite a las aplicaciones a la biblioteca de imágenes de sistema de consultas y crear la interfaz de usuario personalizada para ver y modificar su contenido."
ms.topic: article
ms.prod: xamarin
ms.assetid: 7FDEE394-3787-40FA-8372-76A05BF184B3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: 1f316979c3262883fc58df93de1afb1167fd36ce
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2018
---
# <a name="photokit"></a>PhotoKit

_Kit de fotos permite a las aplicaciones a la biblioteca de imágenes de sistema de consultas y crear la interfaz de usuario personalizada para ver y modificar su contenido._

Kit de foto es un nuevo marco que permite a las aplicaciones para la biblioteca de imágenes de sistema de consulta y crear interfaces de usuario personalizadas para ver y modificar su contenido. Incluye una serie de clases que representan la imagen y recursos de vídeo, así como las colecciones de recursos como álbumes y carpetas.

## <a name="model-objects"></a>Objetos de modelo
Kit de foto representa estos activos en lo que llama a los objetos del modelo. Los objetos de modelo que representan las fotos y vídeos por sí mismos son del tipo `PHAsset`. Un `PHAsset` contiene metadatos tales como el tipo de medio del recurso y su fecha de creación.
De forma similar, el `PHAssetCollection` y `PHCollectionList` clases contienen metadatos acerca de las colecciones de activo y listas de colección respectivamente. Las recopilaciones de activos son grupos de recursos, por ejemplo, todas las fotos y vídeos de un año determinado. Del mismo modo, listas de colección son grupos de colecciones de activos, como fotografías y vídeos que se agrupan por año.

## <a name="querying-model-data"></a>Consultar datos de modelo
Kit de fotografías resulta muy sencillo consultar los datos de modelo a través de una serie de métodos de captura. Por ejemplo, para recuperar todas las imágenes, se llamaría a `PFAsset.Fetch`, pasando el `PHAssetMediaType.Image` tipo de medio.

    PHFetchResult fetchResults = PHAsset.FetchAssets (PHAssetMediaType.Image, null);

El `PHFetchResult` instancia contendría entonces todos los `PFAsset` instancias que representan imágenes. Para obtener las imágenes por sí mismos, se utiliza el `PHImageManager` (o la versión de almacenamiento en caché, `PHCachingImageManager`) para realizar una solicitud de la imagen mediante una llamada a `RequestImageForAsset`. Por ejemplo, el código siguiente recupera una imagen para cada activo en un `PHFetchResult` para mostrar en una celda de la vista de colección:


    public override UICollectionViewCell GetCell (UICollectionView collectionView, NSIndexPath indexPath)
    {
              var imageCell = (ImageCell)collectionView.DequeueReusableCell (cellId, indexPath);
            imageMgr.RequestImageForAsset ((PHAsset)fetchResults [(uint)indexPath.Item], thumbnailSize,
        PHImageContentMode.AspectFill, new PHImageRequestOptions (), (img, info) => {
            imageCell.ImageView.Image = img;
        });
        return imageCell;
    }

Esto resulta en una cuadrícula de imágenes tal y como se muestra a continuación:

![](photokit-images/image4.png "Mostrar una cuadrícula de imágenes de la aplicación en ejecución")
 
## <a name="saving-changes-to-the-photo-library"></a>Guardando los cambios en la biblioteca de fotografías

Se muestra cómo administrar consultas y leer los datos. También puede escribir cambios volver a la biblioteca. Puesto que varias aplicaciones interesadas son puede interactuar con la biblioteca de fotografías de sistema, puede registrar un observador para recibir notificaciones de cambios mediante un PhotoLibraryObserver. A continuación, cuando se conecten los cambios, puede actualizar la aplicación en consecuencia. Por ejemplo, aquí es una implementación simple para volver a cargar la vista de colección anterior:

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
    
Realmente escritura diferida de cambios de la aplicación, cree una solicitud de cambio. Cada una de las clases del modelo tiene una clase de la solicitud de cambio asociada. Por ejemplo, para cambiar un PHAsset, cree un PHAssetChangeRequest. Los pasos para realizar cambios que se vuelve a escribir en la biblioteca de fotografías y se envían a los observadores similar al anterior son:

-   Realizar la operación de edición.
-   Guardar los datos de imagen filtrada en una instancia de PHContentEditingOutput.
-   Realizar una solicitud de cambio para publicar el formulario de cambios en la salida de edición.

Este es un ejemplo que vuelve a escribir un cambio en una imagen que se aplica un filtro de negras imagen Core:

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
 
Y, gracias a la PHPhotoLibraryChangeObserver, el cambio se reflejará en la vista de colección cuando el usuario navega nuevo:

![](photokit-images/image6.png "El cambio se reflejará en la vista de colección cuando el usuario navega atrás")
