## External tools
Mikser is very conservative about the usage of external tools. It doest come with any plug-ins and subsystems that try to build, compile and pre-process things. It provides hooks for using those tools as command line utilities. To keep things simple there are just two hooks in Mikser.

* `compile` - This hook is executed prior to any generation. It is a great place to pre-process your [Less](http://lesscss.org/) and [Sass](http://sass-lang.com/) or compile scripts like [CoffeeScript](http://coffeescript.org/) and [TypeScript](http://www.typescriptlang.org/). You can configure as many commands as you need.
* `build` - This hook is executed after a generation is over. You can use it to add and extra build step like minify images, scripts, lint your files or run an external build system like [Grunt](http://gruntjs.com/) and [Gulp](http://gulpjs.com/). You can configure as many commands as you need.

For each of the hooks you can configure commands either by specifying the command or add an additional pattern that will filter the files this command is relevant for. By default Mikser watches the folder of your site for changes and takes action when something changes, the pattern tells Mikser what to execute on a file change. By default Mikser will inspect commands for common compilators and preprocessors and add appropriate patter to the command. Currently there is build-in support for [Less](http://lesscss.org/), [Sass](http://sass-lang.com/), [CoffeeScript](http://coffeescript.org/) and [TypeScript](http://www.typescriptlang.org/).

### Preprocessors and compilators
Mikser watches and compiles only files located in `files` folder. Let's see how we can configure Mikser to use [Less](http://lesscss.org/) and [CoffeeScript](http://coffeescript.org/).

```yaml
compile:
- 'lessc --source-map files/styles/style.less out/styles/style.css'
- 'coffee --compile --output files/scripts/ out/scripts/'
```

This will pre-process your `styles.less` every time a `*.less` file changes and compile all your coffee script files when `*.coffee` file changes.

An alternative approach is to use [Grunt](http://gruntjs.com/) or [Gulp](http://gulpjs.com/) to compile things for you.

```yaml
compile:
- command: 'grunt compile'
  pattern: '**/*.+(less|coffee)'
```

This configuration assumes that there is `compile` task configured in your `Gruntfile.js` and that grunt configuration is in the same folder as `mikser.yaml`. Mikser will execute `grunt compile` on every less or coffee script change. Here is the an example of a grunt configuration that will process less files.

```js
module.exports = function(grunt) {
  grunt.initConfig({
    less: {
      dev: {
        options: {
          sourceMap: true,
          sourceMapFileInline: true
        },
        files: {
          'out/styles/style.css': 'files/styles/style.less',
        }
      },
      dist: {
        options: {
          sourceMap: false
        },
        files: {
          'out/styles/style.css': 'files/styles/style.less',
        }
      }
    }
  });

  grunt.loadNpmTasks('grunt-contrib-less');
  grunt.loadNpmTasks('grunt-newer');

  grunt.registerTask('compile', ['newer:less:dev']);
  grunt.registerTask('default', ['less:dist']);
};
```

### Build systems
Once the generation is over Mikser will execute the build commands configured in `mikser.yaml`. This the best place to configure the minification of your assets or lint your files. You can use external utilities one by one or use a build system where you can configure all actions together. For example it's a good idea to output the result of this step in the separate `build` folder  which you can use to upload to your hosting.

```yaml
build:
- grunt
```

This configuration will execute `grunt` each time generation is over. By default mikes will regenerate every time document or layout has been changed. 

```js
module.exports = function(grunt) {
  grunt.initConfig({
    less: {
      dev: {
        options: {
          sourceMap: true,
          sourceMapFileInline: true
        },
        files: {
          'out/styles/style.css': 'files/styles/style.less',
        }
      },
      dist: {
        options: {
          sourceMap: false
        },
        files: {
          'out/styles/style.css': 'files/styles/style.less',
        }
      }
    },
    cssmin: {   
      dist: {
        files: [{
          expand: true,
          cwd: 'out/',
          src: ['**/*.css'],
          dest: 'build/'
        }]
      }
    }
  });

  grunt.loadNpmTasks('grunt-contrib-less');
  grunt.loadNpmTasks('grunt-contrib-cssmin');
  grunt.loadNpmTasks('grunt-newer');

  grunt.registerTask('compile', ['newer:less:dev']);
  grunt.registerTask('default', ['newer:less:dist', 'newer:cssmin:dist']);
};
```