title: BootStrap学习笔记
date: 2016-11-25 21:09:19
categories:
tags:
---
bootstrap中的js依赖于jQuery，所以jQuery一定要在BootStrap之前引用
<meta name="viewport" content="width=device-width,initial-scale=1">   支持移动浏览器缩放
```
<div class="jumbotron">
    <h1>Hello, world!</h1>
    <p> calledunique.</p>
    <p><a href="#" >Learn more »</a></p>
</div>

<div class="text-muted">.text-muted 效果</div>
<div class="text-primary">.text-primary效果</div>
<div class="text-success">.text-success效果</div>
<div class="text-info">.text-info效果</div>
<div class="text-warning">.text-warning效果</div>
<div class="text-danger">.text-danger效果</div>

<p class="text-right">给我加个类，我就向右对齐。</p>
列表
<ul class="list-inline">
    <li>W3cplus</li>
    <li>Blog</li>
    <li>CSS3</li>
    <li>jQuery</li>
    <li>PHP</li>
</ul>

<ul class="list-unstyled">
    <li>
        城市:
        <ul class="list-inline">
            <li>北京</li>
            <li>上海</li>
            <li>上海</li>
            <li>上海</li>
        </ul>
    </li>
</ul>
```
自定义列表
```
<dl>
    <dt>W3cplus</dt>
    <dd>一个致力于推广国内前端行业的技术博客</dd>
    <dt>慕课网</dt>
    <dd>一个真心在做教育的网站</dd>
</dl>
```
BootStrap代码风格有三种
```
标签  pre code 和 kbd
```

表格class
```
class="table table-striped table-bordered table-hover"
class="table table-striped"
<div class="table-responsive">
    <table class="table table-bordered">
        <thead>
        <tr>
            <th>表格标题</th>
            <th>表格标题</th>
            <th>表格标题</th>
        </tr>
        </thead>
        <tbody>
        <tr>
            <td>表格单元格</td>
            <td>表格单元格</td>
            <td>表格单元格</td>
        </tr>
        </tbody>
    </table>
</div>

下拉框
<div class="form-group">
  <select multiple class="form-control">
    <option>1</option>
    <option>2</option>
    <option>3</option>
    <option>4</option>
    <option>5</option>
  </select>
</div>

单选按钮
<div class="form-group">
      <label class="radio-inline">
        <input type="radio"  value="option1" name="sex"> 男性
      </label>
      <label class="radio-inline">
        <input type="radio"  value="option2" name="sex"> 女性
      </label>
      <label class="radio-inline">
        <input type="radio"  value="option3" name="sex"> 中性
      </label>
</div>
多选框
<div class="checkbox">
    <label>
      <input type="checkbox" value="">
      记住密码
    </label>
  </div>
```
按钮
btn
btn btn-primary
btn btn-info
btn btn-success
btn btn-warning
btn btn-danger
btn btn-inverse

### 注册表单
```
<br><br><br>
<form role="form" class="form-horizontal">
    <div class="form-group">
        <label for="exampleInputEmail1" class="col-sm-2 control-label" >邮箱</label>
        <div class="col-sm-6">
            <input type="email" class="form-control" id="exampleInputEmail1" placeholder="请输入您的邮箱地址">
        </div>
    </div>
    <div class="form-group">
        <label for="exampleInputPassword1" class="col-sm-2 control-label" >密码</label>
        <div class="col-sm-6">
            <input type="password" class="form-control" id="exampleInputPassword1" placeholder="请输入您的邮箱密码">
        </div>
    </div>
    <div class="form-group">
        <div class="col-sm-offset-2 col-sm-6">
        <div class="checkbox">
            <label>
                <input type="checkbox"> 记住密码
            </label>
        </div>
    </div>
    </div>
    <div class="form-group">
        <div class="col-sm-offset-2 col-sm-6">
            <button type="submit" class="btn btn-default">进入邮箱</button>
        </div>
    </div>
</form>
```
![](http://7xrkr6.com1.z0.glb.clouddn.com/public/16-11-25/18467099.jpg)
```
<form class="form-inline" role="form">
    <div class="form-group">
        <label class="sr-only" for="">邮箱</label>
        <input type="email" class="form-control" id="exampleInputEmail2" placeholder="请输入你的邮箱地址">
    </div>
    <div class="form-group">
        <label class="sr-only" for="">密码</label>
        <input type="password" class="form-control" id="exampleInputPassword2" placeholder="请输入你的邮箱密码">
    </div>
    <div class="checkbox">
        <label>
            <input type="checkbox"> 记住密码
        </label>
    </div>
    <button type="submit" class="btn btn-default">进入邮箱</button>
</form>
```
![](http://7xrkr6.com1.z0.glb.clouddn.com/public/16-11-25/97238877.jpg)

带焦点的input
<form role="form" class="form-horizontal">
    <div class="form-group">
        <div class="col-xs-6">
            <input class="form-control input-lg" type="text" placeholder="不是焦点状态下效果">
        </div>
        <div class="col-xs-6">
            <input class="form-control input-lg" type="text" placeholder="焦点点状态下效果">
        </div>
    </div>
</form>

<input class="form-control input-lg" type="text" placeholder="不是焦点状态下效果" disabled>  禁用input

 <fieldset disabled></fieldset> 整个fieldset里面的内容全部禁用

成功失败标识
<form role="form">
    <div class="form-group has-success">
        <label class="control-label" for="inputSuccess1">成功状态</label>
        <input type="text" class="form-control" id="inputSuccess1" placeholder="成功状态" >
    </div>
    <div class="form-group has-warning">
        <label class="control-label" for="inputWarning1">警告状态</label>
        <input type="text" class="form-control" id="inputWarning1" placeholder="警告状态">
    </div>
    <div class="form-group has-error">
        <label class="control-label" for="inputError1">错误状态</label>
        <input type="text" class="form-control" id="inputError1" placeholder="错误状态">
    </div>
</form>
![](http://7xrkr6.com1.z0.glb.clouddn.com/public/16-11-25/88782845.jpg)

表单反馈
<form role="form">
    <div class="form-group has-success has-feedback">
        <label class="control-label" for="inputSuccess1">成功状态</label>
        <input type="text" class="form-control" id="inputSuccess1" placeholder="成功状态" >
        <span class="glyphicon glyphicon-ok form-control-feedback"></span>
    </div>
    <div class="form-group has-warning has-feedback">
        <label class="control-label" for="inputWarning1">警告状态</label>
        <input type="text" class="form-control" id="inputWarning1" placeholder="警告状态">
        <span class="glyphicon glyphicon-warning-sign form-control-feedback"></span>
    </div>
    <div class="form-group has-error has-feedback">
        <label class="control-label" for="inputError1">错误状态</label>
        <input type="text" class="form-control" id="inputError1" placeholder="错误状态">
        <span class="glyphicon glyphicon-remove form-control-feedback"></span>
    </div>
</form>
![](http://7xrkr6.com1.z0.glb.clouddn.com/public/16-11-25/2652598.jpg)


















