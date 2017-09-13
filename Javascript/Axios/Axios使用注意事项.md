>参考：
https://github.com/mzabriskie/axios/issues/350

>https://github.com/mzabriskie/axios/issues/97

## request 时的数据类型默认为 JSON
>JQuery的ajax默认不是JSON

使用axios发送Ajax请求时，通过Request Headers 可以发现 Content-Type 默认为 `application/json;charset=UTF-8`。
此时，发送请求的数据在request的body中，而不是在url中。因此，Spring MVC 的Controller中** 不能使用 `@RequestParam` 或 `String XX`的形式获取到请求参数！**。必须使用**`@RequestBody`**注解到实体类（不是单一的参数，如`@RequestBody String xxx` 是不行的）（确保配置了 `message-converters`，使用Jackson或FastJSON）
>[Post JSON to spring REST webservice](https://www.leveluplunch.com/java/tutorials/014-post-json-to-spring-rest-webservice/)

## 使用常规形式( 即`application/x-www-form-urlencoded` )发送请求数据
### 配置请求头，指定数据类型
```javascript
axios.post(
    '/foo',
    { some: 'data' },
    { headers: { 'Content-Type': 'application/x-www-form-urlencoded' } }
);
```
### 使用URLSearchParams
```javascript
var params = new URLSearchParams();
params.append('param1', 'value1');
params.append('param2', 'value2');
axios.post('/foo', params);
```
注意：浏览器**不支持** URLSearchParams，因此需要使用 polyfill(用于实现浏览器并不支持的原生API的代码),如 [url-search-params](https://github.com/WebReflection/url-search-params)
### 使用[qs](https://github.com/ljharb/qs)库
```javascript
var qs = require('qs');
axios.post('/foo', qs.stringify({ 'bar': 123 });
```
### Node 环境下使用 querystring
```javascript
var querystring = require('querystring');
axios.post('/foo', querystring.stringify({
  'bar': 123
});

// 
var querystring = require('querystring');
axios.post('/foo', querystring.stringify({
  'bar': 123
}, {
  params: { foo: 'bar' } // query string
});


```
## params 部分可以使用`@RequestParam`获取
>data 部分不行
```javascript
this.$http.post('/project/create', this.project, { params: { a: 'test' } }
).then(）
```
`@RequestParam String a`