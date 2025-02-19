---
title: Acerca de las URL anonimizadas
intro: 'Si cargas una imagen o video a {% data variables.product.product_name %}, la URL de estos medios se modificará para que tu información no se pueda rastrear.'
redirect_from:
  - /articles/why-do-my-images-have-strange-urls
  - /articles/about-anonymized-image-urls
  - /authenticating-to-github/about-anonymized-image-urls
  - /github/authenticating-to-github/about-anonymized-urls
  - /github/authenticating-to-github/keeping-your-account-and-data-secure/about-anonymized-urls
versions:
  fpt: '*'
  ghec: '*'
topics:
  - Identity
  - Access management
ms.openlocfilehash: b96c01144d28d668d33e96e4067801395aaa8275
ms.sourcegitcommit: 47bd0e48c7dba1dde49baff60bc1eddc91ab10c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/05/2022
ms.locfileid: '145120009'
---
Para hospedar las imágenes, {% data variables.product.product_name %} usa el [proyecto de código abierto Camo](https://github.com/atmos/camo). Camo genera un proxy de URL anónimo para cada archivo que oculta los detalles de tu buscador y la información relacionada de otros usuarios. La dirección URL empieza por `https://<subdomain>.githubusercontent.com/`, con subdominios diferentes en función de cómo haya cargado la imagen. 

También se asignan URL anonimizadas a los videos, las cuales tienen el mismo formato que las URL de imagen, pero no se procesan a través de Camo. Esto es porque {% data variables.product.prodname_dotcom %} no es compatible con videos hospedados externamente, así que la URL anonimizada es un enlace al video cargado que hospeda {% data variables.product.prodname_dotcom %}.

Cualquiera que reciba tu URL anonimizada directa o indirectamente podrá ver tu imagen o video. Para mantener privados los archivos de medios sensibles, restríngelos a una red o servidor privado que requiere autenticación en vez de utilizar Camo.

## Solución de problemas con Camo

En circunstancias excepcionales, las imágenes procesadas mediante Camo podrían no aparecer en {% data variables.product.prodname_dotcom %}. Aquí presentamos algunos pasos que puedes tomar para determinar dónde está el problema.

{% windows %}

{% tip %}

Los usuarios de Windows deberán usar Git PowerShell (que se instala junto con [{% data variables.product.prodname_desktop %}](https://desktop.github.com/)) o descargar [curl para Windows](http://curl.haxx.se/download.html).

{% endtip %}

{% endwindows %}

### Una imagen no aparece

Si se muestra una imagen en tu buscador pero no en {% data variables.product.prodname_dotcom %}, puedes intentar solicitarla localmente.

{% data reusables.command_line.open_the_multi_os_terminal %}
1. Solicite los encabezados de la imagen mediante `curl`.
  ```shell
  $ curl -I https://www.my-server.com/images/some-image.png
  > HTTP/2 200
  > Date: Fri, 06 Jun 2014 07:27:43 GMT
  > Expires: Sun, 06 Jul 2014 07:27:43 GMT
  > Content-Type: image/x-png
  > Server: Google Frontend
  > Content-Length: 6507
  ```
3. Compruebe el valor de `Content-Type`. En este caso, es `image/x-png`.
4. Compruebe que el tipo de contenido se muestra [en la lista de tipos compatibles en Camo](https://github.com/atmos/camo/blob/master/mime-types.json).

Si Camo no admite tu tipo de contenido, puedes probar varias acciones:
  * Si eres propietario del servidor que aloja la imagen, modifícalo para que devuelva un tipo de contenido correcto para las imágenes.
  * Si estás usando un servicio externo para alojar imágenes, comunícate con servicio técnico para ese servicio.
  * Realiza una solicitud de extracción para que Camo agregue tu tipo de contenido a la lista.

### Una imagen que cambió recientemente no se está actualizando

Si recientemente modificaste una imagen y aparece en tu navegador pero no en {% data variables.product.prodname_dotcom %}, puedes intentar restablecer la caché de la imagen.

{% data reusables.command_line.open_the_multi_os_terminal %}
1. Solicite los encabezados de la imagen mediante `curl`.
  ```shell
  $ curl -I https://www.my-server.com/images/some-image.png
  > HTTP/2 200
  > Expires: Fri, 01 Jan 1984 00:00:00 GMT
  > Content-Type: image/png
  > Content-Length: 2339
  > Server: Jetty(8.y.z-SNAPSHOT)
  ```

Compruebe el valor de `Cache-Control`. En este ejemplo, no hay ningún `Cache-Control`. En tal caso:
  * Si es el propietario del servidor que hospeda la imagen, modifíquelo para que devuelva un `Cache-Control` de `no-cache` para las imágenes.
  * Si estás usando un servicio externo para alojar imágenes, comunícate con servicio técnico para ese servicio.

 Si `Cache-Control`*está* establecido en `no-cache`, póngase en contacto con el {% data variables.contact.contact_support %} o busque en el {% data variables.contact.community_support_forum %}.

### Eliminar una imagen desde la caché de Camo

Purgar la caché fuerza a cada usuario de {% data variables.product.prodname_dotcom %} a volver a solicitar la imagen, por lo que deberías usarla con mucha prudencia y solo en el caso de que los pasos anteriores no hayan funcionado.

{% data reusables.command_line.open_the_multi_os_terminal %}
1. Purgue la imagen mediante `curl -X PURGE` en la dirección URL de Camo.
  ```shell
  $ curl -X PURGE https://camo.githubusercontent.com/4d04abe0044d94fefcf9af2133223....
  > {"status": "ok", "id": "216-8675309-1008701"}
  ```

### Visualizar imágenes en redes privadas

Si una imagen está siendo proporcionada desde una red privada o desde un servidor que requiere de autenticación, se puede ver mediante {% data variables.product.prodname_dotcom %}. De hecho, no puede ser vista por ningún usuario sin pedirle que se registre en el servidor.

Para solucionar esto, mueva la imagen a un servicio que esté disponible públicamente.

## Información adicional

- "[Proxy de imágenes de usuario](https://github.com/blog/1766-proxying-user-images)" en el {% data variables.product.prodname_blog %}
