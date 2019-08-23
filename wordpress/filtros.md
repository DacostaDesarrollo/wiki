# Creando un archivo WP con filtro de campo personalizado

Este código proporciona la funcionadlidad para crear filtros con Acf plugin, identificando taxonomias, tipos de contenido y contenidos relacionados

```php
// array of filters (field key => field name)
$GLOBALS['perfiles_user'] = array(
	'taxonomia'				=> 'categorias',
	'postType'				=> array(
		'name' => 'cinemateca_virtual',
		'relationship' => 'peliculas'
	),
	'field_5d25f54cc72b0'	=> 'lugar_de_residencia',
);


// action
add_action('pre_get_posts', 'my_pre_get_posts', 10, 1);

function my_pre_get_posts( $query ) {

	// bail early if is in admin
	if( is_admin() ) return;


	// bail early if not main query
	// - allows custom code / plugins to continue working
	if( !$query->is_main_query() ) return;


	// get meta query
	$meta_query = $query->get('meta_query');
	$tax_query = $query->get('tax_query');


	if (count($_GET) > 0) {
		# code...

		// loop over filters
		foreach( $GLOBALS[$query->query['post_type']] as $key => $name ) {
			// continue if not found in url

			if ($key == 'postType') {
				# code...
				$name = $name['relationship'];
			}

			if( empty($_GET[ $name ]) ) {

				continue;

			}


			// get the value for this filter
			// eg: http://www.website.com/events?city=melbourne,sydney
			$value = explode(',', $_GET[ $name ]);

			if ($key == 'taxonomia') {
				// append tax query
		        $tax_query[] = array(
		            'taxonomy' => $name,
		            'field' => 'slug',
		            'terms' => $value,
		            'operator'=> 'IN'
		        );

			}else{

				// append meta query
		    	if ($key == 'postType') {
		    		$meta_query[] = array(
			            'key'		=> $name,
			            'value'		=> '"' . $value[0]. '"',
			            'compare'	=> 'LIKE',

		        	);



		    	}else{
			    	$meta_query[] = array(
			            'key'		=> $name,
			            'value'		=> $value,
			            'compare'	=> 'IN',
			        );
		    	}

			}
		}
	}

	$query->set( 'tax_query', $tax_query );
	// update meta query
	$query->set('meta_query', $meta_query);

}

```

Este fragmento de código se usa dentro de un archivo de plantilla que se muestra en la página de archivo para su tipo de publicación, imprime todos los controles necesarios para hacer filtros tales como select en caso de taxonomia o input para busqueda.

```php

<div id="archive-filters" class="row mb50">
            <?php foreach( $GLOBALS['perfiles_user'] as $key => $name ):
                if ($key == 'postType') {
                    # code...
                    $valor = (isset($_GET[ $name['name'] ]))? $_GET[ $name ]:null;
                }else{
                    $valor = (isset($_GET[ $name ]))? $_GET[ $name ]:null;
                }

                switch ($key) {
                    case 'taxonomia':
                        # code...
                        ?>

                        <div class="filter col-lg-4 col-md-6 col-sm-12" data-filter="<?php echo $name; ?>">
                            <?php $terms = get_terms($name); ?>
                                <?php if (!empty($terms) && !is_wp_error($terms)): ?>

                                <select name="<?php echo $name; ?>">
                                    <option value="" data-i="0">- Select -</option>
                                    <?php foreach($terms as $term): ?>
                                        <option <?php echo ( $valor == $term->slug ? 'selected="selected"' : '' ) ?> value="<?php echo $term->slug ?>">
                                            <?php echo $term->name ?>
                                        </option>

                                    <?php endforeach; ?>

                               </select>
                                <?php endif ?>
                        </div>
                        <?php
                        break;
                    case 'postType':

                        $postsType = get_posts(array(
                            'post_type' =>$name['name'],
                        ));?>

                            <?php if ($postsType): ?>
                                <div class="filter col-lg-4 col-md-6 col-sm-12" data-filter="<?php echo $name['relationship']; ?>">

                                    <select name="<?php echo $name['relationship']; ?>">
                                        <option value="" data-i="0">- Select -</option>
                                    <?php foreach ($postsType as $key => $postType): ?>
                                        <option <?php echo ( $valor == $postType->ID ? 'selected="selected"' : '' ) ?> value="<?php echo $postType->ID ?>">
                                                <?php echo get_the_title( $postType->ID ); ?>
                                        </option>

                                    <?php endforeach ?>
                                    </select>

                                </div>
                            <?php endif ?>

                        <?php
                        break;

                    default:
                        // get the field's settings without attempting to load a value
                        $field = get_field_object($key, false, false);

                        // set value if available
                        if( isset($_GET[ $name ]) ) {

                            $field['value'] = explode(',', $_GET[ $name ]);
                            // create filter

                        }?>
                        <div class="filter col-lg-4 col-md-6 col-sm-12" data-filter="<?php echo $name; ?>">
                            <?php create_field( $field ); ?>
                        </div>
                        <?php
                        break;
                }?>

            <?php endforeach; ?>
</div>

```

El siguiente código llama al evento de los filtros por medio de javascript, adiciona los parametros por url y redirecciona con los datos que hay escogido el usuario.

```javascript
(function($) {
	// change
	$('#archive-filters').on('change', 'input[type="checkbox"],select,input', function(){

		// vars
		var url = '<?php echo home_url('property'); ?>';
			args = {};

		// loop over filters
		$('#archive-filters .filter').each(function(){

			// vars
			var filter = $(this).data('filter'),
				vals = [];

			// find checked inputs
			$(this).find('input:checked').each(function(){
				vals.push( $(this).val() );
			});

			// append to args
			args[ filter ] = vals.join(',');

		});

		// update url
		url += '?';

		// loop over args
		$.each(args, function( name, value ){

			url += name + '=' + value + '&';

		});

		// remove last &
		url = url.slice(0, -1);

		// reload page
		window.location.replace( url );
	});

})(jQuery);
```