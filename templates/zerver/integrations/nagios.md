First, create the stream you’d like to use for Nagios notifications,
and subscribe all interested parties to this stream. The integration
will use the default stream `nagios` if no stream is supplied in the
configuration; you still need to create the stream even if you are
using this default.

{! download-python-bindings.md !}

Next, open `integrations/nagios/zuliprc.example` in your favorite
editor, and change the following lines to specify the email address
and API key for your Nagios bot, saving it to `/etc/nagios3/zuliprc`
on your Nagios server:

```
[api]
email = NAGIOS_BOT_EMAIL_ADDRESS
key = NAGIOS_BOT_API_KEY
{% if api_site_required %}site = {{ external_api_uri_subdomain }}{% endif %}
```

Copy `integrations/nagios/zulip_nagios.cfg` to `/etc/nagios3/conf.d`
on your Nagios server.

Finally, add `zulip` to the `members` list for one or more of the
contact groups in the `CONTACT GROUPS` section of
`/etc/nagios3/conf.d/contacts.cfg`, doing something like:

```
define contactgroup {
    contactgroup_name       admins
    alias                   Nagios Administrators
    members                 monitoring, zulip
}
```

Once you’ve done that, reload your Nagios configuration using
`/etc/init.d/nagios3 reload`.

When your Nagios system makes an alert, you’ll see a message like the
following, to the stream `nagios` (to change this, edit the arguments
to `nagios-notify-zulip` in `/etc/nagios3/conf.d/zulip_nagios.cfg`)
with a topic indicating the service with an issue.

{! congrats.md !}

![](/static/images/integrations/nagios/001.png)

**Testing**

If you have
[external commands enabled in Nagios](http://nagios.sourceforge.net/docs/3_0/extcommands.html),
you can generate a test notice from your Nagios instance by visiting
using the `Send custom service notification` command in the `Service
Commands` section of any individual service’s page on your Nagios
instance.

**Troubleshooting**

You can confirm whether you’ve correctly configured Nagios to run the
Zulip plugin by looking for `SERVICE NOTIFICATION` lines mentioning
zulip in `/var/log/nagios3/nagios.log`. You can confirm whether you’ve
configured the Zulip plugin code correctly by running
`/usr/local/share/zulip/integrations/nagios/nagios-notify-zulip`
directly.
