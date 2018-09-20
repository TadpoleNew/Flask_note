##Django和Flask的区别
### 1.模型Models

#####1.1模型中不定义数据库的表名时
在Django中默认表名为：‘应用app名_模型名小写’；

	指定表名语句：
	class Meta:
		db_table = 'student'
在Flask中默认表名为：模型名小写，所以可缺省指定表名。

	指定表名语句：
	__tablename__ = 'student'

##### 1.2 定义字段

在Django中定义字段的格式：

    models.字段类型(字段其它属性=值, ...)

例如：
<pre>
class UserModel(<b style="color:red;">models.Model</b>):
    username = models.CharField(max_length=20, unique=True)
    password = models.CharField(max_length=20)
    ...
</pre>

在Flask中定义字段的格式：

    db.Column(db.字段类型, 字段其它属性=值, ...)

例如：
<pre>
class Student(<b style="color:red;">db.Model</b>):
    
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    s_name = db.Column(db.String(10), unique=True)
    s_age = db.Column(db.Integer, default=10)
    ...
</pre>


#####1.3 自增id字段

Django中默认会创建自增的主键id

Flask中需要自己写自增的主键id

    class Student(db.Model):
        id = db.Column(db.Integer, primary_key=True, autoincrement=True)
        ...

##### 1.4 保存数据

Django中instance自带了save()方法

    instance.save()

Flask中需要使用事务

    db.session.commit()

Flask中可以在相应的模型中自定义一个save()方法

    class Student(db.Model):
	    ...
        def save(self):
            db.session.add(self)
            db.session.commit()

    保存数据时直接调用save()方法：
    views.py文件中：
    @blue.route('create_stu/')
    def create_stu():
        stu = Student()
        stu.s_name = '小兰'
        stu.s_age = 18

        stu.save()
        return '创建学生信息成功'



##### 1.5 查询所有数据的语句及结果 -- all()

在Django中：
<pre>
students = Student.<b style="color:red;">objects</b>.all()

students的结果为<b> QuerySet</b>
</pre>

在Flask中：
<pre>
students = Student.<b style="color:red;">query</b>.all()

students的结果为<b> List</b>
</pre>



##### 1.6 查询满足条件的数据语句及类型 -- filter()

在Django中(ORM):
<pre>
students = Student.objects.<b style="color:red;">filter(id=1)</b>

查询的结果的类型为 <b>QuerySet</b>


<b style="color:#c96be6;"> 获取一个实例的方法:</b>
<pre>
student = Student.objects.<b style="color:red;">filter(id=1)</b>.<b style="color:#1663da;">first()</b>

student = Student.objects.<b style="color:red;">filter(id=1)</b>.<b style="color:#1663da;">last()</b>


查询的结果的类型为 <b>object</b>
</pre></pre>

在Flask中:
<pre>
<b style="font-size:18px;">ORM操作：</b>

students = Student.query.<b style="color:red;">filter_by(id=1)</b>
或者：
students = Student.query.<b style="color:red;">filter(Student.id == 1)</b>

查询结果的类型为 <b>BaseQuery objects</b>
<hr>
students = Student.query.<b style="color:red;">filter_by(id=1)</b><b style="color:#1663da;">.all()</b>
或者：
students = Student.query.<b style="color:red;">filter(Student.id == 1)</b><b style="color:#1663da;">.all()</b>

查询结果的类型为 <b>List</b>


<b style="color:#c96be6;">  获取一个实例的方法:</b>
<pre>
对该列表进行遍历得到一个实例或者通过[:]来获取某个实例

students = Student.query.filter_by(id=1).all()<b style="color:#red;">[0]</b>
students = Student.query.filter(Student.id == 1).all()<b style="color:#red;">[0]</b>

students = Student.query.filter_by(id=1)<b style="color:#1663da;">.first()</b>
students = Student.query.filter(Student.id == 1)<b style="color:#1663da;">.first()</b>

注意：Flask中没有last()方法
</pre>
<b style="font-size:18px;">原生SQL语句：</b>
sql = 'select * from student where id=1'
students = db.session.execute(sql)

students查询结果是<b>ResultProxy object</b>

stu_list = students.<b style="color:red;">fetchall()</b>
stu_list查询结果是<b>List</b>

