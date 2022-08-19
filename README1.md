# 预习作业：图书信息管理系统

- 电气2003黄惠东 U202012268
- 语言：C#、SQL
- 软件：Visual Studio 2022、SQL Server

### 界面设计
---------
1. 登录界面如下：

![](https://cdn.jsdelivr.net/gh/hhdong1/My-Photos@main/img/Form1.png)

2. 用户登录后界面如下：

![](https://cdn.jsdelivr.net/gh/hhdong1/My-Photos@main/img/user1.png)

3. 图书查看与借阅界面如下：

![](https://cdn.jsdelivr.net/gh/hhdong1/My-Photos@main/img/user2.png)

4. 已借图书与归还界面如下：

![](https://cdn.jsdelivr.net/gh/hhdong1/My-Photos@main/img/user3.png)

5. 管理员登录后界面如下：

![](https://cdn.jsdelivr.net/gh/hhdong1/My-Photos@main/img/admin1.png)

6. 图书管理界面如下：

![](https://cdn.jsdelivr.net/gh/hhdong1/My-Photos@main/img/admin2.png)

7. 添加图书界面如下：

![](https://cdn.jsdelivr.net/gh/hhdong1/My-Photos@main/img/admin21.png)

8. 修改图书信息界面如下：

![](https://cdn.jsdelivr.net/gh/hhdong1/My-Photos@main/img/admin22.png)

### 功能介绍
--------------------------

1. **登录界面**中，用户或管理员可在选取相应身份选项后，通过输入正确的ID和密码实现登录，并跳转至**用户登录界面**或**管理员登录界面**。<label style="color:red"><b>未选取正确身份</b></label>时，页面会弹窗提醒“选取正确身份”；身份选取正确但<label style="color:red"><b>ID和密码不匹配</b></label>时，则会提醒“输入正确的ID和密码”。

2. **用户登录界面**首页上方显示用户功能菜单。用户点击“系统”选项时，系统将通过弹窗提供相应帮助；点击“图书查看和借阅”或“我当前的图书和归还”可跳转至**借书页面**和**归还页面**。

   - **借书页面**中，表格将显示当前图书库中图书的信息以及库存。用户可在表格中选取想借阅的书籍后，通过点击“借书”按钮实现借书，弹窗“借书成功”后，借书记录将保存至**归还页面**，同时对应图书库存量-1。

   - **归还页面**中，表格将显示用户的书籍借阅记录。点击归还按键，系统将弹窗显示“归还成功”，该行选中借阅记录删除，同时图书馆对应图书的库存+1。

3. **管理员登录界面**首页上方显示管理员功能菜单。管理员点击“系统”选项时，系统将通过弹窗提供相应信息；点击“图书管理”时，将进入**图书管理界面**；点击“帮助”时，系统将弹窗提供帮助。

   - **图书管理界面**中，管理员可通过点击对应按键，实现添加、修改、删除、查找图书功能。其中，点击“添加图书”和“修改图书”按键，会跳转至相应界面，管理员需在对应文本框内输入<label style="color:red"><b>符合数据类型</b></label>的图书信息才会正常录入库，否则显示“信息错误”。“刷新”按键可更新当前表中图书信息。
   - **图书管理界面**上方的两个文本框，可显示当前表中选中图书的书名和书号。
   - **删除图书**时，系统将弹窗提示是否要删除，选择“OK”则删除，“CANCEL”则取消删除。
   - 关闭**添加图书页面**和**修改图书页面**，**图书管理界面**中表格内容也会实现更新。

### 实现功能所需函数
--------------------------

1. **跳转页面**

- 举例：从“用户”跳至“查看图书和借阅”
```csharp
private void 图书查看和借阅ToolStripMenuItem_Click(object sender, EventArgs e)
｛
    user2 user = new user2();
    user.Showdialog();
｝

```


2. **判断性弹窗**

- 举例：“admin2”中“删除图书”后弹窗
   + 由于在表格中的图书数据类型一定符合删除要求，故可能出现的错误情况只可能是“未选中图书信息”，利用`try-catch`结构，若报错则弹出`catch`下属内容。
   + `Dao`类中：`Execute(sql)`可执行语句，并返回受该SQL语句影响的行数
   + `Show`中的`MessageBoxButtons.OKCancel`可表现出含“OK”和“Cancel”按键的弹窗。用`DialogResult`定义弹窗结果，判断是否要进行删除操作。
```csharp
private void button3_Click(object sender, EventArgs e)
{
    try
    {
        string id = dataGridView1.SelectedRows[0].Cells[0].Value.ToString();//获取书号
        label2.Text=id + dataGridView1.SelectedRows[0].Cells[0].Value.ToString();
        DialogResult dr = MessageBox.Show("确认删除吗？","信息提示",MessageBoxButtons.OKCancel,MessageBoxIcon.Question);
        if(dr == DialogResult.OK)
        {
            string sql = $"delete from List_3 where id ='{id}'";
            Dao dao = new Dao();
            if(dao.Execute(sql)>0)
            {
                MessageBox.Show("删除成功！");
                Table();
            }
            else
            {
                MessageBox.Show("删除失败！");
            }
            dao.DaoClose();
        }
    }
    catch
    {
        MessageBox.Show("请先在表格选中要删除的图书记录！", "信息提示", MessageBoxButtons.OK, MessageBoxIcon.Question);
    }
}
```

3. **实现图书信息的添加、修改、删除、借阅、还书**

- 添加
```csharp
string sql = $"insert into List_3 values('{textBox1.Text}','{textBox2.Text}','{textBox3.Text}','{textBox4.Text}','{textBox5.Text}')";
Dao dao = new Dao();
dao.Execute(sql);
//  数据库中insert语句
```

- 修改
```csharp
string sql = $"update List_3 set id='{textBox1.Text}',[name]='{textBox2.Text}',author='{textBox3.Text}',press='{textBox4.Text}',number='{textBox5.Text}' where id='{ID}'";
Dao dao = new Dao();
dao.Execute(sql);
```

- 删除
```csharp
string sql = $"delete from List_3 where id ='{id}'";
Dao dao = new Dao();
dao.Execute(sql);
//  update
```

- 借阅
```csharp
string id = dataGridView1.SelectedRows[0].Cells[0].Value.ToString();//获取书号
int number = int.Parse(dataGridView1.SelectedRows[0].Cells[4].Value.ToString());//库存
if(number>1)
{
string sql = $"insert into Lend_1([uid],bid,[datetime])values('{Data.UID}','{id}',getdate());"
 +
 $"update List_3 set number = number - 1 where id = '{id}'";
 Dao dao = new Dao();
 dao.Execute(sql);
}
```

- 还书
```csharp
string no = dataGridView1.SelectedRows[0].Cells[0].Value.ToString();
string id = dataGridView1.SelectedRows[0].Cells[1].Value.ToString();
string sql = $"delete from Lend_1 where [no]={no};update List_3 set number=number+1 where id ='{id}'";
Dao dao = new Dao();
dao.Execute(sql);
```

