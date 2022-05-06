## 接口方法的参数
**目前还不是百分百确定，还需要查找文献再次确认一下。**

使用 `#{0}`、`#{1}` 取值：`select(int id, String name)`。

使用 `#{id}`、`#{name}` 属性名取值：`select(int id, String name)`，`select(Map<String, Object>)`，`select(@Param("id") int id, @Param("name") String name)`。

使用 `#{obj.name}`、`#{obj.gender}` 对象.属性名取值：`select(User obj)`，`select(@Param("obj") User obj)`。






