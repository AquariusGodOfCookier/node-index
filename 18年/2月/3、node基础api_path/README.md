## path 的常用api
- [normalize](./01、path_normalize.js) 处理路径，修复和优化路径结构的
- [join](./02、path_join.js) 可以帮我们拼接路径，如果路径不是很标准，可以帮我们处理一下。(内部相当于调用了一次normalize)
- [resolve](./03、path_resolve.js) 可以把相对路径处理为绝对路径
- [关于name 的三个api](./04、path_names.js)
    - basename 文件名
    - dirname 文件所处的路径
    - extname 文件的扩展名
- [parse](05、path_parse&&format.js) 可以吧路径解析出来，
- [format](05、path_parse&&format.js) 上面路径解析的逆向过程，可以吧路径对象合并成一个真正的路径结构
- [sep、delimiter、win32、posix](./06、path_sep&&delimiter&&win32&&posix.js)是看一些系统变量的一些参数用的（几乎没有啥子作用。。。）
- __dirname 和 __filename 总是返回文件的绝对路径
- [process.cwd()](./07、paths.js) 总是返回执行node 命令行所在的文件夹