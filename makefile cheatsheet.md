# Makefile cheatsheet

## 变量赋值

```makefile
foo  = "bar"
bar  = $(foo) foo  # 动态（递归）赋值
foo := "boo"       # 一次性赋值, $(bar) 现在变为 "boo foo"
foo ?= /usr/local  # 安全赋值,如果前面已经被赋值过就不会执行, $(foo) and $(bar) still the same
bar += world       # 追加赋值, $(bar) 现在是 "boo foo world"
foo != echo fooo   # 执行shell command 并且赋值给 foo 变量
# $(bar) now is "fooo foo world"
```

注意！ = 仅在使用时计算

## 自动变量（Magic变量）

```makefile
out.o: src.c src.h
  $@   # "out.o" (目标)
  $<   # "src.c" (第一个依赖)
  $^   # "src.c src.h" (所有依赖，去重)

%.o: %.c
  $*   # 模式匹配中的词干 stem ("foo.c" 中匹配到的 "foo")

also:
  $+   # prerequisites (所有依赖，不去重)
  $?   # prerequisites (新的依赖)
  $|   # prerequisites (order-only 依赖)

  $(@D) # target directory
```

order-only 依赖是什么？去 STFW

## 命令前缀

| 参数  | 含义                                             |
| ----- | ------------------------------------------------ |
| `-` | 忽略错误                                         |
| `@` | 不要打印命令                                     |
| `+` | 即使 Make 处于‘don’t execute’模式下，依旧执行 |

```makefile
build:
    @echo "compiling"
    -gcc $< $@

-include .depend
```

## 查找文件

```makefile
js_files  := $(wildcard test/*.js)
all_files := $(shell find images -name "*")
```

## 替换

```makefile
file     = $(SOURCE:.cpp=.o)   # foo.cpp => foo.o
outputs  = $(files:src/%.coffee=lib/%.js)

outputs  = $(patsubst %.c, %.o, $(wildcard *.c))  # .c能匹配的文件都替换为.o
assets   = $(patsubst images/%, assets/%, $(wildcard images/*))
```

## 更多常用函数

```makefile
$(strip $(string_var))  # 去空格

$(filter %.less, $(files)) # 过滤出.less文件
$(filter-out %.less, $(files)) # 过滤掉 .less文件
```

## Includes

```makefile
-include foo.make
```

## make选项

```sh
make
  -e, --允许环境变量覆盖 makefile 中定义的变量
  -B, --强制重新构建所有目标，无论文件时间戳如何
  -s, --silent
  -j, --jobs=N   # 并行处理
```

## 条件命令

```makefile
foo: $(objects)
ifeq ($(CC),gcc)
  $(CC) -o foo $(objects) $(libs_for_gcc)
else
  $(CC) -o foo $(objects) $(normal_libs)
endif
```

## 递归处理

```makefile
deploy:
  $(MAKE) deploy2
```
