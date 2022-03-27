## 单独使用@Validated校验失效
把 `@RequestBody` 注解删去之后，虽然还是可以自动填充实体，但是数据校验失效了。**实际上没有失效**，我编写了如下代码：

```java
@ControllerAdvice
public class MethodArgumentNotValidExceptionHandler {
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseBody
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public Result handleMethodArgumentNotValidException(Exception ex) throws Exception{
        String message = ex.getMessage();
        if (ex instanceof BindException) {
            BindException bindEx = (BindException) ex;
            message = bindEx.getAllErrors()
                    .stream()
                    .map(exObj -> exObj.getDefaultMessage())
                    .collect(Collectors.joining("；", "：", "。"));
        }
        return Result.error().tip("所填数据具有如下问题" + message);
    }
}
```

可以发现我绑定的是 `MethodArgumentNotValidException` 异常类。

当单独使用 `@Validated` 时，Spring 会抛出它的父类 `BindException`。所以校验失败信息没有以 json 格式返回前台。只要改改这里的代码，就可以解决了。

## 访问jsp出现404
如果配置没问题，请检查你 `@RequestMapping` 中所写的 url 是不是以 `.jsp` 结尾。似乎不支持这个后缀……