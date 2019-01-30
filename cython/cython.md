## cython编译
1. IPython环境（交互式编译与运行）
2. 在import的时候自动编译
3. 通过编译工具编译（比如distutils)
4. 通过cmake或者make等标准构建工具

Cython code -> Python extension module -> 
两阶段编译
1. cython compiler: cython -> c/c++
2. c/c++ compiler: c/c++ -> a shared library
生成so文件（linux系统）或者pyd文件（win系统）
extension module

## 标准路线：distutils与cythonize
distutils: 将c source编译为extension module
cythonize: cython源码编译为c/c++

```python
from distutils.core import setup 
from Cython.Build import cythonize
setup(ext_modules=cythonize('fib.pyx'))
```

编译命令
```shell
python setup.py build_ext --inplace
```

## 封装c/c++
如果用来封装c/c++，我们还需要涵盖更多的源文件。

```python
from distutils.core import setup, Extension 
from Cython.Build import cythonize

# First create an Extension object with the appropriate name and sources.
ext = Extension(name="wrap_fib", sources=["cfib.c", "wrap_fib.pyx"]) 

# Use cythonize on the extension object.
setup(ext_modules=cythonize(ext))
```

## 封装so文件
如果封装现有的so文件，可以使用以下方式

```python
from distutils.core import setup, Extension 
from Cython.Build import cythonize

ext = Extension(name="wrap_fib",
                sources=["wrap_fib.pyx"],
                library_dirs=["/path/to/libfib.so"],
                libraries=["fib"])
setup(ext_modules=cythonize(ext))
```

## pyximport: compiling on-the-fly
```python
import pyximport
pyximport.install()
```

## pyximport的依赖
`pyxdeps`文件

`pyxbld`文件

## 手动编译
第一步比较简单： cython xx.pyx

```shell
$ CFLAGS=$(python-config --cflags)
$ LDFLAGS=$(python-config --ldflags)
$ cython fib.pyx # --> outputs fib.c
$ gcc -c fib.c ${CFLAGS} # outputs fib.o
$ gcc fib.o -o fib.so -shared ${LDFLAGS} # --> outputs fib.so
```

## 其他构件工具
## cmake
```
# Detects and activates Cython
include(UseCython)
# Specifies that Cython source files should generate C++
set_source_files_properties(
  ${CYTHON_CMAKE_EXAMPLE_SOURCE_DIR}/src/file.pyx
  PROPERTIES CYTHON_IS_CXX TRUE )
# Adds and compiles Cython source into an extension module
cython_add_module( modname file.pyx cpp_source.cxx)
```

## 独立运行的可执行文件
```shell
cython --embed xx.py
```

## 深层解析
cython能够加速的原因1：the Python interpreter can run compiled C code directly and transparently to the end user （extension module)

原因2：将dynamic dispath替换为static typing

## 语法

### 最重要的关键字`cdef`
```python
cdef int i 
cdef int j 
cdef float k
j=0
i=j 
k=12.0 
j=2*k
```

需要注意的是，这里已经改变了“赋值”的含义。

### cython中的自动类型推断
```python
cimport cython
@cython.infer_types(True) 
def more_inference():
	i=1 
	d=2.0 
	c=3+4j 
	r=i*d+c 
	return r
```

### cython中的指针
```python
cdef double golden_ratio
cdef double *p_double
p_double = &golden_ratio
p_double[0] = 1.618  // dereference稍微特殊一点
```

### 动、静混合类型的变量
```python
cdefinta,b,c
# ...Calculations using a, b, and c... 
tuple_of_ints = (a, b, c)
```

### statically declaring variables with a python type
we have used `cdef` to statically declare variables with a C type.
It is also possible to use `cdef` to statically declare variables with a python type.
对于内置类型list, tuple, dict是可以生效的，对于numpy中的array也同样生效。

```python
cdef list particles, modified_particles 
cdef dict names_from_particles
cdef str pname
cdef set unique_particles
```

### static typing for speed

### reference counting and static string types

### cython的三种函数
But Python functions are more flexible and powerful. Python functions are first-class citizens, meaning that they are objects with state and behavior. 

cython同时支持python和c的函数，并且允许他们之间相互调用。

#### 第一种：python函数
`def`关键字

标准

```python
def py_fact(n): 
	"""Computes n!""" 
	ifn<=1:
		return 1
	return n * py_fact(n - 1)

def typed_fact(long n): 
	"""Computes n!""" 
	ifn<=1:
		return 1
	return n * typed_fact(n - 1)
```

