---
seo:
  title: Sender Policy Framework
  description: Sender Policy Framework (SPF) is an open standard aimed at preventing sender address forgery
  keywords: sender policy framework, whitelabeling, dkim, domain keys
title: Sender Policy Framework
weight: 0
layout: page
navigation:
  show: true
---

[Sender Policy Framework]({{root_url}}/Glossary/spf.html) (SPF) is an open standard aimed at preventing sender address forgery.

{% anchor h2 %}
SPF and Whitelabel
{% endanchor %}

As part of the [whitelabel]({{root_url}}/User_Guide/Setting_Up_Your_Server/Whitelabeling/index.html) process you must create a subdomain (such as em.example.com) which is used for [click and open tracking]({{root_url}}/Delivery_Metrics/email_activity.html) as well as in the Return-Path email header. SPF uses the domain value in the Return-Path header for the DNS lookup to determine the permitted senders for the domain.

If you have an SPF record set for your root domain (i.e. yourdomain.com), you must add {% codeblock %}include:sendgrid.net{% endcodeblock %} before the all mechanism of this record. If you do not have an SPF record for your domain you must create a TXT record with the value:

{% codeblock %}
v=spf1 include:sendgrid.net ~all
{% endcodeblock %}

Do not create more than one SPF1 record for a given domain. If you need more than one SPF record, you will want to merge the additional SPF records into a single SPF record.

{% info %}
You cannot have more than 10 DNS lookups in your SPF record.
{% endinfo %}

{% anchor h2 %}
Already have an SPF record for your domain?
{% endanchor %}

No problem. You simply need to add the SendGrid include mechanism lookup into your existing record.

For example, if your record looks like this:

{% codeblock %}
v=spf1 a mx include:_spf.google.com include:spf.protection.outlook.com ~all
{% endcodeblock %}

You would just need to add our lookup at the end of the string, before the {% codeblock %}~all{% endcodeblock %} mechanism, like so:

{% codeblock %}
v=spf1 a mx include:_spf.google.com include:spf.protection.outlook.com include:sendgrid.net ~all
{% endcodeblock %}


{% anchor h2 %}
Don't want to include another hostname lookup?
{% endanchor %}

If you would rather not include SendGrid's SPF hostname lookup in your record, or perhaps you just have too many already, you can also choose to give permission to a specific IP address to send mail for your domain. This is accomplished using the ip4 mechanism.

If you have a Silver or higher level package, you can choose to specify your dedicated IP address as a lookup, meaning that only mail coming from that particular IP address will be considered a permitted sender within SendGrid for that domain. An example of such an include looks like this:

{% codeblock %}
v=spf1 a mx include:_spf.google.com include:spf.protection.outlook.com ip4:12.34.56.78 ~all
{% endcodeblock %}

If you wish to add multiple ip4 lookups (if you have an account that sends from multiple dedicated IPs, for example), simply add them into your record separated by spaces. Unlike with include:hostname lookups, a SPF1 record can have any number of ip4 includes.

Please note, the validation step in the [Whitelabel Wizard]({{root_url}}/User_Guide/Setting_Up_Your_Server/Whitelabeling/whitelabel_wizard.html) will not recognize the ip4 mechanism, it will only accept {% codeblock %}include:sendgrid.net{% endcodeblock %}

For more information on SPF best practices and syntax, check out [www.openspf.org](http://www.openspf.org)

{% anchor h2 %}
Record flattening
{% endanchor %}
There is an experimental tool called the [dmarcian SPF Record Flattener](https://dmarcian.com/spf-survey/bitcointalk.org), which should be considered experimental. From their site: "[this tool] rewrites this record by removing duplicate netblocks, collapsing any overlapping netblocks, and using 0 DNS-querying mechanisms/modifiers."

If you choose to use this functionality, we suggest that you test it extensively to make sure that your customers will receive your emails and their servers can look up your records properly.