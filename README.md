# oscar-paypalcheckout

This package provides integration between django-oscar and the [v2/checkout/orders](https://developer.paypal.com/docs/api/orders/v2/)  it basesd on the [Samples](https://github.com/paypal/Checkout-Python-SDK/) provided by paypal.


## Prerequisites

Python 3.0+ and Djangp Oscar 2.0+


## Requirements

pip install paypalcheckoutsdk


##usage

### Setting up credentials and setting


In Oscars settings.py add the application  'paypalcheckout'
and add the nessary config parameter

```python

#must match withj view.py
PAYPAL_SUCCESS_PAGE ="http://127.0.0.1:8000/paypal/success/"
PAYPAL_CANCEL_PAGE ="http://127.0.0.1:8000/paypal/cancel/"
PAYPAL_BRAND_NAME ="Some Brand Name"

#provided my paypal developer network
PAYPAL_CLIENT_ID ="some-id"
PAYPAL_CLIENT_SECRET ="some-secret"
# if true json data is dump to console
PAYPAL_DEBUG = True
#'live'  or 'sandbox'
PAYPAL_ENVIRONMENT ="sandbox"
```



### Fork  the checkout Application 

for more inofrmation see:
https://django-oscar.readthedocs.io/en/2.0.0/topics/customisation.html


### Override PaymentDetailsView.handle_payment
In the forked checkout application  override PaymentDetailsView.handle_payment method.
The sample code check if the payment method is paypal. Then it creates a paypal order and raise a RedirectRequired. The redirect is handled by Oscar.

When the user authenticated or cancel the order, the provided views in the paypalcheckout application handel remaining steps of submiting the order.


```python

from paypalcheckout.paypalcommands import CreatePaypalOrder


 def handle_payment(self, order_number, total, **kwargs):
    
        if(  self.checkout_session.payment_method() == "paypal"):
            frozzen_basket=self.get_submitted_basket()
            shipping_address = self.get_shipping_address(frozzen_basket)
            pp_order = CreatePaypalOrder()
            pp_link=pp_order.create_order(order_number, total, shipping_address)
            raise RedirectRequired(pp_link)

```