注意：执行fetchall()、fetchone()只能执行一次

<b style="color:#c96be6;">  获取一个实例的方法:</b>
<pre>
stu_list = students.<b style="color:red;">fetchall()</b>
stu = stu_list[0]
或
stu = students.fetchall()<b style="color:#1663da;">[0]</b>
</pre></pre>

##### 1.6 创建

Django中
<pre>
<b>方法一：</b> 赋值
stu = Student()
stu.stu_name = '小兰'
stu.save()

<b>方法二：</b> 初始化，__init__方法
stu = Student('小芳2')
stu.save()

<b>方法三：</b><span style="color:red;">create()</span>
Student.objects.create(stu_name='妲己')
</pre>
Flask中
<pre>
<b>方法一：</b> 赋值,<span style="color:red;">add()</span>
stu = Student()
stu.stu_name = '小兰'
db.session.add(stu)
db.session.commit()

<b>方法二：</b> 初始化，__init__方法 【批量创建，<span style="color:red;">add_all(列表)</span>】
stus_list = [Student('丁小', 12), Student('山竹', 14)]
db.session.add_all(stus_list)
db.session.commit()               <span style="color:red;"> *add_all(参数)  参数必须是一个列表</span>

</pre>

##### 1.7 删除

Django中
   
     instance.delete()

     instance表示一个实例

Flask中

    db.session.delete(instance)
    db.session.commit()

##### 1.8 更新

Django中
<pre>
方法一：
stu = Student.objects.filter(id=2).first()
stu.stu_name = '小山竹'
stu.save()

方法二：
Student.objects.filter(id=3).update(stu_name='丁小')  <span style="color:red">*赋值形式传参, 字段名=值</span>
</pre>
Flask中
<pre>
方法一：
stu = Student.query.filter_by(id=id).first()
stu.s_name = username
db.session.add(stu)
db.session.commit()   <span style="color:red">*事务提交</span>

方法二：     <span style="color:red">*传参方式不同, {key:value}形式传参</span>
Student.query.filter_by(id=id).update({'s_name': username, 's_age': age})
db.session.commit()
</pre>

##### 1.9 get()：用于查询结果只返回一条记录
Django中
<pre><span style="color:red"># get(): 若返回结果超过一条，会报错； 若获取不到值，会报错</span>

# 获取id=1的学生信息
stu = Student.objects.get<b style="color:#1663da;">(id=1)</b>
# 获取年龄是15岁的学生信息
stu = Student.objects.get(s_age=15)  # 若返回结果是多条记录，会报错，提示返回的数量超过一条。改用stu = Student.objects.filter(s_age=15)
</pre>

Flask中
<pre><span style="color:red"># get(): 只能用来获取id; 如果get()获取不到值，返回None，不会报错;</span>

# 获取id=2的学生信息
students = Student.query.get<b style="color:#1663da;">(2)</b>
</pre>

#### 2.其它

