# 【实践记录】获取服务器公网 IP

文章编写于 2022年05月12日

## 依赖
引入扩展包： `composer require zhenmu/support`

## 代码

方式1
```
$ip = trim(CommandTool::make()->createProcess(CommandTool::formatCommand("dig +short myip.opendns.com @resolver1.opendns.com"))->run()->getOutput());

var_dump($ip);
```

方式2
```
$ip = gethostbyname(\request()->headers->get('host'));

var_dump($ip);
```