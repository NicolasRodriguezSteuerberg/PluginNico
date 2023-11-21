# Nico Words Plugin de WordPress

## Descripcion
El plugin de WordPress Nico Words es una herramienta simple de filtrado de contenido diseñada para el curso de desarrollo de plugins de WordPress. Reemplaza palabras ofensivas con alternativas no ofensivas en el contenido de entradas y páginas.

## ¿Cómo funciona?
+ El plugin escanea el contenido de entradas y páginas en busca de una lista predefinida de palabras ofensivas.
+ Cuando encuentra una palabra ofensiva, la reemplaza por una palabra no ofensiva de la lista correspondiente.

## Cómo usar
+ Una vez instalado y activado, el plugin comenzará a filtrar automáticamente el contenido de las entradas y páginas durante su visualización.

## Que es add_filter
+ add_filter es una función de WordPress que permite modificar el contenido de una variable antes de que se muestre en la pantalla.
+ este add_filter recibe dos parámetros: el nombre del filtro y el nombre de la función que se ejecutará cuando se llame al filtro.

## Que función se ejecuta cuando se llama al filtro
+ La función que se ejecuta cuando se llama al filtro es la función `renym_wordpress_typo_fix`.
+ Esta funcion es el núcleo de este plugin. Su propósito es realizar cambios en el contenido de las entradas y páginas de WordPress, remplazando las palabras ofensivas por alternativas no ofensivas:
  + La función examina el contenido de una entrada o página en busca de palabras ofensivas definidas en la lista $offensiveWordsList.
  + Cada vez que encuentra una palabra ofensiva, la sustituye por la palabra no ofensiva correspondiente en la lista $nonOffensiveWordsList.

## Uso en el Contexto de WordPress
+ La función se utiliza como filtro en el gancho the_content. Esto significa que se aplica al contenido de las entradas y páginas cada vez que se muestra en el sitio.