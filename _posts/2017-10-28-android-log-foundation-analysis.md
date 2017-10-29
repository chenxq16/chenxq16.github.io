---
layout: post
title:  android开源日志框架解析
subtitle:   ""
author: "MakerChen"
# header-img: ""
date:   2017-10-28 23:26:00
tags:
    - android
---



## XLog
基于国人的开源框架XLog：com.elvishew.xlog，版本1.4.0。

#### 入口类XLog

![XLog](/media/android/XLog.png "XLog")

XLog作为框架程序的入口类，主要关联了3个主要的类:LogConfiguration、Logger、Printer，后面我们一一解释。

![XLogMethod](/media/android/XLog-method.png "XLogMethod")

从图上可以看出，XLog作为入口类，提供了较为便捷的日志输出API及丰富的自定义入口。这也是一贯常用的设计。将复杂的实现包装起来，对外尽量保持简单、可扩展。

#### LogConfiguration

![LogConfiguration](/media/android/LogConfiguration.png "LogConfiguration")

从名字可以看出其功能就是对输出log日志输出进行相关配置。其关联了Formatter的各种子类用于格式化输出各种日志,包括StackTraceFormatter、ObjectFormater、ThreadFormatter、XmlFormatter、ThrowableFormatter、BorderFormater、JsonFormatter;还有Interceptor,用于日志打印前的拦截操作。

在Formatter的子类中，StackTraceFormatter、ThreadFormatter、BorderFormater等，每段日志输出都会包含这些信息，方便排查、展示问题，所以在日志输出的时候都会涉及这些Formatter。如果不需要这些额外信息，可通过开关进行打开、关闭。但其中的ObjectFormater、XmlFormatter、ThrowableFormatter、JsonFormatter等，则是对不同的日志格式进行分门别类格式化输出。

LogConfiguration该类整体采用的是Builder模式，用于灵活构建各种LogConfiguration,也提供了一些快捷构建方法，例如LogConfiguration.Builder#st()、LogConfiguration.Builder#t()等方法快速创建打开、关闭stacktrace日志的配置。

其中其关联的Formatter的子类、Interceptor接口，都提供了默认实现，也开放了其扩展实现自定义。

#### Printer

![Printer](/media/android/Printer.png "Printer")

从名字看是负责日志输出的（好的类名有多重要）。

其子类有ConsolePrinter、ContainerPrinter、RemotePrinter、AndroidPrinter、FilePrinter、PrinterSet。不作一一介绍，看其类名也能猜出个12。例如ConsolePrinter用于在控制台输出；AndroidPrinter用于在android的Logcat中输出等；特别介绍一下PrinterSet，从类图中可以看出其不仅实现了Printer还依赖着Printer。查看源码我们可以发现，其实他维护了一个Printer集合，作用可想而知是为了同时能够输出在不同的环境中。

ConsolePrinter、FilePrinter都同时关联了Flatter接口。Flatter译为平面锤，从源代码中可以看出，其对log中的logLevel、tag、message做了摊平操作，方便在不同环境下，输出时候的显示问题。


    public CharSequence flatten(int logLevel, String tag, String message) {  
    		return Long.toString(System.currentTimeMillis())
    				+ '|' + LogLevel.getShortLevelName(logLevel)
   					+ '|' + tag
    				+ '|' + message;
    }

其中FilePrinter，还关联了BackupStrategy、FileNameGenerator等接口用于日志备份策略、文件名生成的逻辑，当然这些也是可以进行扩展自定义的。

#### Logger

![Logger](/media/android/Logger.png "Logger")

从UML类图中可以看出,Logger与XLog的关系结构雷同,事实也是如此。XLog其实是Logger的一个代理类，从截取的代码中也可以看出。

	public static void d(Object object) {
    	assertInitialization();
    	sLogger.d(object);
  	}

既然差不多就不对Logger做过多解释了。那为何还要XLog或者Logger呢？看段下面的代码便知。

	 public static Logger.Builder nt() {
    	return new Logger.Builder().nt();
  	 }

XLog本身全局持有了一个Logger对象的应用，而在部分方法中（如上）创建了一个新的Logger对象。这样的设计，可以满足在一个APP中通过全局的配置，到达统一的输出。而对于不同模块或者具有不同打印日志需求的可以区别输出。

#### Interceptor

Looger中关联了该类的集合（见上图）。从语义中可以看出,该类用于在日志输出前的拦截操作。并且一个loggger对象可以关联多个拦截器。其默认实现了，白名单、黑名单，拦截操作。当然你也可以自定义拦截规则。

