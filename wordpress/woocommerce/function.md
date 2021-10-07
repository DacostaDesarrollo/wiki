# Título al lado de la galería (woocommerce_single_product_summary)
```php
/**
 * Título de primero al lado de la galería
 */

remove_action( 'woocommerce_single_product_summary', 'woocommerce_template_single_title ');
add_action( 'woocommerce_single_product_summary', 'woocommerce_template_single_title', 1 );
```

# Adicionar descripción del producto en (woocommerce_single_product_summary)

```php
add_action( 'woocommerce_after_single_product_summary', 'bbloomer_wc_output_long_description', 10 );
  
function bbloomer_wc_output_long_description() {
?>
   <div class="woocommerce-description">
   <?php the_content(); ?>
   </div>
<?php
}
```

# Adicionar contenedor personalizado al la página del producto

Cubre toda la información del prodcucto
```php
add_action('woocommerce_before_main_content', 'my_theme_wrapper_start', 10);
add_action('woocommerce_after_main_content', 'my_theme_wrapper_end', 10);

function my_theme_wrapper_start() {
    echo '<div class="contendor_principal">';
}

function my_theme_wrapper_end() {
    echo '</div>';
}
```

# Eliminar pestañas de productos

Estas pestañas aparecen en la parte inferior de la interna del producto se eliminan con esta instrucción.

```php
add_filter( 'woocommerce_product_tabs', 'woo_remove_product_tabs', 98 );

function woo_remove_product_tabs( $tabs ) {
    unset( $tabs['description'] );          // Remove the description tab
    unset( $tabs['reviews'] );          // Remove the reviews tab
    unset( $tabs['additional_information'] );   // Remove the additional information tab
    return $tabs;
}
```

# Remover el sku de los productos

Este campo es el identificador de cada producto normalmente es util para el inventario
```php
function sv_remove_product_page_skus( $enabled ) {
    if ( ! is_admin() && is_product() ) {
        return false;
    }
    return $enabled;
}
add_filter( 'wc_product_sku_enabled', 'sv_remove_product_page_skus' );
```

# Mostrar imágenes de la galería en el catálogo de WooCommerce

```php

add_action('woocommerce_shop_loop_item_title','dl_galeria_fuera_en_tienda', 5);
function dl_galeria_fuera_en_tienda() {
	if ( is_shop() ) {
		global $product;
		$attachment_ids = $product->get_gallery_attachment_ids();
		echo '<div class="dl-producto-img">';
		foreach( array_slice( $attachment_ids, 0,3 ) as $attachment_id ) {
		  	$thumbnail_url = wp_get_attachment_image_src( $attachment_id, 'thumbnail' )[0];
		  	echo '<img class="dl-img" src="' . $thumbnail_url . '">';
		}
		echo '</div>';
	}
 }
```


# Hook de woocommerce 

