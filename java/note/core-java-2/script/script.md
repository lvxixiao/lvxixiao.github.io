# 脚本



#### Java平台的脚本（P352）

脚本语言是一种通过在运行时解释程序文本，从而避免使用通常的编辑/编译/链接/运行循环的语言。

#### 脚本语言的优势（P352）

* 便于快速变更，鼓励不断试验
* 可以修改运行着的程序的行为
* 支撑程序用户的定制化

#### 脚本引擎（P353）

脚本引擎是一个可以执行用户某种特定语言编写的脚本的类库。当虚拟机启动时，它会发现可用的脚本引擎。

<table>
  <thead>
    <tr>
      <th style="text-align:left">引擎</th>
      <th style="text-align:left">名字</th>
      <th style="text-align:left">MIME类型</th>
      <th style="text-align:left">文件扩展</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Nashorn</td>
      <td style="text-align:left">
        <p>nashorn,Nashorn,js,JS,</p>
        <p>JavScript,javascript,ECMAScript,</p>
        <p>ecmascript</p>
      </td>
      <td style="text-align:left">
        <p>application/javascript,application/</p>
        <p>ecmascript,test/javacript,test/ecmascript</p>
      </td>
      <td style="text-align:left">js</td>
    </tr>
    <tr>
      <td style="text-align:left">Grovy</td>
      <td style="text-align:left">groovy</td>
      <td style="text-align:left">无</td>
      <td style="text-align:left">groovy</td>
    </tr>
    <tr>
      <td style="text-align:left">Renijin</td>
      <td style="text-align:left">Renijin</td>
      <td style="text-align:left">test/x-R</td>
      <td style="text-align:left">R,r,S,s</td>
    </tr>
    <tr>
      <td style="text-align:left">SISC Scheme</td>
      <td style="text-align:left">sisc</td>
      <td style="text-align:left">无</td>
      <td style="text-align:left">scheme,sisc</td>
    </tr>
  </tbody>
</table>通常，可以通过名字、MIME类型或文件扩展来获得引擎

```text
ScriptEngine engine = manager.getEngineByName("nashorn");
```

Java SE8 包含一个Nashorn版本，这是由Oracle开发的一个JavaScript解释器。可以通过在类路径中提供必要的JAR文件来添加对更多语言的支持。

#### 相关API

* javax.script.ScriptEngineManager 6
  * List getEngineFactories\(\)
    * 获取所有发现的引擎工厂的列表
  * ScriptEngine getEngineByName\(String name\)
  * ScriptEngine getEngineByExtension\(String extension\)
  * ScriptEngine getEngineByMimeType\(String mineType\)
    * 获取给定名字、脚本文件扩展名或MIME类型的脚本引擎
* javax.script.ScriptEngineFactory 6
  * List getName\(\)
  * List getExtensions\(\)
  * List getMimeTypes\(\)
    * 获取该工厂所了解的名字、脚本文件扩展名和MIME类型

#### 调用脚本的函数和方法

要调用一个函数，需要用函数名来调用invokeFunction方法，函数名后面是函数的参数

```text
//Define greet function in JavaScript
engine.eval("function greet(how, whom){ return how + ', '+ whom + '!'}");

//Call the function with arguments "Hello", "World"
result = ((Invocable) engine).invokeFunction("greet","Hello","World");
```

如果脚本语言是面向对象的，可以调用: nvoke Method:

```text
//Define Greeter class in JavaScript
engine.eval("function Greeter(how) { this.how = how}");
engine.eval("Greeter.prototype.welcome = function(whom){return this.how + ',' + whom + '!'}");

//Construct an instance
Object yo = engine.eval("new Greeter('Yo')");

//Call the welcome method on the instance
result = ((Invocable) engine).invokeMethod(yo,"welcome","World");
```

我们可以更进一步，让脚本引擎去实现一个Java接口，然后就可以用Java方法调用的语法来调用脚本函数。

```text
public inerface Greeter{
    String welcome(String whom);
}

//Define welcome function in JavaScript
engine.eval("function welcom(whom) {return 'Hello, '+ whom + '!'}");

//Get a Java object and call a Java metohd
Greeter g = ((Invocable) engine).getInterface(Greeter.class);
result = g.welcome("world");
```

#### 编译脚本

某些脚本引擎出于对执行效率的考虑，可以将脚本代码编译为某种中间格式。这些引擎实现了Compilable接口。

下面示例展示了如何编译和计算包含在脚本文件中的代码

```text
Reader reader = new FileReader("mycript.js");
CompiledScript script = null;
if(engine implements Compilable)
    script = ((Compilable) engine).compile(reader);

if(script != null)
    script.eval();
else
    engine.eval(reader);
```

