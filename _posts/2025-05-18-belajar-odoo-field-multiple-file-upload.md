---
layout: post
title: "Belajar Odoo: Field multiple file upload"
date: 2025-05-18
update_date: 2025-09-26
tags: [odoo]
---

<img src="/assets/images/odoo.png" />

Tambahkan field "many2many" dengan model "ir.attachment":

{% highlight python %}
attachment_ids = fields.Many2many(
	"ir.attachment",
	string='Upload Document'
)
{% endhighlight %}

lalu tambahkan juga di views dengan widget "many2many_binary":

{% highlight xml %}
<field name="attachment_ids" widget="many2many_binary" />
{% endhighlight %}