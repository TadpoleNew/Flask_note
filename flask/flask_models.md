[模型配置中文文档](http://www.pythondoc.com/flask-sqlalchemy/config.html)

[扩展库网址](http://flask.pocoo.org/extensions)

#### 查询

1.查询所有用户数据

    User.query.all()

2.查询有多少个用户

    User.query.count()

3.查询第1个用户

    User.query.first()

4.查询id为4的用户 3种方法
<pre>
1) User.query.get(4)
2) User.query.filter_by(id=4).first()
3) User.query.filter(User.id == 4).first()
<span style="color:red">
get(): 只能用来获取id; 如果get()获取不到值，返回None，不会报错;
filter_by(): 用于查询简单的列名，不支持比较运算符
</span>
</pre>

5.查询名字结尾/包含/开始字符为"g"的所有数据

    User.query.filter(User.name.endswith('g')).all()
    
    User.query.filter(User.name.contains('g')).all()

    list = User.query.filter(User.name.startswith('g')).all()

6.查询数据，获取第五个到第十个的数据

    students = Student.query.all()[4:10]
    students = Student.query.offset(4).limit(6)

    每页2条数据
    page = 1 
    students = Student.query.offset((page-1) * 2).limit(2)   # 分页

7.每页3个，查询第2页的数据
 
    User.query.paginate(2,3,False).items  查询数据
    User.query.paginate(2,3,False).page  ---当前页
    User.query.paginate(2,3,False).pages ---总页数

8.其它

<table>
	<tr>
		<td align="center">!= </td>
		<td>不等于</td>
              <td></td>
	</tr>
	<tr>
		<td align="center">in_([]) </td>
		<td>存在于列表中的值</td>
		<td>Student.query.filter(Student.id.in_([10, 11, 12]))<br>查询id为10、11、12的学生
             </td>
	</tr>
	<tr>
		<td align="center">__gt__() </td>
		<td>大于</td>
		<td></td>
	</tr>
	<tr>
		<td align="center">__lt__() </td>
		<td>小于</td>
		<td></td>
	</tr>
	<tr>
		<td align="center">__ge__() </td>
		<td>大于等于</td>
		<td></td>
	</tr>
	<tr>
		<td align="center">__le__() </td>
		<td>小于等于</td>
		<td></td>
	</tr>
	<tr>
		<td>like(_/%)</td>
		<td>模糊查询</td>
		<td>_表示代替任意一个字符；%表示任意个字符</td>
	</tr>
	<tr>
		<td align="center">and_ </td>
		<td>与</td>
		<td></td>
	</tr>
	<tr>
		<td align="center">or_ </td>
		<td>或</td>
		<td></td>
	</tr>
	<tr>
		<td align="center">not_</td>
		<td>非</td>
		<td></td>
	</tr>
	<tr>
		<td align="center">order_by()</td>
		<td>降序<br>升序</td>
		<td>
		order_by('-Field' / desc(Field) / 'Field desc')<br>
		order_by('Field' / asc(Field) / 'Field asc')
             </td>
	</tr>
</table>