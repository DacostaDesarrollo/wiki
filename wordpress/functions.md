# Como declarar un sidebar
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

## Paginación en wordpres con números (solo uso en categorías)

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

## Paginación para paginas tipo page y wp_query personalizados

```php

<?php $paged = ( get_query_var( 'paged' ) ) ? get_query_var( 'paged' ) : 1; ?>
		<?php $tecnologias = new WP_Query( array(
		    'post_type' => 'tecnologias',
		    'post_status' => 'publish',
		    'posts_per_page' => 8,
		    'paged' => $paged
		) );

		if ( $tecnologias->have_posts() ) : ?>
			<section class="tecnologias-list">
		    	<?php while ( $tecnologias->have_posts() ) : $tecnologias->the_post();?>
					<div class="item-tecnologia">
						<?php the_title() ?>
					</div>
		    	<?php endwhile; wp_reset_postdata(); ?>
				<!-- show pagination here -->
		    	<div class="pagination">
				    <?php
				    //is_rtl() Determina si la configuración regional actual es de derecha a izquierda (RTL).

				    $prev_arrow = is_rtl() ? '<span class="text_np">Siguiente</span><span class="arrow_next">›</span>' : '<span class="arrow_prev">‹</span><span class="text_np">Anterior</span>';

					$next_arrow = is_rtl() ? '<span class="arrow_prev">‹</span><span class="text_np">Anterior</span>' : '<span class="text_np">Siguiente</span><span class="arrow_next">›</span>';
					//optienes el numero maximo de páginas
					$total = $tecnologias->max_num_pages;
					$big = 999999999; // necesita un entero improbable
					if( $total > 1 )  {
						 if( !$current_page = get_query_var('paged') )
							 $current_page = 1;
						 if( get_option('permalink_structure') ) {
							 $format = 'page/%#%/';
						 } else {
							 $format = '&paged=%#%';
						 }
						echo paginate_links(array(
							'base'			=> str_replace( $big, '%#%', esc_url( get_pagenum_link( $big ) ) ),
							'format'		=> $format,
							'current'		=> max( 1, get_query_var('paged') ),
							'total' 		=> $total,
							'mid_size'		=> 3,
							'type' 			=> 'list',
							'prev_text'		=> $prev_arrow,
							'next_text'		=> $next_arrow,
						 ) );
					} ?>
				</div>

			</section>

		<?php else : ?>
		    <!-- show 404 error here -->
		    <h2>No hay tecnologías</h2>
		<?php endif; ?>

```



## Habilitar la extención de svg en wordpress
Por defecto wordpress no permite la subida de este tipo de archivos, a continuación el código del filtro para habilitar esa opción:

```php
function cc_mime_types($mimes) {
  $mimes['svg'] = 'image/svg+xml';
  return $mimes;
}
add_filter('upload_mimes', 'cc_mime_types');
```
En las ultimas versiones de wordpress esta es la que funciona.

```php
function add_file_types_to_uploads($file_types){
	$new_filetypes = array();
	$new_filetypes['svg'] = 'image/svg+xml';
	$file_types = array_merge($file_types, $new_filetypes );
	return $file_types;
}
add_action('upload_mimes', 'add_file_types_to_uploads');
```
Resgistro de una nueva imagen

```php
add_image_size( string $name, int $width, int $height, bool|array $crop = false )

add_image_size( 'thumbnail-home', '1024', '600', [ "center", "center"] );
add_image_size( 'name-your-image', 220, 180, true );

if ( function_exists( 'add_image_size' ) ) add_theme_support( 'post-thumbnails' );
if ( function_exists( 'add_image_size' ) ) {
	add_image_size( 'cat-thumb', 200, 200 );
	add_image_size( 'search-thumb', 220, 180, true );
}

add_image_size( 'sidebar-thumb', 120, 120, true ); // Hard Crop Mode
add_image_size( 'homepage-thumb', 220, 180 ); // Soft Crop Mode
add_image_size( 'singlepost-thumb', 590, 9999 ); // Unlimited Hee
add_image_size( 'singlepost-thumb', 590, 9999 ); // Unlimited ight Mode

```

## Como crear un shostcode con los tabs de bootstrap 3

Un shortcode de WordPress es un pequeño código que se inserta en las páginas o entradas de tu web para que aparezca un determinado elemento que no viene por defecto en WordPress.

