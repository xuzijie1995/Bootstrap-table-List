# Bootstrap-table-List For Distribution template <br/>基于bootstrap-table的配送模板表格列

The bootstrap-table list which I have designed is Based on bootstrap-table. (Your Product Manager:'I want it.')<br/>
我设计了一个基于bootstrap-table的表格列，在特定场合会用到它,尤其是产品经理坚持的时候，这次是一个配送模板

# For your valuable time <br/> 这不是所有人的菜，把你的时间用到需要的地方去吧

This is the special advanced use of Bootstrap-table(It's more complicated than usual, at least I think :P). The Bootstrap-table basic source code changes are not involved. As long as you use Bootstrap, it is not difficult. And you do not need it for most of your time until your product manager thinks you need it.<br/> 
这只是我对于Bootstrap-table的特殊使用方法，不涉及源码的改动,只要你用过Bootstrap-table，这肯定不难，只是你未必会想到用它做个我这样的配送模板出来，一般table的配置使用已经满足绝大部分场景，所以这种操作并不是必须的，除非像我这样有需求的并且不得不这么干的时候。

# What does it look like <br/> 来看看它到底长啥样

Please click the picture. 
点击看大图会好点。
![图片名称](https://raw.githubusercontent.com/xuzijie1995/Bootstrap-table-List/master/images/bootstrap-table-list.png)

+ That's it. Just a little bit changes when you set your bootstrap-table.
+ You may ask 'Why don't you set the detailView true'. Of course, the detailView can handle it,but my PM wants that the details should be more intuitive. So I design this.
+ 就是这样，在你设置表格的时候来点小操作
+ 父子表是我首先想到的，只不过，这不能很直观的看到所有表格里面的数据，尽管有方法，但我没有那么做，我不太喜欢那样。

# The setting code <br/> 代码

## First <br/> 首先

All of the following must be relied upon. Also, you can search 'bootstrap-table' to find js & css files you need <br/>
确保你有以下引用,或者你可以搜索'bootstrap-table'文档来明确你所需要的的js与css文件以及如何去使用

```jsx
+ <script src="jquery.min.js"></script>
+ <script src="bootstrap.min.js"></script>
+ <script src="bootstrap-table.js"></script>
+ <-- put your locale files after bootstrap-table.js -->
+ <script src="bootstrap-table-zh-CN.js"></script>
+ <link rel="stylesheet" href="bootstrap.min.css">
+ <link rel="stylesheet" href="bootstrap-table.css">
```

## My setting code <br/> 设置代码


```jsx
<html>
  ...
  <div class="form-group traTablelist">
			
  </div> 
  ...
</html>
```

```jsx
js/jq in script
 
var $n=[];
var $id=[];
var $d={};

function addTra($data){
	var fragment = document.createDocumentFragment();
	for(var i=0;i<$data.length;i++){
		var $dd=[];
		$n.push($data[i]['shipping_name']);
		$id.push($data[i]['shipping_id']);
		//My processe data 我的加工数据 start==>
		var $m=$data[i]['methods'];
		for(var j=0;j<$m.length;j++){
			var temp={  
				shipping_area_name:$m[j]['shipping_area_name'],
				is_default:$m[j]['is_default'],
				firstNum:$m[j]['config']['firstNum'],
				firstFee:$m[j]['config']['firstFee'],
				conNum:$m[j]['config']['conNum'],
				conFee:$m[j]['config']['conFee'],
			}
			$dd.push(temp);
		}
		$d[i]=$dd;
		//Processe data 处理数据 ==>end
		var $e = document.createElement("table");
		fragment.appendChild($e);
	}
	$(".traTablelist").append(fragment);
	//create tables 生成配送表格
	$(".traTablelist").find('table').each(function(i,e){
		var oTraTable = new TraTableInit($n[i],$d[i],$id[i],e);
		oTraTable.Init();
	});
	//init listener 初始化表格内的修改&删除按钮事件
	var oTraButtonInit = new TraButtonInit();
	oTraButtonInit.Init();
}
//main ajax 主异步函数
$(function(){
	$.ajax({
		type: "POST",
		url: url,
		contentType: 'application/x-www-form-urlencoded; charset=UTF-8',
		dataType: "json",
		success: function(data){
		    ...
			addTra(data.data);
		    ...
		},
		error:function(data){
			...
	    	}
	});
})
//table setting 配置表格
var TraTableInit = function ($t,$d,$id,$e) {
	var oTraTableInit = new Object();
	$($e).data("name",$t);
	//初始化Table
	oTraTableInit.Init = function () {
		$($e).bootstrapTable({
			url: '',   
			method: 'post',    
			contentType: 'application/x-www-form-urlencoded; charset=UTF-8',
			striped: true,    
			cache: false,      
			pagination: false,     
			sortable: false,      
			sortOrder: "asc",   
			queryParams: '',
			sidePagination: "client", 
			pageNumber:1, 
			pageSize: 100,
			pageList: [],
			search: false,
			strictSearch: false,
			showColumns: false,
			showRefresh: false,
			minimumCountColumns: 2,
			clickToSelect: false, 
			singleSelect: false,
			height: '',
			uniqueId: '',
			showToggle:false,
			cardView: false,
			detailView: false,
			showHeader:true,
			columns: [
			[{
				field: '',
				title: '模块名称:'+$t,
				align: 'left'
			},{
				field: '',
				title: '<a href="javascript:void(0)" data-id="'+$id+'" data-url="distributionEdit.html" data-index="1400">编辑</a><span style="margin-right:15px"></span>',
				align: 'right',
				colspan: 4,
			}],[{
				field: 'shipping_area_name',
				title: '配送区域',
				formatter:function(value,row,index){
					if(empty(value)){ //function empty 0->F 1->T
						return '默认';
					}else{
						return value;
					}
				}
			},{
				field: 'firstNum',
				title: '首件',
				width:100
			},{
				field: 'firstFee',
				title: '首费',
				width:100
			},{
				field: 'conNum',
				title: '续件',
				width:100
			},{
				field: 'conFee',
				title: '续费',
				width:100
			}]],
			data: $d
		});
	};
	return oTraTableInit;
};

var TraButtonInit = function () {
	var oTraButtonInit = new Object();
	var postdata = {};
	oTraButtonInit.Init = function () {
	//Delegated events 委托事件
		$("div.traTablelist").on("click","a",function(e){
			if(e.target.nodeName.toLowerCase()=='a'){
				edit($(this));//funtciont edit ->change <a>'s href 为a标签赋予href值会触发跳转 
			}
		});
	};
	return oTraButtonInit;
};
 

```
### Key Optimization 关键优化
+ ```jsx document.createDocumentFragment(); ```<br/>Create a virtual node object, and then only operate 'append' once for DOM, reducing backflow.<br/>创建一个虚拟的节点对象,最后只对DOM操作append一次，减少回流
+ ```jsx $.each(); ``` 遍历
+ ```jsx$("div.traTablelist").on("click","a",function(e){...}); ```<br/>Delegated events<br/>委托事件

## Asynchronous data <br/> 异步数据

```jsx
{"data":[
	{
		"shipping_id":"1",
		"shipping_name":"整车模板",
		"methods":[
			{
				"shipping_area_name":"",
				"shipping_id":"1",
				"parent_ids":"",
				"is_default":"1",
				"config":
					{
						"firstNum":"1",
						"firstFee":"300.00",
						"conNum":"1",
						"conFee":"300.00"
					}
			},
			{
				"shipping_area_name":"北京,天津,河北,山西,内蒙古,辽宁,上海,江苏,浙江,安徽,江西,山东,河南,湖北,湖南,广东,广西,重庆,四川,贵州,云南,陕西,青海,宁夏回族自治区,",
				"shipping_id":"1",
				"parent_ids":"",
				"is_default":"0",
				"config":
					{
						"firstNum":"1",
						"firstFee":"300.00",
						"conNum":"1",
						"conFee":"300.00"
					}
			},
			{
				"shipping_area_name":"吉林,黑龙江,甘肃,",
				"shipping_id":"1",
				"parent_ids":"",
				"is_default":"0",
				"config":
					{
						"firstNum":"1",
						"firstFee":"500.00",
						"conNum":"1",
						"conFee":"500.00"
					}
			},
			{	
				"shipping_area_name":"西藏,",
				"shipping_id":"1",
				"parent_ids":"",
				"is_default":"0",
				"config":
					{
						"firstNum":"1",
						"firstFee":"800.00",
						"conNum":"1",
						"conFee":"100.00"
					}
			},
			...
			]
	},
	...
	]
}
```


To be continue
