# handlebars-helpers注入服务端渲染方法               

直接上源码： 
```javascript
'use strict';
/**
 * 初始化helper
 */

const path = require('path');
const fs = require('fs');
const Handlebars = require('handlebars');
const handlebarsLayouts = require('./handlebars-layouts');
const recursive = require('recursive-readdir');
const log = require('./log');


/**
 * 初始化项目模板helper
 * @param  {String} appDir  项目目录
 * @param  {Object} config  配置
 * @return {Object}         Promise对象
 */
module.exports = function(appDir, config) {
    log.info('开始初始化项目helper');
    const viewDir = path.join(__dirname, '../views');
    const helperDir = path.join(__dirname, '../helpers/');
    log.info('开始初始化helper-layout');
    // handlebarsLayouts.register(Handlebars);
    handlebarsLayouts.register(Handlebars, {
        suffix: 'html',
        extendDir: [path.join(__dirname, '../views/partials/'), path.join(viewDir, '../views/components/')]
    });
    log.success('初始化helper-layout成功');
    // 自定义helper扩展
    if (fs.existsSync(helperDir)) {
        log.info('查询到handlebars自定义helper文件夹,开始解析');
        // 自定义方法
        recursive(helperDir, function (err, files) {
            if(err) {
                log.error('查询handlebars自定义helper错误');
                return reject(err);
            }
            files.forEach(function(filePath) {
                const helper = require(filePath);
                if(typeof helper === 'function') {
                    Handlebars.registerHelper(helper(Handlebars));
                }
                else Handlebars.registerHelper(helper);
            });
        });
    }else{
        log.info('没有handlebars自定义helper文件夹,被略过');
    }
    log.success('初始化自定义helper成功');
};
```

[注入方式可以参考项目示例：test/express](../../../test/express)