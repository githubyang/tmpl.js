**【本工具已经集成至前后端一体化MVC框架 [Codekart](https://github.com/myworld4059/Codekart)，欢迎使用！】**

**【框架地址：https://github.com/myworld4059/Codekart/ 】**

tmpl.js
=======

微型高效javascript模板引擎


微型模板引擎 tmpl 0.3

0.1版：http://ejohn.org/blog/javascript-micro-templating/

0.2版：http://yiheng.iteye.com/blog/1577360

**0.3 更新:**

1. 放弃低效的 with 语句从而最高提升3.5倍的执行效率
2. 使用随机内部变量防止与模板变量产生冲突
3. 放弃使用页面嵌入模板
4. 将包裹字符从<% %>变成[# #]防止与运算冲突

###example

直接传入模板：

```javascript
var str = '<ol title="[#=name#]">'
  + '[# for (var i = 0, l = list.length; i < length; i ++) { %>'
  + '<li>[#=list[i]#]</li>'
  + '[# } #]'
  + '</ol>';
var obj = {name: 'demo data', list: [202, 96, 133, 134]};
//render为编译好的缓存函数
var render = tmpl(str);
render(obj);
render.$ //解析好的javascript中间代码
//也可以直接解析，不缓存多次使用效率低下
tmpl(str,obj);
```

###模板内容如下：

```javascript
window.tmpl = function (str, data) {
    var $ = '$'+(+new Date) //临时变量名，随机 防止冲突
        , fn = function (data) { //解析函数
            var i, variable = [o], value = [[]];
            for (i in data) {
                variable.push(i);
                value.push(data[i]);
            }
            //返回解析后的字符串
            return (new Function(variable, fn.$))
                .apply(data, value).join("");
        };

    //将模板编译成js代码
        fn.$ = fn.$ || $ + ".push('"
            + str.replace(/\\/g, "\\\\")
            .replace(/[\r\t\n]/g, " ")
            .split("[#").join("\t")
            .replace(/((^|#])[^\t]*)'/g, "$1\r")
            .replace(/\t=(.*?)#]/g, "',$1,'")
            .split("\t").join("');")
            .split("#]").join($ + ".push('")
            .split("\r").join("\\'")
            + "');return " + $;

    /*
    如果未定义data则返回编译好的函数，使用时直接传入数据即可，
    省去每次解析成函数的时间
     */
    return data ? fn(data) : fn;
};
```

已经将此模板集成至Node.js开发框架[Codekart](http://codekart.jojoin.com)，地址https://github.com/myworld4059/Codekart
