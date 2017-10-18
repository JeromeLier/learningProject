# Vue 配合element 组件整理



作为一个新手，在两眼一抹黑的情况下，去添加一个vue工程项目， 过程遇到很多的问题，希望汇总汇总，能帮到一些迷茫中的小伙伴

## Table组件

### 下拉菜单选择（值没有回填）不生效的问题

```xml
<el-form-item label="备选车次" prop="supportOptionalCoach">
	<el-radio-group v-model="editForm.supportOptionalCoach">
		<el-radio class="radio" :label="1">开</el-radio>
			<el-radio class="radio" :label="0">关</el-radio>
		</el-radio-group>
	</el-form-item>
```

​	问题描述： 我添加了一个下拉菜单，  比如我选择 ‘开’， 然后值没有会填到选择框中。

​	问题的原因是 这个字段 supportOptionalCoach 没有定义。 需要在默认定义字段的地方将其定义即可。



## 资料怎么找



* github

  *  github有很多开源的项目， 可以拿来参考借鉴
  * vue-admin、 vue-demo 等等

* 官网

  ​    vue 官网  ： https://vuefe.cn/v2/guide/

  ​    vue-element： http://element.eleme.io/#/zh-CN/component/installation

     