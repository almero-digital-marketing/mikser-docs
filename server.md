## Development server
Mikser comes with build-in development server that will serve your generated website. The server is meant for development use only. We recommend using [Apache](https://httpd.apache.org/) or [Nginx](http://nginx.org/) in production.

By default Mikser will pick a first free port to run the server and later on report the address where you can access your site. This is very convenient but every time you start Mikser you will end up with a different port and address. If you want to define the port yourself you can put in the `mikser.yaml`

```yaml
serverPort: 55001
```

If you don't want Mikser to start the development server at all you can do it by running it with an extra flag `mikser -S` or if you are using it as a local dependency by adding `server: false` option.

```js
var mikser = require('mikser');
mikser.run({
	server: false
});
```

### Live reload
By default Mikser will inject [LiveReload](https://github.com/livereload/livereload-js) script in your pages which will reload your browser when the file changes. The best thing with this approach is that using this script you will get real-time updates of the pages. For style sheets and images the reload will happen without browser even reloading the page. You can connect to the server from many clients and get simultaneous updates, on your desktop, tablet and phone for example. 

Mikser injects those scripts when serving pages so your output result stays clean from extra scripts. You can disable the live reload feature of the development server in the `mikser.yaml`

```yaml
livereload: false
```

### Real-time preview
When dealing with static site generators you regenerate your site after a document or layout changes. Most of the other solutions we tried do a full regeneration even when its not necessary. Mikser keeps track of its state and regenerates only the pages that are affected by the change. Because Mikser stores this info in a database it can do this even across different runs. For example you can run Mikser then stop it, update from your source control and run Mikser again and it will regenerate only the things changed and their relations.

Another huge improvement is the fact that while Mikser serves content through its development server it keeps track what pages you have opened and renders them first. This guarantees that you will see your preview instantly and even if you browse to a different address Mikser will render this page upfront the entire queue of pending renders. This guarantees that you will see the result of the changes you made in a real-time no matter if the site has 10 or 10'000 pages.

### Watching folders
Mikser watches your site folder and takes the appropriate action when something changes. If you have a lot assets this might take a lot of memory. And if you have a continuous integration server this might not be even needed so you can disable it by adding a flag when you run Mikser `mikser -W` or if you are using it as a local dependency by adding `watch: false` option. 

```js
var mikser = require('mikser');
mikser.run({
	watch: false
});
```

If you disable both the development server and the watching feature by `mikser -S -W` Mikser will exit immediately after the generation has finished.