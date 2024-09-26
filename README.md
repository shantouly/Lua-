### Table -- Lua中一种十分关键的数据结构。Lua中的数组、二维数组、字典、类、结构体等等都是用table来进行实现  
#### 数组与二维数组：  
    1:)直接获取长度的方法为#  
    2:)Lua中的数组和二维数组的索引是从1开始的。  
    3:)Lua中数组末尾的元素为nil时，计算长度不会加进去；在低版本的Lua中，数组中元素为nil时，会直接截断，只计算nil前面的长度（这个在更高级的Lua版本已经修改了，还是将这个nil计算到数组的长度中)  
    4:)Lua中的数组是支持自定义索引的，但是，在获取长度的时候会有错误。在使用#直接获取长度的时候，小于等于0的索引会直接剔除。  
      tips：当自定义索引中索引跳过了超过两个以上的话，会把当前及其之后的索引给剔除，最多间隔1  
          :c = {[1] = 1,[2] = 2,[4] = 3}  
    5:)数组的遍历  
      for i = 1, #a, 1 do  
        print(a[i])  
      end  
      二维数组：  
      -- 二维数组的遍历，这里如果不声明b的话，那么b应该就是一个全局变量,print(b)加上了local之后打印出来的为nil  
      for i = 1, #aa, 1 do
          local b = aa[i]
          for j = 1, #b, 1 do
              print(b[j])
          end
      end  
#### iparis和paris  
    iparis -- 不能找到0和0以下的自定义索引的内容，如果从1开始 索引不是连续的话，后面的内容也会找不到  
    pairs -- 建议用来遍历各种不规则的表，它可以得到所有信息  
      
    用iparis遍历数组：  
    a = {1,2,3,4,5}  
    for i,k in ipairs(a)do  
        print(i.."_"..k)  
    end  
    -- 用paris遍历自定义索引会把所有的键值遍历出来，不管是不是小于等于0  
    for i,k in pairs(a) do  
        print(i.."_"..k)  
    end  

    用paris遍历字典：  
    a = {["name"] = "Ly",["age"] = 14,["1"] = 1}  
    -- 使用paris  
    for _,k in pairs(a) do  
        print(_, k)  
    end  
#### Function  
    两种申明方式：一个在Fcuntion后面取名字；一个不去名字，用一个变量来存储  
    1:) function func()  
    ....  
    end  
    2:) f1 = function ()  
        ...  
        end  
    函数的传参：个数传多了或者传少了都不会报错；多了会进行丢弃，少了则会补空  
    函数的返回值：可以有多个返回值；外部用多个变量来接取，接少接多都不影响；少了就丢弃；多了就补空  
    函数的type：Function  
    lua中不支持函数的重载  
    lua中的边长参数： -> ... 来申明，先用表接然后再进行使用  
    函数的嵌套：就是函数里面申明函数；闭包，改变了变量的生命周期  
#### lua中封装  
     封装:
        表就是表现类的一种形式  
        实现了new方法本质上是创建了一个空表，设置了一个元表，使用__index来指向自己  
        修改创建出来的对象的属性变量时，返回出来的空表对象新建了一个成员属性（变量）  
        冒号：self代表函数调用者，表里面要有对应的self函数才行，普通函数是不能这样调用的  
        Object = {}  
        function Object:new()  
            local obj = {}  
            self.__index = self  
            setmetatable(obj,self)  
            return obj  
        end  
#### lua中的继承  
    function Object:subClass(className)  
        _G[className] = {}  
        local obj = _G[className]  
        obj.base = self  
        self.__index = self  
        setmetatable(obj,self)  
    end  
#### lua中的多态  
    -- 申明一个新的类  
    Object:SubClass("GameObject")  
    -- 成员变量  
    GameObject.posX = 0  
    GameObject.posY = 0  
      
    -- 成员方法  
    function GameObject:Move()  
        self.posX = self.posX + 1  
        self.posY = self.posY + 1  
    end  
      
    -- 实例化对象使用  
    local obj = GameObject:new()  
    print(obj.posX)  
    obj:Move()  
    obj:Move()  
    print(obj.posX)  
    obj:Move()  
      
    local obj1 = GameObject:new()  
    print(obj1.posX)  
    obj1:Move()  
    print(obj1.posX)  
      
    -- 申明一个新的类 Player 继承 GameObject  
    GameObject:SubClass("Player")  
    -- 多态 重写了 GameObject的Move方法  
    function Player:Move()  
        self.base.Move(self)  
    end  
      
    -- 实例化Player对象  
    print("------------------------------------")  
    local p1 = Player:new()  
    print(p1.posX)  
    p1:Move()  
    print(p1.posX)  
      
    local p2 = Player:new()  
    print(p2.posX)  
    p2:Move()  
    print(p2.posX)  
#### lua中的垃圾回收  
    test =     {id = 1,name = "1234"}  
    -- 垃圾回收关键字  
    -- collectgarbage  
    -- 获取当前lua占用内存数 k字节 用返回值*1024 就可以得到具体的内存占用字节数  
    print(collectgarbage("count")  
    -- 对当前的程序进行垃圾回收  
    collectgarbage("collect")  
    print(collectgarbage("count")  
    进行了垃圾回收之后，再调用collect时，打印出来的值应该会有所减少的  
#### lua中的协程  
    1:) 协程的创建  
    func = function()  
        print(123)  
    end  
    co = coroutine.create(func) -- 这个的返回值类型是thread  
    co1 = coroutine.wrap(func) -- 这个的返回值类型是function  
    2:) 协程的挂起  
    fun2 = function()  
     loacl i = 1  
     while true do  
         print(i)  
         i = i + 1  
         coroutine.yield()  
    end  
    end  
    这个跟c#的是类似的  
#### 协程的状态  
    coroutine.status(协程对象) --- 调用方式,参数不能是协程函数，只能是协程对象(用create创建的)  
    dead --- 结束  
    suspended --- 暂停  
    running --- 进行中  
    normal --- 活动但是没有运行  
    coroutine.running() --- 可以获取当前正在进行中的协程对象  
  
    -- 协程的执行  
    co = coroutine.create(function) --> coroutine.resume(co)  
  
    co1 = coroutine.warp(function) --> co1()  
#### lua中的本地变量和全局变量  
    在lua中，没有在变量前面加上local的都是全局变量，即使是在function中定义的变量
#### lua中的多脚本执行（在使用require来调用其他的lua脚本时，当我调用之后，是可以使用另一个脚本中的全局变量的。但是本地变量无法使用）  
    Test.lua:  
    print("Test测试")  
    testA = 123  
    local testLocalA = 321  
      
    return testLocalA -- 这里，脚本也可以返回自己的一个本地变量，但是在其他lua脚本中接收的时候只能接收一个变量。  
    -- 例子；local a = require("Test")  

    require.lua  
    require("Test")  
    print(testA)  
    print(testlocalA)  -- 这个是打印不出来的  
#### 脚本状态的查看，卸载和大G表  
    package.Load["Test"] -- 返回这个脚本是否被加载  
    package.load["Test"] = nil -- 将这个脚本进行卸载  
    tips:当我require了一次脚本之后，再执行require语句是不会将这个脚本再加载一次的  
    _G -- _G表是一个总表(table) 他将我们申明的所有全局的变量都存储再其中(只存储全局变量)  
    -- 这里，如果我没有require另一个lua脚本的话，那么此时遍历这个大G表是不会出现另一个脚本的全局变量的  
    for key,value in paris(_G) do  
        print(key,value)  
    end  
    
    
      
      
