# Bootstrap-table-List For Distribution template <br/>基于bootstrap-table的配送模板表格列

The bootstrap-table list which I have designed is Based on bootstrap-table.(Your Product Manager:'I want it.')<br/>
我设计了一个基于bootstrap-table的表格列，在特定场合会用到它,尤其是产品经理坚持的时候，这次是一个配送模板

# For your valuable time <br/> 这不是所有人的菜，把你的时间用到需要的地方去吧

This is the special advanced use of Bootstrap-table(It's more complicated than usual, at least I think :P) .The Bootstrap-table basic source code changes are not involved. As long as you use Bootstrap, it is not difficult. And you do not need it for most of your time until your product manager thinks you need it.<br/> 
这只是我对于Bootstrap-table的特殊使用方法，不涉及源码的改动,只要你用过Bootstrap-table，这肯定不难，只是你未必会想到用它做个我这样的配送模板出来，一般table的配置使用已经满足绝大部分场景，所以这种操作并不是必须的，除非像我这样有需求的并且不得不这么干的时候。

# What does it look like <br/> 来看看它到底长啥样

Please click the picture. 
点击看大图会好点。
![图片名称](https://raw.githubusercontent.com/xuzijie1995/Bootstrap-table-List/master/images/bootstrap-table-list.png)

+ That's it.Just a little bit changes when you set your bootstrap-table.
+ You may ask 'Why don't you set the detailView true'.Of course, the detailView can handle it,but my PM wants that the details should be more intuitive.So I design this.
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
		var $m=$data[i]['methods'];
		for(var j=0;j<$m.length;j++){
			var temp={
				shipping_area:$m[j]['shipping_area'],
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
		var $e = document.createElement("table");
		fragment.appendChild($e);
	}
	$(".traTablelist").append(fragment);
	//生成配送表格
	$(".traTablelist").find('table').each(function(i,e){
		var oTraTable = new TraTableInit($n[i],$d[i],$id[i],e);
		oTraTable.Init();
	});
	//初始化表格内的修改&删除按钮事件
	var oTraButtonInit = new TraButtonInit();
	oTraButtonInit.Init();
}
//主函数
$(function(){
	loadingOpen();
	$.ajax({
		type: "POST",
		url: $base_path+'c=Shipping&m=getShippingList',
		contentType: 'application/x-www-form-urlencoded; charset=UTF-8',
		dataType: "json",
		success: function(data){
			loadingClose();
		    switch (parseInt(data.code)){
			case 1400300 :addTra(data.data);break;
			default : error(this,data);break;
		    }
		},
		error:function(data){
		error(this,data);
	    }
	});
})
//配送模块表格初始化构造
var TraTableInit = function ($t,$d,$id,$e) {
	var oTraTableInit = new Object();
	$($e).data("name",$t);
	//初始化Table
	oTraTableInit.Init = function () {
		$($e).bootstrapTable({
			url: '',   //请求后台的URL（*）
			method: 'post',      //请求方式（*）
			contentType: 'application/x-www-form-urlencoded; charset=UTF-8',
			striped: true,      //是否显示行间隔色
			cache: false,      //是否使用缓存，默认为true，所以一般情况下需要设置一下这个属性（*）
			pagination: false,     //是否显示分页（*）
			sortable: false,      //是否启用排序
			sortOrder: "asc",     //排序方式
			queryParams: '',//传递参数（*）
			sidePagination: "client",   //分页方式：client客户端分页，server服务端分页（*）
			pageNumber:1,      //初始化加载第一页，默认第一页
			pageSize: 100,      //每页的记录行数（*）
			pageList: [],  //可供选择的每页的行数（*）
			search: false,      //是否显示表格搜索，此搜索是客户端搜索，不会进服务端，所以，个人感觉意义不大
			strictSearch: false,
			showColumns: false,     //是否显示所有的列
			showRefresh: false,     //是否显示刷新按钮
			minimumCountColumns: 2,    //最少允许的列数
			clickToSelect: false,    //是否启用点击选中行
			singleSelect: false,
			height: '',      //行高，如果没有设置height属性，表格自动根据记录条数觉得表格高度
			uniqueId: '',      //每一行的唯一标识，一般为主键列
			showToggle:false,     //是否显示详细视图和列表视图的切换按钮
			cardView: false,     //是否显示详细视图
			detailView: false,     //是否显示父子表
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
					if(empty(value)){
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
//配送模块-初始化页面上面的按钮事件
var TraButtonInit = function () {
	var oTraButtonInit = new Object();
	var postdata = {};
	oTraButtonInit.Init = function () {
	//初始化页面上面的按钮事件
		$("div.traTablelist").on("click","a",function(e){
			if(e.target.nodeName.toLowerCase()=='a'){
				edit($(this));
			}
		});
	};
	return oTraButtonInit;
};
 
 
```
To be continue
