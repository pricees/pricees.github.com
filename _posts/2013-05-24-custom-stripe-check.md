---
layout: post
title: "Check Inputs, Git Money"
description: "Integrating Stripe Checkout, Coupons, and Validation"
category: jquery
tags: [stripe, javascript, jquery, bvalidator, validation, coupons]
---
{% include JB/setup %}

After a few initial discussions with my cofounder, we decided that a multi-pronged attack was needed to entice new users to our app.

First, we needed a A/B landing pages, one page with a free option, two pages without a free option.

Second, we needed to offer coupons so that we could track certains ads and other promotionals.  The gotcha is that some of the coupons were 100% off.

Third, we needed to be able to get money.  Using the Stripe Checkout seemed like _the simplest solution_.

Fourth, we wanted to do client-side validation.  We felt that billing a user, using Stripe checkout, without creating an account, due to invalid form data, was a poor user experience.

For this project we used:
* jQuery bValidator
* Stripe Checkout
* Padrino
* HTML

### Include the Javascript & CSS ###


{% highlight html linenos %}
<script src="/javascripts/jquery.js"></script>
<script src="/javascripts/jquery.bvalidator-yc.js"></script>
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
<div class="inputs>
  <%= f.label :coupon_code, caption: "Coupon Code:" %>
  <%= text_field_tag :coupon_code, value: params[:coupon_code],
    id: :coupon_code, :class => :input %>

  <a href="#" id="apply_coupon" class="btn btn-large info_button">Apply Coupon</a>
</div>

<div class="inputs" id="total_payment_div" style="display:none">
  <%= f.label :total, caption: "Total Payment", style:"color: #F00" %>
  <span id="total_payment"></span>
</div>
{% endhighlight %}

{% highlight javascript linenos %}
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

      $('#stripe_button').click(function(){
        // validation this monkey
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
{% endhighlight %}

### Create a form, Part 3 ###

{% highlight erb linenos %}
<div class="inptfilds" id="stripe_payment" style="display: <%= show ? :block : :none %>">
<button class="btn btn-primary btn-large submit_button" id="stripe_button"> Create My Account
  </button>
</div>

    <script>
{% endhighlight %}

{% highlight html linenos %}
<div class="inptfilds" id="save_and_cancel"
  style="display: <%= show ? :block : :none %>">
  <label/>
  <button class="btn btn-primary btn-large submit_button" id="save_button">
    Create My Account</button>
</div>
<script>
  $('#save_button').click(function(){
    // validation this monkey
    if (!($('#account_form').data('bValidator').validate())) { return false; }
    $('#account_form').submit();
    return false;
  });
</script>
{% endhighlight %}

