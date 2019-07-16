# xlrd & xlwt

## 读取操作
1. 打开excel   
xlrd.open_workbook('url')
2. 找到sheet   
xlsx.sheet_by_name('表名')   
xlsx.sheet_by_index(表序号)
3. 根据行列读取内容   
table.cell_value(行，列)   
table.cell(行，列).value   
table.row(行)[列].value

eg:
```py
import xlrd
xlsx = xlrd.open_workbook('C:/Users/zhuqi/Desktop/软件183人员信息.xlsx')
table = xlsx.sheet_by_index(0)
print(table.cell_value(0,0))
```

## 写入操作
1. 新建工作簿   
xlwt.Workbook()
2. 新建表   
add_sheet('表名')
3. 写入值   
worksheet.write(行，列，'内容')
4. 保存并关闭   
new_workbook.save('保存位置')

eg:
```py
import xlwt
new_workbook = xlwt.Workbook()
worksheet = new_workbook.add_sheet('new_test')
worksheet.write(0,0,'test')
new_workbook.save('d:/test.xls')
```