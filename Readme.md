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

## Mejora en el plugin añadiendo bases de datos
+ Para mejorar el plugin se añadió una base de datos para almacenar las palabras ofensivas y no ofensivas.
### Creación de la tabla en la base de datos
+ Se creó una tabla en la base de datos de WordPress que contenga las palabras.
  + Esta tabla se crea cuando se activa el plugin.
  + Para crear la tabla se utiliza la funció "dbDelta" de WordPress, a este se le asigna una consulta SQL para crear la tabla, esta tabla tabla tendrá 3 atributos, un id, palabrotas y eufemismo.
```
  function createTable(){
    global $wpdb; // this is how you get access to the database
    $table_name = $wpdb->prefix . 'nicoWords';

    $charset_collate = $wpdb->get_charset_collate();
    // SQL sentence
    $sql = "CREATE TABLE IF NOT EXISTS $table_name (
    id mediumint(9) NOT NULL AUTO_INCREMENT,
    word varchar(255) NOT NULL,
    replacement varchar(255) NOT NULL,
    PRIMARY KEY (id)
    ) $charset_collate;";
    // including the file to use dbDelta
    require_once( ABSPATH . 'wp-admin/includes/upgrade.php' );
    // executing the SQL sentence
    dbDelta( $sql );
  }
```
+ Para poder hacer posible la creación de la tabla WP nos proporciona 2 variables:
  + $wpdb: es un objeto que nos permite acceder a la base de datos de WordPress.
  + charset_collate: es una variable que contiene el conjunto de caracteres y la configuración de la base de datos.
+ Tambien precisamos usar la función dbDelta para crear la tabla, esta función se encarga de crear la tabla y de actualizarla si es necesario.

### Insertar los datos en la tabla
+ Para insertar los datos en la tabla se utiliza la función "insert" de WordPress, esta función recibe 2 parámetros, el nombre de la tabla, y un array que contiene los datos de los atributos de la tabla (palabrotas y eufemismo).
  + $offensieWordsList: es un array que contiene las palabras ofensivas.
  + $nonOffensiveWordsList: es un array que contiene las palabras no ofensivas.
  + Utilizamos hasSomething para ver si la tabla esta vacia, si es el caso, insertamos los datos en la tabla.
```
  function insertData(){
      global $wpdb, $offensiveWordsList, $nonOffensiveWordsList;
      $table_name = $wpdb->prefix . 'nicoWords';
      // we see if the table is empty
      $hasSomething = $wpdb->get_results( "SELECT * FROM $table_name" );
      if ( count($hasSomething) == 0 ) {
          // if it is empty, we insert the words
          for ($i = 0; $i < count($offensiveWordsList); $i++) {
              $wpdb->insert(
                  $table_name,
                  array(
                      'offensiveWords' => $offensiveWordsList[$i],
                      'nonOffensiveWord' => $nonOffensiveWordsList[$i]
                  )
              );
          }
      }
  }
```
### Uso del plugin con la base de datos y la obtención de los datos
+ Para obtener los datos de la base de datos se utiliza la función "get_results" de WordPress, la que recibe la consulta SQL que retorna todos los datos de la tabla.
```
  function selectData(){
    global $wpdb;
    $table_name = $wpdb->prefix . 'nicoWords';
    $results = $wpdb->get_results( "SELECT * FROM $table_name" );
    return $results;
  }
```
+ Estos datos se guardan en un array llamado $results, este array se recorre con un foreach, en cada iteración se obtiene la palabra ofensiva y la palabra no ofensiva, para luego reemplaza las palabras.
```
  function renym_wordpress_typo_fix($text){
    // take the words from the table
    $words = selectData();
    foreach ($words as $result){
        $offensiveWords[] = $result->offensiveWords; // -> para seleccionar que columna escoger
        $nonOffensiveWords[] = $result->nonOffensiveWord;
    }
    return str_replace($offensiveWords, $nonOffensiveWords, $text);
  }
```