#### Utils

除了上面介绍的、层次比较分明的功能模块。XLog还提供了许多不错的工具类。如下，可供参考。譬如StackTraceUtil如何追踪调用方法的堆栈信息。

![Utils](/media/android/Utils.png "Utils")


### 总结
XLog是一个非常不错的用于打印日志的开源库。可扩展性非常高，日志的输出也是非常美观、明确。值得大家使用、推荐。而且是国人写的，应当star、fork支持一下。

详细内容可直接访问原项目：
> https://github.com/elvishew/xLog

##Logger
基于开源框架Logger：com.orhanobut.logger，版本2.1.1。
![com.orhanobut.logger](/media/android/com.orhanobut.logger.png "com.orhanobut.logger")

Logger日志框架层次分明。

Logger作为入口类，关联了Printer这个接口，采用代理模式代理了Printer及其实现。为方便使用方，Logger依赖了LogAdapter（其实其依赖关系仍然在Printer中）。当然Printer你也可以自己实现。

Printer子类LoggerPrinter本身并未做过多的事，除了将json、xml格式化输出。其关联了LogAdapter集合，并在日志输出的时候托管给了具体的Adapter的实现。

	public synchronized void log(int priority, String tag, String message, Throwable throwable) {
    	if (throwable != null && message != null) {
      		message += " : " + Utils.getStackTraceString(throwable);
    	}
    	if (throwable != null && message == null) {
      		message = Utils.getStackTraceString(throwable);
    	}
    	if (Utils.isEmpty(message)) {
      		message = "Empty/NULL log message";
    	}

    	for (LogAdapter adapter : logAdapters) {
      		if (adapter.isLoggable(priority, tag)) {
        		adapter.log(priority, tag, message);
      		}
    	}
  	}

LogAdapter的两个子类AndroidLogAdapter、DiskLogAdapter，都关联了FormatStrategy。而LogAdapter的作用也仅是拦截是否需要记录日志。

	boolean isLoggable(int priority, String tag);

其log日志记录，也是托管给FormatStrategy去实现的。默认的AndroidLogAdapter关联的是PrettyFormatStrategy、DiskLogAdapter关联的是CsvFormatStrategy。当然这里的任何实现都是可以替代的，贴切符合开闭原则。

FormatStrategy从语义上看，其实就是将日志按照既定格式进行格式化、排版，使得在显示的时候更加的合理、美观。当然你可以扩展。

完成字符串的格式化后，Strategy关联了LogStrategy，最终完成了日志的输出显示。

####总结
Logger相对来说轻量级一点，但也就意味着已实现的功能不会特别多，需要自己扩展，当然扩展性是没有问题的。如果Logger能够满足要求，可推荐。

Logger中有个小问题值得指出，LogAdapter、FormatStrategy、LogStrategy，其在接口级别上就存在关联关系，但是作者并未将关联关系依赖在接口上，而是在其子类上建立的这种关系。虽然说灵活程度更高了，但是使用方也会在一定程度上破坏这种结构；或者遵循这种结构，写了些冗余代码。


## 大总结

XLog相比Logger更加的复杂，已实现的功能也比较多且贴近实际使用场景，输出也比较优美、扩展性也不错。但其为了XLog作为统一入口，在各层次都依赖着LogConfiguration、XXXFormatter、XXXInterceptor。这估计作者也是故意而为之的设计，将复杂的设计囊括在系统内部，对外尽量暴露最简单的API，保留其最直接的扩展性，坚持一个出口，这些都是不错的考量，但同时造成了结构上的复杂、难以理解、上手起来有一定困难。但管它呢不是，你只管用就行了。再者，经过我这么一剖析，结构也算是比较清楚的了。

Logger相对轻量级一些，但不能说其扩展性就不好。只不过一些实现，得由你自个来完成了。

但无论用哪个框架,建议都是在其上面再封一层。无论以后替换框架，提供一定程度的自定义实现，都是很有必要的。

有人说：一个小小的日志打印功能，本可以使用一个工具类就可以完成的，何必这么大动干戈。马克思告诉我们，具体问题得具体分析。在一些小应用上，你可以使用一个工具类就完成了，不反对。但目前国内的APP，随便就50M+，涉及开发团队5、6、7、8个，再简单的日志打印需求也会变得不这么简单了。对于客户端底层的架构，始终要保持高度的扩展性，才能够符合未来的、多团队的一些需求。所以有一定必要的大动干戈。建议小团队也这么做，毕竟轮子造好了，干嘛不用呢。
