# package(包)

一种可包含子模块或递归地包含子包的 Python module。 从技术上说，包是具有 __path__ 属性的 Python 模块。

从层级上来定义:
In Python, a package is a way of organizing related modules into a directory hierarchy

只是为了让层级更加清晰,比如java的package概念

包包含
- Regular Package (Traditional Package)
- Namespace Package

### Regular Package

包含有一个 __init__.py 文件的目录

当包被导入的时候,__init__.py会被执行

### namespace package -- 命名空间包

[PEP 420](https://peps.python.org/pep-0420/) 所引入的一种仅被用作子包的容器的 package，命名空间包可以没有实体表示物，其描述方式与 regular package 不同，因为它们没有 __init__.py 文件。

a namespace package is a composite of multiple directories (or distribution packages) that are combined to form a single virtual package

简单来说:
1. 没有__init__.py
2. 可以存在多个目录

They are used when you want to split a package across multiple projects or directories. This allows different portions of a package to be installed in different locations, but still be imported as if they were one package.

Namespace packages are created automatically when two or more directories are found in the search path that have the same package name and neither of them has an __init__.py 

### example

##### 1. Regular Package

```
my_package/
    __init__.py
    module1.py
    module2.py

In this case, `my_package` is a regular package because it has an `__init__.py` file.
```

##### 2. namespace package
```
Suppose we have two directories in the sys.path:

Directory1:
    my_namespace/
        module_a.py

Directory2:
    my_namespace/
        module_b.py

Neither of the `my_namespace` directories has an `__init__.py` file. Then, when you import from `my_namespace`, Python will create a namespace package that combines both directories.

So, you can do:

    from my_namespace import module_a
    from my_namespace import module_b

And both modules will be accessible under the same namespace.
```

### Regular Package VS namespace package

|Feature	|Regular Package	|Namespace Package|
| --- | --- | --- |
|__init__.py |	Required| 	Absent|
|Initialization Code|	Supported (in __init__.py)|	Not supported|
|Distribution	|Single directory/install	|Split across multiple locations
|Python Version	|All versions|	Python 3.3+|
|Performance|	Slightly faster imports|	Slower due to path scanning

### 使用场景
##### Regular Package
Default choice for most projects. Use when your package is self-contained and doesn’t need to span multiple directories/distributions.

##### Namespace Package

Plugins or modular frameworks (e.g., zope, pkg_resources).

Large projects split into subcomponents (e.g., google.* packages).

Avoiding dependency conflicts by splitting functionality.

简单的说:在项目不大的时候,优先考虑Regular Package
