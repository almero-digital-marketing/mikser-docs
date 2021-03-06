## Folder structure
Mikser is a flat file CMS. Web site is generated from files separated into several folders. When you run Mikser for the first time, in an empty folder, it will create all the necessary folders inside.

* `documents` - Here you put all the documents that represent the data for your web site. The documents can be plain text, html, markdown, any of those with front matter or documents that store serialized data in YAML, TOML, JSON, ArchieML, etc.
* `layouts` - Here you put all the layouts(templates) that define how to render those documents. Mikser supports many template engine like Ect, Eco, Jade, Swig, Ejs, etc. Mikser supports also blocks(partials) and short codes which are also stored in this folder. Layouts can have front matter too.
* `files` - Here you store all assets for the web site like style sheets, scripts, images, vendor libraries, etc. Contents of this folder are synchronized to the `out` folder.
* `out` - In this folder Mikser generates your web site based on the documents and layouts you provide. This folder should be ignored from the source control. If there are errors Mikser can delete this folder. You should not store any persistent information there. 
* `runtime` - This folder is used for storing runtime information. It should be ignored from the source control. In this folder we store the Mikser database(MongoDB). The database is used only for querying and storing runtime information. You can delete the runtime folder and Mikser will recreate it on the next run.
* `plugins` - Here you can put all your site specific plugins. Plugins are not loaded by default, you have to add them to Mikser configuration or the the layout front matter in order to use them. Mikser comes with a set of build-in plugins. You can install plugins with npm which are stored in `node_modules` folder. Mikser will look for plugins in all those locations.
* `shared` - This folder is used in multi domain web sites. Here you put all the files that are not shared between the different domains.

## System files
* `mikser.yml` - This is the default Mikser configuration file. Mikser does not auto reload this file. If you change the configuration you have to restart Mikser.
* `mikser.js` - If you use Mikser as a local dependency you can initialize it here by doing `require('mikser').run();`. You can start your project with `node mikser`.
* `*.yml` - You can have several environments that will overwrite `mikser.yml` configuration. This feature is very useful for using different settings for development in `dev.yml`, for continuous integration in `ci.yml` and for testing in `test.yml`. You can run Mikser with any of them by doing `mikser -e dev` or `mikser -e ci`. If you use Mikser as local dependency you can do the same with `node mikser -e dev` or `node mikser -e ci`.

[![Analytics](https://ga-beacon.appspot.com/UA-78544431-1/folders.md?pixel)](https://github.com/igrigorik/ga-beacon)