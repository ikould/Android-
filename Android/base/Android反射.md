# 获取Class的方法：

1. getClass() 
> Test test = new Test();  
Class testClass = test.getClass();
    
2. getSuperClass()
> Class testSuper = testClass.getSuperClass();

3. forName()
> Class strClass = Class.forName("java.lang.String");

4. class
> Class strClass = String.class;

5. TYPE
> Class strClass = Boolean.TYPE;

# 获取构造方法

1. Constructor getConstructor(Class[] params)     根据构造函数的参数，返回一个具体的具有public属性的构造函数  

2. Constructor getConstructors()     返回所有具有public属性的构造函数数组  

3. Constructor getDeclaredConstructor(Class[] params)     根据构造函数的参数，返回一个具体的构造函数（不分public和非    public属性） 

4. Constructor getDeclaredConstructors()    返回该类中所有的构造函数数组（不分public和非public属性）  

# 获取成员方法

1. Method getMethod(String name, Class[] params)    根据方法名和参数，返回一个具体的具有public属性的方法  

2. Method[] getMethods()    返回所有具有public属性的方法数组

3. Method getDeclaredMethod(String name, Class[] params)    根据方法名和参数，返回一个具体的方法（不分public和非public属性）

4. Method[] getDeclaredMethods()    返回该类中的所有的方法数组（不分public和非public属性）

# 获取成员属性

1. Field getField(String name)    根据变量名，返回一个具体的具有public属性的成员变量

2. Field[] getFields()    返回具有public属性的成员变量的数组

3. Field getDeclaredField(String name)    根据变量名，返回一个成员变量（不分public和非public属性）

4. Field[] getDelcaredField()    返回所有成员变量组成的数组（不分public和非public属性）

# 获取修饰类型（Class、 Method、 Constructor、 Field）

int getModifiers()；不同int值对应的不同修饰类型
例如：

```
Class class = something.getClass();
int type1 = class.getModifiers();
Method method = class.getMethod("",class );
int type2 = method.getModifiers();
```

# 反射代码示例

```
[MainActivity]
private static int add(int s) {
    return 555;
}

[反射获取代码]
try {
    Class<?> classs = Class.forName("com.xxx.MainActivity");
    //多个参数使用 new Class[]{int.class,String.class ...}
    Method method = classs.getDeclaredMethod("add", int.class);
    method.setAccessible(true);
    int s = (int) method.invoke(null, 3);//方法如果是静态就用null，不然就是new 类名();后面3...为参数
    Log.d("OtherActivity", "initView: method = " + s);
} catch (Exception e) {
    e.printStackTrace();
}
```
