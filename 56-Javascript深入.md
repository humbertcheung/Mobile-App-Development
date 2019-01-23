# Javascript深入

## 复选框交互功能


```html
<!DOCTYPE html>
<html>

<head>
	<meta charset="utf-8">
	<meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no, width=device-width">
	<title></title>
	<link href="https://cdn.bootcss.com/ionic/1.3.2/css/ionic.css" rel="stylesheet">
	<script src="https://cdn.bootcss.com/ionic/1.3.2/js/ionic.bundle.min.js"></script>
</head>

<body ng-app="ionicApp" ng-controller="ionicCtrl">
	<ion-header-bar class="bar-calm">
		<h3 class="title">会员列表</h3>
	</ion-header-bar>
	
	<ion-content>
		<div class="list">
			<ion-checkbox ng-repeat="user in users" ng-model="user.isMember" ng-checked="user.isMember">
				{{user.name}}
			</ion-checkbox>
			<div class="item">
				<!--将result以json的格式输出到页面-->
				<pre ng-bind="result | json"></pre>
			</div>
		</div>
	</ion-content>
	
	<script>
		var app = angular.module('ionicApp', ['ionic']);
		app.controller('ionicCtrl', function($scope) {
			$scope.result = [];
			$scope.users = [{
				name: "Annie",
				isMember: true
			}, {
				name: "Marlly",
				isMember: false
			}, {
				name: "Molly",
				isMember: false
			}, {
				name: "Seth",
				isMember: true
			}, {
				name: "Vincent",
				isMember: false
			}, {
				name: "Franke",
				isMember: false
			}]

			//监听users，一旦users发生变化，则会调用第二个函数参数，该函数会遍历数组
			$scope.$watch("users",function(newValue, oldValue){
				
				$scope.result = []
				for(index in $scope.users){
					//判断isMember是否为true
					if(newValue[index].isMember){
						$scope.result.push(newValue[index])
					}
				}
				console.log($scope.result)
			}, true)
		});

	</script>
</body>

</html>

```

![-c300](media/15444464943623/2018-12-10%2021-56-50.2018-12-10%2021_57_27.gif)

## 单选框交互功能


```html
<html>
    <head>
	<meta charset="utf-8">
	<meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no, width=device-width">
	<title>单选框</title>
	<link href="https://cdn.bootcss.com/ionic/1.3.2/css/ionic.css" rel="stylesheet">
	<script src="https://cdn.bootcss.com/ionic/1.3.2/js/ionic.bundle.min.js"></script>
</head>

<body ng-app="ionicApp" ng-controller="ionicCtrl">

	<ion-header-bar class="bar-calm">
		<h3 class="title">单选框</h3>
	</ion-header-bar>

	<ion-content>

		<div class="list">
			<ion-radio ng-repeat="subject in subjects" ng-value="subject.value" ng-model="msg.content">
				{{subject.text}}
			</ion-radio>
			<div class="item item-divider">
				被选中的值为：{{msg.content}}
			</div>
		</div>

	</ion-content>
	<script>
		var app = angular.module('ionicApp', ['ionic']);
		app.controller('ionicCtrl', function($scope) {
			$scope.subjects = [{
					text: "移动互联",
					value: "iOS & Web & Hybrid"
				},
				{
					text: "前端",
					value: "Web"
				},
				{
					text: "Java",
					value: "Java"
				},
				{
					text: ".Net",
					value: "C#"
				}
			];
			$scope.msg = {
				content: '空'
			};
			$scope.$watch("msg", function(newValue, oldValue){
				console.log($scope.msg)
			}, true);
		});
	</script>
</body>
</html>
```

![-c300](media/15444464943623/2018-12-10%2022-10-02.2018-12-10%2022_10_45.gif)

### 开关交互事件


```html
<!DOCTYPE html>
<html ng-app="ionicApp">

<head>
	<meta charset="utf-8">
	<meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no, width=device-width">

	<title>Toggle</title>
	<link href="https://cdn.bootcss.com/ionic/1.3.2/css/ionic.css" rel="stylesheet">
	<script src="https://cdn.bootcss.com/ionic/1.3.2/js/ionic.bundle.min.js"></script>
	<script type="text/javascript">
		angular.module('ionicApp', ['ionic'])

			.controller('MainCtrl', function($scope) {
				$scope.result = []
				$scope.settingsList = [{
						text: "无线局域网",
						checked: true
					},
					{
						text: "蓝牙",
						checked: false
					},
					{
						text: "蜂窝移动网络",
						checked: false
					},
					{
						text: "个人热点",
						checked: false
					}
				];
				$scope.$watch("settingsList", function(newValue, oldValue) {
					$scope.result = []
					for (index in newValue) {
						if (newValue[index].checked) {
							$scope.result.push(newValue[index])
						}
					}
					console.log($scope.result)
				}, true)
			});
	</script>

</head>

<body ng-controller="MainCtrl">

	<ion-header-bar class="bar-calm">
		<h1 class="title">Toggle</h1>
	</ion-header-bar>

	<ion-content>

		<div class="list">

			<div class="item item-divider">
				设置
			</div>

			<ion-toggle ng-repeat="set in settingsList" ng-model="set.checked" ng-checked="set.checked">
				{{ set.text }}
			</ion-toggle>

			<div class="item">
				<pre ng-bind="result | json"></pre>
			</div>

		</div>

	</ion-content>

</body>

</html>
```

![-c300](media/15444464943623/2018-12-10%2022-58-04.2018-12-10%2022_59_04.gif)

