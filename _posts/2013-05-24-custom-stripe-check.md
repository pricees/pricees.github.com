---
layout: post
title: "Check Inputs, Git Money"
description: "Integrating Stripe Checkout, Coupons, and Validation"
category: jquery
tags: [stripe, javascript, jquery, bvalidator, validation, coupons]
---
{% include JB/setup %}

## The Problem ##

After a few initial discussions with my cofounder, we decided that a multi-pronged attack was needed to entice new users to our app.

First, we needed a A/B landing pages, one page with a free option, two pages without a free option.

Second, we needed to offer coupons so that we could track certain ads and other promotionals.  The gotcha is that some of the coupons were 100% off.

Third, we needed to be able to get money.  Using the Stripe Checkout seemed like _the simplest solution_.

Fourth, we wanted to do client-side validation.  We felt that billing a user, using Stripe checkout, without creating an account, due to invalid form data, was a poor user experience.

## Jeah ##

  This is how we set up **client-side validation**, **Stripe Payments**, and **coupons** at VersesByPhone.com

For this app we use:
* jQuery, bValidator plug-in
* Stripe Checkout
* Padrino
* HTML, bootstrap

### Include the Javascript & CSS ###

{% highlight html linenos %}
<script src="/javascripts/jquery.js"></script>
<script src="/javascripts/jquery.bvalidator-yc.js"></script>
<script src="/javascripts/verses.custom.js"></script>
<script src="https://checkout.stripe.com/v2/checkout.js"></script>
<link rel="stylesheet" href="/stylesheets/bvalidator.css" media="all" />
... [ bootstrap includes ]
{% endhighlight %}

This is basic:
1. Include the jQuery library
2. Include the bValidator jQuery plug-in
3. Include the Stripe Checkout javascript for express payment
4. Have a bit of style for our client side validation
5. I happen to use bootstrap as well, because my design skills are strugglin\'

### Create a form, Part 1 ###
{% highlight erb linenos %}
 <% form_for :account, url(:accounts, :create), {id: "account_form" } do |f| %>
<div class="input">
  <%= f.label :name, caption: "First Name" %>
  <%= f.text_field :name, :class => :input, :"data-bvalidator" => "required"  %>
  <%= f.error_message_on :name, id: "account_name_error" %>
</div>

<div class="input">
  <%= f.label :email, caption: "Email" %>
  <%= f.text_field :email, :class => :input, :"data-bvalidator" => "required,email"  %>
  <%= f.error_message_on :email, id: "account_email_error"  %>
</div>


<%
#  NOTE: Assume:
#  @subscription_types = {
#    id: 0, label: "Free",
#    id: 1, label: "Weekly ($5.99)",
#    id: 2, label: "Yearly $(34.99)",
%>
-->
<div class="input">
  <%= f.label :subscription_type_id, caption: "Subscription:" %>
  <%= select_tag(:subscription_type_id,
    collection: @subscription_types, fields: [:label, :id],
    selected: params[:subscription_type_id],
    class: "select",
    id: :subscription_type_id,
  ) %>
  <%= f.error_message_on :subscription_type_id %>
</div>

<%= partials :"accounts/_coupon_code", locals: { f: f } %>
<%= partials :"accounts/_payment", locals: { f: f } %>
{% endhighlight %}

Here we have set up a trivial form.
- We ask the user for her first name and email, and subscription (from a select box).
- First name and email are required during validation, using the **data-bvalidator** attributes.
- Furthermore, the email should be a valid _email_ address.
- We include the coupon partial
- We include the payment partial

### Create a form, Part 2 ###

**_accounts/\_coupon\_code.erb_**:

{% highlight erb linenos %}
<div class="input">
  <%= f.label :coupon_code, caption: "Coupon Code:" %>
  <%= text_field_tag :coupon_code, value: params[:coupon_code],
    id: :coupon_code, :class => :input %>

  <a href="#" id="apply_coupon" class="btn btn-large info_button">Apply Coupon</a>
</div>

<div class="input" id="total_payment_div" style="display:none">
  <%= f.label :total, caption: "Total Payment", style:"color: #F00" %>
  <span id="total_payment"></span>
</div>
{% endhighlight %}

This code:
- Displays a text box for entering a coupon code
- Exposes a button to "apply coupon"
- Creates a hidden div for displaying total payment

### Create a form, Part 3 ###
**_accounts/\_payment.erb_**:
{% highlight erb linnos %}

<div class="input" id="save_and_cancel"
  style="display: <%= show ? :block : :none %>">
  <button class="btn btn-primary btn-large submit_button" id="save_button">
    Create My Account
  </button>
</div>

<div class="inptfilds" id="stripe_payment"
  style="display: <%= show ? :block : :none %>">
    <button class="btn btn-primary btn-large submit_button" id="stripe_button">
      Create My Account
  </button>
</div>
{% endhighlight %}

Th above code creates two buttons:
- One for the free subscriptions (100% coupon, per se)
- One for paid subscriptions

### The jQuery code ###
**_/javascripts/verses.custom.js_**

