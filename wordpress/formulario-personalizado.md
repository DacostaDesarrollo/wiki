## Como crear un formulario en el frontend con campos personalizados

1. crear  el formulario personalizados con cada unos de los campos ACF, el campo personalizado empresa que es utilizado en este ejemplo debe de estar creado con el plugin Advanced Custom Fields en este plugin se basa este tutorial

```html
<h3 class="title-form">Mi formulario*</h3>
<form name="form" id="form">
	<div class="form-group">
	    <label for="title">Titulo</label>
	    <input type="text" class="form-control" name="title" id="title" placeholder="Titulo reto" required>
	    <small class="form-text text-muted"></small>
	</div>
	<div class="form-group">
		<label for="content">Descripción</label>
		<textarea class="form-control" name="content" id="content" rows="3" placeholder="Descripción desafio"></textarea>
	        <small class="form-text text-muted"></small>
	</div>
	<div class="form-group">
		<label for="empresa">Empresa</label>
		<input type="text" class="form-control" name="empresa" id="empresa" placeholder="Mi empresa" required>
		<small class="form-text text-muted"></small>
	</div>
</form>
```

2. Crear un nuevo script exponiendo la ruta de las peticiones en wordpress

```php
wp_localize_script('mi-ajax','cms_vars',['ajaxurl'=>admin_url('admin-ajax.php')]);
```

3. Crear una variable global con la ruta de las peticiones de wordpress

```php
add_action('wp_head', 'myplugin_ajaxurl');

function myplugin_ajaxurl() {

   echo '<script type="text/javascript">
           var ajaxurl = "' . admin_url('admin-ajax.php') . '";
         </script>';
}
```

4. crear un evento para enviar los datos por ajax al servidor

```jsvascript
$('#form').submit(function(event) {
    event.preventDefault();
    let $form = $(event.target);
    /* Act on the event */
    $.ajax({
        url : ajaxurl,
        type: 'post',
        data: {
            action : 'cms_guardar_post',
            data: $form.serialize()
        },
        beforeSend: function(){
            //link.html('Cargando ...');
        },
        success: function(resultado){
             //$('#post-'+id).find('.entry-content').html(resultado);
        }

    });
});
```

5. Recoger los datos en el backend y guardar los datos en wordpress

```php
//Devolver datos a archivo js
add_action('wp_ajax_nopriv_cms_gestion_retos','cms_enviar_contenido');
add_action('wp_ajax_cms_gestion_retos','cms_enviar_contenido');

function cms_enviar_contenido()
{
	$params = array();
	//parseamos los valores serializados que llegan del frontend
	parse_str($_POST['data'], $params);

	$post = array(
        'post_title'    => $params['title'],
        'post_content'  => $_POST['content'],
        'post_status'   => 'pending',   // Could be: publish,pending,draft,private,trash
        'post_type' 	=> $params['post-type'] // Could be: `page` or your CPT
    );

    $post_id = wp_insert_post($post);

    if(is_wp_error( $post_id ) || $post_id == 0){

        echo json_encode(
        	array(
        		'status' => false,
        		'message' => 'No se pudo crear la película',
        		'error' => $post_id->get_error_messages(),
        ));

    } else {

    	// Save a basic text value.

    	//Empesa
		$field_key = "field_5d6d7dd99a0bf";
		$value = (isset($params['empresa']))? $params['empresa']:null;
		update_field( $field_key, $value, $post_id );

        echo json_encode(
        	array(
        		'status' => true,
        		'message' => 'El post se creo correctamente',
        		'id_post' => $post_id,
        ));
    }
	wp_die();
}
```