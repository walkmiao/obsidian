``` ActivityHistory
/
```


- 今天是`=dateformat(date(today),"DD")`，`=date(today).year` 年已经过去了 `=(date(today)-date(date(today).year + "-01-01")).days` 天

- 今年你总共在`$=dv.pages('"日记/2024"').file.length`天中留下了你的足迹，继续加油！


今天是 `=dateformat(date(today),"DD")`，那年今日，你曾记录过：
```dataview
table WITHOUT ID file.link AS "日记", dateformat(file.ctime,"DD") AS "创建时间", dateformat(file.mtime,"DD HH:mm:ss") AS "最近修改时间" from "/" where dateformat(file.ctime,"MM-dd") = dateformat(date(today),"MM-dd") limit 100
```