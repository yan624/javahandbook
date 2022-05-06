dto 指的是 data transfer object，用于在各层之间传输数据。有种说法是 dto 不需要包含任何的方法，因为它只是数据的载体。

bo 指的是 business object，里面蕴含商业逻辑，可以包含方法。

如果你想在某层返回一个具有商业逻辑的对象，那么可以用 bo 替换 dto。例如你想要实现国际化，可以将 dto 转为 bo，并提供一个国际化的方法（2022年5月1日->2022.05.01；x 项->x items）。

po 指的是 persistent object。

如果你只想在数据表中保存一条数据，那么可以将 dto 转为 po。

vo 指的是 view object，用于在前端展示数据。

如果前端想要展示表格数据，那么可以将 dto 转为 vo，里面填写一些表格（table）需要的额外属性（有些属性可能是前端框架所需的）。

现在流行前后端分离，一般用不上 vo。

[pojo](https://en.wikipedia.org/wiki/Plain_old_Java_object) 指的是 plain old java object，由 Rebecca Parsons, Josh MacKenzie, Martin Fowler 于 2020.09 的会议上提出。他们指出将商业逻辑编码进常规的 java 对象而不是实体对象有很多好处。他们好奇人们为什么对此如此抗拒，之后得出结论：这种简单的对象没有一个花哨的名字。所以他们给了它一个，即 pojo，现在很流行。[出处](https://www.martinfowler.com/bliki/POJO.html)。