```php
function tabs($params = array()) {

	//depende de custom fields pro plugin
	// default parameters
	extract(shortcode_atts(array(
		'idPost' => ''
	), $params));

	// Return output
	ob_start();?>
	<section id="tabs-articulos-relacionados" class="tabs">
			<?php $articulos = new WP_Query(array(
				'post_type' => 'articulos',
				'meta_query' => array(
					array(
						'key' => 'relacion', // name of custom field
						'value' => '"' . get_the_ID() . '"', // matches exactly "123", not just 123. This prevents a match for "1234"
						'compare' => 'LIKE'
					)
				)
			)); ?>
			<?php if ( $articulos->have_posts() ) : ?>
				<!-- Nav tabs -->
				<ul class="nav nav-tabs" role="tablist">
					<?php // loop through the rows of data
					$contT=1;?>

				   	<?php while ( $articulos->have_posts() ) : $articulos->the_post(); ?>
						<li role="presentation" class="<?php echo ($contT == 1)?'active':''; ?>"><a href="#tab-<?php echo $contT; ?>" aria-controls="#tab-<?php echo $contT; ?>" role="tab" data-toggle="tab"> <?php the_title(); ?></a></li>
						<?php $contT++; ?>
				   	<?php endwhile; ?>
			   	</ul>

				<!-- Tab panes -->
				<div class="tab-content">
					<?php // loop through the rows of data
					$contC = 1;?>

				   	<?php while ( $articulos->have_posts() ) : $articulos->the_post(); ?>
						<div role="tabpanel" class="tab-pane <?php echo ($contC == 1)?'active':''; ?>" id="tab-<?php echo $contC; ?>">
							<div class="row">
								<div class="col-50 texto">
									<div class="titulo-articulo">
										<h2><?php the_title(); ?></h2>
									</div>
									<div class="tab-texto">
										<?php the_content(); ?>
									</div>
									<?php if (get_field('mostrar_enlace')): ?>
										<div class="enlace">
											<a href="<?php echo esc_url(the_field('enlace')); ?>" title="<?php the_title_attribute(); ?>"><?php pll_e("Learn more here"); ?></a>
										</div>
									<?php endif ?>
								</div>
								<div class="col-50">

									<?php if (get_field('galeria') != false): ?>
										<?php if (have_rows('galeria')): ?>
											<div class="imagen-content-tab">
												<div class="galeria-articulos owl-carousel owl-theme">
													<?php // loop through the rows of data
												    while ( have_rows('galeria') ) : the_row(); ?>
														<?php $imageArti = get_sub_field('imagen_galeria');
														$size = 'thumbnail-articulos'; // (thumbnail, medium, large, full or custom size)?>
															<div class="item-galeria">
																<?php echo wp_get_attachment_image( $imageArti, $size , "", ["class" => "img-responsive"] ); ?>
																<div class="titulo-galeria">
																	<?php the_sub_field('titulo'); ?>
																</div>
															</div>
												   	<?php endwhile; ?>
												</div>
											</div>
										<?php endif ?>
									<?php endif ?>

									<?php if (get_field('galeria') == false): ?>
										<div class="imagen-content-tab">
										<?php the_post_thumbnail('thumbnail-articulos', ['class' => 'img-responsive', 'title' =>get_the_title()]); ?>
										</div>
									<?php endif ?>

								</div>
							</div>
						</div>
						<?php $contC++; ?>
				   	<?php endwhile; ?>
				</div>

			<?php endif ?>
	</section><?php

	return ob_get_clean();
}
add_shortcode('tabs', 'tabs');
```

## Limite para los textos cortos de excerpt()

para limitar la cantidad de palabras utilizamos el siguiente hook

```php
// Filter except length to 35 words.
// tn custom excerpt length
function tn_custom_excerpt_length( $length ) {
return 35;
}
add_filter( 'excerpt_length', 'tn_custom_excerpt_length', 999 );
```

## Personalizar el fin del texto cortado por excerpt()

Por defexto wordpress el fin del texto lo visualiza con [...] para cambiarlo basta con utilizar el siguiente hook

```php
// Replaces the excerpt "Read More"
function new_excerpt_more($more) {
	return '...';
}
add_filter('excerpt_more', 'new_excerpt_more');

```