{% highlight javascript linenos %}
// apply_coupon button clicked
$('#apply_coupon').click(function(){
$.post('/apply_coupon?',
  $('#account_form').serialize(),
  function(data) {
    res = $.parseJSON(data);

    $('#total_payment').html(res.str);
    $('#total_payment_div').show();

    if (res.free) {
      $('#stripe_payment').hide();
      $('#save_and_cancel').show();
    } else {
      $('#stripe_payment').show();
      $('#save_and_cancel').hide();
    }
  });
  return false;

// stripe_button clicked:
// - validate form
// - if valid, show Stripe Payment form
$('#stripe_button').click(function(){
  if (!($('#account_form').data('bValidator').validate())) { return false; }

  var token = function(res) {
    var $input = $('<input type=hidden name=stripeToken />').val(res.id);
    $('#account_form').append($input).submit();
  };

  StripeCheckout.open({
    key:         '<%= settings.publishable_key %>',
    image:       '/images/logo.png',
    name:        'versesByPhone.com',
    description: 'Subscription to versesByPhone.com',
    panelLabel:  'Purchase',
    token:       token,
  });

  return false;
});

// save_button is clicked:
// - validate form
// - if valid, show Stripe Payment form
$('#save_button').click(function(){
  if (!($('#account_form').data('bValidator').validate())) { return false; }
  $('#account_form').submit();
  return false;
});

{% endhighlight %}

The code above is a bit hairy.  So I will explain block by code block.

When the apply_coupon button is clicked:
- We serialize the form, and send it to the route "/apply_coupon"
- The ajax call is returns a JSON object _{ str: "[price with coupon applied]", free: [true|false] }_
- The string from the json is placed into the hidden div \#total_payment.  This div is displayed to the user
- If the subscription is _free_ -- w/ or w/o the coupon -- the stripe-payment button is hidden and the save button is exposed
- If the subscription is not free, the stripe button is exposed, the save button is hidden

When the stripe_button is clicked:
- Validate the form text boxes against the bValidator rules found in the input tags attributes
- If the form is appears to be valid, create the stripeToken hidden input field, and raise the Stripe Checkout form
- If the Stripe checkout form is filled with valid input and submitted, it will set the stripeToken field, and submit the form

When the save_button is clicked:
- Validate the form text boxes against the bValidator rules found in the input tags attributes
- If the form is appears to be valid, submit the form

### The Route Handler ###

{% highlight ruby linenos %}
MyApp.controllers :base do

  [...]

  post :apply_coupon, map: "/apply_coupon" do
    unless (options = params[:subscription_type_id])
      return { str: 'No Data', free: false }
    end

    s = Subscription.new(options)

    res = [s.price_frequency, s.coupon_status].compact.join(" ")

    {str: res || s.price_frequency, free: s.free? }.to_json
  end

  [...]
end
{% endhighlight %}

This above code does the following:
- Looks for a subscription_type_id passed from the select box
- If subscription_type_id doesn't exist, it explicitly returns with a some info for the user.
- If subscription_type_id exists, it builds a Subscription object from the params
- The Subscription object makes calls to build a string to display in the total_payment div, as well as whether the subscription is free after the coupon is or isn't applied
- The response is returned as a json hash

### The Model ###

**_/models/subscription.rb_**
{% highlight ruby linenos %}

class Subscription

  many_to_one :subscription_type
  attr_reader :coupon_status

  def price
    @price ||= begin
      if subscription_type
        subscription_type.price
      else
        raise "Subscription Type Error for Subscription ##{id}, [type id: #{subscription_type_id.inspect}]"
      end
    end
  end

  def stripe_coupon
    @stripe_coupon ||= Stripe::Coupon.retrieve(coupon)
  rescue Stripe::InvalidRequestError
    @coupon_status = "(error with coupon '#{coupon}')"
    nil
  end

  def adjusted_price
    return price if stripe_coupon.nil?

    @coupon_status = "(coupon applied)"
    if (amt = stripe_coupon["amount_off"])
      price - amt
    elsif (pct = stripe_coupon["percent_off"])
      price - (price * pct.fdiv(100))
    end
  end

  def price_frequency
    dec_price =  "%0.02f" % adjusted_price.fdiv(100)

    "$#{dec_price}/#{subscription_type.freq}"
  end

  def free?
    @free ||= subscription_type.free? || adjusted_price.zero?
  end
end
{% endhighlight %}

This is where all of the fun happens.  We will go through this block by block.

Assume the model:
  SubscriptionType:
    price (USD, in cents)
    freq  (monthly, weekly)

**Subscription#price**
We attempt to grab a base price to which a coupon might be applied.  It looks for the subscription_type.  If the subscription_typ is nil, an RunTimeError is raised.

**Subscription#stripe_coupn**
We attempt to grab the Stripe coupon by name.  If successful, this call will return a hash that will contain the keys "amount_off", "percent_off", one of which will be nil, the other, set.
If the coupon doesn\'t exist, an exception will be thrown, which we rescue.  We set the coupon status accordingly, to let the user know that their coupon was bogus.  Bogus!

**Subscription#adjusted_price**
No we are cookin\' with gasoline!  If the coupon doesn\'t exist, return the default price.  If the coupon does exists, set the status to "coupon applied."  If the coupon contains a set amount off, we subtract this amount from the base price.  We the coupon is a percent off, we subscription the percentage of the price from the base.

**Subscription#price_frequency**
We generate a string as a label that will allow the user to know the amount he will be charged, and the frequency that we will attempt to charge them.  This is based off the adjusted price and the frequency of the subscription type he have chosen.

**Subscription#free?**
This returns true for the following conditions:
- The Subscription Type is free, such as our "2 Week Trial"
- The coupon, when applied, adjusts the price to $0.00, such as our 100% off promo


## Final Thoughts ##

  I think thats about it.
  This is how we set up **client-side validation**, **Stripe Payments**, and **coupons** at VersesByPhone.com
  I am not going to proof read this blog posting until months from now.  Its taken me about 2 hours to right this thing, and I am tired.  I forgot why I stopped blogging, its because its tiring.
  I didn\'t include all of the code, so if you are having trouble, feel free to comment and I will get back to you.
  There are plenty of places to refactor this code.  If you have suggestions I am all ears.

