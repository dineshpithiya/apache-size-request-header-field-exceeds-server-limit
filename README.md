# Apache – “Size of a request header field exceeds server limit”

- ![#f03c15](https://placehold.it/15/f03c15/000000?text=+) `#f03c15`
- ![#c5f015](https://placehold.it/15/c5f015/000000?text=+) `#c5f015`
- ![#1589F0](https://placehold.it/15/1589F0/000000?text=+) `#1589F0`

## ![#f03c15]Your browser sent a request that this server could not understand.
Size of a request header field exceeds server limit.

![alt text](Apache-Size-of-a-request-header-field-exceeds-server-limit.jpg)

That means that your browser is trying to send too much at once. Probably, it’s size of the cookies. The easy way to fix it – delete cookies. Of course, you lose all info in the cookies, but at least you can access site again.

Searching on the Apache debug error traces took me to this error line:

>[Fri Mar 09 07:34 2018] [error] [client 22.6.6.80] request failed: error reading the headers, referer: http://www.domain.com/

This seems to be usually caused by a very large cookie, so the limit configured in **Apache for a request header** field must be increased.That limit is controlled by the **LimitRequestFieldSize** directive.

As you can see in this office IBM [To know click here](http://www-01.ibm.com/support/docview.wss?uid=swg21384722) article, the default limit for the IBM HTTP Server (IHS) is 8K. And suggests to change it to 12288 or 16384 bytes.

This directive can be applied at server-config or virtualhost level [see APCHE doc](http://httpd.apache.org/docs/2.2/mod/core.html#limitrequestfieldsize). I changed it at virtualhost level:

## **Solution**

If you develop/manage web site and your customers complain of this error message – there are two things to do:
	- Review cookies and limit the size of cookies your application stores in the browser.
	- Increase allowed request size in web server configuration

```
<VirtualHost 22.6.6.80:80>
	ServerName www.domain.com
	
	LimitRequestFieldSize 16384
	
	RewriteEngine On
	...
	...
</VirtualHost>
```

Don't forgot last step

After restarting services, the problem is solved. 

so now restart apache

>ubuntu OS
```
sudo service apache2 restart
```
>linux OS
```
sudo service httpd restart
```