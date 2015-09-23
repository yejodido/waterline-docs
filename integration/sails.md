---
layout: default
---

# Integrating Waterline in Sails.js

## Notes for models

In `sails/config/models.js` you can add a `validations` section and define an array of properties to ignore.

{% highlight javascript %}
	validations: {
		ignoreProperties: [ 'async', 'special']
	},
{% endhighlight %}
