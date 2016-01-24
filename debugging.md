## Debugging
Mikser has two goals, first is to be extremely fast and second to provide the best possible debug information so the users can find the problems instantly. Although Mikser has support for many template and markup engines it tries to inspect the exceptions they provide and give you precise error reports.

### Error diagnostics
For every error Mikser will report the render phase when it has happened and the exact place the error took place. In short the Mikser work-flow is the following, it first loads the data defined in each layout, then it renders the document content, then the document layout and all the inherited layouts and at the end it executes the long running operations like image and video processing. You should keep this in mind when looking at the error reports.

![Error diagnostics](http://almero.pro/error.png)

As you can see at the example error report the document was processed fine. Then the `/technologies.ect` and `/article.ect` layouts was rendered fine and last `/default.ect` has failed to render. At the details below you can see what might led to the error and the exact line number and a snippet of the source that caused it. Mikser will prepare that kind of report for almost every aspect of the generation process.

### Debug information
If you more details whats going on under the cover you can run Mikser with the debug flag `mikser -d`. This will output all the debug information. Sometimes this is too much information. 

![Debug information](http://almero.pro/debug.png)

There are several options to limit the output information. You can do it by providing the list of Mikser's module names `mikser -d manager,generator,server` or by excluding a list of modules by `mikser -D runtime`. The first command will output the debug information only for manager,generator and server modules. The second command will filter the debug information for runtime module and show all the rest. This is the list of [Mikser modules](https://github.com/almero-digital-marketing/mikser/tree/master/lib). 

If you need to narrow down an error for a specific use case you can filter the debug information by creating a file called `debug` inside the `runtime` folder and adding the JavaScript regular expressions inside. Mikser will output the debug information that matches any of the expressions inside the file. Expressions should be added one at a line.This is very useful technique when developing Mikser plug-ins.