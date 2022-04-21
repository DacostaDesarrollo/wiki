# Función para separar las categorias por comas

```php
$term_obj_list = get_the_terms( $post->ID, 'taxonomy' );
$terms_string = join(', ', wp_list_pluck($term_obj_list, 'name'));
```
