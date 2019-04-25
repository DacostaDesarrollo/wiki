#Como declarar un sidebar
Los sidebar sirven para posicionar html en una cierta parte definida de nuestro sitio webm dentro de ellos puedes adicionaar widget y asi se declara el el archivo functions.php.

```php
register_sidebar( array(
    'name'          => esc_html__( 'Sidebar name', 'xtreme-jam' ),
    'id'            => 'sidebar-id',
    'description'   => esc_html__( 'Add widgets here.', 'xtreme-jam' ),
    'before_widget' => '<section id="%1$s" class="widget %2$s redes">',
    'after_widget'  => '</section>',
    'before_title'  => '<h2 class="widget-title">',
    'after_title'   => '</h2>',
) );
```

## Qué deberías incluir en la sidebar de WordPress?

La barra lateral primaria tiene una función fundamental que consiste en destacar contenidos importantes sin robar atención al contenido principal, como pueden será las entradas de tu blog, por ejemplo.

- **El formulario de suscripción a mi newsletter.** Tener una lista de suscriptores es muy importante, desde tu lista te pueden llegar muchas oportunidades de negocio, así que te recomiendo que la ubiques cuanto más arriba mejor.

- **Mis perfiles en las redes sociales.** En mi caso cumplen una doble función, por un lado estoy facilitando la opción de seguirme en las redes sociales al visitante que lo desee y por otro muestro mi foto del perfil de Google + para posicionar mi marca personal.

- **Un buscador de contenidos.** Al principio tendrás pocos contenidos y no será necesario, pero poco a poco tu web irá creciendo y tener un buscador facilitará a tus visitantes la tarea de encontrar contenidos.

- **Temas principales del blog organizados por etiquetas.** Es una buena forma de que los usuarios encuentren contenidos sobre temáticas que les interesen.
Una insignia de Iron Blogger. Porque mola 🙂

Para asignar un sidebar en una parte del template simplemente ponemos este código con su identificador

```php
<?php if ( is_active_sidebar( 'sidebar-id' ) ) : ?>
    <?php dynamic_sidebar( 'sidebar-id' ); ?>
<?php endif; ?>
```

##Paginación en wordpres con números

Esta función nos imprime de una manera más clara la cantidad de páginas dividida en números, ya que la paginación por defecto de wordpress no nos permite mostrar ese dato

```php
function wpbeginner_numeric_posts_nav() {
	if( is_singular() )
		return;

	global $wp_query;

	/** Stop execution if there's only 1 page */
	if( $wp_query->max_num_pages <= 1 )
		return;

	$paged = get_query_var( 'paged' ) ? absint( get_query_var( 'paged' ) ) : 1;
	$max   = intval( $wp_query->max_num_pages );

	/**	Add current page to the array */
	if ( $paged >= 1 )
		$links[] = $paged;

	/**	Add the pages around the current page to the array */
	if ( $paged >= 3 ) {
		$links[] = $paged - 1;
		$links[] = $paged - 2;
	}

	if ( ( $paged + 2 ) <= $max ) {
		$links[] = $paged + 2;
		$links[] = $paged + 1;
	}

	echo '<div class="pagination"><ul>' . "\n";

	/**	Previous Post Link */
	if ( get_previous_posts_link() )
		printf( '<li>%s</li>' . "\n", get_previous_posts_link('<span class="arrow_prev">‹</span><span class="text_np">Anterior</span>') );

	/**	Link to first page, plus ellipses if necessary */
	if ( ! in_array( 1, $links ) ) {
		$class = 1 == $paged ? ' class="active"' : '';

		printf( '<li%s><a href="%s">%s</a></li>' . "\n", $class, esc_url( get_pagenum_link( 1 ) ), '1' );

		if ( ! in_array( 2, $links ) )
			echo '<li>…</li>';
	}

	/**	Link to current page, plus 2 pages in either direction if necessary */
	sort( $links );
	foreach ( (array) $links as $link ) {
		$class = $paged == $link ? ' class="active"' : '';
		printf( '<li%s><a href="%s">%s</a></li>' . "\n", $class, esc_url( get_pagenum_link( $link ) ), $link );
	}

	/**	Link to last page, plus ellipses if necessary */
	if ( ! in_array( $max, $links ) ) {
		if ( ! in_array( $max - 1, $links ) )
			echo '<li>…</li>' . "\n";

		$class = $paged == $max ? ' class="active"' : '';
		printf( '<li%s><a href="%s">%s</a></li>' . "\n", $class, esc_url( get_pagenum_link( $max ) ), $max );
	}

	/**	Next Post Link */
	if ( get_next_posts_link() )
		printf( '<li>%s</li>' . "\n", get_next_posts_link('<span class="text_np">Siguiente</span><span class="arrow_next">›</span>') );

	echo '</ul></div>' . "\n";

}
```