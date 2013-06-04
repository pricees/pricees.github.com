---
layout: post
title: "Luhn Algorithm In Ruby"
description: ""
category:
tags: []
---
{% include JB/setup %}
# The Problem #

I was givena take-home programming problem for an interview process.  I was told to use the [Luhn Algorithm](http://en.wikipedia.org/wiki/Luhn_algorithm) to validate credit cards in this problem.  The algorithm is trivial and yet I wasted more time Googling and Stack Overflowing, than it would have taken me to write the dang thing myself.

# The Solution #

Below is a Ruby, out of the box solution, with testing

{% highlight ruby %}
require "minitest"
require "minitest/autorun"

# Public: Validates number against Luhn 10 scheme
#
# Luhn Algo ripped from: http://en.wikipedia.org/wiki/Luhn_algorithm
# 1. From the rightmost digit, which is the check digit, moving left, double the value of every second digit; if product of this doubling operation is greater than 9 (e.g., 7 * 2 = 14).
# 2. Sum the digits of the products (e.g., 10: 1 + 0 = 1, 14: 1 + 4 = 5) together with the undoubled digits from the original number.
# 3. If the total modulo 10 is equal to 0 (if the total ends in zero) then the number is valid according to the Luhn formula; else it is not valid.
#
# Returns true or false
def luhn_valid?(number)
  number.gsub!(/\D/, '')
  sum = 0
  (number.length - 1).downto(0).each_with_index do |idx, i|
    n = number[idx].to_i

    # Step 1
    n = (i % 2).zero? ? n : (n * 2)

    # Step 2
    n = 1 + (n % 10) if n >= 10

    sum += n
  end

  # Step 3
  (sum % 10).zero?
end

describe "Luhn Algorithm" do
  # Cards ripped from paypal: http://www.paypalobjects.com/en_US/vhelp/paypalmanager_help/credit_card_numbers.htm
  CC_NUMBERS = {
    "American Express" => "378282246310005",
    "American Express 2" => "371449635398431",
    "American Express Corporate" => "378734493671000",
    "Australian BankCard" => "5610591081018250",
    "Diners Club" => "30569309025904",
    "Diners Club 2" => "38520000023237",
    "Discover" => "6011111111111117",
    "Discover 2" => "6011000990139424",
    "JCB" => "3530111333300000",
    "JCB 2" => "3566002020360505",
    "MasterCard" => "5555555555554444",
    "MasterCard 2" => "5105105105105100",
    "Visa" => "4111111111111111",
    "Visa 2" => "4012888888881881",
    "Visa 3" => "4222222222222",
  }

  it "returns true for valid numbers" do
    assert CC_NUMBERS.values.all? { |number| luhn_valid?(number) }
  end

  it "returns false for invalid numbers" do
    CC_NUMBERS.values.each do |number|
      me_turn_bad = (number.to_i + 1).to_s
      refute luhn_valid?(me_turn_bad)
    end
  end
end
{% endhighlight %}

# Results #

It may be an ugly method, but it does the trick.
