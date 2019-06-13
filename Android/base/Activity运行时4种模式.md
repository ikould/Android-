# Statard模式
> 这是默认的模式。   
> 这种模式下，当Intent发送的时候，Activity总是被创建一个新的出来单独工作。想象一下，如果有发送10个撰写邮件的Intent，那么将有10个不同的Activity启动。

# SingleTop模式
> 接下来就是singleTop模式。它的表现几乎和standard模式一模一样，一个singleTop Activity 的实例可以无限多，唯一的区别是如果在栈顶已经有一个相同类型的Activity实例，Intent不会再创建一个Activity，而是通过onNewIntent()被发送到现有的Activity。

# SingleTask
> 这种模式和standard以及singleTop有很大不同。singleTask模式的Activity只允许在系统中有一个实例。如果系统中已经有了一个实例，持有这个实例的任务将移动到顶部，同时intent将被通过onNewIntent()发送。如果没有，则会创建一个新的Activity并置放在合适的任务中。

# singleInstance
> 这个模式非常接近于singleTask，系统中只允许一个Activity的实例存在。区别在于持有这个Activity的任务中只能有一个Activity：即这个单例本身。
