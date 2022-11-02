## Mongo 命令

### 删除数组元素

```
	db.ep_core_release.update({'pjId':9580},{$pull:{ "userAndRoles": {"userName":"sh12"} }})	
```

### 修改数据元素

```
db.ep_core_release.updateOne({'pjId':9697},{$set:{"uatInfo.scenarioList.$[].scenario":"进入 https://sellerportal.newegg.com/v3/module/account/datafeed-settings Data Feed Setting页面，查看 FTP设置 功能是否正常","uatInfo.scenarioList.$[].criteria":"Data Feed Setting页面正常显示 FTP设置"}})
```

#### 根据条件更新指定的数组元素

```plsql
db.ep_core_release.updateOne({'pjId':8771},{$set:{"uatInfo.scenarioList.$[x].scenario":"mytest","uatInfo.scenarioList.$[x].criteria":"my"}},{arrayFilters:[{"x.scenario":{$eq:"test"}}]})
```

