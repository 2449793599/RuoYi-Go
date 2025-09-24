# 笔记


## 1. 项目运行

1. 项目对应前端代码为官方代码的指定提交：RuoYi-Vue3
```
commit 84b3565732b3a361966deff110124665133b9e15
Author: RuoYi <yzz_ivy@163.com>
Date:   Mon Mar 11 10:47:10 2024 +0800

    update copyright 2024
```
新增的前端DOCKERFILE：
```
# 使用官方Node.js作为基础镜像
FROM node:16 as builder

# 设置工作目录
WORKDIR /app

# 首先复制项目的依赖配置文件
COPY package.json yarn.lock ./

# 安装项目依赖，这一步会生成一个独立的层，并且只有在package.json或yarn.lock变化时才会重新执行
RUN yarn install

# 接着复制项目所有文件，这一步会生成一个新的层
COPY . .

# 构建项目，这一步也会生成一个新的层
RUN yarn build
```
2. 运行前端代码
```
git clone https://github.com/yangzongzhuan/RuoYi-Vue3.git

git checkout 84b3565732b3a361966deff110124665133b9e15

npm install
npm run dev
```
3. 运行后端代码
```
git clone https://github.com/Kun-GitHub/RuoYi-Go.git

go mod tidy
```
- 导入数据：db/ry_20240529.sql
- 调整配置：config/demo.yaml
- 配置加载：
```
func LoadConfig() (config.AppConfig, error) {

	var config config.AppConfig

	// viper.SetConfigName("config")
	viper.SetConfigName("demo")                     // 调整配置文件名称
	viper.SetConfigType("yaml")
	viper.AddConfigPath("./config")
	// viper.AutomaticEnv() //将环境变量与配置绑定

	if err := viper.ReadInConfig(); err != nil {
		return config, err
	}

	if err := viper.Unmarshal(&config); err != nil {
		return config, err
	}

	return config, nil

}
```
4. 运行项目
```
go run cmd/api/main.go
```

账户密码：admin/admin
```
强行修改密码：

$2a$10$7JB720yubVSZvUI0rEqK/.OXv/er2A3Wb8D5jQRHXi7aO1h0gPV3a
```

BUG修复：
- 验证码开启时，后端未返回开启标识字段！
```captcha_usercase.go
c := model.CaptchaImage{
    Code:           common.SUCCESS,
    Uuid:           id,
    Img:            b64s[strings.Index(b64s, ",")+1:],
    Message:        "操作成功",
    CaptchaEnabled: true,               TODO：这个每添加
}
```
