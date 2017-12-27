---
title: thinkphp学习(四)_模版
date: 2016-06-14 19:45:23
tags: [php,thinkphp]
category: [php]

---
简单介绍thinkphp中的模版
<!--more-->
## 一、模版
### 1.1、模版的输出页面
- Tpl->Index->index.html
```
<html>
<head>
<meta charset='UTF-8'>
</head>
<body>
hello,world
</body>
</html>
```
- Lib->Action->IndexAction.class.php
```
<?php
// 本类由系统自动生成，仅供测试用途
class IndexAction extends Action {
    public function index(){
        $this->display();//默认显示index
        //eg:$this->display('Index/test.html');
    }
}
```

### 1.2、模版变量后台传递前端(1)

- Tpl->Index->index.html
```
<html>
<head>
<meta charset='UTF-8'>
</head>
<body>
<?php
echo $name1;
echo $name2;
?>
</body>
</html>
```
- Lib->Action->IndexAction.class.php
```
<?php
// 本类由系统自动生成，仅供测试用途
class IndexAction extends Action {
    public function index(){
        $this->name1 = 'haha';//不推荐这个
        $this->assign('name2','hehe')->assign('name3','xixi');//assign能一直定义值，推荐这个
        $this->display();//默认显示index
        //eg:$this->display('Index/test.html');
    }
}
```

### 1.3、模版的后台传递前端(2)

- Tpl->Index->index.html
```
<html>
<head>
<meta charset='UTF-8'>
</head>
<body>
{$me['name']}
{$me.name}
{$me.sex|default='man'//可以在这里定义默认的值}
{$me['age']+1    //只能用这种方式}
{$me.age+1    //这种方式是错误的，不会出现结果}
</body>
</html>
```
- Lib->Action->IndexAction.class.php
```
<?php
// 本类由系统自动生成，仅供测试用途
class IndexAction extends Action {
    public function index(){
        $me['name'] = 'cuicui';
        $me['age'] = 2;
        $this->display();
    }
}
```
### 1.4、模版的调用函数

- Tpl->Index->index.html
```
<html>
<head>
<meta charset='UTF-8'>
</head>
<body>
{$name}
{$me|md5|substr=0,5}
//上面两个结果是一样的
{$now|date='Y-m-d H:i:s',###}
//用三个变量来代替值
{$Think.now//系统函数}
{$Think.server.http_host}
</body>
</html>
```
- Lib->Action->IndexAction.class.php
```
<?php
// 本类由系统自动生成，仅供测试用途
class IndexAction extends Action {
    public function index(){
        $me = 'cuicui';
        $name = substr(md5($me),0,5);
        $this->assign('name',$name);
        $this->assign('now',time());
        $this->display();
    }
}
```

### 1.5、模版输出数组

- Tpl->Index->index.html
```
<html>
<head>
<meta charset='UTF-8'>
</head>
<body>
<volist name = 'arr' id = 'data'>
{$data['name']}-------{$data['age']}<br/>
</volist>

//只输出两个
<volist name = 'arr' id = 'data' offset='1'length='2'>
{$data['name']}-------{$data['age']}<br/>
</volist>

<foreach name = 'arr' item='data'>
{$data['name']}-------{$data['age']}<br/>
</foreach>

</body>
</html>
```
- Lib->Action->IndexAction.class.php
```
<?php
// 本类由系统自动生成，仅供测试用途
class IndexAction extends Action {
    public function index(){
        $arr = array(
        1=>array('name'=>'haha','age'=>'11'),
        2=>array('name'=>'uwuw','age'=>'12'),
        3=>array('name'=>'xxhx','age'=>'13'),
        );
        $this->assign('arr',$arr);
        $this->display();
    }
}
```

### 1.6、比较标签eq=  neq!=  gt>  egt>= lt<  elt<=  heq===  nheq!==
```
<eq name='num'value='11'>num=11<else/>num!=11</eq>
<neq name='num'value='11'>num!=11</neq>
//这个牛逼啊
<compare name='num'value='10'type='eq'>num=10</compare>
```
### 1.7、区间标签 in notin between(1-10) notbetween
```
<in name='num'value='1,2,3'>zai <else/>buzai</in>
<between name='num'value='1,10'>zai<else/>buzai</between>
```
### 1.8、for循环
```
<for start='1' end='10' comparison='elt' name='k'>
{$k}<br/>
</for>
```
### 1.9、if判断
```
<if condition='$num gt 10'>//没有结束符号
num大10
<elseif condition='$num lt 10'/>//有结束符号
num小10
<else/>//有结束符号
</if>
```
### 1.10、switch判断
```
<switch name='name'>
<case value = 'xiaoming'>out</case>
<case value = 'xiaohong'>heihei</case>
<default/>haha
</switch>
```