```php
/**
 * WooCommerce Template Hooks
 *
 * Action/filter hooks used for WooCommerce functions/templates.
 *
 * @author     WooThemes
 * @category   Core
 * @package   WooCommerce/Templates
 * @version     2.1.0
 */
if ( ! defined( 'ABSPATH' ) ) {
  exit; // Exit if accessed directly
}
add_filter( 'body_class', 'wc_body_class' );
add_filter( 'post_class', 'wc_product_post_class', 20, 3 );
/**
 * WP Header.
 *
 * @see  wc_generator_tag()
 */
add_action( 'get_the_generator_html', 'wc_generator_tag', 10, 2 );
add_action( 'get_the_generator_xhtml', 'wc_generator_tag', 10, 2 );
/**
 * Content Wrappers.
 *
 * @see woocommerce_output_content_wrapper()
 * @see woocommerce_output_content_wrapper_end()
 */
add_action( 'woocommerce_before_main_content', 'woocommerce_output_content_wrapper', 10 );
add_action( 'woocommerce_after_main_content', 'woocommerce_output_content_wrapper_end', 10 );
/**
 * Sale flashes.
 *
 * @see woocommerce_show_product_loop_sale_flash()
 * @see woocommerce_show_product_sale_flash()
 */
add_action( 'woocommerce_before_shop_loop_item_title', 'woocommerce_show_product_loop_sale_flash', 10 );
add_action( 'woocommerce_before_single_product_summary', 'woocommerce_show_product_sale_flash', 10 );
/**
 * Breadcrumbs.
 *
 * @see woocommerce_breadcrumb()
 */
add_action( 'woocommerce_before_main_content', 'woocommerce_breadcrumb', 20, 0 );
/**
 * Sidebar.
 *
 * @see woocommerce_get_sidebar()
 */
add_action( 'woocommerce_sidebar', 'woocommerce_get_sidebar', 10 );
/**
 * Archive descriptions.
 *
 * @see woocommerce_taxonomy_archive_description()
 * @see woocommerce_product_archive_description()
 */
add_action( 'woocommerce_archive_description', 'woocommerce_taxonomy_archive_description', 10 );
add_action( 'woocommerce_archive_description', 'woocommerce_product_archive_description', 10 );
/**
 * Products Loop.
 *
 * @see woocommerce_result_count()
 * @see woocommerce_catalog_ordering()
 */
add_action( 'woocommerce_before_shop_loop', 'woocommerce_result_count', 20 );
add_action( 'woocommerce_before_shop_loop', 'woocommerce_catalog_ordering', 30 );
add_action( 'woocommerce_no_products_found', 'wc_no_products_found' );
/**
 * Product Loop Items.
 *
 * @see woocommerce_template_loop_product_link_open()
 * @see woocommerce_template_loop_product_link_close()
 * @see woocommerce_template_loop_add_to_cart()
 * @see woocommerce_template_loop_product_thumbnail()
 * @see woocommerce_template_loop_product_title()
 * @see woocommerce_template_loop_category_link_open()
 * @see woocommerce_template_loop_category_title()
 * @see woocommerce_template_loop_category_link_close()
 * @see woocommerce_template_loop_price()
 * @see woocommerce_template_loop_rating()
 */
add_action( 'woocommerce_before_shop_loop_item', 'woocommerce_template_loop_product_link_open', 10 );
add_action( 'woocommerce_after_shop_loop_item', 'woocommerce_template_loop_product_link_close', 5 );
add_action( 'woocommerce_after_shop_loop_item', 'woocommerce_template_loop_add_to_cart', 10 );
add_action( 'woocommerce_before_shop_loop_item_title', 'woocommerce_template_loop_product_thumbnail', 10 );
add_action( 'woocommerce_shop_loop_item_title', 'woocommerce_template_loop_product_title', 10 );
add_action( 'woocommerce_before_subcategory', 'woocommerce_template_loop_category_link_open', 10 );
add_action( 'woocommerce_shop_loop_subcategory_title', 'woocommerce_template_loop_category_title', 10 );
add_action( 'woocommerce_after_subcategory', 'woocommerce_template_loop_category_link_close', 10 );
add_action( 'woocommerce_after_shop_loop_item_title', 'woocommerce_template_loop_price', 10 );
add_action( 'woocommerce_after_shop_loop_item_title', 'woocommerce_template_loop_rating', 5 );
/**
 * Subcategories.
 *
 * @see woocommerce_subcategory_thumbnail()
 */
add_action( 'woocommerce_before_subcategory_title', 'woocommerce_subcategory_thumbnail', 10 );
/**
 * Before Single Products Summary Div.
 *
 * @see woocommerce_show_product_images()
 * @see woocommerce_show_product_thumbnails()
 */
add_action( 'woocommerce_before_single_product_summary', 'woocommerce_show_product_images', 20 );
add_action( 'woocommerce_product_thumbnails', 'woocommerce_show_product_thumbnails', 20 );
/**
 * After Single Products Summary Div.
 *
 * @see woocommerce_output_product_data_tabs()
 * @see woocommerce_upsell_display()
 * @see woocommerce_output_related_products()
 */
add_action( 'woocommerce_after_single_product_summary', 'woocommerce_output_product_data_tabs', 10 );
add_action( 'woocommerce_after_single_product_summary', 'woocommerce_upsell_display', 15 );
add_action( 'woocommerce_after_single_product_summary', 'woocommerce_output_related_products', 20 );
/**
 * Product Summary Box.
 *
 * @see woocommerce_template_single_title()
 * @see woocommerce_template_single_rating()
 * @see woocommerce_template_single_price()
 * @see woocommerce_template_single_excerpt()
 * @see woocommerce_template_single_meta()
 * @see woocommerce_template_single_sharing()
 */
add_action( 'woocommerce_single_product_summary', 'woocommerce_template_single_title', 5 );
add_action( 'woocommerce_single_product_summary', 'woocommerce_template_single_rating', 10 );
add_action( 'woocommerce_single_product_summary', 'woocommerce_template_single_price', 10 );
add_action( 'woocommerce_single_product_summary', 'woocommerce_template_single_excerpt', 20 );
add_action( 'woocommerce_single_product_summary', 'woocommerce_template_single_meta', 40 );
add_action( 'woocommerce_single_product_summary', 'woocommerce_template_single_sharing', 50 );
/**
 * Reviews
 *
 * @see woocommerce_review_display_gravatar()
 * @see woocommerce_review_display_rating()
 * @see woocommerce_review_display_meta()
 * @see woocommerce_review_display_comment_text()
 */
add_action( 'woocommerce_review_before', 'woocommerce_review_display_gravatar', 10 );
add_action( 'woocommerce_review_before_comment_meta', 'woocommerce_review_display_rating', 10 );
add_action( 'woocommerce_review_meta', 'woocommerce_review_display_meta', 10 );
add_action( 'woocommerce_review_comment_text', 'woocommerce_review_display_comment_text', 10 );
/**
 * Product Add to cart.
 *
 * @see woocommerce_template_single_add_to_cart()
 * @see woocommerce_simple_add_to_cart()
 * @see woocommerce_grouped_add_to_cart()
 * @see woocommerce_variable_add_to_cart()
 * @see woocommerce_external_add_to_cart()
 * @see woocommerce_single_variation()
 * @see woocommerce_single_variation_add_to_cart_button()
 */
add_action( 'woocommerce_single_product_summary', 'woocommerce_template_single_add_to_cart', 30 );
add_action( 'woocommerce_simple_add_to_cart', 'woocommerce_simple_add_to_cart', 30 );
add_action( 'woocommerce_grouped_add_to_cart', 'woocommerce_grouped_add_to_cart', 30 );
add_action( 'woocommerce_variable_add_to_cart', 'woocommerce_variable_add_to_cart', 30 );
add_action( 'woocommerce_external_add_to_cart', 'woocommerce_external_add_to_cart', 30 );
add_action( 'woocommerce_single_variation', 'woocommerce_single_variation', 10 );
add_action( 'woocommerce_single_variation', 'woocommerce_single_variation_add_to_cart_button', 20 );
/**
 * Pagination after shop loops.
 *
 * @see woocommerce_pagination()
 */
add_action( 'woocommerce_after_shop_loop', 'woocommerce_pagination', 10 );
/**
 * Product page tabs.
 */
add_filter( 'woocommerce_product_tabs', 'woocommerce_default_product_tabs' );
add_filter( 'woocommerce_product_tabs', 'woocommerce_sort_product_tabs', 99 );
/**
 * Additional Information tab.
 *
 * @see wc_display_product_attributes()
 */
add_action( 'woocommerce_product_additional_information', 'wc_display_product_attributes', 10 );
/**
 * Checkout.
 *
 * @see woocommerce_checkout_login_form()
 * @see woocommerce_checkout_coupon_form()
 * @see woocommerce_order_review()
 * @see woocommerce_checkout_payment()
 */
add_action( 'woocommerce_before_checkout_form', 'woocommerce_checkout_login_form', 10 );
add_action( 'woocommerce_before_checkout_form', 'woocommerce_checkout_coupon_form', 10 );
add_action( 'woocommerce_checkout_order_review', 'woocommerce_order_review', 10 );
add_action( 'woocommerce_checkout_order_review', 'woocommerce_checkout_payment', 20 );
/**
 * Cart widget
 */
add_action( 'woocommerce_widget_shopping_cart_buttons', 'woocommerce_widget_shopping_cart_button_view_cart', 10 );
add_action( 'woocommerce_widget_shopping_cart_buttons', 'woocommerce_widget_shopping_cart_proceed_to_checkout', 20 );
/**
 * Cart.
 *
 * @see woocommerce_cross_sell_display()
 * @see woocommerce_cart_totals()
 * @see woocommerce_button_proceed_to_checkout()
 */
add_action( 'woocommerce_cart_collaterals', 'woocommerce_cross_sell_display' );
add_action( 'woocommerce_cart_collaterals', 'woocommerce_cart_totals', 10 );
add_action( 'woocommerce_proceed_to_checkout', 'woocommerce_button_proceed_to_checkout', 20 );
/**
 * Footer.
 *
 * @see  wc_print_js()
 * @see woocommerce_demo_store()
 */
add_action( 'wp_footer', 'wc_print_js', 25 );
add_action( 'wp_footer', 'woocommerce_demo_store' );
/**
 * Order details.
 *
 * @see woocommerce_order_details_table()
 * @see woocommerce_order_again_button()
 */
add_action( 'woocommerce_view_order', 'woocommerce_order_details_table', 10 );
add_action( 'woocommerce_thankyou', 'woocommerce_order_details_table', 10 );
add_action( 'woocommerce_order_details_after_order_table', 'woocommerce_order_again_button' );
/**
 * Auth.
 *
 * @see woocommerce_output_auth_header()
 * @see woocommerce_output_auth_footer()
 */
add_action( 'woocommerce_auth_page_header', 'woocommerce_output_auth_header', 10 );
add_action( 'woocommerce_auth_page_footer', 'woocommerce_output_auth_footer', 10 );
/**
 * Comments.
 *
 * Disable Jetpack comments.
 */
add_filter( 'jetpack_comment_form_enabled_for_product', '__return_false' );
/**
 * My Account.
 */
add_action( 'woocommerce_account_navigation', 'woocommerce_account_navigation' );
add_action( 'woocommerce_account_content', 'woocommerce_account_content' );
add_action( 'woocommerce_account_orders_endpoint', 'woocommerce_account_orders' );
add_action( 'woocommerce_account_view-order_endpoint', 'woocommerce_account_view_order' );
add_action( 'woocommerce_account_downloads_endpoint', 'woocommerce_account_downloads' );
add_action( 'woocommerce_account_edit-address_endpoint', 'woocommerce_account_edit_address' );
add_action( 'woocommerce_account_payment-methods_endpoint', 'woocommerce_account_payment_methods' );
add_action( 'woocommerce_account_add-payment-method_endpoint', 'woocommerce_account_add_payment_method' );
add_action( 'woocommerce_account_edit-account_endpoint', 'woocommerce_account_edit_account' );
```
# Elimina el zoom de la imagen cuando se pasa el mouse por encima

