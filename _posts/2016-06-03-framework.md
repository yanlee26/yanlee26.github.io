---
date: 2016-06-03
layout: post
title: Framework resharpe
thread: 9
categories: JS
tags: [BIT]
excerpt: Framework resharpe
---
# js 常用封装总结

```
//自制框架
//定义一个对象 - 名字是$$
var $$ = function () {
};
//第二种写法
$$.prototype = {
    $q:function (str) {
        return document.querySelector(str)
    },
    $qa:function (str) {
        return document.querySelectorAll(str)
    },
    $id: function (str) {
        return document.getElementById(str)
    },
    $tag: function (tag) {
        return document.getElementsByTagName(tag)
    },
    //去除左边空格
    ltrim: function (str) {
        return str.replace(/(^\s*)/g, '');
    },
    //去除右边空格
    rtrim: function (str) {
        return str.replace(/(\s*$)/g, '');
    },
    //去除空格
    trim: function (str) {
        return str.replace(/(^\s*)|(\s*$)/g, '');
    },
    //ajax
    myAjax: function (URL, fn) {
        var xhr = createXHR();	//返回了一个对象，这个对象IE6兼容。
        xhr.onreadystatechange = function () {
            if (xhr.readyState === 4) {
                if (xhr.status >= 200 && xhr.status < 300 || xhr.status == 304) {
                    fn(xhr.responseText);
                } else {
                    alert("错误的文件！");
                }
            }
        };
        xhr.open("get", URL, true);
        xhr.send();
        //闭包形式，因为这个函数只服务于ajax函数，所以放在里面
        function createXHR() {
            if (typeof XMLHttpRequest != "undefined") {
                return new XMLHttpRequest();
            } else if (typeof ActiveXObject != "undefined") {
                if (typeof arguments.callee.activeXString != "string") {
                    var versions = ["MSXML2.XMLHttp.6.0", "MSXML2.XMLHttp.3.0",
                            "MSXML2.XMLHttp"
                        ],
                        i, len;
                    for (i = 0, len = versions.length; i < len; i++) {
                        try {
                            new ActiveXObject(versions[i]);
                            arguments.callee.activeXString = versions[i];
                            break;
                        } catch (ex) {
                            //skip
                        }
                    }
                }
                return new ActiveXObject(arguments.callee.activeXString);
            } else {
                throw new Error("No XHR object available.");
            }
        }
    },
    //tab
    tab: function (id) {
        var box = document.getElementById(id);
        var spans = box.getElementsByTagName('span');
        var lis = box.getElementsByTagName('li');
        for (var i = 0; i < spans.length; i++) {
            spans[i].index = i;
            spans[i].onmouseover = function () {
                for (var i = 0; i < spans.length; i++) {
                    spans[i].className = '';
                    lis[i].className = '';
                }
                this.className = 'select';
                lis[this.index].className = 'select';
            }
        }
    },
    //简单的数据绑定formateString
    formateString: function (str, data) {
        return str.replace(/@\((\w+)\)/g, function (match, key) {
            return typeof data[key] === "undefined" ? '' : data[key]
        });
    },
    //给一个对象扩充功能
    extendMany: function () {
        var key, i = 0, len = arguments.length, target = null, copy;
        if (len === 0) {
            return;
        } else if (len === 1) {
            target = this;
        } else {
            i++;
            target = arguments[0];
        }
        for (; i < len; i++) {
            for (key in arguments[i]) {
                copy = arguments[i][key];
                target[key] = copy;
            }
        }
        return target;
    },
    extend: function (tar, source) {
        for (var i in source) {
            tar[i] = source[i];
        }
        return tar;
    },
    random: function (begin, end) {
        return Math.floor(Math.random() * (end - begin)) + begin;
    },
    isNumber: function (val) {
        return typeof val === 'number' && isFinite(val)
    },
    isBoolean: function (val) {
        return typeof val === "boolean";
    },
    isString: function (val) {
        return typeof val === "string";
    },
    isUndefined: function (val) {
        return typeof val === "undefined";
    },
    isObj: function (str) {
        if (str === null || typeof str === 'undefined') {
            return false;
        }
        return typeof str === 'object';
    },
    isNull: function (val) {
        return val === null;
    },
    isArray: function (arr) {
        if (arr === null || typeof arr === 'undefined') {
            return false;
        }
        return arr.constructor === Array;
    }
};
//在框架中实例化
$$ = new $$();
$$.extend($$, {
    $id: function(id) {
        return document.getElementById(id);
    },
    $tag: function(tag, context) {
        if (typeof context == 'string') {
            context = $$.$id(context);
        }
        if (context) {
            return context.getElementsByTagName(tag);
        } else {
            return document.getElementsByTagName(tag);
        }
    },
    $class: function(className, context) {
        var i = 0,
            len, dom = [],
            arr = [];
        if ($$.isString(context)) {
            context = document.getElementById(context);
        } else {
            context = document;
        }
        if (context.getElementsByClassName) {
            return context.getElementsByClassName(className);
        } else {
            dom = context.getElementsByTagName('*');
            for (i; len = dom.length, i < len; i++) {
                if (dom[i].className) {
                    arr.push(dom[i]);
                }
            }
        }
        return arr;
    },
    $group: function(content) {
        var result = [],
            doms = [];
        var arr = $$.trim(content).split(',');
        for (var i = 0, len = arr.length; i < len; i++) {
            var item = $$.trim(arr[i])
            var first = item.charAt(0)
            var index = item.indexOf(first)
            if (first === '.') {
                doms = $$.$class(item.slice(index + 1));
                pushArray(doms, result)
            } else if (first === '#') {
                doms = [$$.$id(item.slice(index + 1))] ;
                pushArray(doms, result)
            } else {
                doms = $$.$tag(item);
                pushArray(doms, result)
            }
        }
        return result;
        function pushArray(doms, result) {
            for (var j = 0, domlen = doms.length; j < domlen; j++) {
                result.push(doms[j])
            }
        }
    },
    $cengci: function(select) {
        var sel = $$.trim(select).split(' ');
        var result = [];
        var context = [];
        for (var i = 0, len = sel.length; i < len; i++) {
            result = [];
            var item = $$.trim(sel[i]);
            var first = sel[i].charAt(0)
            var index = item.indexOf(first)
            if (first === '#') {
                pushArray([$$.$id(item.slice(index + 1))]);
                context = result;
            } else if (first === '.') {
                if (context.length) {
                    for (var j = 0, contextLen = context.length; j < contextLen; j++) {
                        pushArray($$.$class(item.slice(index + 1), context[j]));
                    }
                } else {
                    pushArray($$.$class(item.slice(index + 1)));
                }
                context = result;
            } else {
                if (context.length) {
                    for (var j = 0, contextLen = context.length; j < contextLen; j++) {
                        pushArray($$.$tag(item, context[j]));
                    }
                } else {
                    pushArray($$.$tag(item));
                }
                context = result;
            }
        }

        return context;
        function pushArray(doms) {
            for (var j = 0, domlen = doms.length; j < domlen; j++) {
                result.push(doms[j])
            }
        }
    },
    $select: function(str) {
        var result = [];
        var item = $$.trim(str).split(',');
        for (var i = 0, glen = item.length; i < glen; i++) {
            var select = $$.trim(item[i]);
            var context = [];
            context = $$.$cengci(select);
            pushArray(context);

        }
        return result;
        function pushArray(doms) {
            for (var j = 0, domlen = doms.length; j < domlen; j++) {
                result.push(doms[j])
            }
        }
    },
    $all: function(selector, context) {
        context = context || document;
        return context.querySelectorAll(selector);
    },
});
$$.extend($$, {
    css: function(context, key, value) {
        console.log('dfdfd')
        var dom = $$.isString(context) ? $$.$all(context) : context;
        //Èç¹ûÊÇÊý×é
        if (dom.length) {
            //ÏÈ¹Ç¼Ü¹Ç¼Ü -- Èç¹ûÊÇ»ñÈ¡Ä£Ê½ -- Èç¹ûÊÇÉèÖÃÄ£Ê½
            //Èç¹ûvalue²»Îª¿Õ£¬Ôò±íÊ¾ÉèÖÃ
            if (value) {
                for (var i = dom.length - 1; i >= 0; i--) {
                    setStyle(dom[i], key, value);
                }
                //            Èç¹ûvalueÎª¿Õ£¬Ôò±íÊ¾»ñÈ¡
            } else {
                return getStyle(dom[0]);
            }
            //Èç¹û²»ÊÇÊý×é
        } else {
            if (value) {
                setStyle(dom, key, value);
            } else {
                return getStyle(dom);
            }
        }

        function getStyle(dom) {
            if (dom.currentStyle) {
                return dom.currentStyle[key];
            } else {
                return getComputedStyle(dom, null)[key];
            }
        }

        function setStyle(dom, key, value) {
            dom.style[key] = value;
        }
    },
    cssNum: function(context, key) {
        return parseFloat($$.css(context, key))
    },
    show: function(content) {
        var doms = $$.$all(content)
        for (var i = 0, len = doms.length; i < len; i++) {
            $$.css(doms[i], 'display', 'block');
        }
    },
    hide: function(content) {
        var doms = $$.$all(content)
        for (var i = 0, len = doms.length; i < len; i++) {
            $$.css(doms[i], 'display', 'none');
        }
    },
    Width: function(id) {
        return $$.$id(id).clientWidth
    },
    Height: function(id) {
        return $$.$id(id).clientHeight
    },
    scrollWidth: function(id) {
        return $$.$id(id).scrollWidth
    },
    scrollHeight: function(id) {
        return $$.$id(id).scrollHeight
    },
    scrollTop: function(id) {
        return $$.$id(id).scrollTop
    },
    scrollLeft: function(id) {
        return $$.$id(id).scrollLeft
    },
    screenHeight: function() {
        return window.screen.height
    },
    screenWidth: function() {
        return window.screen.width
    },
    wWidth: function() {
        return document.documentElement.clientWidth
    },
    wHeight: function() {
        return document.documentElement.clientHeight
    },
    wScrollHeight: function() {
        return document.body.scrollHeight
    },
    wScrollWidth: function() {
        return document.body.scrollWidth
    },
    wScrollTop: function() {
        var scrollTop = window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop;
        return scrollTop
    },
    wScrollLeft: function() {
        var scrollLeft = document.body.scrollLeft || (document.documentElement && document.documentElement.scrollLeft);
        return scrollLeft
    }
})
$$.extend($$, {
    attr: function(content, key, value) {
        var dom = $$.$all(content);
        if (dom.length) {
            if (value) {
                for (var i = 0, len = dom.length; i < len; i++) {
                    dom[i].setAttribute(key, value);
                }
            } else {
                return dom[0].getAttribute(key);
            }
        } else {
            if (value) {
                dom.setAttribute(key, value);
            } else {
                return dom.getAttribute(key);
            }
        }
    },
    addClass: function(context, name) {
        var doms = $$.$all(context);
        if (doms.length) {
            for (var i = 0, len = doms.length; i < len; i++) {
                addName(doms[i]);
            }
        } else {
            addName(doms);
        }
        function addName(dom) {
            dom.className = dom.className + ' ' + name;
        }
    },
    removeClass: function(context, name) {
        var doms = $$.$all(context);
        if (doms.length) {
            for (var i = 0, len = doms.length; i < len; i++) {
                removeName(doms[i]);
            }
        } else {
            removeName(doms);
        }
        function removeName(dom) {
            dom.className = dom.className.replace(name, '');
        }
    },
    hasClass: function(context, name) {
        var doms = $$.$all(context)
        var flag = true;
        for (var i = 0, len = doms.length; i < len; i++) {
            flag = flag && check(doms[i], name)
        }

        return flag;
        //ÅÐ¶¨µ¥¸öÔªËØ
        function check(element, name) {
            return -1 < (" " + element.className + " ").indexOf(" " + name + " ")
        }
    },
    getClass: function(id) {
        var doms = $$.$all(id)
        return $$.trim(doms[0].className).split(" ")
    }
})
$$.extend($$, {
    html: function(context, value) {
        var doms = $$.$all(context);
        if (value) {
            for (var i = 0, len = doms.length; i < len; i++) {
                doms[i].innerHTML = value;
            }
        } else {
            return doms[0].innerHTML
        }
    }
});
$$.extend($$, {
    on: function (id, type, fn) {
        var dom = $$.isString(id) ? document.getElementById(id) : id;
        if (dom.addEventListener) {
            dom.addEventListener(type, fn, false);
        } else if (dom.attachEvent) {
            dom.attachEvent('on' + type, fn);
        }
    },
    un: function (id, type, fn) {
        var dom = $$.isString(id) ? document.getElementById(id) : id;
        if (dom.removeEventListener) {
            dom.removeEventListener(type, fn);
        } else if (dom.detachEvent) {
            dom.detachEvent(type, fn);
        }
    },
    click: function (id, fn) {
        this.on(id, 'click', fn);
    },
    mouseover: function (id, fn) {
        this.on(id, 'mouseover', fn);
    },
    mouseout: function (id, fn) {
        this.on(id, 'mouseout', fn);
    },
    hover: function (id, fnOver, fnOut) {
        if (fnOver) {
            this.on(id, "mouseover", fnOver);
        }
        if (fnOut) {
            this.on(id, "mouseout", fnOut);
        }
    },
    delegate: function (pid, eventType, selector, fn) {
        //参数处理
        var parent = $$.$id(pid);
        function handle(e) {
            var target = $$.GetTarget(e);
            if (target.nodeName.toLowerCase() === selector || target.id === selector || target.className.indexOf(selector) != -1) {
                fn.call(target);
            }
        }
        parent[eventType] = handle;
    },
    //事件基础
    getEvent: function (event) {
        return event ? event : window.event;
    },
    //获取目标
    GetTarget: function (event) {
        var e = $$.getEvent(event);
        return e.target || e.srcElement;
    },
    //组织默认行为
    preventDefault: function (event) {
        var event = $$.getEvent(event);
        if (event.preventDefault) {
            event.preventDefault();
        } else {
            event.returnValue = false;
        }
    },
    //阻止冒泡
    stopPropagation: function (event) {
        var event = $$.getEvent(event);
        if (event.stopPropagation) {
            event.stopPropagation();
        } else {
            event.cancelBubble = true;
        }
    }
})
$$.extend($$, {
    eq: function() {},
    first: function() {},
    last: function() {},
    append: function() {},
    empty: function() {},
    remove: function() {},
    clone: function() {}
});
$$.extend($$, {
    sjson: function(json) {
        return JSON.stringify(json);
    },
    json: function(str) {
        return eval(str);
    }
});
$$.cache = {
    data: [],
    get: function(key) {
        console.log('111')
        var value = null;
        console.log(this.data)
        for (var i = 0, len = this.data.length; i < len; i++) {
            var item = this.data[i]
            if (key == item.key) {
                value = item.value;
            }
        }
        console.log('get' + value)
        return value;
    },
    add: function(key, value) {
        var json = { key: key, value: value };
        this.data.push(json);
    },
    delete: function(key) {
        var status = false;
        for (var i = 0, len = this.data.length; i < len; i++) {
            var item = this.data[i]
            if (item.key.trim() == key) {
                this.data.splice(i, 1);
                status = true;
                break;
            }
        }
        return status;
    },
    update: function(key, value) {
        var status = false;
        for (var i = 0, len = this.data.length; i < len; i++) {
            var item = this.data[i]
            if (item.key.trim() === key.trim()) {
                item.value = value.trim();
                status = true;
                break;
            }
        }
        return status;
    },
    isExist: function(key) {
        for (var i = 0, len = this.data.length; i < len; i++) {
            var item = this.data[i]
            if (key === item.key) {
                return true;
            } else {
                return false;
            }
        }
    }
}
$$.cookie = {
    setCookie: function(name, value, days, path) {
        var name = escape(name);
        var value = escape(value);
        var expires = new Date();
        expires.setTime(expires.getTime() + days * 24 * 60 * 60 * 1000);
        path = path == "" ? "" : ";path=" + path;
        _expires = (typeof hours) == "string" ? "" : ";expires=" + expires.toUTCString();
        document.cookie = name + "=" + value + _expires + path;
    },
    getCookie: function(name) {
        var name = escape(name);
        var allcookies = document.cookie;
        name += "=";
        var pos = allcookies.indexOf(name);
        if (pos != -1) {
            var start = pos + name.length;
            var end = allcookies.indexOf(";", start);
            if (end == -1) end = allcookies.length;
            var value = allcookies.substring(start, end);
            return unescape(value);
        } else return "";
    },
    deleteCookie: function(name, path) {
        var name = escape(name);
        var expires = new Date(0);
        path = path == "" ? "" : ";path=" + path;
        document.cookie = name + "=" + ";expires=" + expires.toUTCString() + path;
    }
}
$$.store = (function() {
    var api = {},
        win = window,
        doc = win.document,
        localStorageName = 'localStorage',
        globalStorageName = 'globalStorage',
        storage;
    api.set = function(key, value) {};
    api.get = function(key) {};
    api.remove = function(key) {};
    api.clear = function() {};

    if (localStorageName in win && win[localStorageName]) {
        storage = win[localStorageName];
        api.set = function(key, val) { storage.setItem(key, val) };
        api.get = function(key) {
            return storage.getItem(key) };
        api.remove = function(key) { storage.removeItem(key) };
        api.clear = function() { storage.clear() };

    } else if (globalStorageName in win && win[globalStorageName]) {
        storage = win[globalStorageName][win.location.hostname];
        api.set = function(key, val) { storage[key] = val };
        api.get = function(key) {
            return storage[key] && storage[key].value };
        api.remove = function(key) { delete storage[key] };
        api.clear = function() {
            for (var key in storage) { delete storage[key] } };

    } else if (doc.documentElement.addBehavior) {
        function getStorage() {
            if (storage) {
                return storage }
            storage = doc.body.appendChild(doc.createElement('div'));
            storage.style.display = 'none';
            // See http://msdn.microsoft.com/en-us/library/ms531081(v=VS.85).aspx
            // and http://msdn.microsoft.com/en-us/library/ms531424(v=VS.85).aspx
            storage.addBehavior('#default#userData');
            storage.load(localStorageName);
            return storage;
        }
        api.set = function(key, val) {
            var storage = getStorage();
            storage.setAttribute(key, val);
            storage.save(localStorageName);
        };
        api.get = function(key) {
            var storage = getStorage();
            return storage.getAttribute(key);
        };
        api.remove = function(key) {
            var storage = getStorage();
            storage.removeAttribute(key);
            storage.save(localStorageName);
        }
        api.clear = function() {
            var storage = getStorage();
            var attributes = storage.XMLDocument.documentElement.attributes;;
            storage.load(localStorageName);
            for (var i = 0, attr; attr = attributes[i]; i++) {
                storage.removeAttribute(attr.name);
            }
            storage.save(localStorageName);
        }
    }
    return api;
})();
var store = (function () {
    var api               = {},
        win               = window,
        doc               = win.document,
        localStorageName  = 'localStorage',
        globalStorageName = 'globalStorage',
        storage;
    api.set    = function (key, value) {};
    api.get    = function (key)        {};
    api.remove = function (key)        {};
    api.clear  = function ()           {};
    if (localStorageName in win && win[localStorageName]) {
        storage    = win[localStorageName];
        api.set    = function (key, val) { storage.setItem(key, val) };
        api.get    = function (key)      { return storage.getItem(key) };
        api.remove = function (key)      { storage.removeItem(key) };
        api.clear  = function ()         { storage.clear() };

    } else if (globalStorageName in win && win[globalStorageName]) {
        storage    = win[globalStorageName][win.location.hostname];
        api.set    = function (key, val) { storage[key] = val };
        api.get    = function (key)      { return storage[key] && storage[key].value };
        api.remove = function (key)      { delete storage[key] };
        api.clear  = function ()         { for (var key in storage ) { delete storage[key] } };

    } else if (doc.documentElement.addBehavior) {
        function getStorage() {
            if (storage) { return storage }
            storage = doc.body.appendChild(doc.createElement('div'));
            storage.style.display = 'none';
            // See http://msdn.microsoft.com/en-us/library/ms531081(v=VS.85).aspx
            // and http://msdn.microsoft.com/en-us/library/ms531424(v=VS.85).aspx
            storage.addBehavior('#default#userData');
            storage.load(localStorageName);
            return storage;
        }
        api.set = function (key, val) {
            var storage = getStorage();
            storage.setAttribute(key, val);
            storage.save(localStorageName);
        };
        api.get = function (key) {
            var storage = getStorage();
            return storage.getAttribute(key);
        };
        api.remove = function (key) {
            var storage = getStorage();
            storage.removeAttribute(key);
            storage.save(localStorageName);
        }
        api.clear = function () {
            var storage = getStorage();
            var attributes = storage.XMLDocument.documentElement.attributes;;
            storage.load(localStorageName);
            for (var i=0, attr; attr = attributes[i]; i++) {
                storage.removeAttribute(attr.name);
            }
            storage.save(localStorageName);
        }
    }
    return api;
})();
Function.prototype.before = function( func ) {
    var __self = this;
    return function() {
        if ( func.apply( this, arguments ) === false ) {
            return false;
        }
        return __self.apply( this, arguments );
    }
}
Function.prototype.after = function( func ) {
    var __self = this;
    return function() {
        var ret = __self.apply( this, arguments );
        if( ret === false) {
            return false;
        }
        func.apply( this, arguments );
        return ret;
    }
}
```