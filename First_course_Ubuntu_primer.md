#### 1. make工具

利用make工具可以自动完成编译工作，这些工作包括：

- 如果修改了某几个源文件，则只重新编译这几个源文件
- 如果某个头文件被修改了，则重新编译所有包含该头文件的源文件

make工具通过一个称为Makefile的文件来完成并自动维护编译工作，Makefile文件描述了整个工程的编译、连接规则。

#### 2. Makefile文件

Makefile基本规则：

```makefile
TARGET...: DEPENDENCIES...
	COMMAND
	...
```

- TARGET：目标程序产生的文件，如可执行文件和目标文件，目标也可以是要执行的动作，如clean，也称为伪目标。
- DEPENDENCIES：依赖是用来产生目标的输入文件列表，一个目标通常依赖于多个文件。
- COMMAND：命令是make执行的动作（命令是shell命令或是可在shell下执行的程序），**注意每个命令行的起始字符必须为TAB字符**。
- 如果DEPENDENCIES中有一个或多个文件更新的话，COMMAND就要执行，这就是Makefile最核心的内容。

#### 3. Makefile变量

```makefile
#Makefile变量的使用
objects = main.o input.o calcu.o
main: $(objects)
	gcc -o main $(objects)
```

##### 3.1 赋值符“=”

使用“=”在给变量赋值的时候，不一定要用已经定义好的值，也可以使用后面定义的值，比如如下代码：

```makefile
#变量的定义取决于它所引用的变量的最后一次有效值
name = zzk
curname = $(name)
name = zuozhongkai

print:
#加上@表示不显示命令的执行过程
	@echo curname: $(curname)
```

##### 3.2 赋值符“:=”

```makefile
name = zzk
curname := $(name)
name = zuozhongkai
```

“:=”不会使用后面定义的变量，只能使用前面已经定义好的，这就是“=”和“:=”两个的区别。

##### 3.3 赋值符“?=”

```makefile
curname ?= zuozhongkai
```

上面代码的意思是，如果变量curname前面没有被赋值，那么此变量就是“zuozhongkai”，如果前面已经赋过值了，那么就使用前面赋的值。

##### 3.3 变量追加“+=”

Makefile中的变量是字符串，有时候我们需要给前面已经定义好的变量添加一些字符串进去，此时就要使用到符号“+=”，比如：

```makefile
objects = main.o input.o
objects += calcu.o
```

#### 4. Makefile模式规则

模式规则中的”%“表示对文件名的匹配，”%“表示长度任意的非空字符串，比如”%.c“就是所有的以.c结尾的文件，类似于通配符。

```makefile
objects = main.o input.o calcu.o
main: $(objects)
	gcc -o main $(objects)
	
%.o : %.c
	#命令
	
clean:
	rm *.o
	rm main
```

#### 5. Makefile自动化变量

![image-20231021150343043](E:\ZDYZ_Linux\ZDYZ_Linux\image-20231021150343043.png)

示例：

```makefile
objects = main.o input.o calcu.o
main: $(objects)
	gcc -o main $(objects)
	
%.o : %.c
	gcc -c $<
	
clean:
	rm *.o
	rm main
```

#### 6. Makefile伪目标

Makefile中一般的目标名都是要生成的文件，而伪目标不代表真正的目标名，在执行make命令的时候通过指定这个伪目标来执行其所在规则的定义的命令。

使用伪目标主要是为了避免Makefile中定义的只执行命令的目标和工作目录下的实际文件出现名字冲突，有时候需要编写一个规则用来执行一个命令，但是这个规则不是用来创建文件的，比如：

```makefile
clean:
	rm *.o
	rm main
#如果工作目录中有名为clean的文件，规则因为没有依赖文件，所以目标被认为是最新的，那么后面的rm命令就不会执行，此时可以把clean声明为伪目标
.PHONY:clean
clean:
	...
```

#### 7. Makefile条件判断

```makefile
<条件关键字>
	<条件为真时执行的语句>
endif
#####
<条件关键字>
	<条件为真时执行的语句>
else
	<条件为假时执行的语句>
endif
#####
#条件关键字：ifeq、ifneq、ifdef和ifndef
ifeq(<参数1>, <参数2>)
ifeq '<参数1>', '<参数2>'
ifeq "<参数1>", "<参数2>"
ifeq "<参数1>", '<参数2>'
ifeq '<参数1>', "<参数2>"

ifdef <变量名>
```

#### 8. Makefile函数使用

Makefile中的函数是已经定义好的，可以直接使用，**不支持自定义函数**。

函数用法：

```makefile
$(函数名 参数集合)
#或者
${函数名 参数集合}
#参数之间可以用','隔开
```