```php
add_filter ('woocommerce_single_product_zoom_options', 'custom_single_product_zoom_options', 10, 3);
function custom_single_product_zoom_options ($zoom_options) {
    // Desactivar zoom magnificar:
    $zoom_options ['magnify'] = 0;
    return $zoom_options;
}
```
# Cómo personalizar los campos de pago de WooCommerce


WooCommerce proporciona todos los campos esenciales para su página de pago. De forma predeterminada, solicita a los clientes:

- Detalles de facturación
- Nombre de pila
- Apellido
- Nombre de empresa
- País
- Dirección
- Pueblo / Ciudad
- Distrito
- Código postal / ZIP
- Teléfono
- Dirección de correo electrónico
- Pedidos

Hay muchas formas de personalizar la página, que incluyen:

- Editando el diseño
- Cambiar el texto en el botón "Realizar pedido" 
- Eliminar un campo
- Hacer un campo obligatorio (o no obligatorio)
- Cambiar las etiquetas de los campos de entrada y el texto del marcador de posición
- Recopilar los números de cuenta de los clientes
- Verificación de una preferencia de entrega
- Permitir a los clientes solicitar una fecha de entrega o un plazo
- Establecer un método de contacto preferido

Estas son solo algunas de las personalizaciones que puede realizar; WooCommerce proporciona una flexibilidad casi infinita para todos los niveles de experiencia. Si se siente cómodo editando código, puede personalizarlo con fragmentos de código. Si prefiere un poco más de estructura, hay una variedad de extensiones y complementos para editar los campos de pago.

