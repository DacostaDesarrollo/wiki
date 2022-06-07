# Función para separar las categorias por comas

```php
$term_obj_list = get_the_terms( $post->ID, 'taxonomy' );
$terms_string = join(', ', wp_list_pluck($term_obj_list, 'name'));
```

# Estructura de consulta sql personalizada para wordpress y evitar la inyección sql

```php 
function get_ciudades(){
    // Check for nonce security
    $nonce = sanitize_text_field( $_GET['nonce'] );
    if ( ! wp_verify_nonce( $nonce, 'my-ajax-nonce' ) ) {
        die ( 'Busted!');
    }
    global $wpdb;
    $value = '%' . $wpdb->esc_like( $_GET['term'] ) . '%';
    $sql = $wpdb->prepare( "SELECT {$wpdb->prefix}city.name,{$wpdb->prefix}state.name as state FROM {$wpdb->prefix}city left join {$wpdb->prefix}state on {$wpdb->prefix}city.state_id = {$wpdb->prefix}state.id WHERE ({$wpdb->prefix}city.name LIKE %s or {$wpdb->prefix}state.name LIKE %s) and ({$wpdb->prefix}state.country_id = 233) limit 8", $value,$value );
    
    $data = $wpdb->get_results( $sql );
    if($data){
        for ($i=0; $i < count($data); $i++) { 
            $respuesta[] = array('id'=>$data[$i]->name.', '.$data[$i]->state,'value'=>$data[$i]->name.', '.$data[$i]->state,'label'=>$data[$i]->name.', '.$data[$i]->state);
        }
    }
    echo json_encode($respuesta,true);
    die();
}

add_action( 'wp_ajax_nopriv_get_ciudades', 'get_ciudades' );
add_action( 'wp_ajax_get_ciudades', 'get_ciudades' );

```
