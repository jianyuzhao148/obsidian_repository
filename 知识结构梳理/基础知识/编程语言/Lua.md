.和: :

- .为普通调用
- :为对象调用，包含self（this）指针

pairs和ipairs：

- 都可以遍历表、数组，但ipairs 仅仅遍历值，按照索引升序遍历，索引中断停止遍历，而pairs 能遍历集合的所有元素
- pairs 遍历的顺序是随机的

表：

- 当表的键为整数时，表就可以作为数组使用，没有固定长度，索引从1开始
- 当表的键为字符串时 ，t["string"] 可写为 t.string

_ENV 和  _G：

- lua在编译运行时首先创建一个默认环境（全局环境，_G表），若没有人为自定义_ENV表，则默认把_G用作_ENV

元表：

- setmetatable(table,metatable) 对指定 table 设置元表(metatable)，如果元表 (metatable) 中存在 __metatable 键值，setmetatable 会失败
- getmetatable(table) 返回对象的元表 (metatable)
- __index：当通过键来访问 table 的时候，如果这个键没有值，那么 Lua 就会寻找该 table 的 metatable 中的 __index 键。如果 __index 包含一个 table ，Lua 则会在 table 中查找相应的键。如果__index是一个函数那么 Lua 就会调用那个函数，table 和键会作为参数传递给函数，返回该函数的返回值

rawget：绕过__index查询

rawset：绕过__newindex方法

Lua协程：

- 线程是抢占式执行，协程是协作式执行，本质是挂起和恢复
- Lua coroutine库提供协程功能：
    - Coroutine.create(f):创建一个执行f函数的新协程，返回一个类型为"thread"的对象，协程创建完成后不马上执行,需要调用coroutine.resume
    - coroutine.resume(co[val1,…]):开始或继续协程调用后执行权交给co协程，然后等待直到co执行结束或co调用coroutine.yield主动挂起，执行权才交还回来
        - coroutine.resume的val参数有两种情况
            - coroutine.resume用来启动协程，val将作为f函数的参数
            - coroutine.resume用来恢复协程，val将作为coroutine.yield函数的返回值
- resume如果执行成功，第1个返回值是true，如果是继续一个挂起的协程，后面还会跟着传入coroutine.yield的那些参数；如果有任何错误发生， resume 返回 false 和错误消息。
- coroutine.running () : 返回当前执行的协程对象，如果是主协程，后面跟一个true，否则跟一个false。主协程是指Lua的默认执行线，用coroutine.create创建的协程不是主协程。
- coroutine.yield (···): 挂起当前协程，...是任意参数，这些参数会传递给coroutine.resume的返回值。我们仔细思考coroutine.resume和coroutine.yield，这两个函数构成了协程协作的核心：
- 先假设有两个协程co1, co2；co1调用coroutine.create创建了co2，然后调用coroutine.resume(co2)启动co2，此时执行权交到co2，co1则一直停在resume那里。
- co2开始执行，到中间时调用coroutine.yield(1, 2)把自己挂机，并传入1和2；现在执行权又交到co1；而co2则一直停在yield这里。
- co1从resume返回并继续执行，返回值是true 1 2。co1执行到一半又调用coroutine.resume(co2, "hello", true)把co2唤醒，这时执行又到co2去了，co1停在resume这里。
- co2得到执行权，从yield返回，返回值是"hello" true，然后自己执行到结束，执行权才又给了co1。
- 这个过程大概就是这样，很清楚地展示了协程之间的协作。
- coroutine.status (co): 返回co协程的状态，协程有4个状态：
- running: 协程正在运行，可以确定就是调用coroutine.status的这个协程。
- suspended：协程挂起，协程创建后会处于这个状态，或是自己调用yield之后也处于这个状态。
- dead: 协程执行完毕，或因错误停止，处于死亡状态。
- normal: 当协程co1调用resume之后，co1停止运行，co1会处于这个状态。
- 关于数值类型
    1. Lua5.2之前只有双精度浮点型，Lua5.3之后提供integer的64位整型和float双精度浮点型(float不代表单精度类型)，对于资源受限的平台 ， 可以将Lua5.3 编译为精简 Lua ( Small Lua ）模式，在该模式中使用 32 位整型和单精度浮点类型（精简Lua和标准Lua除了使用了 LUA_3 2BIT S 宏定义以外其他源码一致，除了数值类型占用字节不一致，完全一致）

lua查找表中的元素时规则如下：

1.在表中查找，如果找到，返回该元素，找不到则继续 2.判断该表是否有元表，如果没有元表，返回nil，有元表则继续 3.判断元表有没有__index方法，如果__index方法为nil，则返回nil；如果__index方法是一个表，则重复1、2、3；如果__index方法是一个函数，则返回该函数的返回值