#### 第二种：c函数
`cdef`关键字

```python
cdef long c_fact(long n):
	if n<=1:
		return 1;
	return n * c_fact(n-1)
```

 重点在于返回值是有静态类型的。

 cdef函数可以在同一文件中被cdef或者def函数调用，但是不能为外部python函数调用。

 #### 第三种：def/cdef==>cpdef

`cpdef`混合了两种的特性，解决了各自的局限: we get a C-only version of the function and a Python wrapper for it, both with the same name.

`cpdef` combine the accessibility of `def` functions with the performance of `cdef` functions.

```python
cpdef long cp_fact(long n): 
	"""Computes n!""" 
	ifn<=1:
		return 1
	return n * cp_fact(n - 1)
```

#### 函数与异常处理
```python
cpdef int divide_ints(int i, int j) except? -1: 
	returni/j
```

#### struct, union和enum的使用
Cython also understands how to declare, create, and manipulate C structs, unions, and enums.
```c
struct mycpx { 
	int a;
	float b; 
};
union uu { 
	int a;
	short b, c; 
};
```
而对等的cython定义如下

```python
cdef struct mycpx: 
	float real 
	float imag

cdef union uu: 
	int a
	short b, c

cdef enum PRIMARIES: 
	RED=1
	YELLOW = 3 
	BLUE=5

cdef enum SECONDARIES: 
	ORANGE, GREEN, PURPLE

cdef mycpx zz
```
我们有三种初始化方法

第一种

```python
cdef mycpx a = mycpx(3.1415, -1.0)
cdef mycpx b = mycpx(real=2.718, imag=1.618034)
```

第二种

```python
cdef mycpx zz 
zz.real = 3.1415 
zz.imag = -1.0
```

第三种

```python
cdef mycpx zz = {'real': 3.1415, 'imag': -1.0}
```

#### cython处理for和while循环
```python
cdef unsigned int i, n = 100 for i in range(n):
for (i=0; i<n; ++i) { 
	/* ... */
}
```

#### cython预处理器
`DEF`关键字与C中的`#define`宏一样的效果。

```python
DEF E = 2.718281828459045
DEF PI = 3.141592653589793

def feynmans_jewel():
	"""Returns e**(i*pi) + 1. Should be ~0.0""" 
	returnE**(1j*PI)+1.0
```

## cython与扩展类型
cython对python类的扩展

### python类与扩展类型的对比
We can also create our own types at the C level directly using the Python/C API; these are known as extension types. (原本的内置类型object, list, dict, file ,int ,float等就是这么定义的，当然我们也可以)


`cdef class`语句

Despite the syntactic similarities, it is important to remember that a cdef class has fast C-level access to all methods and data. This feature is the most significant difference between an extension type and a plain Python class defined in a .py module.

### cython中的扩展类型

python原生的

```python
class Particle(object): 
	"""Simple Particle type.""" 
	def __init__(self, m, p, v):
            self.mass = m
            self.position = p
            self.velocity = v
	def get_momentum(self):
		return self.mass * self.velocity
```

cython版

```python
cdef class Particle:
	"""Simple Particle extension type.""" 
	cdef double mass, position, velocity # ...
```

Extension type attributes are private by default, and are accessible by the methods of the class.

### type attributes and access control
```python
cdef class Particle:
	"""Simple Particle extension type."""
	cdef readonly double mass, position, velocity # ...
```

或者

```python
cdef class Particle:
	"""Simple Particle extension type.""" 
	cdef readonly double mass
	cdef double position, velocity
# ...
```

默认是`private`的。

也是声明为`public`使得可以读写

```python
cdef class Particle:
	"""Simple Particle extension type.""" 
	cdef public double mass
	cdef readonly double position
	cdef double velocity
# ...
```

#### 初始化
The fact that we have a C struct behind every extension type instance has other implications, particularly for object creation and initialization. When Python calls `__init__`, the self argument is required to be a valid instance of that extension type. When `__init__` is called, it typically initializes the attributes on the self argument. At the C level, before `__init__` is called, the instance’s struct must be allocated, and all struct fields must be in a valid state, ready to accept initial values.

```python
cdef class Matrix: 
	cdef:
		unsigned int nrows, ncols
		double *_matrix
	def __cinit__(self, nr, nc):
		self.nrows = nr
		self.ncols = nc
		self._matrix = <double*>malloc(nr * nc * sizeof(double)) 
		if self._matrix == NULL:
			raise MemoryError()
```


