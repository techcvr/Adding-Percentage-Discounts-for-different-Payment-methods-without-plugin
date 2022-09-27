# Adding-Percentage-Discounts-for-different-Payment-methods-without-plugin

The code below adds a discount to the total amount when PayPal is chosen as the payment method on WooCommerce checkout page. By assigning a value to $percent, you can specify the percentage of discount you wish to apply on the desired payment method. The default payment methods in WooCommerce are Direct Bank Transfer (bacs), Check payments (cheque), Cash on Delivery (cod) and PayPal (paypal), with their ids mentioned in the brackets.

## Add the below code in function.php

```php
add_action( 'woocommerce_cart_calculate_fees','ts_add_discount', 20, 1 );

function ts_add_discount( $cart_object ) {

if ( is_admin() && ! defined( 'DOING_AJAX' ) ) return;

// Mention the payment method e.g. cod, bacs, cheque or paypal
$payment_method = 'paypal';

// The percentage to apply
$percent = 2; // 2%

$cart_total = $cart_object->subtotal_ex_tax;

$chosen_payment_method = WC()->session->get('chosen_payment_method'); //Get the selected payment method

if( $payment_method == $chosen_payment_method ){

$label_text = __( "PayPal Discount" );

// Calculating percentage
$discount = number_format(($cart_total / 100) * $percent, 2);

// Adding the discount
$cart_object->add_fee( $label_text, -$discount, false );
}
}

add_action( 'woocommerce_review_order_before_payment', 'ts_refresh_payment_method' );


function ts_refresh_payment_method(){
  // jQuery
  ?>
  <script type="text/javascript">
    (function($){
      $( 'form.checkout' ).on( 'change', 'input[name^="payment_method"]', function() {
        $('body').trigger('update_checkout');
      });
    })(jQuery);
  </script>
  <?php
}