```php
add_filter('woocommerce_checkout_fields', 'custom_override_checkout_fields');
function custom_override_checkout_fields($fields)
 {
	 unset($fields['billing']['billing_address_2']);
	 $fields['billing']['billing_company']['placeholder'] = 'Business Name';
	 $fields['billing']['billing_company']['label'] = 'Business Name';
	 $fields['billing']['billing_first_name']['placeholder'] = 'First Name'; 
	 $fields['shipping']['shipping_first_name']['placeholder'] = 'First Name';
	 $fields['shipping']['shipping_last_name']['placeholder'] = 'Last Name';
	 $fields['shipping']['shipping_company']['placeholder'] = 'Company Name'; 
	 $fields['billing']['billing_last_name']['placeholder'] = 'Last Name';
	 $fields['billing']['billing_email']['placeholder'] = 'Email Address ';
	 $fields['billing']['billing_phone']['placeholder'] = 'Phone ';
	 return $fields;
 }
```

# Cómo personalizar el texto del boton del carrito de compras

```php
<?php

// Single Product
add_filter( 'single_add_to_cart_text', 'custom_single_add_to_cart_text' );
function custom_single_add_to_cart_text() {
	return 'Add to cart'; // Change this to change the text on the Single Product Add to cart button.
}

// Variable Product
add_filter( 'variable_add_to_cart_text', 'custom_variable_add_to_cart_text' );
function custom_variable_add_to_cart_text() {
	return 'Select options'; // Change this to change the text on the Variable Product button.
}

// Grouped Product
add_filter( 'grouped_add_to_cart_text', 'custom_grouped_add_to_cart_text' );
function custom_grouped_add_to_cart_text() {
	return 'View options'; // Change this to change the text on the Grouped Product button.
}

// External Product
add_filter( 'external_add_to_cart_text', 'custom_external_add_to_cart_text' );
function custom_external_add_to_cart_text() {
	return 'Read More'; // Change this to change the text on the External Product button.
}

// Default
add_filter( 'add_to_cart_text', 'custom_add_to_cart_text' );
function custom_add_to_cart_text() {
	return 'Add to cart'; // Change this to change the text on the Default button.
}
```