<table>
	<tr>
		<th>运算符</th>
		<th>Django</th>
		<th>Flask</th>
	</tr>
	<tr>
		<td>不等于</td>
		<td>exclude()</td>
		<td>!=</td>
	</tr>
	<tr>
		<td>存在于列表中指定的值</td>
		<td>filter(id__in=[1, 2])</td>
		<td>filter(Student.id.in_([10, 11, 12])</td>
	</tr>
	<tr>
		<td>包含</td>
		<td>stu_name__contains='Jack'<br>stu_name__<b style="color:red;">i</b>contains='Jack'</td>
		<td>stu_name.contains('小')</td>
	</tr>
	<tr>
		<td>以...开始</td>
		<td>stu_name__startswith='J'<br>stu_name__<b style="color:red;">i</b>startswith='J'</td>
		<td>stu_name.startswith('J')</td>
	</tr>
	<tr>
		<td>以...结束</td>
		<td>stu_name__endswith='小'</td>
		<td>stu_name.endswith('小')</td>
	</tr>
	<tr>
		<td>大于</td>
		<td>stu_age__gt=16</td>
		<td>stu_age__gt__(16)/ stu_age >16</td>
	</tr>
	<tr>
		<td>小于</td>
		<td>stu_age__lt=16</td>
		<td>stu_age__lt__(16)/ stu_age < 16</td>
	</tr>
	<tr>
		<td>大于等于</td>
		<td>stu_age__gte=16</td>
		<td>stu_age__ge__(16)</td>
	</tr>
	<tr>
		<td>小于等于</td>
		<td>stu_age__lte=16</td>
		<td>stu_age__le__(16)</td>
	</tr>
	<tr>
		<td>与或非: Q()</td>
		<td>~Q(条件)、 Q(条件) & Q(条件)、Q(条件) | Q(条件)</td>
		<td>not_(条件) 、and_(条件1，条件2)、 or_(条件1，条件2)</td>
	</tr>
	<tr>
		<td>算术运算F()</td>
		<td>maths__gt=F('Chinese') + 10</td>
		<td>无</td>
	</tr>
	<tr>
		<td>order_by()</td>
		<td>升序：order_by('id')<br> 降序：order_by('-id')</td>
		<td>升序：order_by('-Field' / desc(Field) / 'Field desc')<br>
		降序：order_by('Field' / asc(Field) / 'Field asc')</td>
	</tr>
</table>

### 3.跳转

<table>
	<tr>
		<th>功能</th>
		<th>Django</th>
		<th>Flask</th>
	</tr>
	<tr>
		<td>当前页面渲染</td>
		<td>render(request, 'web/index.html', {key: value, key: value,...})</td>
		<td>render\_template('stus.html',参数1=值1，参数2=值2,...)</td>
	</tr>
	<tr>
		<td>上一页的页数</td>
		<td>HttpResponse('hello world')</td>
		<td>redirect('/url_prefix/函数名/')</td>
	</tr>
	<tr>
		<td>是否存在下一页</td>
		<td>HttpResponseRedirect(reverse( 'namespace.name'))</td>
		<td>redirect(url\_for(url\_prefix.函数名))</td>
	</tr>
<table>

### 4.分页

**差些说明！！**
<table>
	<tr>
		<th>功能</th>
		<th>Django</th>
		<th>Flask</th>
	</tr>
	<tr>
		<td>是否存在上一页</td>
		<td>page.has_previous</td>
		<td>paginate.has_prev</td>
	</tr>
	<tr>
		<td>上一页的页数</td>
		<td>page.previous_page_number</td>
		<td>paginate.prev_num</td>
	</tr>
	<tr>
		<td>是否存在下一页</td>
		<td>page.has_next</td>
		<td>paginate.has_next</td>
	</tr>
	<tr>
		<td>下一页的页数</td>
		<td>page.next_page_number</td>
		<td>paginate.next_num</td>
	</tr>
	<tr>
		<td>当前页的所有数据对象</td>
		<td>object_list</td>
		<td>paginate.items</td>
	</tr>
	<tr>
		<td>页码范围</td>
		<td>page.paginator.page_range()</td>
		<td>paginate.iter_pages()</td>
	</tr>
	<tr>
		<td>查询结果的记录总数</td>
		<td>page.paginator.count</td>
		<td> paginate.total </td>
	</tr>
	<tr>
		<td>查询得到的总页数</td>
		<td>page.paginator.num_pages</td>
		<td>paginate.pages</td>
	</tr>
	<tr>
		<td>当前页</td>
		<td>page.number</td>
		<td> paginate.page </td>
	</tr>
	<tr>
		<td>指定每页显示的记录数</td>
		<td>page.paginator.per_page</td>
		<td>paginate.per_page </td>
	</tr>
       <tr>
		<td>返回当前页面对象的个数</td>
		<td>len()</td>
		<td> </td>
	</tr>

</table>

### 5.模型间的关系

<pre>
class Student(db.Model):
    s_g = db.Column(db.Integer, db.ForeignKey('grade.id'), nullable=True)

class Grade(db.Model):
student = db.relationship('Student', backref='stu', lazy=True)

s_c = db.Table('s_c',
               db.Column('s_id', db.Integer, db.ForeignKey('student.id')),
               db.Column('c_id', db.Integer, db.ForeignKey('course.id'))
               )

class Course(db.Model):
    student = db.relationship('Student', secondary=s_c, backref='cou')


</pre>