Initial research
===

The tax helper refers all calculation to the Tax/Calculation Model. This model
has a number of rounding and math functions. Specifically, the rounding
functions should be refered to Zend::Locale::Math without further
complications and the truncate should become a no-op logging its invocation to
Mage::Log for further investiation. Truncation with an consumer supplied
precision parameter can be a root cause of rounding errors.

Function list
---
`app/code/core/Mage/Tax/Model/Calculation.php`:

* round, roundUp, roundDown, truncate
* calcTaxAmount
* getRateRequest() has no math, but determines where to base it's tax on. This
  is filled with if branches. Investigate.
* Other methods fetch configuration values to determine tax strategy.

`app/code/core/Mage/Tax/Model/Calculation/Rate.php`:

* `_beforeSave` has a number of `is_numeric` checks and does some conversion
* `saveTitles` does a little more than that, among others saves the rate

`app/code/core/Mage/Core/Model/Store.php`:

* roundPrice(), this accepts any value. So investigate it's consumers.
* formatPrice calls getCurrentCurrency which supposedly returns a string but
  method format() is called on it, so must be `Varien_String` or something.

Finding calculations
---

`find app/code/core/Mage/ -exec pcregrep  -- '(?<!/)[/*+-] [$0-9]' {} +|less`


