# yii2-region
Yii2 中国省市区三级联动(https://github.com/chenkby/yii2-region)

## 安装

添加到你的composer.json文件

```
"chenkby/yii2-region": "dev-master"
```

## 配置

1、在地区的Model中添加以下方法
```php
    public static function getRegion($parentId=0)
    {
        $result = static::find()->where(['parent_id'=>$parentId])->asArray()->all();
        return ArrayHelper::map($result, 'id', 'name');
    }
```

2、在controller中添加以下action, if using advanced template, then we need change app as common
```php
    public function actions()
    {
        $actions=parent::actions();
        $actions['get-region']=[
            'class'=>\chenkby\region\RegionAction::className(),
            'model'=>\common\models\Region::className()
        ];
        return $actions;
    }
```

3、启用urlManager的enablePrettyUrl的，即隐藏index.php

## 使用

```php
$url=\yii\helpers\Url::toRoute(['get-region']);

echo $form->field($model, 'province')->widget(\chenkby\region\Region::className(),[
    'model'=>$model,
    'url'=>$url,
    'province'=>[
        'attribute'=>'province',
        'items'=>Region::getRegion(),
        'options'=>['class'=>'form-control form-control-inline','prompt'=>'选择省份']
    ],
    'city'=>[
        'attribute'=>'city',
        'items'=>Region::getRegion($model['province']),
        'options'=>['class'=>'form-control form-control-inline','prompt'=>'选择城市']
    ],
    'district'=>[
        'attribute'=>'district',
        'items'=>Region::getRegion($model['city']),
        'options'=>['class'=>'form-control form-control-inline','prompt'=>'选择县/区']
    ]
]);
```
province为省份配置，可用的选项可以查看Html::dropdownList。如果不需要县/区，可以把district删除。

## demo

![image](https://raw.githubusercontent.com/chenkby/yii2-region/master/demo.png)


##$model is the database table to store user address 

###table structure
```
CREATE TABLE IF NOT EXISTS `address` (
  `id` int(11) NOT NULL,
  `user_id` int(11) NOT NULL,
  `province` int(11) NOT NULL,
  `city` int(11) NOT NULL,
  `district` int(11) NOT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1;
```

###in controller
```
use common\models\Address;
...
...
$model = new Address();

....
```

###Rendering the view and passing this $model in
```
        return $this->render('order', [
			'model'=> $model,
        ]);

```
