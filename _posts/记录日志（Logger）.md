# 记录日志（Logger）

```
Logger logger = Logger.getAnonymousLogger();
logger.setLevel(Level.FINE);

logger.warning("info: File->Open Menu item selected...");
logger.info("info: File->Open Menu item selected...");
logger.fine("fine: File->Open Menu item selected...");
```

```
Aug 09, 2017 3:32:26 PM logger.LoggerDemo main
INFO: info: File->Open Menu item selected...
```

> 对于一个要被记录的日志记录，它的日志记录级别必须高于日志记录器和处理器的阈值，日志管理器的配置文件设置的默认控制台处理器的日记记录级别为`INFO`



记录`FINE`级别的日志：

- 修改配置文件
- 安装自定义处理器`ConsoleHandler`

```
Logger logger = Logger.getAnonymousLogger();
logger.setLevel(Level.FINE);

ConsoleHandler handler = new ConsoleHandler();
handler.setLevel(Level.FINER);		

logger.addHandler(handler);
logger.info("info: File->Open Menu item selected...");
logger.fine("fine: File->Open Menu item selected...");	
```

```
Aug 09, 2017 3:30:02 PM logger.LoggerDemo main
INFO: info: File->Open Menu item selected...
Aug 09, 2017 3:30:02 PM logger.LoggerDemo main
INFO: info: File->Open Menu item selected...
Aug 09, 2017 3:30:02 PM logger.LoggerDemo main
FINE: fine: File->Open Menu item selected...
```

默认情况，日志记录器将记录发送到自己的处理器和父处理器。自定义日志记录器是原始日志记录器的子类，而原始日志记录器会将所有等于或高于INFO级别的记录发送到控制台。

`setUseParentHandlers(false)`

```
Logger logger = Logger.getAnonymousLogger();
logger.setLevel(Level.FINE);
logger.setUseParentHandlers(false);

ConsoleHandler handler = new ConsoleHandler();
handler.setLevel(Level.FINER);		

logger.addHandler(handler);
logger.warning("info: File->Open Menu item selected...");
logger.info("info: File->Open Menu item selected...");
logger.fine("fine: File->Open Menu item selected...");	
```

```
Aug 09, 2017 3:27:03 PM logger.LoggerDemo main
INFO: info: File->Open Menu item selected...
Aug 09, 2017 3:27:03 PM logger.LoggerDemo main
FINE: fine: File->Open Menu item selected...
```

- FileHandler
- SocketHandler

```
FileHandler fileHandler = new FileHandler("/test.log", true);
logger.addHandler(fileHandler);
```

