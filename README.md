# router

diggphp router

## 特性

* 支持绑定中间件
* 支持参数绑定
* 支持分组路由
* 支持正则路由

## 实例化

``` php
$router = new \DiggPHP\Router\Router;
```

## 代码示例

``` php
$router->get('/path1/{id:\d+}', 'somehandler1');
$router->get('/path2[/{id:\d+}]', 'somehandler2');
$router->addGroup('/group', function (Collector $collector) {
    $collector->bindMiddlewares(['somemiddleware1', 'somemiddleware2']);
    $collector->bindParams([
        'q' => '111',
    ]);
    $collector->get('/sub1', 'otherhandler1');
    $collector->get('/sub2', 'otherhandler2', 'name1', ['middleware3']);
    $collector->get('/sub3/{id:\d+}', 'otherhandler3', 'name2', ['middleware3']);
});

$res = $router->dispatch('GET', '/path2/33');
// Array
// (
//     [0] => 1
//     [1] => somehandler2
//     [2] => Array
//         (
//             [id] => 33
//         )

//     [3] => Array
//         (
//         )

//     [4] => Array
//         (
//         )

// )

$res = $router->dispatch('GET', '/group/sub1');
// Array
// (
//     [0] => 1
//     [1] => otherhandler1
//     [2] => Array
//         (
//         )

//     [3] => Array
//         (
//             [0] => somemiddleware1
//             [1] => somemiddleware2
//         )

//     [4] => Array
//         (
//             [q] => 111
//         )

// )

$res = $router->dispatch('GET', '/group/sub2');
// Array
// (
//     [0] => 1
//     [1] => otherhandler2
//     [2] => Array
//         (
//         )

//     [3] => Array
//         (
//             [0] => middleware3
//             [1] => somemiddleware1
//             [2] => somemiddleware2
//         )

//     [4] => Array
//         (
//             [q] => 111
//         )

// )

$res = $router->dispatch('GET', '/group/sub3/11');
// Array
// (
//     [0] => 1
//     [1] => otherhandler3
//     [2] => Array
//         (
//             [id] => 11
//         )

//     [3] => Array
//         (
//             [0] => middleware3
//             [1] => somemiddleware1
//             [2] => somemiddleware2
//         )

//     [4] => Array
//         (
//             [q] => 111
//         )

// )

$res = $router->build('name2', ['id' => 11]);
// /group/sub3/11
```