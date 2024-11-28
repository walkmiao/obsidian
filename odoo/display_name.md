所有模型都具有 display_name 字段，该字段以人类可读格式显示记录表示，自版本 8.0 起已自动添加到所有模型中。默认的 _compute_display_name（） 方法使用 _rec_name 属性确定哪个字段包含显示名称的数据。要自定义显示名称，可以覆盖 _compute_display_name（） 方法并提供您的逻辑。该方法应返回一个元组列表，每个元组都包含记录 ID 和 Unicode 字符串表示。For example, to have the hostel name and hostel code in the representation, such as Youth Hostel (YHG015), we can define the following:例如，要在表示中具有旅舍名称和旅舍代码，例如青年旅舍（YHG015），我们可以定义以下内容：Take a look at the following example. This will add a release date to the record’s name:请看下面的例子。这将为记录的名称添加发布日期：[插图]Afte
``` python
@api.depends('hostel_code')     
def _compute_display_name(self):         
	for record in self:             
		name = record.name             
		if record.hostel_code:                 
			name = f'{name} ({record.hostel_code})'          
		record.display_name = name
```

运行后显示如下：
![[Pasted image 20240703161343.png]]