### iterator支持

## cython代码组织

cython提供三种文件类型来组织代码。

pyx --> implementation files

pxd --> definition files

pxi --> include files

除此之外，还提供`cimport`在编译时用于导入C组件(在定义文件pxd中)

### pyx与pxd文件

If we have a small Cython project and no other code needs to access C-level constructs in it, then a single implementation file is sufficient. 

But as soon as we want to share its C-level constructs, we need to create a definition file.

Because definition files are meant for compile-time access, note that we put only C-level declarations in it.

我们仅将C-level声明放入pxd文件。

1. ctype declaration
2. declarations for external c/c++ libraries (cdef extern)
...

### cimport

### cdef extern

定义文件可以包含`cdef extern`模块。

```python
cdef extern from "mt19937ar.h":
	# initializes mt[N] with a seed 
	void init_genrand(unsigned long s)
    # generates a random number on [0,0xffffffff]-interval
    unsigned long genrand_int32()

### 预定义的pxd文件

常用的

libc from stdlib, stdio, math, string, stdint等

libcpp for stl (string, vector, list, map, pair, set)

```python
from libc cimport math 
math.sin(3.14)

from libcpp.vector cimport vector
cdef vector[int] *vi = new vector[int](10)
```

### include文件与include语句

Include files and the include statement provide one way to ac‐ complish our nice platform-independent design goals.

主要目的是解决平台异构问题。

e place three different implementations of the extension type in three .pxi files: linux.pxi, darwin.pxi, and windows.pxi.

那么在pyx文件中，我们可以采用如下使用方式

```python
IF UNAME_SYSNAME == "Linux": 
	include "linux.pxi"
ELIF UNAME_SYSNAME == "Darwin": 
	include "darwin.pxi"
ELIF UNAME_SYSNAME == "Windows": 
	include "windows.pxi"
```

### 总体结构

```python
from distutils.core import setup 
from Cython.Build import cythonize
setup(name="simulator",
        packages=["simulator", "simulator.core",
                  "simulator.utils", "simulator.plugins"],
        ext_modules=cythonize("**/*.pyx"),
)
```

## 使用cython封装c库
To wrap a C library with Cython, we must first declare in Cython the interface of the C components we wish to use.

因此，cython提供了extern语法。

```python
cdef extern from "header_name":
	indented declarations from header file
```

### Cython Does Not Automate Wrapping
As mentioned, the only C code that is generated for the entire extern block is a single `#include "header.h"` line. 

We still have to write `def` and `cpdef` (and possibly cdef) functions that call the C functions declared in the extern block.

If we do not, then the external C functions declared in the extern block cannot be accessed from Python code. Cython does not parse C files and automate wrapping C libraries.

原始头文件

```c
#define M_PI 3.1415926
#define MAX(a, b) ((a) >= (b) ? (a) : (b))
double hypot(double, double); typedef int integral;
typedef double real;
void func(integral, integral, real);
real *func_arrays(integral[], integral[][10], real **);
```

转换到cython

```python
cdef extern from "header.h": double M_PI
        float MAX(float a, float b)
        double hypot(double x, double y)
		ctypedef int integral ctypedef double real
        void func(integral a, integral b, real c)
        real *func_arrays(integral[] i, integral[][10] j, real **k)
```

### 封装C函数
```python
from distutils.core import setup, Extension 
from Cython.Build import cythonize
    ext = Extension("mt_random",
                    sources=["mt_random.pyx", "mt19937ar.c"])
    setup(
        name="mersenne_random",
        ext_modules = cythonize([ext])
)
```

## 封装C++
```python
namespace mtrandom {
const static unsigned int N = 624;
class MT_RNG { 
	public:
		MT_RNG();
		MT_RNG(unsigned long s);
		MT_RNG(unsigned long init_key[], int key_length);
        // initializes RNG state, called by constructors
		void init_genrand(unsigned long s);
		// generates a random number on [0,0xffffffff]-interval
		unsigned long genrand_int32();
		// generates a random number on [0,1]-real-interval
		double genrand_real1();
	private:
		unsigned long mt[N]; int mti;
}; // class MT_RNG
} // namespace mtrandom
```

改到cython之后

```python
cdef extern from "mt19937.h" namespace "mtrandom": 
	unsigned int N
	cdef cppclass MT_RNG: 
		# ...
```
















